---
title: Controllable music generator
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

It has a piano roll where you can make music. When needed, you can ask AI to generate a bar for you. It will offer multiple options for your choice, and you can also edit its generation. Occasionally, there emerges music that sounds amazingly good but you didn't think of, to my belief, that kind of accident is the main value of this AI in music composition.

The reason I assume that the AI is only an auxiliary inspiration provider and that it's still human composing music is that the AI has yet no ability to percept the global structure (I'm currently working on a new model which remedies that). You have to fix parts it generate strangely, and sometimes you need to specify chords and velocities for each bar to increase the chance you get a reasonable outcome. 


There are four controllable features: chord, velocity, note density, and polyphony, where chord can be set in a per-beat granularity, and other features can be set per bar.

The underlying model is a transformer trained to generate symbolic music auto-regressively, with a dataset consisting of piano performances of pop music.



