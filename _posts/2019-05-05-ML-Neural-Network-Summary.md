---
title: 机器学习 - 相关概念的总结
date: 2019-05-05
tags: machine-learning neural-network deep-learning backpropagation
key: 2019-05-05-ML-Neural-Network-Summary
---

## 概念解释

- 假设函数(_Hypothesis Function_): 用来拟合数据集，有线性函数和非线性函数。

- 正向传播(_Forward Propagation_): 用来计算假设函数的输出值 $h_\theta(x)$(即 $\hat{y}$)。

- 代价函数(_Cost Function_): 用来检验假设函数的输出值 $\hat{y}$ 与真实值 $y$ 之间的误差大小。

- 反向传播(_Backpropagation_): 用来计算代价函数 $J(\theta)$ 关于参数 $\theta$ 的偏导数 $\dfrac{\partial}{\partial \theta}J(\theta)$。

- 优化算法(_Optimization Method_): 使用参数 $\theta$ 最小化代价函数 $J(\theta)$。

## 设计模型结构

- 输入单元的数量 = 特征 $x^{(i)}$ 的维度。

- 输出单元的数量 = 分类的个数。

- 隐藏层单元数量 = 通常越多越好，但太多会导致运算量过大。

- 隐藏层数量 = 默认为一个隐藏层，如果设计多个隐藏层，建议隐藏层单元数量相同。

## 训练神经网络

1. 随机初始化权重(即参数 $\theta$)。

    ```octave
    Theta = rand(10, 11) * (2*INIT_EPSILON) - INIT_EPSILON;
    ```

2. 实现正向传播得到任意输入 $x^{(i)}$ 的 $h_\theta(x^{(i)})$。

3. 实现代价函数。

4. 实现反向传播来计算偏导数。

5. 用梯度检查(_gradient checking_)来确定反向传播计算正确，然后在代码中禁掉梯度检查。

    $$
    gradApprox = \dfrac{(J(\theta+\epsilon) - J(\theta-\epsilon))}{(2\epsilon)}
    $$

6. 用梯度下降(_gradient descent_)或内建的优化函数，使用权重($\theta$)来最小化代价函数。

## 模型评估

### Bias vs. variance

**高偏差(_high bias_)**: 欠拟合(underfitting)，$J_{train}(\Theta)$ 和 $J_{CV}(\Theta)$ 都很高，并且 $J_{train}(\Theta) \approx J_{CV}(\Theta)$。

**高方差(_high variance_)**: 过拟合(overfitting), $J_{train}(\Theta)$ 很低，但是 $J_{CV}(\Theta)$ 要比 $J{train}(\Theta)$ 高很多。

![bias vs. variance](/assets/images/machine-learning/bias_vs_variance.png)

### 学习曲线(_learning curve_)

只有少量训练样本时(例如1个，2个或3个)，很容易得到0误差，因为总可以找到一个二次曲线能够通过每个样本点。因此:

- 随着样本规模变大，二次函数的误差值会增加
- 当样本数量达到m个或一定规模时，误差值会逐渐稳定

**高偏差(_high bias_)**:

**较小的训练集**: $J_{train}(\Theta)$ 会比较低而 $J_{CV}(\Theta)$ 会比较高。

**较大的训练集**: $J_{train}(\Theta)$ 和 $J_{CV}(\Theta)$ 都会很高，并且 $J_{train}(\Theta) \approx J_{CV}(\Theta)$。

如果遇到**高偏差(_high bias_)**的情况，增加更多训练数据没有什么帮助。

![high bias](/assets/images/machine-learning/high_bias.png)

**高方差(_high variance_)**

**较小的训练集**: $J_{train}(\Theta)$ 会比较低而 $J_{CV}(\Theta)$ 会比较高。

**较大的训练集**: $J_{train}(\Theta)$ 随着训练集规模变大而增加而 $J_{CV}(\Theta)$ 会持续下降，并且 $J_{train}(\Theta) \lt J_{CV}(\Theta)$，但二者之间的差距仍然非常大。

如果遇到**高方差(_high variance_)**的情况，增加更多训练数据会有帮助。

![high variance](/assets/images/machine-learning/high_variance.png)

### 处理数据倾斜(_handle skewed data_)

Error Metrics for Skewed Class

![precision and recall](/assets/images/machine-learning/precision_and_recall.png)

**准确率(_Accuracy_)**

$$
\text{Accuracy} = \dfrac{\text{Number of correct predictions}}{\text{Total number of preditcions}} = \dfrac{\text{TP+TN}}{\text{TP+TN+FP+FN}}
$$

**精确率(_Precision_)**

$$
\text{Precision} = \dfrac{\text{True Positives}}{\text{Number of predict as positive}} = \dfrac{\text{True Positives}}{\text{True Positives + False Positives}}
$$

**召回率(_Recall_)**

$$
\text{Recall} = \dfrac{\text{True Positives}}{\text{Number of actual positive}} = \dfrac{\text{True Positives}}{\text{True Positives + False Negatives}}
$$

![precision vs recal base](/assets/images/machine-learning/precision_vs_recall_base.svg)

|   真正例(True Positives): 8    |   假正例(False Positives): 2   |
| :----------------------------: | :----------------------------: |
| **假负例(False Negatives): 3** | **真负例(True Negatives): 13** |

![precision vs recal raise threshold](/assets/images/machine-learning/precision_vs_recall_raise_threshold.svg)
提高分类阈值，**假正例**减少，**精确率**提高，同时**假负例**增多，**召回率**降低。

![precision vs recal lower threshold](/assets/images/machine-learning/precision_vs_recall_lower_threshold.svg)
降低分类阈值，**假正例**增加，**精确率**降低，同时**假负例**减少，**召回率**提高。

$\boldsymbol F_1$ **score**

$$
F_1 = 2\dfrac{P \cdot R}{P+R}
$$