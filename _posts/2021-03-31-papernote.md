---
layout: post
title: "Paper Note:三月末的最后paper note"
date: 2021-03-31 11:00:00
author: "Shawn"
header-style: text
catalog: true
tags:
  - timeseries
---

#### DEEP MULTI-TASK REPRESENTATION LEARNING: A TENSOR FACTORISATION APPROACH

tensor factorisation has been used to address problems where tasks are described by multiple independent factors rather than merely indexed by a single factor.

#### End-to-end learning of a Convolutional Neural Network via Deep Tensor Decomposition

In this paper, we propose an algorithm for approximately learning an arbitrarily deep CNN model with rigorous guarantees.

- Insights: Provide theoretical insights towards better understanding when training deep CNN architectures is computationally tractable and how much data is required for successful training. We focus on a realizable model where the inputs are chosen i.i.d. from a Gaussian distribution and the labels are generated according to planted convolutional kernels.
- We show that our algorithm approximately learns the kernels (up to sign/scale ambiguities) as soon as the size of the training data is proportional to the total number of parameters of the planted CNN model.
- Our results can be viewed as a first step towards provable end-to-end learning of practical deep CNN models. Extending the connections between neural networks and tensors [7, 19, 40].

How to deepTD? In this paper, it expresses as

1. given any input data $\boldsymbol{x} \in \mathbb{R}^{p}$ , we form a D-way tensor $\boldsymbol{X} \in \mathbb{R} \otimes_{\ell=1}^{D} d_{\ell}$ as follows
2. First, we convert $x$ into a matrix by placing every $d_1$ consecutive entries of $x$ as a row of a matrix of size $p_1$ × $d_1$.
3. From this matrix we then create a 3-way tensor of size $p_2$ × $d_2$ × $d_1$ by grouping $d_2$ consecutive entries of each of the $d_1$ columns and so on.
4. We repeat this procedure D times to arrive at the D-way tensor $\boldsymbol{X} \in \mathbb{R}^{\bigotimes_{\ell=1}^{D} d_{\ell}}$. We define $\mathcal{T}: \mathbb{R}^{p} \mapsto \mathbb{R}^{\otimes_{\ell=1}^{D} d_{\ell}}$ as the corresponding tensor operation that maps $\boldsymbol{x} \in \mathbb{R}^{p}$ to $\boldsymbol{X} \in \mathbb{R} \otimes_{\ell=1}^{D} d_{\ell}$.

- 用Factorized Tensor学习神经网络

#### Matrix Factoriztion via Deep Learning

Very similar to ***Deep Matrix Factorization Models for Recommender Systems∗***. **IJCAI2017**

I think this is more like a tabular learning, compared to other literature which talks about these *SVD* or *PCA* like matrix decomposition methods.
$$
\begin{aligned}
M_{t j}^{(I) \&(I I)} &=F\left(M_{t,:}^{(I) \&(I I)}, M_{:, j}^{(I)}\right) \\
M_{i k}^{(I) \&(I I I)} &=F\left(M_{i,:}^{(I)}, M_{:, k}^{(I) \&(I I I)}\right) \\
M_{t k}^{(I)-(I V)} &=F\left(M_{t,:}^{(I) \&(I I)}, M_{:, k}^{(I) \&(I I I)}\right)
\end{aligned}
$$

$$
R_{i j}=F\left(X_{i}, Y_{j}\right)=\frac{\left[h_{X}^{W_{X}}\left(X_{i}\right)\right]^{\top} h_{Y}^{W_{Y}}\left(Y_{j}\right)}{\left\|h_{X}^{W_{X}}\left(X_{i}\right)\right\|_{2}\left\|h_{Y}^{W_{Y}}\left(Y_{j}\right)\right\|_{2}}
$$

$$
\mathcal{L}=\frac{1}{\left|\Omega_{t r}\right|} \sum_{i j \in \Omega_{t r}}\left(F\left(X_{i}, Y_{j}\right)-M_{i j}\right)^{2}+\gamma\left(\left\|W_{X}\right\|_{2}^{2}+\left\|W_{Y}\right\|_{2}^{2}\right)
$$

#### Sparse Canonical Temporal Alignment With Deep Tensor Decomposition for Action Recognition

Propose a deep non-negative tensor factorization(DNTF) method to find a tensor subspace integrated with SCTA (sparse canonical temporal alignment) scheme.

面向背景对齐的场景：Our framework is able to tackle three problems: sub-action, multi-subject, and multi-modality by key frame alignment in a new tensor subspace.  也是因此，tensor factorization在这篇工作中占据了比较重要的地位。使用Non-Negative Tensor Factorization来进行分解。

**Signal Processing**

### Irregular Sampled Time Series Related

#### MULTI-TIME ATTENTION NETWORKS FOR IRREGULARLY SAMPLED TIME SERIES

- 