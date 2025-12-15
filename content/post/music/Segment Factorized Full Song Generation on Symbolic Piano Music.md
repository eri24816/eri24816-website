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
summary: My first music generation paper with Chih-Pin Tan and Yi-Hsuan Yang proposes a full-song generation model that uses selective attention to related segments and can smoothly co-compose with the user in a flexible order.🎉
video: YkrsK2dMfU8
---
[paper](https://arxiv.org/pdf/2510.05881) | [project page](https://sfs-demo.eri24816.tw/)
We propose the Segmented Full-Song Model (SFS) for symbolic full-song generation. The model accepts a user-provided song structure and an optional short seed segment that anchors the main idea around which the song is developed. By factorizing a song into segments and generating each one through selective attention to related segments, the model achieves higher quality and efficiency compared to prior work. To demonstrate its suitability for human–AI interaction, we further wrap SFS into a web application that enables users to iteratively co-create music on a piano roll with customizable structures and flexible ordering.