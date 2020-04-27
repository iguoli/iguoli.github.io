---
title: Matplotlib 基础用法
date: 2017-12-05
tags: Python
key: 2017-12-05-Matplotlib
---

# ipython 使用交互式绘图功能

```zsh
ipython --matplotlib
```

<!--more-->

# jupyter notebook 使用交互式绘图功能

```python
# 静态
%matplotlib inline

# 动态
%matplotlib notebook
```

# 导入常用包

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import scipy.stats as scs
from sympy import *
```

# matplotlib 中的 Figure 和 AxesSubplot 对象

```python
# 创建Figure对象
fig = plt.figure()
# 创建一个AxesSubplot对象
ax = fig.add_subplot(1, 1, 1)  # 可以使用简易写法fig.add_subplot(111)
# 在一个2 x 2的布局中，创建其中一个subplot
ax = fig.add_subplot(2, 2, 1)  # 返回2行x2列中第1个subplot
ax = fig.add_subplot(223)      # 返回2行x2列中第3个subplot

# fig.add_subplot()对应的顶级方法是
ax = plt.subplot(1, 1, 1)      # 添加一个subplot到当前figure

# 使用Figure对象一次创建2x3个subplot(NumPy二维数组，squeeze参数调节维度)
axes = fig.subplots(nrows=2,ncols=3)  # 2行 x 3列

# 使用plt一次性创建新的Figure和2x3个subplot(NumPy二维数组，squeeze参数调节维度)
fig, axes = plt.subplots(1)
fig, axes = plt.subplots(2,3)    # 2行 x 3列

# 返回fig对象下的subplot对象列表
axes2 = fig.get_axes()

# 下面两个subplot是同一对象
axes2[0] is axes[0][0]

# 调整subplot间的间距
plt.subplots_adjust(wspace=0.1, hspace=0.1)  # 间距的宽和高，百分比，0.1=10%
fig.subplots_adjust(wspace=0.1, hspace=0.1)

# 得到当前Figure和AxesSubplot对象
plt.gcf()
plt.gca()
fig.gca()

# 清除当前AxesSubplot内容
plt.cla()
ax.cla()
ax.clear()

# 清除当前Figure内容
plt.clf()
fig.clf()
fig.clear()

# 关闭窗口
plt.close()
```

# 绘制函数

```python
fig, axes = plt.subplots(2,2)

# 绘制函数y=x^2
x = np.linspace(-2, 2, num=100)
y = x**2
# 在最后一次使用过的subplot上绘制图像，没有则创建一个
plt.plot(x, y)
# 也可以在指定的subplot绘图
ax1 = axes[0,0]
ax1.plot(x, y)

# 绘制函数x*sin(x)
x = np.linspace(-100,100,1000)
y = list(map(lambda x: x*sin(x), x))
ax2 = axes[0,1]
ax2.plot(x, y)

# 使用pd.Series对象绘制函数sin(x)/x
x = np.linspace(-100,100,1000)
s = pd.Series(x, index=x)
s = s.apply(lambda x: sin(x)/x)).astype(float)
# 在最后一次使用过的subplot上绘制图像，没有则创建一个
s.plot();
# 指定subplot绘图
ax3 = axes[1,0]
s.plot(ax=ax3)
```

# 设置图形的颜色、标记和线形

```python
ax.plot(x, y, 'ro--')
# 等价于
ax.plot(x, y, color='r', marker='o', linestyle='--')
```

# 设置坐标轴范围、刻度、标签和图例

```python
# 以下Y轴与X轴设置一样，只要把x改成y即可

# 得到和设置当前subplot的X轴范围
plt.xlim()
plt.xlim([-5, 5])

# 同时得到和设置X轴和Y轴范围
plt.axis()
plt.axis([-5, 5, -5, 5])

# X轴范围
ax.get_xlim()
ax.set_xlim([-2, 2])
ax.axis()

# X轴刻度
ax.get_xticks()
ax.set_xticks([-2, -1, 0, 1, 2])

# X轴刻度标签
ax.get_xticklabels()
ax_set_xticklabels(['-two', '-one', 'zero', 'one', 'two'], rotation=30, fontszie='small')

# X轴名称
ax.get_xlabel()
ax.set_xlabel('Steps')

# 坐标轴标题
ax.get_title()
ax.set_title('My first matplotlib plot')

# 设置X, Y轴刻度长度一样
ax.set_aspect('equal')
```

# [绘制直方图(Histogram)](https://github.com/khrapovs/dataanalysispython/blob/master/lectures/matplotlib.ipynb)

- 条形图是用条形的长度表示各类别频数的多少，其宽度（表示类别）则是固定的；
- 直方图是用面积表示各组频数的多少，矩形的高度表示每一组的频数或频率，宽度则表示各组的组距，因此其高度与宽度均有意义。
- 由于分组数据具有连续性，直方图的各矩形通常是连续排列，而条形图则是分开排列。
- 条形图主要用于展示分类数据，而直方图则主要用于展示数据型数据

```python
mu, sigma = 100, 15
x = mu + sigma * np.random.randn(10000)
n, bins, patches = plt.hist(x, 50, normed=1, facecolor='g', alpha=0.75, lw=0)
plt.plot(bins, scs.norm.pdf(bins, mu, sigma), lw=2, c='b', ls='--')
plt.xlabel('Smarts')
plt.ylabel('Probability')
plt.title('Histogram of IQ')
plt.text(60, .025, r'$\mu=100,\ \sigma=15$')
plt.axis([40, 160, 0, 0.03])
plt.grid()
plt.show()
```