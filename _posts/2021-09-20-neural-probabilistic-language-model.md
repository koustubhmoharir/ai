---
layout: post
title:  "Paper Notes: A Neural Probabilistic Language Model"
date:   2021-09-19 21:31:37 +0530
categories: papers
tags: word-embedding
use_math: true
---
## Paper Details
[PDF Link](https://www.jmlr.org/papers/volume3/bengio03a/bengio03a.pdf)

Authors
- Yoshua Bengio, bengioy@iro.umontreal.ca
- Réjean Ducharme, ducharme@iro.umontreal.ca
- Pascal Vincent, vincentp@iro.umontreal.ca
- Christian Jauvin, jauvinc@iro.umontreal.ca

Year of publication: 2003

## Notes

>
If one wants to model the joint distribution of 10 consecutive words in a natural language with a vocabulary V of size 100,000, there are potentially $100000^{10} − 1 = 10^{50} − 1$ free parameters. When modeling continuous variables, we obtain generalization more easily (e.g. with smooth classes of functions like multi-layer neural networks or Gaussian mixture models) because the function to be learned can be expected to have some local smoothness properties. For discrete spaces, the generalization structure is not as obvious: any
change of these discrete variables may have a drastic impact on the value of the function to be estimated, and when the number of values that each discrete variable can take is large, most observed objects are almost maximally far from each other in hamming distance

Need to read up on Hamming distance to understand the last line.

---

>
A useful way to visualize how different learning algorithms generalize, inspired from the view of non-parametric density estimation, is to think of how probability mass that is initially concentrated on the training points (e.g., training sentences) is distributed in a larger volume, usually in some form of neighborhood around the training points. In high dimensions, it is crucial to distribute probability mass where it matters rather than uniformly in all directions around each training point.

---

The paper defines a statistical model of language where the probability of a sequence of words is the product of probabilities of each word in the sequence conditional on all its previous words.
In an n-gram model, the product is restricted to last n words only.

>
1. Associate with each word in the vocabulary a distributed word feature vector (a real valued vector in $\mathbb R^m$)
2. Express the joint probability function of word sequences in terms of the feature vectors of these words in the sequence
3. Learn simultaneously the word feature vectors and the parameters of that probability function

Number of features m is much smaller than the vocabulary size.

Step 2 above is implemented by training a neural network that takes the word feature vectors of previous `n` words as inputs and produces a probability for each word in the vocabulary as an output. Because the weights in this neural network are the same regardless of the words in the sequence, it is expected that differences in the words will get encoded in the feature vectors, while the "logic" of the distribution function distribution will get encoded in the weights of the neural network.

For Step 3, the average log likelihood of all observed sequences in the corpus is maximized. A penalty term which is the squared norm of all the parameters (features vectors as well as weights of subsequent layers of the network, but not biases) is included in the objective function to be maximized. The penalty term _regularizes_ the weights by preventing any one weight from becoming much larger than others. Regularization of feature vectors seems a bit dubious.

The paper says that this idea is expected to work because words that occur in similar positions will have similar feature vectors and hence similar sentences will have similar probabilities. Hence, even if a particular sentence does not appear in the training corpus, its probability will be correctly estimated if similar sentences _have_ appeared.

>
Having seen the sentence “The cat is walking in the bedroom” in the training corpus should help us generalize to make the sentence “A dog was running in a room” almost as likely, simply because “dog” and “cat” (resp. “the” and “a”, “room” and “bedroom”, etc...) have similar semantic and grammatical roles.

One limitation of such models is that they do not take the rules of grammar into account. Consider the sequence `A man`. It is clear that adding an adjective between these two words does not change the relationship between `A` and `man`, but a simple model based on word order does not account for this and creates additional "free parameters".

## What did not work?

> Experiments suggest that learning jointly the representation (word features) and the model is very useful. We tried (unsuccessfully) using as fixed word features for each word `w` the first principal components of the co-occurrence frequencies of `w` with the words occurring in text around the occurrence of `w`.

**This is a very interesting point**. This seems to indicate that the feature vector representation is tied to a particular task and will not necessarily be suitable for a different task.

---

## Referenced Work to be studied

- [ ] Back-off trigram models (Katz, 1987) and smoothed (or interpolated) trigram models (Jelinek and Mercer, 1980)
- [ ] Goodman (2001) combines many tricks to get substantial improvements
- Older ideas of learning a distributed representation for symbolic data
  - [ ] Hinton 1986
  - [ ] Elman 1990
- [ ] Miikkulainen and Dyer, 1991 for language models using Neural Networks
- The idea of discovering some similarities between words to obtain generalization from training sequences to new sequences is not new. For example, it is exploited in approaches that are based on learning a clustering of the words 
  - [ ] Brown et al., 1992
  - [ ] Pereira et al., 1993
  - [ ] Niesler et al., 1998
  - [ ] Baker and McCallum, 1998)
- The idea of using a vector-space representation for words has been well exploited in the area of information retrieval, where feature vectors for words are learned on the basis of their probability of co-occurring in the same documents
  - [ ] Schutze, 1993
  - [ ] Latent Semantic Indexing, see Deerwester et al., 1990
- The idea of a vector-space representation for symbols in the context of neural networks has also previously been framed in terms of a parameter sharing layer, for secondary structure prediction, and for text-to-speech mapping.
  - [ ] Riis and Krogh, 1996
  - [ ] Jensen and Riis, 2000
