---
title: My first music generator
date: 2025-08-24
authors:
  - eri24816
image: https://i.imgur.com/6oABwUs.jpeg
draft: false
tags:
  - music
  - music-generation
  - web
  - highlights
categories: music
series:
summary:
---
![](https://i.imgur.com/6oABwUs.jpeg)

After 3 years of training music generation models and interacting with them through CLI, this is my first attempt to build a user interface for them. Playing with it feels really great, it feels like the model is actually usable.

[https://github.com/eri24816/midi-gen](https://github.com/eri24816/midi-gen)

It has a piano roll where you can make music. When needed, you can ask AI to generate a bar for you. It will offer multiple options for your choice, which can be further customized. Occasionally, there emerges music that sounds amazingly good but you didn't think of, to my belief, that kind of accident is the main value of this AI in music composition.

I assume that in this interactive scenario the AI is only an auxiliary inspiration provider and that it's still human composing music because the AI has yet no ability to percept the global structure (I'm currently working on a new model which remedies that). You have to fix glitches in the generation, and sometimes you need to specify chords and velocities for each bar to increase the chance you get a reasonable outcome. You can also try asking it to generate all bars with no hints provided, and it will generate something locally reasonable but globally weird.


