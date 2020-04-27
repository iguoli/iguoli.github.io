---
title: 支持向量机(Support Vector Machine)
date: 2019-05-16
tags: Machine-Learning
key: 2019-05-16-Support-Vector-Machine
---

## SVM 的决策边界(_Decision Boundary_)

其目标是找到一个线性函数 $\theta^Tx$，可以将样本集中的正类和负类分隔开，当 $\theta^Tx \ge 0$ 时表示正类，当 $\theta^Tx \lt 0$ 时表示负类，而 $\theta^Tx = 0$ 所代表的直线则是区分正负类的决策边界。

$$
\begin{aligned}
    \theta^Tx \ge 0 &\quad \text{if} \; y^{(i)} = 1 \\
    \theta^Tx \lt 0 &\quad \text{if} \; y^{(i)} = 0
\end{aligned}
$$

## 线性优化函数

由向量内积可知，$\theta^Tx = \\|x\\| \cdot \\|\theta\\| \cdot \cos(\alpha) = x_{proj} \cdot \\|\theta\\|$，将 $x_{proj}$ 简写为 $p$，则有 $\theta^Tx = p \cdot \\|\theta\\|$。

SVM 的优化函数如下:

$$
\min_\theta \dfrac{1}{2} \sum_{j=1}^n \theta^2 = \min_\theta \dfrac{1}{2} \|\theta\|^2
$$

$$
\begin{aligned}
    \text{s.t. } \;& p^{(i)} \cdot \|\theta\| \ge 1 & \text{if} \; y^{(i)} = 1 \\
    & p^{(i)} \cdot \|\theta\| \le -1 & \text{if} \; y^{(i)} = 0
\end{aligned}
$$

或

$$
\min_\theta C \sum_{i=1}^m\left[y^{(i)}cost_1(\theta^Tx^{(i)}) + (1-y^{(i)})cost_0(\theta^Tx^{(i)})\right] + \dfrac{1}{2}\sum_{j=1}^n\theta_j^2
$$

其中

$$
cost_1(z) =
\begin{cases}
    -z + 1 & z \lt 1 \\
    0 & z \ge 1
\end{cases} \qquad
cost_0(z) =
\begin{cases}
    z + 1 & z \gt -1 \\
    0 & z \le -1
\end{cases}
$$

![SVM Cost Function](/assets/images/machine-learning/svm_cost_function.png)

支持向量机有时又称为**大间距分类器(_Large Margin Classifier_)**，优化函数找到尽可能大的 $p^{(i)}$，使得 $\\|\theta\\|$ 尽可能的小，通过图形可知，$p^{(i)}$ 即为样本 $x^{(i)}$ 到决策边界的距离(_margin_)。

$C$ 参数是一个正数，用来对训练样本的误分类进行惩罚。如果 $C$ 比较大，SVM 会尝试对所有样本进行正确分类。$C$ 相当于 $\frac{1}{\lambda}$，$\lambda$ 是逻辑回归中的正则化参数。

## SVM with Gaussian Kernel

使用 SVM 寻找非线性的决策边界需要用到**高斯核(_Gaussian Kernel_)**，可以将 Gaussian Kernel 想像为测量一对样本间"距离"的 similarity function。

$$
K_{gaussian}(x^{(i)},x^{(j)}) = \exp\left(-\dfrac{\|x^{(i)} - x^{(j)}\|^2}{2\sigma^2}\right) = \exp\left(-\dfrac{\sum_{k=1}^n(x_k^{(i)} - x_k^{(j)})^2}{2\sigma^2}\right)
$$