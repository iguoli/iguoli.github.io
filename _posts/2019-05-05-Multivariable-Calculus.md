---
title: 多元微积分
date: 2019-05-05
categories: math calculus multivariable
---

## 复合函数的中间变量均为一元函数

设

$$
z = f(u, v), \quad
\begin{cases}
    u = g(t) \\
    v = h(t)
\end{cases}
$$

则

$$
\dfrac{dz}{dt} =
\left[\dfrac{\partial z}{\partial u} \; \dfrac{\partial z}{\partial v}\right]
\begin{bmatrix}
    \dfrac{du}{dt} \\
    \dfrac{dv}{dt}
\end{bmatrix} =
    \dfrac{\partial z}{\partial u}\dfrac{du}{dt} + \dfrac{\partial z}{\partial v}\dfrac{dv}{dt}
$$

## 复合函数的中间变量均为多元函数

设

$$
z = f(u, v), \quad
\begin{cases}
    u = g(x, y) \\
    v = h(x, y)
\end{cases}
$$

则

$$
\begin{bmatrix}
    \dfrac{\partial z}{\partial x} \\
    \dfrac{\partial z}{\partial y}
\end{bmatrix} =
\begin{bmatrix}
    \dfrac{\partial u}{\partial x} & \dfrac{\partial v}{\partial x} \\
    \dfrac{\partial u}{\partial y} & \dfrac{\partial v}{\partial y}
\end{bmatrix}
\begin{bmatrix}
    \dfrac{\partial z}{\partial u} \\
    \dfrac{\partial z}{\partial v}
\end{bmatrix} =
\begin{bmatrix}
    \dfrac{\partial z}{\partial u}\dfrac{\partial u}{\partial x} + \dfrac{\partial z}{\partial v}\dfrac{\partial v}{\partial x} \\
    \dfrac{\partial z}{\partial u}\dfrac{\partial u}{\partial y} + \dfrac{\partial z}{\partial v}\dfrac{\partial v}{\partial y}
\end{bmatrix}
$$

## 复合函数的中间变量既有一元函数，又有多元函数

设

$$
z = f(u, v), \quad
\begin{cases}
    u = g(x, y) \\
    v = h(y)
\end{cases}
$$

则

$$
\begin{bmatrix}
    \dfrac{\partial z}{\partial x} \\
    \dfrac{\partial z}{\partial y}
\end{bmatrix} =
\begin{bmatrix}
    \dfrac{\partial u}{\partial x} & 0 \\
    \dfrac{\partial u}{\partial y} & \dfrac{dv}{dy}
\end{bmatrix}
\begin{bmatrix}
    \dfrac{\partial z}{\partial u} \\
    \dfrac{\partial z}{\partial v}
\end{bmatrix} =
\begin{bmatrix}
    \dfrac{\partial z}{\partial u}\dfrac{\partial u}{\partial x} \\
    \dfrac{\partial z}{\partial u}\dfrac{\partial u}{\partial y} + \dfrac{\partial z}{\partial v}\dfrac{dv}{dy}
\end{bmatrix}
$$