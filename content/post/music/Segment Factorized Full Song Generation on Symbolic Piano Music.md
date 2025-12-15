---
title: Segment Factorized Full Song Generation on Symbolic Piano Music
date: 2025-12-15
authors:
  - eri24816
image:
draft: false
tags:
  - music
  - music-generation
categories: music
series:
summary: "My first music generation paper.🎉We propose a full-song generation model with selective attention to related segments. It can smoothly collaborate with human composing music. Authors: Ping-Yi Chen, Chih-Pin Tan, Yi-Hsuan Yang"
video: YkrsK2dMfU8
---
[Paper](https://arxiv.org/pdf/2510.05881) | [Project page](https://sfs-demo.eri24816.tw/)

This is my first music generation paper, presented at NeurIPS 2025 AI for Music Workshop.

Approaches of full-song generation need to address the challenge of simultaneously maintain global coherence and generate long sequence efficiently. In respond, existing approaches has applied techniques such as hierarchical generation and selective attention.

We took inspiration from human songwriters' typical workflow: first decide the theme and structure of the song, then fill in the surrounding content.

Our model takes a user-provided song structure and a seed segment as input, and generates remaining segments through selective attention to related segments. Our model can generate in real-time for a song around 120 bpm and can generate in non-chronological orders.

We also made an interface where people can iteratively co-create music with the model (see 0:40 in the video).

## Abstract:

We propose the Segmented Full-Song Model (SFS) for symbolic full-song generation. The model accepts a user-provided song structure and an optional short seed segment that anchors the main idea around which the song is developed. By factorizing a song into segments and generating each one through selective attention to related segments, the model achieves higher quality and efficiency compared to prior work. To demonstrate its suitability for human–AI interaction, we further wrap SFS into a web application that enables users to iteratively co-create music on a piano roll with customizable structures and flexible ordering.