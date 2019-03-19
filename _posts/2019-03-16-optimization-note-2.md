---
layout: post
#标题配置
title: Optimization Note（最优化笔记）（下）
#时间配置
date:   2019-03-17 23:17:00 +0800
#大类配置
categories: CS231n
#小类配置
tag: 笔记
---

* content
{:toc}

<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width="540" height="86" src="//music.163.com/outchain/player?type=2&id=1146770&auto=1&height=66"></iframe>

## 梯度计算

计算梯度有两种方法：一个是缓慢的近似方法（**数值梯度法**），但实现相对简单。另一个方法是（**分析梯度法**）计算迅速，结果精确，但是容易出错，且需要使用微分。现在对这两种方法进行介绍：

### 使用有限差值进行数值计算

上节中的公式已经给出数值计算梯度的方法。下面代码是一个输入为函数**f**和向量**x**，计算**f**的梯度的通用函数，它返回函数**f**在点**x**处的梯度：

```python
def eval_numerical_gradient(f, x):
  """  
  一个f在x处的数值梯度法的简单实现
  - f是只有一个参数的函数
  - x是计算梯度的点
  """ 

  fx = f(x) # 在原点计算函数值
  grad = np.zeros(x.shape)
  h = 0.00001

  # 对x中所有的索引进行迭代
  it = np.nditer(x, flags=['multi_index'], op_flags=['readwrite'])
  while not it.finished:

    # 计算x+h处的函数值
    ix = it.multi_index
    old_value = x[ix]
    x[ix] = old_value + h # 增加h
    fxh = f(x) # 计算f(x + h)
    x[ix] = old_value # 存到前一个值中 (非常重要)

    # 计算偏导数
    grad[ix] = (fxh - fx) / h # 坡度
    it.iternext() # 到下个维度

  return grad
```

根据上面的梯度公式，代码对是所有维度进行迭代，在每个维度上产生一个很小的变化h，通过观察函数值变化，计算函数在该维度上的偏导数。最后，所有的梯度存储在变量**grad**中。

**实践考量**：注意在数学公式中，**h**的取值是趋近于0的，然而在实际中，用一个很小的数值（比如例子中的1e-5）就足够了。在不产生数值计算出错的理想前提下，你会使用尽可能小的h。还有，实际中用**中心差值公式（centered difference formula）**![](http://latex.codecogs.com/svg.latex?\ [f(x+h)-f(x-h)]/2h)效果较好。细节可查看[wiki](https://en.wikipedia.org/wiki/Numerical_differentiation)。

可以使用上面这个公式来计算任意函数在任一点上的梯度。下面计算权重空间中的某些随机点上，CIFAR-10损失函数的梯度：

```python
# 要使用上面的代码我们需要一个只有一个参数的函数
# (在这里参数就是权重)所以也包含了X_train和Y_train
def CIFAR10_loss_fun(W):
  return L(X_train, Y_train, W)

W = np.random.rand(10, 3073) * 0.001 # 随机权重向量
df = eval_numerical_gradient(CIFAR10_loss_fun, W) # 得到梯度
梯度告诉我们损失函数在每个维度上的斜率，以此来进行更新：

loss_original = CIFAR10_loss_fun(W) # 初始损失值
print 'original loss: %f' % (loss_original, )

# 查看不同步长的效果
for step_size_log in [-10, -9, -8, -7, -6, -5,-4,-3,-2,-1]:
  step_size = 10 ** step_size_log
  W_new = W - step_size * df # 权重空间中的新位置
  loss_new = CIFAR10_loss_fun(W_new)
  print 'for step size %f new loss: %f' % (step_size, loss_new)

# 输出:
# original loss: 2.200718
# for step size 1.000000e-10 new loss: 2.200652
# for step size 1.000000e-09 new loss: 2.200057
# for step size 1.000000e-08 new loss: 2.194116
# for step size 1.000000e-07 new loss: 2.135493
# for step size 1.000000e-06 new loss: 1.647802
# for step size 1.000000e-05 new loss: 2.844355
# for step size 1.000000e-04 new loss: 25.558142
# for step size 1.000000e-03 new loss: 254.086573
# for step size 1.000000e-02 new loss: 2539.370888
# for step size 1.000000e-01 new loss: 25392.214036
```

**在梯度负方向上更新**：在上面的代码中，为了计算**W_new**，要注意我们是向着梯度**df**的负方向去更新，这是因为我们希望损失函数值是降低而不是升高。

**步长的影响**：梯度指明了函数在哪个方向是变化率最大的，但是没有指明在这个方向上应该走多远。在后续的课程中可以看到，选择步长（也叫做*学习率*）将会是神经网络训练中最重要（也是最头痛）的超参数设定之一。还是用蒙眼徒步者下山的比喻，这就好比我们可以感觉到脚朝向的不同方向上，地形的倾斜程度不同。但是该跨出多长的步长呢？不去欸的那个。如果谨慎的小步走，情况可能比较稳定但是进展较慢（这就是步长较小的情况）。相反，如果想尽快下山，那就大步走吧，但结果也不一定尽如人意。在上面的代码中就能看见反例，在某些点如果步长过大，反而可能越过最低点导致更高的损失值。

-----------------------------------------------------

![](/styles/images/2019-03-17-optimization-note-2/stepsize.jpg)
将步长效果视觉化的图例。从某个具体的点W开始计算梯度（白箭头方向是负梯度方向），梯度

告诉了我们损失函数下降最陡峭的方向。小步长下降稳定但进度慢，大步长进展快但风险更大。采取大步长可能大致错过最优点，让损失值上升。步长（后面会称其为**学习率**）将会是我们在调参中最重要的超参数之一。

----------------------------------------------------

效率问题：你可能已经注意到，计算数值梯度的复杂性和参数的量线性相关。在本例中有30730个参数，所以损失函数每走一步就需要计算30731次损失函数的梯度，现代神经网络很容易就有上千万的参数，因此这个问题只会越发严峻。显然这个策略不适合大规模数据，我们需要更好的策略

### 微分分析计算梯度

使用有限差值近似计算梯度比较简单，但缺点在于终究只是近似（因为我们对于*h*值是选取了一个很小的数值，但真正的梯度定义中*h*趋向于0的极限），且耗费计算资源太多。第二个梯度计算方法是利用微分来分析，能够得到计算梯度的公式（不是近似），用公式计算梯度速度很快，唯一不好的就是实现的时候容易出错，为了解决这个问题，在实际操作时常常将分析梯度法的结果和数值梯度法的结果作比较，以此来检查其实现的正确性，这个不走叫做**梯度检查**。

## 梯度下降

## 小结
