---
layout: post
#标题配置
title: Linear Classification（线性分类）（下）
#时间配置
date:   2019-02-18 22:55:00 +0800
#大类配置
categories: CS231n
#小类配置
tag: 笔记
---

* content
{:toc}


<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width="420" height="86" src="//music.163.com/outchain/player?type=2&id=28828076&auto=1&height=66"></iframe>

## Softmax分类器

SVM是最常用的两个分类器之一，而另一个就是Softmax分类器，它的损失函数与SVM的损失函数不同。对于学习过二元逻辑回归分类器的读者来说，Softmax分类器就可以理解为逻辑回归分类器面对多个分类的一般化归纳。SVM将输出![](http://latex.codecogs.com/svg.latex?\ f(x_i，W))作为每个分类的评分（因为无定标，所以难以直接解释）。与SVM不同，Softmax的输出（归一化的分类概率）更加直观，并且从概率上可以解释，这一点后文会讨论。在Softmax分类器中，函数映射![](http://latex.codecogs.com/svg.latex?\ f（x_i;W）= Wx_i)保持不变，但将这些评分制视为每个分类的未归一化的对数概率，并且将*折叶损失（hinge loss）*替换为**交叉熵损失（cross-entropy loss）**。公式如下：

![](http://latex.codecogs.com/svg.latex?\ Li=-log(\frac{e^{f_{y_i}}}{\sum_je^{f_j}}))或等价的![](http://latex.codecogs.com/svg.latex?\ L_i=-f_{y_i}+log(\sum_je^{f_j}))

在上式中，使用![](http://latex.codecogs.com/svg.latex?\ f_j)来表示分类评分向量![](http://latex.codecogs.com/svg.latex?\ f)中的第j个元素。和之前一样，整个数据集的损失值是数据集中所有样本数据的损失值![](http://latex.codecogs.com/svg.latex?\ L_i)的均值与正则化损失![](http://latex.codecogs.com/svg.latex?\ R（W）)之和。其中函数![](http://latex.codecogs.com/svg.latex?\ f_j(z)=\frac{e^{z_j}}{\sum_ke^{z_k}})被称作**Softmax函数**：其输入值是一个向量，其中每个元素值为任意实数的评分值（z中的），函数对其进行压缩，输出一个向量，其中每个元素值在0到1之间，且所有元素之和为1。所以，包含Softmax函数的完整交叉熵损失看起来唬人，实际上还是比较容易理解的。

**信息理论视角**：在“真实”分布p和估计分布q之间的交叉熵定义如下：

![](http://latex.codecogs.com/svg.latex?\ H(p,q)=-\sum_xp(x) logq(x))

因此，Softmax分类器所做的就是最小化在估计分类概率（就是上面的![](http://latex.codecogs.com/svg.latex?\ e^{f_{y_i}}/\sum_je^{f_j})）和“真实”分布之间的交叉熵，在这个解释中，“真实”分布就是所有概率密度都分布在正确的类别上（比如：![](http://latex.codecogs.com/svg.latex?\ p=[0,...1,...,0])中在![](http://latex.codecogs.com/svg.latex?\ y_i)的位置就有一个单独的1）。还有，既然交叉熵可以写成熵和相对熵（Kullback-Leibler divergence）![](http://latex.codecogs.com/svg.latex?\ H(p,q)=H(p)+D_{KL}(p \or q))，并且delta函数p的熵就是0，那么就能等价的看作是对两个分布之间的相对熵做最小化操作。换句话说，交叉熵损失函数“想要”预测分布的所有概率密度都在正确分类上。

**注**：*Kullback-Leibler差异（Kullback-Leibler Divergence）也叫做相对熵（Relative Entropy），它衡量的是相同事件空间里的两个概率分布的差异情况。*

**概率论解释**：先看下面的公式：

![](http://latex.codecogs.com/svg.latex?\ P(y_i \or x_i,W)=\frac{e^{f_{y_i}}}{\sum_je^{f_j}})

可以解释为是给定图像数据![](http://latex.codecogs.com/svg.latex?\  x_i)，以![](http://latex.codecogs.com/svg.latex?\ W)作为参数，分配给正确分类标签![](http://latex.codecogs.com/svg.latex?\ y_i)的归一化概率。为了理解这点，请回忆一下Softmax分类器将输出向量![](http://latex.codecogs.com/svg.latex?\ f)中的评分值解释为没有归一化的对数概率。那么以这些数值做指数函数的幂就得到了没有归一化的概率，而除法操作则对数据进行了归一化处理，使得这些概率的和为1.从概率论的角度来解释，我们就是在最小化正确分类的负对数概率，这可以看做是在进行*最大似然估计（MLE）*。该解释的另一个好处是，损失函数中的正则化部分![](http://latex.codecogs.com/svg.latex?\ R（W）)可以被看作是权重矩阵![](http://latex.codecogs.com/svg.latex?\ W)的高斯先验，这里进行的是最大后验估计（MAP）而不是最大似然估计。提及这些解释只是为了让读者形成直观的印象，具体细节就超过本课程范围了。

