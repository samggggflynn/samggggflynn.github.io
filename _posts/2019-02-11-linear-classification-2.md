---
layout: post
#标题配置
title: Linear Classification（线性分类）（中）
#时间配置
date:   2019-02-12 15:55:00 +0800
#大类配置
categories: CS231n
#小类配置
tag: 笔记
---

* content
{:toc}

<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width="420" height="86" src="//music.163.com/outchain/player?type=2&id=26524326&auto=1&height=66"></iframe>
:laughing:
{% dplayer "url=http://home.ustc.edu.cn/~mmmwhy/GEM.mp4"  "pic=http://home.ustc.edu.cn/~mmmwhy/GEM.jpg" "loop=yes" "theme=#FADFA3" "autoplay=false" "token=tokendemo" %}


# Linear Classification（线性分类）（中）


## 损失函数（Loss function）

在上一节定义了从图像像素值到所属类别的评分函数（score function），该函数的参数是权重矩阵**W**。在函数中，数据![(x_i,y_i)](/styles/images/2019-02-11-linear-classification-1/equation9.svg)是给定的，不能修改。但是我们可以调整权重矩阵这个参数，使得评分函数的结果与训练数据集中图像的真实类别一致，即评分函数在正确的分类的位置应当得到最高的评分（score）。

回到之前那张猫的图像分类例子，它有针对“猫”，“狗”，“船”三个类别的分数。我们看到例子中权重值非常差，因为猫分类的得分非常低（-96.8），而狗（437.9）和船（61.95）比较高。
我们将使用**损失函数（Loss Function）**（有时也叫**代价函数Cost Function**或**目标函数Objective**）来衡量我们对结果的不满意程度。直观地讲，当评分函数输出结果与真实结果之间差异越大，损失函数输出越大，反之越小。

## 多类支持向量机损失（Multiclass Support Vector Machine Loss）

损失函数的具体形式多种多样。首先，介绍常用的多类支持向量机（SVM）损失函数。SVM的损失函数想要SVM在正确分类上的得分始终比不正确分类上的得分高出一个边界值![](http://latex.codecogs.com/svg.latex?\Delta)。我们可以把损失函数想象成一个人，这位SVM先生（或者女士）对于结果有自己的品位，如果某个结果能使得损失值更低，那么SVM就更加喜欢它。

让我们更精确一些。回忆一下，第i个数据中包含图像![](http://latex.codecogs.com/svg.latex?\x_i)的像素和代表正确类别的标签![](http://latex.codecogs.com/svg.latex?\y_i)。评分函数输入像素数据，然后通过公式![](http://latex.codecogs.com/svg.latex?\f(xi,W))来计算不同分类类别的分值。这里我们将分值简写为**s**。比如，针对第j个类别的得分就是第j个元素：![](http://latex.codecogs.com/svg.latex?\\ s_j = f(x_i, W)_j)。针对第i个数据的多类SVM的损失函数定义如下：
![](/styles/images/2019-02-12-linear-classification-2/equation1.svg)

![](http://latex.codecogs.com/svg.latex?\\ L_i = \sum_{j\neq y_i} \max(0, s_j - s_{y_i} + \Delta))

**举例：**用一个例子演示公式是如何计算的。假设有3个分类，并且得到了分值![](/styles/images/2019-02-12-linear-classification-2/equation2.svg)。其中第一个类别是正确类别，即![](http://latex.codecogs.com/svg.latex?\\ y_i = 0)。同时假设![](http://latex.codecogs.com/svg.latex?\Delta)是10