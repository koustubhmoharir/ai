---
layout: post
title:  "Paper Notes: Neural networks and physical systems with emergent collective computational abilities"
date:   2021-09-25 22:43:00 +0530
categories: papers
tags: 
---
## Paper Details
[PDF Link](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC346238/pdf/pnas00447-0135.pdf)

Authors
- J. J. Hopfield

Year of publication: 1982

## Notes

This paper introduces an interesting type of neural network called a Hopfield Network that can remember bit vectors and retrieve them from a partial match or from a "similar" vector.

The network is a fully connected undirected graph of neurons. The values of the vertices of the graph are denoted by $V_i$ and can be either 0 or 1. The value of an edge between vertices $i$ and $j$ is denoted by $T_{ij}$. $T_{ii}$ is always 0.

The network can be trained to remember a set of vectors $V^s$ by setting $T_{ij} = \sum_{s=1}^S {(2 V_i - 1) (2 V_j - 1)}$ for $i \neq j$. This expression basically counts the number of times the vertices $i$ and $j$ have the same value and subtracts the number of times they have opposite values. In other words, it captures the correlation between the vertices.

Starting from arbitrary values of $V$, the value at vertex $i$ can be updated by calculating $\sum_{j=1}^N {T_{ij} V_j}$. If this value is greater than a threshold value (taken to be 0 here), $V_i$ becomes 1, else it becomes 0. The vertices do not need to update simultaneously, but all vertices should update roughly periodically at the same mean rate.

Interestingly, these updates will lead the network to converge to one of the vectors $V^s$ on which the edge weights $T_{ij}$ were trained, provided the initial values of $V$ are set to be "close" to one of the $V^s$.

The energy of the network at any point is defined by

$E = -\frac{1}{2} \sum_{i=1}^N {\sum_{j=1}^N {T_{ij} V_i V_j} }$

If an update rule is applied on some vertex $k$ such that the value after the update is $V_{k}^{\prime}$, the change in energy will be

$\Delta E = -(V_k^{\prime} - V_k) \sum_{i=1}^{N} {T_{ik} V_i} $  

This is obtained by using $T_{ij} = T_{ji}$ and cancelling out terms without changes

If $V^{\prime}_{k} = V_k$, which would be the case at a converged and stable vector, $\Delta E$ will be 0.
Alternatively, if the summation is positive, the new value at vertex $k$ must be 1, and if the summation is negative, the new value must be 0, so that $\Delta E$ will be negative. This means that the updates to the network always lower the energy until it reaches a local minimum.

While this formulation of energy does not seem to be a rigorous proof of convergence and stability, perhaps additional analysis may result in such a proof. Here is an intuitive understanding of the convergence. A positive weight on an edge makes it more likely that its vertices will have the same value and a negative weight makes it more likely that they will have different values. This is also consistent with the way the weights are trained. The energy is a measure of how consistent the current values are with this intent. Thus lowering the energy to a minimum, means that the vertex values are maximally aligned to the intent of the edge weights.