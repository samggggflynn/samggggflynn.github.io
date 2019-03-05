---
layout: post
#标题配置
title: Optimization Note（最优化笔记）（上）
#时间配置
date:   2019-03-04 16:17:00 +0800
#大类配置
categories: CS231n
#小类配置
tag: 笔记
---

<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width="540" height="86" src="//music.163.com/outchain/player?type=2&id=149297&auto=1&height=66"></iframe>

# 最优化分类笔记 （上）

## 简介 

在上一节中，我们介绍了图像分类任务中的两个关键部分：

1.基于参数的**评分函数**。该函数将院士图像像素映射为分类评分值（例如：一个线性函数）。
2.**损失函数**。该函数能够根据分类评分和训练集图像数据实际分类的一致性，衡量某个具体参数集的汗淮。损失函数有多种版本和不同的实现方式（例如：Softmax或SVM）。

上节中，线性函数的形式是![](http://latex.codecogs.com/svg.latex?\ f(x_i, W)=Wx_i)，而SVM实现的公式是：

![](http://latex.codecogs.com/svg.latex?\ L=\displaystyle\frac{1}{N}\sum_i\sum_{j\not= y_i}[max(0,f(x_i;W)_j-f(x_i;W)_{y_i}+1)]+\alpha R(W))

对于图像数据![](http://latex.codecogs.com/svg.latex?\ x_i)，如果基于参数集![](http://latex.codecogs.com/svg.latex?\ W)做出的分类预测与真实情况比较一致，那么计算出来的损失值![](http://latex.codecogs.com/svg.latex?\ L)就很低。现在介绍第三个，也是最后一个关键部分：**最优化 Optimization**。最优化是寻找能够使得损失函数值最小化的惨呼![](http://latex.codecogs.com/svg.latex?\ W)的过程。

**铺垫**：一旦理解了这三个部分是如何相互运作的，我们将会回到第一个部分（基于参数的函数映射），然后将其拓展为一个远比线性函数复杂的函数：首先是神经网阔，然后是卷积神经网络。而损失函数和最优化过程这两个部分将会保持相对稳定。

## 损失函数可视化

本课中讨论的损失函数一般都是定义在高纬度空间中（比如，在CIFAR-10中一个线性分类器的权重矩阵大小是[10×3073]，就有30730个参数），这样要将其可视化就很困难。然儿办法还是有的，在1个维度或者是2个维度的方向上对高纬度空间进行切片，就能得到一些直观感受。例如，随机生成一个权重矩阵![](http://latex.codecogs.com/svg.latex?\ W)，该矩阵就与高纬度空间中的一个点对应。然后沿着某个维度方向前进的同时记录损失函数值的变化。换句话说，就是生成一个随机的方向![](http://latex.codecogs.com/svg.latex?\ W_1)并且沿着此方向计算损失值，计算方法是根据不同的![](http://latex.codecogs.com/svg.latex?\ a)值来计算![](http://latex.codecogs.com/svg.latex?\ L(W+aW_1))。这个过程将生成一个图标，其x轴是![](http://latex.codecogs.com/svg.latex?\ a)值，y轴是损失函数值。同样的方法还可以用在两个维度上，通过改变![](http://latex.codecogs.com/svg.latex?\ a)，![](http://latex.codecogs.com/svg.latex?\ b)来计算损失值![](http://latex.codecogs.com/svg.latex?\ L(W+aW_1+bW_2))，从而给出二维的图像。在图像中，![](http://latex.codecogs.com/svg.latex?\ a)，![](http://latex.codecogs.com/svg.latex?\ b)可以分别用x和y轴表示，而损失函数的值可以用颜色变化来表示：

-------------------------------------------------

![](/styles/images/2019-03-04-optimization-note-1/svm1d.png)

一个无正则化的多类SVM的损失函数的图示。左边和中间只有一个样本数据，右边是CIFAR-10中的100个数据。**左**：a值变化在某个维度方向上对应的损失值变化。**中和右**：两个维度方向上的损失值切片图，蓝色部分是低损失值区域，红色部分是高损失值区域。注意损失函数的分段线性结构。多个样本的损失值是总体的平均值，所以右边的碗状结构是很多的分段线性结构的平均（比如中间这个就是其中之一）。

-------------------------------------------------

我们可以通过数学公式来解释损失函数的分段线性结构。对于一个单独的数据，有损失函数的计算公式如下：

![](http://latex.codecogs.com/svg.latex?\ Li=\sum_{j\not=y_i}[max(0,w^T_jx_i-w^T_{y_i}x_i+1)])

通过公式可见，每个样本的数据损失值是以![](http://latex.codecogs.com/svg.latex?\ W)为参数的线性函数的总和（零阈值来源于![](http://latex.codecogs.com/svg.latex?\ max(0,-))函数。）![](http://latex.codecogs.com/svg.latex?\ W)的每一行（即![](http://latex.codecogs.com/svg.latex?\ w_j)），有时候它前面是一个正好（比如当它对应错误分类的时候），有时候它前面是一个负号（比如当它是是正确分类的时候）。为进一步阐明，假设有一个简单的数据集，其中包含有3个只有1个维度的点，数据集数据点有3个类别。那么完整的无正则化SVM的损失值计算如下：

![](http://latex.codecogs.com/svg.latex?\ L_0=max(0,w^T_1x_0-w^T_0x_0+1)+max(0,w^T_2x_0-w^T_0x_0+1))

![](http://latex.codecogs.com/svg.latex?\ L_1=max(0,w^T_0x_1-w^T_1x_1+1)+max(0,w^T_2x_1-w^T_1x_1+1))

![](http://latex.codecogs.com/svg.latex?\ L_2=max(0,w^T_0x_2-w^T_2x_2+1)+max(0,w^T_1x_2-w^T_2x_2+1))


![](http://latex.codecogs.com/svg.latex?\ L=(L_0+L_1+L_2)/3)

因为这些例子都是一维的，所以数据![](http://latex.codecogs.com/svg.latex?\ x_i)和权重![](http://latex.codecogs.com/svg.latex?\ w_j)都是数字。观察![](http://latex.codecogs.com/svg.latex?\ w_0)，可以看到上面的式子中一些项是![](http://latex.codecogs.com/svg.latex?\ w_0)的线性函数，且每一项都会与0比较，取两者的最大值。可作图如下：

-------------------------------------------------
