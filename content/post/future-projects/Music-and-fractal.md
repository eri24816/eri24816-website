---
title: Music and fractal
date: 2025-08-25
authors:
  - eri24816
image: /Pasted image 20241116182611.png
draft: false
tags:
  - music
  - fractal
  - generative-art
categories: future projects
series: 
summary:
---
I think of two ways converting fractals into sound or music.

Given a fractal on a 2d plane, we can view each vertical slice of pixels as a waveform or spectrum and scan over the fractal horizontally to produce a playable audio. It may require the fractal to be a real-valued map (instead of binary).

The waveform option feels more reasonable because it more likely to produce non-uniform characteristics on the sound, such as varying intensities across frequencies. However, it will be tricky to connect each waveform across time.

Another possibility is reading a fractal as symbolic music. We can sample through the fractal in a quantized grid 