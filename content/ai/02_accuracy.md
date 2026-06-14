---
title: "Improving LLM Accuracy"
weight: 2
---
# Improve accuracy of LLM outputs
LLMs are generic and non-deterministic. This is mostly a good thing, since they can respond to a large variety of prompts, and can also produce "creative" outputs.

However, this is not always the desired output. Accuracy can be critical for certain applications.
For example, consider an international driver's license validator.
The app scans drivers licences from various countries, identify the correct country, and searches the DL database of that country via an API, and validates if the license is genuine or not.

If there are real-life decisions which are impacted by this app, it would be expected to have more than 99% accuracy.

## Talking to LLMs
Before exploring how to make LLM outputs better, it needs to generate an output in the first place.

Okay, you've got a foundational model from a lab. Imagine gemma4. They've provided the weights, now how does one make it "talk"?
What is even the correct way to talk to such a thing?

I'll be exploring that in this section.

## High level ideas
At a high level, these things can improve accuracy:
* Prompt engineering. Steering the LLM in the right direction and providing better context.
* Fine tuning. Post training over specialized datasets to embed more knowledge about them. Driving License dataset in the above case.
* Making the model produce a confidence score, and rejecting outputs below a score. It might already be doing this.