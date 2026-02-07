---
title: AI Is Learning to Think in Secret
description:
date: 07/02/2026
tags:
  - paper
url: https://www.lesswrong.com/posts/gpyqWzWYADWmLYLeX/how-ai-is-learning-to-think-in-secret
---
Chain-of-thought is a god-given gift to safety researchers because it allows some transparency into the model thoughts, and is actually getting more and more widespread. Very nice.

We can see the model reason in plain english and this is awesome because it allows us to monitor the CoT and find about negative behaviours.

![](https://res.cloudinary.com/lesswrong-2-0/image/upload/f_auto,q_auto/v1/mirroredImages/gpyqWzWYADWmLYLeX/uu3dr1is7eomvlq7zmar)

## I wish it was this easy
look at this example:
![](https://res.cloudinary.com/lesswrong-2-0/image/upload/f_auto,q_auto/v1/mirroredImages/gpyqWzWYADWmLYLeX/heyktfzwzo5zzhyffq3z)
The model is not having a stroke, nor hallucinating, rather it's "speaking" in its own language (someone calls it "Thinkish"). but why?

This thinkish is once again very uninterpretable, and it makes it very difficult for us to undersatnd whether the model is scheming.

### Why thinkish?
There are different reasons for why a model may develop thinkish:
1) **efficiency compresses structure**: the model may learn to compress information to make its reasoning more efficient. i.e. you usually don't reason in english, rather you have your own reasoning path that sometimes is not verbalizes
2) **representational need shift vocabulary**: sometimes it's necessary to develop your own semantic to reason more effectively on some specific topic

### Why not train against it?
While this may seem a good idea, for instance by enforcing grammatical coherence in the CoT, early research in this direction shows that probably you'll just create a better model at hiding thinkish.

In general, if we want to have a nice, interpretable CoT we need to **avoid training the CoT** at all cost. 
> sever the pathways that transmit the pressure. Split reasoning and output generation into separate models, so the pressure to look good accumulates only in the part that talks to users - the reasoning model never directly learns to prettify itself because nobody’s judging its appearance (though indirect pressure remains)

### so?
We wanted faithfulness because we were trying to catch bad behavior - deception, manipulation, seizing control of the financial system, tiling the universe with paperclips, etc. If the chain of thought perfectly mirrors the model’s internal computation, then reading it tells us everything, including any nefarious plans. Faithfulness seemed like the path to safety.

But notice that faithfulness is actually a _stronger_ condition than what we need. We don’t need the CoT to be a perfect mirror of the model’s internal computations. We just need it to be _useful for catching bad behavior_.

The question isn’t “does this CoT correspond to some Platonic internal reasoning process?” It’s simpler: “if the model is scheming, will evidence of that show up in the CoT?”

Researchers call this property “monitorability.” And unlike faithfulness, monitorability is something we can actually test directly, by setting up situations where models behave badly and checking whether we can catch them. Either we can or we can’t.

### Actually funny methods for reducing Thinkish
- You could paraphrase each step into a standardized, formal structure—not “hmm, let me think about whether 7 is prime” but “PRIMALITY_CHECK(7)”- to scramble stylistic choices where information might hide.
- You could break reasoning into isolated subtasks. No step can see what the other steps are doing. Hard to maintain a conspiracy when none of the conspirators are allowed to talk to each other.
- You could run “hot potato” schemes where you randomly swap which model instance is writing, mid-thought. “Okay, I’m going to start by-” YOINK, new model, your turn. “Wait what were we-” YOINK. Try maintaining a secret code across that. (**love this one**)
- Have weaker models check whether they can actually follow each step - if Claude Jr. can’t understand what Claude Sr. is saying, maybe Claude Sr. is hiding something: “MOOOOOM, Claude’s being WEIRD again!”

### Looming Neuralese
Some reasearcher is asking "we don't reason in english, so why should models do it?" and is actively developing "reasoning in latent space". This is sometimes called "Neuralese", and the author argues that it's much more difficult to monitor. 

