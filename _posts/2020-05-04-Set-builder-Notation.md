---
title: Set-builder Notation 和 List Comprehension
date: 2020-05-04
modify_date: 2020-05-04
tags: Math
key: 2020-05-04-Set-builder-Notation
---

`set-builder notation` 是一种数学表示法，用于通过枚举其元素或说明其成员必须满足的属性来描述该集合。

集合可以通过枚举来定义

$$
\begin{aligned}
    \{1, 2, 5, 8\} \\[2ex]
    \{a, b, c, d\} \\
\end{aligned}
$$

可以通过`谓词`{:info}来定义

$$
\begin{aligned}
    & \{\, x \in S \, | \, P(x) \,\} \\[1ex]
    & \text{or} \\[1ex]
    & \{\, x \,|\, x \in S, \, P(x)\,\}
\end{aligned}
$$

在这种形式中，集构构造符号包括三部分：`变量`，`冒号或竖线分隔符`以及`逻辑谓词`。

<!--more-->

List Comprehension