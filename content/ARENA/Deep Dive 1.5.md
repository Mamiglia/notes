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

