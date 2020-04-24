---
title: 机器学习 - 各类函数的求导
date: 2019-05-04
categories: machine-learning neural-network deep-learning
---

## 线性函数 $z$ 对参数 $\theta_j$ 求导

线性函数 $z$:

$$
z = \theta_0x_0 + \theta_1x_1 + \dots + \theta_jx_j
$$

其关于 $\theta_j$ 的导数:

$$
\begin{aligned}
    \dfrac{\partial z}{\partial\theta_j} &= \dfrac{\partial}{\partial\theta_j}(\theta_0x_0) + \dfrac{\partial}{\partial\theta_j}(\theta_1x_1) + \dots + \dfrac{\partial}{\partial\theta_j}(\theta_jx_j) \\
    &= 0 + 0 + \dots + x_j \\
    &= x_j
\end{aligned}
$$

***需要注意，$\theta_0$ 是偏差项(bias)，其对应的 $x_0 = 1$，因此***

$$
\begin{cases}
    \color{red}{\dfrac{\partial z}{\partial\theta_0} = 1} \\
    \color{red}{\dfrac{\partial z}{\partial\theta_j} = x_j} \; (j=1,2,\dots,n)
\end{cases}
$$

## Sigmoid 函数对 $z$ 求导

Sigmoid 函数:

$$
\sigma(z) = \dfrac{1}{1+e^{-z}}
$$

其关于 $z$ 的导数:  

令 $u = 1+e^{-z}$ 并且有 $\dfrac{d}{du}\left(\dfrac{1}{u}\right)=-\dfrac{1}{u^2}$

$$
\begin{aligned}
    \dfrac{d}{dz}\left(\dfrac{1}{1+e^{-z}}\right) &= \dfrac{d}{du}\left(\dfrac{1}{u}\right)\dfrac{du}{dz} \\
    &= -\dfrac{1}{(1+e^{-z})^2} \dfrac{d}{dz}(1+e^{-z}) \\
    &= -\dfrac{1}{(1+e^{-z})^2} \left(\dfrac{d}{dz}(1) + \dfrac{d}{dz}(e^{-z})\right) \\
    &= -\dfrac{1}{(1+e^{-z})^2} \left(0 + (-1)(e^{-z})\right) \\
    &= \dfrac{e^{-z}}{(1+e^{-z})^2} \\
    &= \dfrac{1}{1+e^{-z}} \dfrac{e^{-z}}{1+e^{-z}} \\
    &= \dfrac{1}{1+e^{-z}} \dfrac{1+e^{-z}-1}{1+e^{-z}} \\
    &= \dfrac{1}{1+e^{-z}} \left(\dfrac{1+e^{-z}}{1+e^{-z}}-\dfrac{1}{1+e^{-z}}\right) \\
    &= \dfrac{1}{1+e^{-z}} \left(1-\dfrac{1}{1+e^{-z}}\right) \\
    &= \color{red}{\sigma(z)(1-\sigma(z))}
\end{aligned}
$$

## 逻辑回归代价函数 $C$ 对sigmoid函数 $\sigma(z)$ 求导

代价函数 $C$:

$$
\begin{aligned}
    a &= \sigma(z) \\
    C &= -y\ln(a) - (1-y)\ln(1-a)
\end{aligned}
$$

其关于 $a$ 的导数:  

有 $\dfrac{d}{da}\ln(a) = \dfrac{1}{a}$

$$
\begin{aligned}
    \dfrac{dC}{da} &= -y\dfrac{d}{da}\ln(a) - (1-y)\dfrac{d}{da}\ln(1-a) \\
    &= -y\dfrac{1}{a} - (1-y)\dfrac{\dfrac{d}{da}(1) - \dfrac{d}{da}(a)}{1-a} \\
    &= -y\dfrac{1}{a} - (1-y)\dfrac{0-1}{1-a} \\
    &= \dfrac{1-y}{1-a} -\dfrac{y}{a} \\
    &= \dfrac{a(1-y) - (1-a)y}{a(1-a)} \\
    &= \dfrac{a-ay-y+ay}{a(1-a)} \\
    &= \color{red}{\dfrac{a-y}{a(1-a)}} \\
\end{aligned}
$$

## 逻辑回归代价函数 $C$ 对 $z$ 求导

$$
\begin{aligned}
    \dfrac{dC}{dz} &= \dfrac{dC}{da}\dfrac{da}{dz} \\
    &= \dfrac{a-y}{a(1-a)} \cdot a(1-a) \\
    &= a-y
\end{aligned}
$$

## 逻辑回归代价函数 $C$ 对参数 $\theta_j$ 求导

$$
\begin{aligned}
    \dfrac{\partial C}{\partial\theta_j} &= \dfrac{dC}{da}\dfrac{da}{dz}\dfrac{\partial z}{\partial\theta_j} \\
    &= \dfrac{a-y}{a(1-a)} \cdot a(1-a) \cdot x_j \\
    &= (a-y) \cdot x_j
\end{aligned}
$$

***需要注意，$\theta_0$ 是偏差项(bias)，其对应的 $x_0 = 1$，因此***

$$
\begin{cases}
    \color{red}{\dfrac{\partial C}{\partial\theta_0} = a-y} \\
    \color{red}{\dfrac{\partial C}{\partial\theta_j} = (a-y) \cdot x_j} \; (j=1,2,\dots,n)
\end{cases}
$$

## 神经网络中的求导

第 $l$ 层中的第 $i$ 个神经元 $a^{(l)}_i$:

$$
a^{(l)}_i = \sigma(z^{(l)}_i) = \sigma\left(\theta^{(l-1)}_{i0}a^{(l-1)}_0 + \theta^{(l-1)}_{i1}a^{(l-1)}_1 + \dots + \theta^{(l-1)}_{ij}a^{(l-1)}_j\right)
$$

$z^{(l)}\_i$ 关于 $\theta^{(l-1)}\_{ij}$ 的导数:

$$
\begin{aligned}
    \dfrac{\partial z^{(l)}_i}{\partial\theta^{(l-1)}_{ij}} &= \dfrac{\partial}{\partial\theta^{(l-1)}_{ij}}\left(\theta^{(l-1)}_{i0}a^{(l-1)}_0\right) + \dfrac{\partial}{\partial\theta^{(l-1)}_{ij}}\left(\theta^{(l-1)}_{i1}a^{(l-1)}_1\right) + \dots + \dfrac{\partial}{\partial\theta^{(l-1)}_{ij}}\left(\theta^{(l-1)}_{ij}a^{(l-1)}_j\right) \\
    &= 0 + 0 + \dots + a^{(l-1)}_j \\
    &= a^{(l-1)}_j
\end{aligned}
$$

$z^{(l)}\_i$ 关于 $a^{(l-1)}\_j$ 的导数:

$$
\begin{aligned}
    \dfrac{\partial z^{(l)}_i}{\partial a^{(l-1)}_j} &= \dfrac{\partial}{\partial a^{(l-1)}_j}\left(\theta^{(l-1)}_{i0}a^{(l-1)}_0\right) + \dfrac{\partial}{\partial a^{(l-1)}_j}\left(\theta^{(l-1)}_{i1}a^{(l-1)}_1\right) + \dots + \dfrac{\partial}{\partial a^{(l-1)}_j}\left(\theta^{(l-1)}_{ij}a^{(l-1)}_j\right) \\
    &= 0 + 0 + \dots + \theta^{(l-1)}_{ij} \\
    &= \theta^{(l-1)}_{ij}
\end{aligned}
$$

代价函数 $C$ 关于输出层($L$ 层) $\theta^{(L-1)}_{ij}$ 的导数:

$$
\begin{aligned}
    \dfrac{\partial C}{\partial\theta^{(L-1)}_{ij}} &= \dfrac{dC}{da^{(L)}_i}\dfrac{da^{(L)}_i}{dz^{(L)}_i}\dfrac{\partial z^{(L)}_i}{\partial\theta^{(L-1)}_{ij}} \\
    &= (a^{(L)}_i - y) \cdot a^{(L-1)}_j
\end{aligned}
$$