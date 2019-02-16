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

**举例：**用一个例子演示公式是如何计算的。假设有3个分类，并且得到了分值![](/styles/images/2019-02-12-linear-classification-2/equation2.svg)。其中第一个类别是正确类别，即![](http://latex.codecogs.com/svg.latex?\\ y_i = 0)。同时假设![](http://latex.codecogs.com/svg.latex?\Delta)是10（后面会详细介绍该超参数）。上面的公式是将所有不正确分类![](http://latex.codecogs.com/svg.latex?\(j\not=y_i))加起来，所以我们得到两个部分：

![](http://latex.codecogs.com/svg.latex?\ Li=max(0,-7-13+10)+max(0,11-13+10))

可以看到第一个部分结果是0，这是因为[-7-13+10]得到的是负数，经过![](http://latex.codecogs.com/svg.latex?\ max(0,-))函数处理后得到0。这一对类别分数和标签的损失值是0，这是因为正确分类的得分13与错误分类的得分-7的差为20，高于边界值10。而SVM只关心差距至少要大于10，更大的差值还是算作损失值为0。第二个部分计算[11-13+10]得到8。虽然正确分类的得分比不正确分类的得分要高（13>11），但是比10的边界值还是小了，分差只有2，这就是为什么损失值等于8。简而言之，SVM的损失函数想要正确分类类别y_i的分数比不正确类别分数高，而且至少要高![](http://latex.codecogs.com/svg.latex?\Delta)。如果不满足这点，就开始计算损失值。

那么在这次的模型中，我们面对的是线性评分函数![](http://latex.codecogs.com/svg.latex?\（f(x_i,W)=Wx_i）)，所以我们可以将损失函数的公式稍微改写一下：


![](http://latex.codecogs.com/svg.latex?\ L_i=\sum_{j\not=y_i}max(0,w^T_jx_i-w^T_{y_i}x_i+\Delta))

其中![](http://latex.codecogs.com/svg.latex?\ w_j)是权重![](http://latex.codecogs.com/svg.latex?\ W)的第j行，被变形为列向量。然而，一旦开始考虑更复杂的评分函数![](http://latex.codecogs.com/svg.latex?\ f)公式，这样做就不是必须的了。

在结束这一小节前，还必须提一下的属于是关于0的阀值：![](http://latex.codecogs.com/svg.latex?\ max(0,-))函数，它常被称为**折叶损失（hinge loss）**。有时候会听到人们使用平方折叶损失SVM（即L2-SVM），它使用的是![](http://latex.codecogs.com/svg.latex?\ max(0,-)^2，将更强烈（平方地而不是线性地）地惩罚过界的边界值。不使用平方是更标准的版本，但是在某些数据集中，平方折叶损失会工作得更好。可以通过交叉验证来决定到底使用哪个。不使用平方是更标准的版本，但是在某些数据集中，平方折叶损失会工作得更好。可以通过交叉验证来决定到底使用哪个。

> 我们对于预测训练集数据分类标签的情况总有一些不满意的，而损失函数就能将这些不满意的程度量化。

----------------------------------------------

![](/styles/images/2019-02-11-linear-classification-1/margin.jpg)

----------------------------------------------

多类SVM“想要”正确类别的分类分数比其他不正确分类类别的分数要高，而且至少高出![](http://latex.codecogs.com/svg.latex?\Delta)的边界值。如果其他分类分数进入了红色的区域，甚至更高，那么就开始计算损失。如果没有这些情况，损失值为0。我们的目标是找到一些权重，它们既能够让训练集中的数据样例满足这些限制，也能让总的损失值尽可能地低。

----------------------------------------------

**正则化（Regularization）**：上面损失函数有一个问题。假设有一个数据集和一个权重集**W**能够正确的分类每个数据（即所有的边界都满足，都有![](http://latex.codecogs.com/svg.latex?\ L_i = 0))。问题在于这个**W**并不唯一：可能有很多相似的**W**都能正确的分类所有数据。一个简单的例子：如果**W**能够正确分类所有数据，即对于每个数据，损失值都是0.那么当![](http://latex.codecogs.com/svg.latex?\lambda>1)时，任何数乘![](http://latex.codecogs.com/svg.latex?\lambda W)都能使得损失值为0，因为这个变化将所有分值的大小都均等的扩大了，所以它们之间的绝对差值也扩大了。举个例子，如果一个正确分类的分值和举例它最近的错误分类的分值的差距都是15，对**W**乘以2将使得差距变成30.

换句话说，我们希望能向某些特定的权重**W**添加一些偏好，对其他权重则不添加，以此来消除模糊性。这一点是能够实现的，方法是向损失函数增加一个**正则化惩罚（regularization penalty）** ![](http://latex.codecogs.com/svg.latex?\ R(W)))部分。最常用的正则化惩罚是L2范式，L2范式通过对所有参数进行逐元素的平方惩罚来抑制大数值的权重：

![](http://latex.codecogs.com/svg.latex?\ R(W)=\sum_k \sum_l W^2_{k,l})

上面的表达式中，将**W**中所有元素平方后求和。注意正则化函数不是数据的函数，仅基于权重。包含正则化惩罚后，就能够给出完整的多累SVM损失函数了，它由两个部分组成：**数据损失（data loss）**，即所有样例的平均损失![](http://latex.codecogs.com/svg.latex?\ L_i)，以及**正则化损失（regularization loss）。完整公式如下所示：

![](http://latex.codecogs.com/svg.latex?\ L = \underbrace{ \frac{1}{N}\sum_i L_i}_{data \  loss}+\underbrace{\lambda R(W)}_{regularization \ loss})

将其展开完整公式是:

![](http://latex.codecogs.com/svg.latex?\ L=\frac{1}{N}\sum_i\sum_{j\not=y_i}[max(0,f(x_i;W)_j-f(x_i;W)_{y_i}+\Delta)]+\lambda \sum_k \sum_l W^2_{k,l})

其中，![](http://latex.codecogs.com/svg.latex?\ N)是训练集的数据量。