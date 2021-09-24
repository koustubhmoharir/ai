---
layout: post
title:  "Paper Notes: Evaluating Large Language Models Trained on Code"
date:   2021-09-24 23:30:00 +0530
categories: papers
tags: 
---

## Paper Details
[PDF Link](https://arxiv.org/pdf/2107.03374.pdf)

Lots of authors

Year of publication: 2021

## Notes

This paper is about Codex - a suite of large language models with the same architecture as GPT3 trained on code with various levels of fine-tuning. The authors have conducted experiments at various parameter sizes. The framework to evaluate performance is released at [HumanEval](https://www.github.com/openai/human-eval). The level of difficulty is said to be similar to simple software interview questions. Performance is measured by counting questions where the model generates correct code in one or more attempts. Correctness is verified by running unit tests. Codex S - fine tuned on stand-alone python functions is able to produce at least one correct solution to for 77.5% of the problems given 100 attempts per problem. If 100 attempts are allowed but only the most likely one (by probability) is allowed to be verified, the accuracy drops to 44.5%. The fraction of correct solutions in the 100 attempts is not documented.

If larger models are used (largest being 12 billion parameters), the accuracy increases but it is proportional to the logarithm of the number of parameters. To get a 10% improvement with only one evaluation allowed per problem, the model size must be increased by 10 times. Strangely, the authors call this scaling!

On another dataset (APPS, Hendrycks et al. (2021)) used for benchmarking, reported accuracy is similar ranging from 28% for a single attempt to 72% for 100 attempts.

The authors also describe a model to generate doc-strings from code. The accuracy for a single attempt is just 20%. With 10 attempts, it goes up to 46.5%

Several examples are shown in an appendix. The generated solutions include "code" like
```
# TODO: implement this function
pass
```
In most cases, plausible code is generated without syntactical errors, though the semantics are often wrong.

The authors report that the generated solutions usually do not copy code verbatim from the training set. But the model cannot be said to be generating a solution for a problem either.
> ... there is an intuitive notion that, given its training objective, Codex is better described as “trying” to continue the prompt by either matching or generalizing the training distribution, than as “trying” to be helpful to the user.

I think it is abundantly clear that this model does not understanding the code it generates. Like other large language models, it works by approximating the statistical distribution of words in context. As to the question whether these large models contain any latent understanding, my view is that they don't. Large language models get grammar right and similarly Codex gets syntax right. It is easy to see, especially with the syntax of a programming language where there are only a finite (and small) number of grammatical rules, that a model based on statistical distribution will almost always get these rules right.

I cannot imagine how this approach can be extended to produce even marginally useful code in a real-world setting. However the authors seem to have a belief that progress is inevitable and devote a lot of content in this paper to possible consequences (both positive and negative) when such models come into common use. I don't see any meaningful progress happening without incorporating any rigorous rule-based semantic analysis. That said, there are plenty of aspects to a non-trivial program that are totally out-of-bounds for static semantic analysis. While the authors do touch upon some of these (concurrency, parallelism, maintainability, security), and seem to believe that at some point in the future, models will address these, there is no hint of how that might be achieved other than a vague reference to Reinforcement Learning.
