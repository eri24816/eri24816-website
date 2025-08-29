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

There are four controllable features: chord, velocity, note density, and polyphony, where chord can be set in a per-beat granularity, and other features can be set per bar.

The underlying model is a transformer trained to generate symbolic music auto-regressively, with a dataset consisting of piano performances of pop music.



