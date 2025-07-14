---
title: Segmented Patches  for image tokenization
description: 
date: 14/07/2025
tags:
  - unprocessed
url:
---
Traditional Image models and ViT take in input patches of the original image. I posit that these patches are uninformative and tend to lose the global picture, akin to the problems of tokenization in LLMs. As such I propose a framework where images are first segmented in their relevant parts and only then these segmented patches/boxes are fed in input to the ViT.