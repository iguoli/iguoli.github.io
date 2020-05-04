---
title: Set-builder Notation 和 List Comprehension
date: 2020-05-04
modify_date: 2020-05-04
tags: Math
key: 2020-05-04-Set-builder-Notation
---

## Set-builder notation

`set-builder notation` 是一种数学表示法，用于通过枚举其元素或说明其成员必须满足的属性来描述该集合。

<!--more-->

通过枚举来定义集合

$$
\begin{aligned}
    \{1, 2, 5, 8\} \\[2ex]
    \{a, b, c, d\} \\
\end{aligned}
$$

通过`谓词`{:info}来定义集合

$$
\begin{aligned}
    & \{\, x \in S \, | \, P(x) \,\} \\[1ex]
    & \text{or} \\[1ex]
    & \{\, x \,|\, x \in S, \, P(x)\,\}
\end{aligned}
$$

在这种形式中，集合构造符号包括四部分：`变量`，`域`，`冒号或竖线分隔符`以及`逻辑谓词`。冒号或竖线读作"使得" (such that, for which, with the property that)，域表示变量 $x$ 属于一个给定集合 $S$，谓词 $P$ 表示一个返回布尔值的函数 $P:S->\{true, false\}$。通过谓词定义的集合可以读作:  All values of $x$ that belongs to the given set $S$ for which the predicate is true.

## List Comprehension

在某些编程语言中，List Comprehension 表示从一个已有集合构造新的集合，其遵循数学中 `Set-builder notation` 的形式，区别于函数 `map` 和 `filter`。以下面一个集合构造为例:

$$
S = \{\underbrace{2 \cdot x}_\text{output expression}|\underbrace{x}_\text{variable} \in \underbrace{\Bbb{N}}_\text{input set},\underbrace{x^2>3}_\text{predicate}\}
$$

- 变量 $x$ 表示 input set 中的某个元素。
- $\Bbb{N}$ 表示 input set，这里是一个自然数集合。
- $x^2>3$ 是一个谓词表达式，对 input set 中的元素进行过滤。
- 大括号 $\\{\\}$ 表示这是一个集合。
- 竖线 $\vert$ 读作 "使得 (SUCH THAT, s.t.)"。
- 逗号用于分隔谓词，可以读作 "AND"。

List Comprehension 具有相同的语法结构，可以按顺序从输入列表或迭代器中生成新的列表。以 Haskell 语言为例:

```haskell
s = [ 2*x | x <- [0..], x^2 > 3 ]
```

这里的 `[0..]` 表示自然数 $\Bbb{N}$，`x^2 > 3` 表示 predicate，`2*x` 表示 output expression。

以下是几种编程语言的 List Comprehension 语法:

| 语言 | 例1 | 例2 |
| --- | --- | --- |
| Set-builder | $\\{\,l\,\vert\,l \in L\,\\}$ | $\\{\,(k,x)\,\vert\,k \in K,\,x \in X,\,P(x)\,\\}$ |
| Python | `{l for l in L}`{: .language-python} | `{(k,x) for k in K for x in X if P(x)}`{: .language-python} |
| Haskell | `[l | l <- ls]`{: .language-haskell} | `[(k,x) | k <- ks, x <- xs, p x]`{: .language-haskell} |
| Scala | `for (l <- L) yield l`{: .language-scala} | `for (k <- K, x <- X, P(x)`{: .language-scala} |
| SQL | `SELECT l FROM L_set`{: .language-sql} | `SELECT k, x FROM K_set, X_set WHERE P(x)`{: .language-sql} |
