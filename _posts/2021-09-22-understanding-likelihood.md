---
layout: post
title:  "Understanding Likelihood"
date:   2021-09-22 19:30:00 +0530
categories: papers
tags: 
---

Classifier models are trained by maximizing the log-likelihood of the weights - or equivalently, by minimizing the cross entropy loss - the cross entropy loss characterization seems to be more common these days. But what is likelihood?

## Definition of Likelihood

Consider a model that simulates a physical process with a discrete output $Y$ that can take a finite number of values with probabilities that depend on some parameters $W$, but the true values of $W$ are unknown.

Let $f(Y, W)$ be the function that provides the probability of the process producing output $Y$ for parameters $W$. If we fix the values of $W$, $f_W(Y)$ becomes the probability distribution of $Y$. However if we fix $Y$, $f_Y(W)$ is the likelihood of $W$.

In other words, the likelihood of specific values of the parameters of the process, given observations of its output, is the probability that the process would produce the observed outputs if the parameters had the specified values.

$\mathcal{L}(W \mid Y) = P(Y \mid W)$

## Difference between likelihood and probability

$\mathcal{L}(W \mid Y) \neq P(W \mid Y)$.

To get the probability of the parameters given the output, we need to use Bayes theorem

$\displaystyle P(W \mid Y) = \frac{P(Y \mid W) \times P(W)}{P(Y)} $

This can be understood with the example of a coin that is tossed twice and the number of Heads are counted. We will denote the three possible outcomes as $\text{H0}$, $\text{H1}$ and $\text{H2}$ where the number after H is the number of Heads. The parameter here is the fairness of the coin which we can define as the probabily of Heads in a single toss, so that a fairness of 0.5 is a perfectly fair coin.

If we observe a sequence of two consecutive Heads $\text{H2}$, the likelihood that the coin is fair is the probability of observing two consecutive Heads with a fair coin. $\mathcal{L}(W = 0.5 \mid \text{H2}) = P(\text{H2} \mid W = 0.5) = 0.25$

What is the probability of $W = 0.5 \space \mid \space \text{H2}$? For this we need additional information. Suppose that the process of manufacturing the coins produces fair coins with a probability of 0.95 and coins with a fairness of 1 (always Heads) with a probability of 0.05.

$P(W = 0.5) = 0.95$ (This is the prior probability - without observation)

$P(\text{H2}) = P(\text{H2} \mid W = 0.5) \times P(W = 0.5) + P(\text{H2} \mid W = 1) \times P(W = 1)$

$P(\text{H2}) = 0.25 \times 0.95 + 1 \times 0.05 = 0.2875$

$\displaystyle P(W = 0.5 \mid \text{H2}) = \frac{P(\text{H2} \mid W) \times P(W = 0.5)}{P(\text{H2})}$

$P(W = 0.5 \mid \text{H2}) = 0.25 * 0.95 / 0.2875 \approx 0.826 $ (This is the posterior probability - without observation)

In this example, or even in general, it seems unlikely that we would know the prior probability of the parameters. Calculating the likelihood is easy. Calculating the conditional probability is difficult.

## Maximimum Likelihood Estimation

Consider a problem where we can observe the output of a process and know the mechanics of the process (we can calculate the probability of the observed outputs for any given value of the process parameters), but we do not know the parameters. We can find those values of parameters where the likelihood is maximum for given values of the observations. This is called Maximum Likelihood Estimation.

Why do we maximize the likelihood instead of maximizing the conditional probability of the parameters given the observations?

Maximizing the conditional probability will require knowledge of the prior probability of the parameters which is highly unlikely to be available. Maximizing the likelihood attaches importance only to the observations whereas maximizing the conditional probability discounts parameter values that are less probable. In other words, if we have high confidence in the observed outputs, and want to maximize the probability that our model produces those outputs, we should maximize the likelihood.

If the number of observations is large enough, will both maximizations give the same result? Intuitively, yes, but I am not sure.

## Application to a neural classifier

Consider a problem where a known input must be classified into one of $m$ output classes. Suppose $n$ humans are asked to do the classification for the same known input. Let $y_i$ be the frequency reported by humans for output class $i$, so that $\sum_{i=1}^{m} {y_i} = n$. In general, it is assumed that humans may make mistakes or disagree about the output class even though the input is the same. $p_i = y_i / n$ will be the "true" probability for the output class $i$.
Suppose we are modeling the classification process with a neural model with weights $W$. The neural model is setup (perhaps with a softmax output) so that its output can be interpreted as a probability distribution $q_i$ over the output classes.
What is the likelihood of the output $y$ given the weights $W$? If we assume that we will use the model output distribution to sample outputs $n$ times, the probability of each output class $i$ being sampled exactly $y_i$ times is $\binom{n}{y_1} {q_1} ^ {y_1} \times \binom{n - y1}{y_2} {q_2} ^ {y_2}  \times \binom{n - y1 - y2}{y_3} {q_3} ^ {y_3} \times ...$  
Simplifying this, $\displaystyle \mathcal{L}(W \mid y) = n! \prod_{i=1}^{m}{\frac { {q_i} ^ {y_i} } { {y_i}!} }$

Taking the natural logarithm and substituting $y_i = p_i n$, this leads to $\displaystyle \ln(\mathcal{L}(W \mid y)) = \ln(\frac {n!} {\prod_{i=1}^{m} { {y_i}!}}) + n \sum_{i=1}^{m} {p_i \ln(q_i)}$

The first term can be considered a constant if values of $y_i$ are considered constant. In any case, the first term does not depend on weights of the neural model. Hence maximizing the likelihood is the same as maximizing $\displaystyle \sum_{i=1}^{m} {p_i \ln(q_i)}$

If we assume that the true probability is 1 for just one class $i = k$ and 0 for all other classes $i \neq k$, maximizing the likelihood reduces to maximizing $\ln(q_k)$