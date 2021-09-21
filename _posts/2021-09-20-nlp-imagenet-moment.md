---
layout: post
title:  "Article Notes: NLP's ImageNet moment has arrived"
date:   2021-09-19 22:20:00 +0530
categories: articles
tags: word-embedding
---

>
Word2vec found adoption ... Since then, the standard way of conducting NLP projects has largely remained unchanged: word embeddings pretrained on large amounts of unlabeled data ... are used to initialize the first layer of a neural network, the rest of which is then trained on data of a particular task. On most tasks with limited amounts of training data, this led to a boost of two to three percentage points. ... they have a major limitation: they only incorporate previous knowledge in the first layer of the model --- the rest of the network still needs to be trained from scratch.

Improving performance by 2 to 3 percentage points doesn't seem like an advance at all. It seems like benchmark chasing.

>
Word2vec and related methods are shallow approaches ... they will be helpful for many tasks, but they fail to capture higher-level information. A model initialized with word embeddings needs to learn from scratch not only to disambiguate words, but also to derive meaning from a sequence of words. This is the core aspect of language understanding, and it requires modeling complex language phenomena such as compositionality, polysemy, anaphora, long-term dependencies, agreement, negation, and many more. It should thus come as no surprise that NLP models initialized with these shallow representations still require a huge number of examples to achieve good performance.

>
In NLP, models are typically a lot shallower than their CV counterparts. Analysis of features has thus mostly focused on the first embedding layer, and little work has investigated the properties of higher layers for transfer learning

>
In order to predict the most probable next word in a sentence, a model is required not only to be able to express syntax (the grammatical form of the predicted word must match its modifier or verb) but also model semantics. Even more, the most accurate models must incorporate what could be considered world knowledge or common sense. Consider the incomplete sentence "The service was poor, but the food was". In order to predict the succeeding word such as “yummy” or “delicious”, the model must not only memorize what attributes are used to describe food, but also be able to identify that the conjunction “but” introduces a contrast, so that the new attribute has the opposing sentiment of “poor”.

This is a good argument that models that are good at language modeling will memorize a lot of semantics and common-sense information. Doesn't seem like a good way to actually learn anything though.

>
ELMo, ULMFiT, and the OpenAI Transformer have empirically demonstrated how language modeling can be used for pretraining ... All three methods employed pretrained language models to achieve state-of-the-art on a diverse range of tasks ... In many cases ... these improvements ranged between 10-20% better than the state-of-the-art on widely studied benchmarks ... *it is very likely that in a year’s time NLP practitioners will download pretrained language models rather than pretrained word embeddings*

