---
title: A deep dive in Attention Head 1.5 of GPT2
description: 
date: 03/06/2025
tags:
 - post
url:
---
GPT-2 attention head 1.5 presents a particular attention pattern, as it appears to attend to semantically similar pairs. For example the token *"cat"* attends to *"dog"* and *"elephant"* while the token *"red"* attends to *"green"* and *"blue"*. Most interestingly the head avoids attending to the current token, so that *"dog"* doesn't attend to itself, or other occurrences of the same token. This short research project aims to mechanistically explain such a peculiar behaviour, in the hope of developing some interesting technique to analyze attention patterns.

We start by analyzing attention pattern on common sentences and empirically observe the following behaviour:
- A given token belonging to a specific semantic category attends to other tokens in the sequence that belong to the same category.
- The token does **not** attend to itself, or to previous instances of itself within the sequence.
- If no other token belonging to the same semantic category can be found in the sequence than the token attends to the *< bos >* token.

## Component ablation
In order to find out which components matter to the attention head I:
1. Engineer a specific prompt to elicit its behaviour
2. Define a metric to measure distance from expected behaviour
3. Ablate single components

In order to elicit the specific semantic behaviour of the attention head we engineer the prompt as a bunch of tokens belonging to handpicked semantic categories shuffled together. Example:
> `<bos> cat horse blue sun apple 32 red snow sun rain happy 32 blue sun green`

Then we define a 