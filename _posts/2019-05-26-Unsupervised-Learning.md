---
title: 无监督学习
date: 2019-05-26
tags: Machine-Learning
key: Unsupervised-Learning-2019-05-26
---

几种无监督学习的简单介绍。

<!--more-->

## K-means 优化目标

$c^{(i)} =$ 样本 $x^{(i)}$ 当前被赋与的簇索引 $(1,2,\dots,K)$

$\mu_k=$ cluster centroid $k\;(\mu_k \in \Bbb R^n)$

$\mu_{c^{(i)}}=$ 样本 $x^{(i)}$ 被赋与的簇索引所对应的 cluster centroid

$$
J(c^{(1)},\dots,c^{(m)},\mu_1,\dots,\mu_K) = \dfrac{1}{m}\sum_{i=1}^m\|x^{(i)} - \mu_{c^{(i)}}\|^2
$$

$$
\min_{\substack{c^{(1)},\dots,c^{(m)} \\
\mu_1,\dots,\mu_K}} J(c^{(1)},\dots,c^{(m)},\mu_1,\dots,\mu_K)
$$

## K-means 优化算法

### 随机初始化 K-means，选择 $K$ 个 cluster centroid $\mu_1,\mu_2,\dots,\mu_K \in \Bbb R^n$

1. 让 $K \lt m$
2. 随机选取 $K$ 个样本
3. 设置 $\mu_1,\dots,\mu_K$ 等于这些样本

### 重复运行直到 K-means 收敛

$\text{Repeat \\{}$  
$\quad\text{for } i = 1 \text{ to } m$  
$\quad\quad c^{(i)} := \text{index (from } 1 \text{ to } K \text{)} \text{ of cluster centroid cloest to } x^{(i)}$  
$\quad\text{for } k = 1 \text{ to } K$  
$\quad\quad \mu_k := \text{average (mean) of points assigned to cluster } k$  
$\text{\\}}$

### 重复随机初始化，找到最小 $J$ 值

$\text{for } i = 1 \text{ to } 100 \text{ \\{}$  
$\quad$随机初始化 K-means  
$\quad$运行 K-means 得到 $c^{(1)},\dots,c^{(m)},\mu_1,\dots,\mu_K$  
$\quad$计算代价函数(distortion) $J(c^{(1)},\dots,c^{(m)},\mu_1,\dots,\mu_K)$  
$\text{\\}}$

## 主成分分析(_Principal Component Analysis_)

### 数据预处理

1. 训练集: $x^{(1)},x^{(2)},\cdots,x^{(m)}$
2. 预处理 (feature scaling/mean normalization)
   $$
   \begin{aligned}
       \mu_j &= \sum_{i=1}^m x_j^{(i)} \\
       x^{(i)}_j &= \dfrac{x^{(i)}_j-\mu_j}{\max{x_j}-\min{x_j}}
   \end{aligned}
   $$

### PCA 算法

Reduce data from n-dimensions to k-dimensions

Compute "Covariance Matrix"

$$
\Sigma = \dfrac{1}{m}\sum_{i=1}^n(x^{(i)})(x^{(i)})^T
$$

Compute "eigenvectors" of matrix $\Sigma$，in octave, the command is `[U,S,V] = svd(Sigma)`, svd is sigular vector decomposition.

```matlab
Sigma = (1/m) * x' * x;
[U,S,V] = svd(Sigma);
U_reduce = U(:, 1:k);
z = U_reduce' * x;
```