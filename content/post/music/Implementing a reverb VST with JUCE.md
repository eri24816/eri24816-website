---
title: Implementing a reverb VST with JUCE
date: 2022-03-16T17:33:37+08:00
draft: false
image: https://i.imgur.com/8qGh9bk.png
categories: music
summary: 我學到非常多好玩的觀念，像是 z transform、怎麼看 zero pole plot、如何用 C++ 來 OOP (踩各種指標的坑XD)。
tags:
  - highlights
  - cpp
  - audio
  - VST
---
# Background

When something I’ve always wanted to do ends up being taught in a class, that class instantly becomes really attractive to me.

In the course _Analysis of Digital Music Signal_, each group of students used **JUCE** (a C++ framework for making VSTs) to build a VST effect. Our group made a **reverb**. The purpose of reverb is to add echoes to music, creating the feeling of being in a cathedral or concert hall.

This was the most fun course I’ve taken this year. I learned many interesting concepts, like the **z-transform**, how to read **zero-pole plots**, and how to do **OOP in C++** (while stepping into all kinds of pointer traps XD).


# Overall Architecture

The diagram below shows the architecture we referenced.

![Image](https://i.imgur.com/gLgWwXH.jpg#center)
https://ccrma.stanford.edu/~jos/pasp/Zita_Rev1.html

First, the 2 input channels are split into 8 channels by a **2×8 matrix**, then sent into a loop. Inside the loop, all 8 channels are processed in parallel. The signal passes sequentially through four basic filters: **all pass**, **feedback matrix**, **lowpass**, and **delay line**, and then repeats. After the feedback matrix, there’s an output path that exits the loop, going through an **8×2 matrix** before being sent back to 2 output channels.

Although the system looks straightforward (we “just” need to implement each basic module), the real difficulty lies in ensuring two aspects at the same time:

1. Producing good sound
2. Maintaining IIR stability

This is because we used an **IIR** structure, which contains a feedback loop and becomes unstable if the parameters are not tuned carefully. With an **FIR**, stability wouldn’t be an issue, but it requires more computational cost.

So, after building the overall architecture, we spent most of our time experimenting with parameters to achieve the best sound possible while keeping the IIR stable.

## Implementing the basic modules

Our reverb filter implementation is built from several modules (delay line, low-pass filter, etc.), each of which is a multi-channel causal filter. A module can itself contain smaller sub-modules, with the reverb filter being the top-level module.

Each module is a class which implements

```c++
float* update(float* input)
```
In each time step of calculation, this method receives a float array as input, do calculations, and returns another float array as output. Most modules maintain internal state, making the `update` function not pure.

## Delay line
A delay line simply outputs the input after a delay of several samples. In the frequency domain, it acts as $z^{-n}$ . It can be easily implemented with `std::queue`.
```c++
float* update(float* input) override{
    for (int i = 0; i < inputDim; i++) {
        queues[i].push(input[i]);
        outputBuffer[i] = queues[i].front();
        queues[i].pop();
    }
    return outputBuffer;
}
```
Later, to reduce computation time, I wrote another version without using `std::queue`.
```c++
float* update(float* input) override {
    for (int i = 0; i < inputDim; i++) {
        outputBuffer[i] = arr[i][pos[i]];
        arr[i][pos[i]] = input[i];
        pos[i]++;
        if (pos[i] == len[i])pos[i] = 0;
    }
    return outputBuffer;
}
```
## Low pass filter
This module is a first-order low pass filter. It acts as$(1-a)+az^{-1}$.

We implemented it by adding the previous output to the current input and using that as the current output, which effectively apply exponential smoothing on the waveform.
```c++
float* update(float* input) override {
    mult(inputDim, input, 1 - a);
    mult(inputDim, feedback, a);
    add(inputDim, input, feedback);
    copy(inputDim, feedback, input);
    return mult(inputDim, input,1);
}
```

Parameter `a` is used to control the cutoff frequency. They relatio
$$a=e^{-2\pi \frac{ \mathit{Cutoff}} {\mathit{SampleRate}}}$$

## Allpass

它是二階的 all pass filter。這是這個 project 最難做的 filter，我們必須由該 filter 應具有的性質，推導出實作的方法。

二階 all pass filter 的 pole-zero plot 有兩個 poles 和兩個 zeros，且此 filter 的性質受到以下限制:
1. 根據 complex conjugate root theorem，兩個 poles(zeros) 必共軛
2. 為了讓各頻率的 amplitude response 皆維持在 1，每個 pole 對單位圓的反演處必須有一個 zero，反之亦然

結合這兩項限制，代表我們只需要一組參數 $(r,θ)$ 來控制某一個 pole 的位置，即可以決定所有 pole 和 zero 的位置，也決定了這個二階 filter。

![Image](https://i.imgur.com/4HVI7Xu.png#centers)

接下來就是用 $(r,θ)$ 來推出 IIR 的結構。

設此 filter 的 response 為 $H(z)=\frac{P(z)}{Q(z)}$，$P$ 的兩根為 zero，$Q$ 的兩根為 pole。以此為出發點，P 和 Q 為:[^1][^2]
[^1]: 國中學了但從未用過的根與係數終於在這裡用到了，覺得國中很浪費時間的心情稍稍下降了一點。
[^2]:第一行的 $r^2$ 用於 normalize

$$\begin{aligned}
P(z) &= r^2(z-r^{-1}e^{iθ}) (z-r^{-1}e^{-iθ})  \\\\
&=r^2z^2-r(e^{iθ}+e^{-iθ})z+1\\\\
&=r^2z^2-2r\cos(θ)z+1
\end{aligned}$$

$$\begin{aligned}
Q(z)&=(z-re^{iθ})(z-re^{-iθ})\\\\
&=z^2-r(e^{iθ}+e^{-iθ})z+r^2\\\\
&=z^2-2r\cos(θ)z+r^2
\end{aligned}$$

令$X(z)$為輸入，$Y(z)$為輸出:
$$\begin{aligned}
Y(z)&=H(z)X(z)\\\\
&=\frac{P(z)}{Q(z)}X(z)\\\\
\end{aligned}$$

$$\begin{aligned}
Q(z)Y(z)&=P(z)X(z)\\\\
(z^2-2r\cos(θ)z+r^2)Y(z)&=(r^2z^2-2r\cos(θ)z+1)X(z)\\\\
(1-2r\cos(θ)z^{-1}+r^2z^{-2})Y(z)&=(r^2-2r\cos(θ)z^{-1}+z^{-2})X(z)\\\\
\end{aligned}$$

經過移項，最後可以得到以下 difference equation:
$$y[n] = r^2 * x[n]-2r\cos(θ) * x[n-1]+x[n-2]+2r \cos(θ) * y[n-1]-r^2* y[n-2]$$

其中，$x[n]$為目前輸入的 sample，$y[n]$為目前欲輸出的 sample。因為計算 $y[n]$ 會用到 4 個以前的值，所以此 filter 需要 4 條 delay line(或 4 個 memory)。實作如下:
```c++
float* update(float* input)override {
    for (int i =0;i<inputDim;i++) {
        output[i] = R2[i] * input[i] - twoRCosTheta[i] * x1[i] + x2[i] + twoRCosTheta[i] * y1[i] - R2[i]*y2[i];

        x2[i] = x1[i];
        x1[i] = input[i];

        y2[i] = y1[i];
        y1[i] = output[i];
    }
    return output;
}
```

## Comb
Comb 超簡單，因為它是 FIR，沒有 feedback:
```c++
float* update(float* input)override {
    add(inputDim, input, delay.update(input));
    return input;
}
```

![Image](https://i.imgur.com/u0USmfH.png#centers)

(但其實這個沒有用到，我們用 all pass 代替它了

## Reverb

Reverb 這個最大的 filter 就是把所有小 filter 組裝起來。

```c++
float* update(float* input) override{
    
    float dry[2];
    copy(inputDim, dry, input);

    input = distrib * inDelay.update(input);

    delayFilters.update(feedBack);

    add(NCH,input, fbDelayLine.update(feedBack));

    input = allpass.update(input);

    input = mult(inputDim, input, _decay);

    float* output = feedbackmatrix * input;

    copy(NCH,feedBack, output);

    return dcBlocker.update(add(inputDim,mult(inputDim,outDistrib*output,wetAmount), mult(inputDim, dry,dryAmount)));
}
```


## 調整參數

把所有 filter 組裝起來之後，我們遇到的第一個問題是跑了一段時間後數值很容易爆炸。這是因為在主迴圈中，如果有某個頻率的 amplitude response 超過 1，經過數次遞迴，那個頻率的強度就會指數發散。

我們隨即調低迴圈的 feedback matrix 的值，使得 amplitude response 下降。這時又出現另一個問題:殘響時間不夠長。當迴圈的 amplitude response 小於 1 太多，經過數次遞迴，聲音就會快速衰減並消失。

也就是說，必須讓每種頻率的 amplitude response 都小於 1，但只能小一點點。

後來我們找到的作法是:

1. low pass 的 amplitude response 小於等於 1
2. all pass 的 amplitude response 等於 1 (當然)
3. feedback matrix 的每個 row 絕對值總和稍微小於 1 

這樣的話就可以保證不會爆炸了。原因如下:
以最嚴格的情況來看，假設 low pass 和 all pass 的 amplitude response 都是 1，訊號從 feedback matrix 的輸出繞一圈回到 feedback matrix 前的 amplitude 增益就是 1，強度不變。而 feedback matrix 會將 8 個 channel 重新混合，feedback matrix 的每個 row 絕對值總和小於 1 這項限制保證了混合後的訊號不會因疊加而增強。

不過因為訊號繞了一圈後，會發生複雜的項位改變，就算 feedback matrix 每個 row 絕對值總和非常接近 1，訊號卻很容易因為破壞性疊加而有很大的衰減率。而且 VST 的 sample rate 非常高(例如44100Hz)，所以還是會有不到 1 秒聲音就幾乎不見的情況。後來我們對這個問題的解法是在 IIR 前串聯約 50ms 的 convolution (FIR) ，最終效果不錯。(暴力解XD)