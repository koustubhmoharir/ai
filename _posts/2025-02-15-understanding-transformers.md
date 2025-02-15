---
layout: post
title:  "Understanding Transformers"
date:   2025-02-15 18:53:00 +0530
categories: concepts
tags: 
---

Transformers have a very simple architecture with just a few types of mathematical operations, so that the results achieved with transformers over the last several years seem highly surprising. Why do they work?

## Background

The core idea of a neural network is to approximate precise maths (even if it is discrete) with end-to-end differentiable operations involving a large number of parameters in continuous space, so that optimization algorithms can be applied to find the likely values of the parameters that produce desired results. How does this apply in the context of a transformer?

## Attention

Given a sequence of vectors $x_i$, each of dimensionality $d$, the attention mechanism calculates queries $q_i$, keys $k_i$, and values $v_i$ using transformation matrices $Q$, $K$, and $V$. The query and key have the same dimensionality $d_h$ which is usually much smaller than $d$, and the value can have a different dimensionality.

$q_i = Q x_i$

$k_i = K x_i$

$v_i = V x_i$

Attention is calculated using

$\Large a_i = \frac {\sum_{j=1}^{N} \exp(\frac {q_i.k_j} {\sqrt d_h}) v_j} {\sum_{j=1}^{N} \exp(\frac {q_i.k_j} {\sqrt d_h})}$

where $N$ is the number of vectors in the sequence. In case masked attention is being used, the summation goes to $i$ instead of $N$.

$\exp(\frac {q_i.k_j} {\sqrt d_h})$ is the weight calculated for $v_j$, and the attention is the weighted average over all $v_j$. The dot product $q_i.k_j$ is a measure of similarity between the query and key vectors and $\sqrt d_h$ is just a scaling factor to prevent the exponential from becoming too large or too small.

## Interpreting attention as a differentiable approximation

Suppose $query$, $key$, and $value$ are functions that take a single parameter $x$ of some type $T$. $attention$ is a function defined as shown below

```typescript
function attention(xi: T, xs: T[]) {
    let q = query(xi)
    for (let j = 0; j < xs.length; j++) {
        let xj = xs[j]
        let kj = key(xj)
        if (equals(q, kj)) {
            let vj = value(xj)
            return vj
        }
    }
}
```

The core ideas in making discrete logic differentiable are
* Types are vector spaces. Objects are vectors.
* A simple function that is based on the value of one or more attributes of an object is one row of a matrix. A set of such functions is a matrix.
* Evaluating a set of functions on an object is a matrix multiplication: matrix * vector.
* A set of more complex functions that do not involve branching (conditional logic) is a feed forward neural network.
* A lookup based on basic branching is attention.

Specifically, w.r.t attention, 
* `equals(q, kj)` in the code above is approximated by $\exp(\frac {q.k_j} {\sqrt d_h})$.
* The conditional `return vj` is approximated by a weighted average over all $v_j$ with the condition as weight.
* Moreover, if $x$ contains positional information via some form of positional encoding / embedding, the query, key, and value functions can make use of that information to make the lookup dependent on the absolute and / or relative positions of $i$ and $j$. In other words, although the code above does not show any usage of the index variables $i$ and $j$ in the query, key, and value functions, the attention mechanism is capable of using them.

Each head of attention is an approximation of a lookup function. It is important to note that the lookup function is operating on a vector with a large number of dimensions and it also produces a vector. Hence, it is more appropriate to think of each head of attention as a lookup function that looks up many different attributes simultaneously based on the same condition.

## Combination of attention heads

The description above did not include notation for multiple attention heads. In this section, $a^k$ denotes the result of the attention head $k$ out of $H$ attention heads. Ths subscript $i$ is ignored here because the same operations are performed for each $i$

The results of all attention heads are *concatenated* (not added) and the result is transformed with another matrix such that the dimensionality of the result matches $d$.

$
a = W_o \begin{bmatrix}
                a^1 \cr
                a^2 \cr
                \vdots \cr
                a^H
            \end{bmatrix}
$

$a$ is added to $x$ and the result is normalized.

$\widehat{a} = normalize(x + a)$

This normalized vector goes into a feed forward network, and the result is again added and normalized.

$z = normalize(\widehat{a} + FFN(\widehat{a}))$

$z$ is the output of one transformer block and becomes the input of the next block which has the same structure but with different parameters in the various matrices $Q^k$ $K^k$, $V^k$, $W_o$, and the matrices in the FFN. The basic transformer does not do anything else (except for the layer after the last block).

## Interpreting the calculations after attention

Concatenation is straightforward to interpret. It just considers all information from all heads without any transformation. The projection into the embedding space via $W_o$ is a linear combination of the lookups from the attention heads in different ways (one per dimension). It is not apparent why the result of these combinations should be in the same vector space as the input, but adding $a$ to $x$ implies that it may make sense to interpret it to be in the same space. Moreover, $\widehat{a}$ and $z$ are unit vectors. Assuming that the input $x$ to the first transformer block is also a unit vector, each transformer block simply performs two rotations of its input.

## Zooming out

Specifically for an LLM starting from a single token, the transformer takes the embedding of this initial token and an empty context to begin with as input, and produces the next token via a sequence of rotations - two per transformer block. As this process continues producing more tokens, the output at each transformer block forms the context for each subsequent token for the next block. The first rotation in each transformer block depends on this context via attention on the output of the immediately previous block for all previous tokens. The second rotation does not depend on the context because it is just the result of a feed forward network.

The vocabulary of an LLM is a tiny number of points on a unit sphere in the embedding vector space (tiny because the vector space has a large dimensionality so that the surface area of the unit sphere is enormous and compared to that, the vocabulary is tiny). Each sequence of tokens that the model has been trained on is a series of rotations on the surface of the unit sphere from token to token. A transformer learns a single formula that can perform any of these rotations given all the positions that came before it. This single formula must reproduce all the paths in the training corpus as best it can. It does this by breaking each token-to-token rotation into a sequence of block-to-block rotations. Since each transformer block only depends on the outputs of the block immediately before it, it is possible to consider all intermediate blocks to be independent vector spaces. However, the output of the final block definitely produces a point in the embedding space itself, because that is how it is trained. Hence, it seems more reasonable to assume that the output of every block remains in the embedding space too.

## Zooming back in to a block

The attention output $a$ is a combination of lookups from all previous tokens including itself. Adding this to the input $x$ and normalizing is *one* possible way to rotate $x$. It is not clear why this is a *good* way. Regardless, the rotation needs to capture *all* the information from the context that is required to predict the next token, but it *does not* need to capture information required to predict subsequent tokens, because the attention mechanism will operate on the full context again for subsequent tokens. Hence, it only needs to do a *little* bit of work. Unlike RNNs like LSTMs, it does not need to build up a single representation of the entire context, which would be a *lot* of work. This explains why transformers are easier to train.

The second rotation arising out of the application of the feed forward network followed by addition and normalization is difficult to interpret, but it does not use any context. It is just a function of its own input. A reasonable interpretation of this is that after accounting for context in the previous step, some regularization is needed to distribute the information obtained from the context across the dimensions of the vector. The next block will then use this regularized vector to query the context again.

## Zooming out again

Overall, the operation of a transformer in an LLM can be understood as implementing the nested loops shown below

```
for each token
    x <- embedding of the token
    for each block
        construct a set of queries
            (one query per attention head)
        calculate and store a key and value from x
            (for the current block)
        fetch the stored keys and values
            (for older tokens for the current block)
        lookup and combine values for attention
        
        rotate x based on attention

        rotate x to regularize it using a FFN
            (preparation for the next block)
    end
end
```

In other words, the first block of the transformer uses the context to rotate the current token towards the next token in two steps - lookup, then regularize. The second block again uses the context to refine or continue the rotation made by the first block with the same kind of steps - lookup, then regularize. Each layer continues to make further rotations until the final block reaches the next token.

A final but extremely important point is that the vector produced by the model for the next token will not in general match any vector in the vocabulary exactly. Instead it will be close to *all* the tokens that may reasonably occur next, such that the distance between the output and each token in the vocabulary is a measure of the frequency of occurence of that token. Since the number of possible sequences of tokens is enormously large, it is easy to see that the vocabulary will need to be placed (embedded) on the unit sphere in a very specific way for such a result to be possible at all. Moreover, the single formula that the transformer learns to produce rotations heavily depends on the embeddings. The transformer parameters and the embeddings need to be generated simultaneously from the training process for the best results.