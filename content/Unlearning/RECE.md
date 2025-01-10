---
title: RECE
description: 
date: 10/01/2025
tags:
  - paper
url: http://arxiv.org/abs/2407.12383
---
RECE proposes a zero-shot method for unlearning concepts from T2I models. They do so by iteratively applying a closed-form matrix alignment method.

1. They align the K,V projection matrices of the concept to be erased (source) to another concept (destination)
2. They find an embedding $c'$ s.t. they reproduce the $k,v$ of the original projection matrix
3. goto step 1, now trying to forget $c'$

All of this can be done through closed form solutions. 

They apply the operation iteratively because applying it just once (UCE) is susceptible to adversarial attacks, capable of recovering the original embeddings (although it's not clear how could an attacker know the original embedding or original projection matrix)


