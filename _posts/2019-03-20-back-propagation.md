---
layout: post
#标题配置
title: Back Propagation（反向传播）（下）
#时间配置
date:   2019-03-20 23:17:00 +0800
#大类配置
categories: CS231n
#小类配置
tag: 笔记
---

* content
{:toc}

<div class="h_iframe">
    <iframe frameborder="no" border="0" marginwidth="0" marginheight="0"  height="86" src="//music.163.com/outchain/player?type=2&id=406232&auto=1&height=66"></iframe>
</div>

## 简介

**目标**：本节将帮助读者对**反向传播**形成直观而专业的理解。反向传播是利用**链式法则**递归计算表达式的梯度的方法。理解反向传播过程及其精妙之处，对于理解、实现、设计和调整神经网络非常**关键**。

**问题陈述**：这节的核心问题是：给定函数![](http://latex.codecogs.com/svg.latex?\ f(x))，其中![](http://latex.codecogs.com/svg.latex?\ x)是输入数据的向量，需要计算函数![](http://latex.codecogs.com/svg.latex?\ f)关于![](http://latex.codecogs.com/svg.latex?\ x)的梯度，也就是![](http://latex.codecogs.com/svg.latex?\ \nabla f(x))。

**目标**：之所以关注上述问题，是因为在神经网络中![](http://latex.codecogs.com/svg.latex?\ f)对应的是损失函数![](http://latex.codecogs.com/svg.latex?\ （L）)，输入![](http://latex.codecogs.com/svg.latex?\ x)里面包含训练数据和神经网络的权重。举个例子，损失函数可以是SVM的损失函数，输入则包含了训练数据![](http://latex.codecogs.com/svg.latex?\ (x_i,y_i),i=1...N)、权重![](http://latex.codecogs.com/svg.latex?\ W)和偏差![](http://latex.codecogs.com/svg.latex?\ b)。注意训练集是给定的（在机器侠学习中通常都是这样），而权重是可以控制的变量。因此，即使能用反向传播计算输入数据![](http://latex.codecogs.com/svg.latex?\ x_i)上的梯度，但在实际为了进行参数更新，通常也只有计算参数（比如![](http://latex.codecogs.com/svg.latex?\ W，b)）的梯度。然而![](http://latex.codecogs.com/svg.latex?\ x_i)的梯度有时仍然是有用的：比如将神经网络所做的事情可视化便于直观理解的时候，就能用上。

如果读者之前对利用链式法则计算偏微分已经很熟悉，仍然建议浏览本笔记。因为它呈现了一个相对成熟的反向传播视角，在该视角中能过够看见基于实数值回路的反向传播过程，而对其细节的理解和收获将帮助读者更好的通过本课程。

## 简单表达式和理解梯度

## 复合表达式，链式法则，反向传播

## 直观理解反向传播

## 模块：Sigmoid例子

## 反向传播实践：分段计算

## 回传流中的模式

## 用户向量化操作的梯度

## 小结


