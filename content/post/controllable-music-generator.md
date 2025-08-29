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

After 3 years of training music generation model and interacting with them through cli, this is my first attempt to build a user interface for them.

The underlying model is a transformer trained to generate symbolic music auto-regressively, with a dataset consisting piano performances of pop music. Four bar-level features: chord, velocity, note density, and polyphony, can be controlled by user.