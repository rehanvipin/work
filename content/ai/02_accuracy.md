---
title: "Improving LLM Accuracy"
weight: 2
---
# Improve accuracy of LLM outputs
LLMs are generic and non-deterministic. This is mostly a good thing, since they can respond to a large variety of prompts, and can also produce "creative" outputs.

However, this is not always the desired output. Accuracy can be critical for certain applications.
For example, consider an international driver's license validator.
The app scans drivers licences from various countries, identify the correct country, and searches the DL database of that country via an API, and validates if the license is genuine or not.

If there are real-life decisions which are impacted by this app, it should be expected to have more than 99.9% accuracy at least.

## Talking to LLMs
Before exploring how to make LLM outputs better, it needs to generate an output in the first place.

Okay, you've got a foundational model from a lab. Imagine gemma4. They've provided the weights, now how does one make it "talk"?
What is even the correct way to talk to such a thing?

I'll be exploring that in this section.

This was good - https://huggingface.co/docs/transformers/chat_templating?template=Mistral

Language models are trained to complete sentences. In a basic setup, when the user gives a text to the model,
it would continue it till the max token count or an end of sentence is reached.
However, for a chat experience, the base model needs to be fine tuned to understand the roles in a chat and *respond* instead of just completing sentences.

The main roles in a chat are : user (the end-user talking to the llm), system (any extra prompts added by the chat client to make the model respond in a certain manner, e.g., coding harnesses), and assistant (the llm responses themselves).

Now this fine tuning is doing via special tokens which are not visible in the chat, otherwise the chat would look cluttered. Example from qwen:
```
<|im_start|>system
You are a helpful assistant<|im_end|>
<|im_start|>user
Hello<|im_end|>
<|im_start|>assistant
Hi there<|im_end|>
<|im_start|>user
How are you?<|im_end|>
<|im_start|>assistant
<think>
</think>
```
This tells the model how to continue the conversation. If you really want, you can make the model just continue the user's sentences too. This might be useful in certain cases, e.g., code completion in an IDE. Otherwise you can tell the model that it needs to provide a response to the user's message.

Each company and model can have its own "chat template", and you'd need to use that since that's what the model was trained on.

### Model Architectures
The simple completion architecture described above is for a Causal Language Model, which is used by GPT, Claude.

### Randomness
At a high-level, when communicating with the model via one of the common completion API, you can control the randomness of the response via two parameters:
* top_p
* temperature

To understand these, first we need to understand that to predict the next token, LLMs will have a set of tokens to choose from.
Within that set, the LLM would have assigned each one a probability of how likely it is to be the next in the sequence.
It then samples from that set to generate the next token in the sequence.

Temperature adjusts this by "smoothening" the distribution curve, it varies from 0 to 1, and each probability is raised to that power. E.g. everything gets raised to the power of 0.5. Higher the temperature, more likely is the random output.

Top P is an algorithm where you sort tokens by probability, highest probability first, and then start creating a smaller sample. Pick the first token, see if the cumulative proability is less than P, else pick another one, and keep going till P is reached.
Lower the value of P, lesser is the final sample set likely to be, and less random the output.

## Process
At a high level, these things can improve accuracy:
* Prompt engineering. Steering the LLM in the right direction and providing better context.
* Fine tuning. Post training over specialized datasets to embed more knowledge about them. Driving License dataset in the above case.
* Making the model produce a confidence score, and rejecting outputs below a score. It might already be doing this.

To reduce hallucinations, you need to iteratively improve the prompt and see how it behaves.
If the LLM Output is used for further processing, it can also be made to generate structured output,
e.g., by using special tokens like mentioned above, where you give it the starting of a JSON / XML string and ask it to complete it.

An evaluation dataset can also help in refining the prompt and adjusting the inference parameters (e.g., temperature) to test how well the setup is working in a real world setting.
An eval dataset has prompts and the expected results which the user wants from each one. The LLM itself can be used to judge how close the actual output is close to the expected output.

Instruction Tuning. Learn more here : https://huggingface.co/learn/smol-course/unit1/1 .