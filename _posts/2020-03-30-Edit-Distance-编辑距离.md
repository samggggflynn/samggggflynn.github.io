---
layout: post
#标题配置
title: 编辑距离python实现
date:   2020-03-30 08:31:00 +0800
#大类配置
categories: 数据结构
#小类配置
tag: 笔记
---

* content
{:toc}
---


定义：编辑距离是**针对2个字符串（例如英文文字）的差异程度的量化量测**，量测方式是看至少需要经过多少次的处理才能将一个字符串变成另外一个字符串。编辑距离可以用在自然语言处理中，例如**拼写检查**可以根据一个拼错的字和其他正确的字的编辑距离，判断哪一个（或哪几个）是比较可能的字。DNA也可以视为用A、C、G和T组成的额字符串，因此编辑距离也用在生物信息学中，**判断2个DNA的类似程度**。Unix下的diff及patch即是利用编辑距离来进行文编辑对比的例子。

> 它可以用来做**DNA分析**，**拼字检测**，**抄袭识别**等，一般只进行**插入**、**删除**、**替换**操作。

编辑距离有几种不同的定义，差异在对字符串进行的处理。

- 在**[莱温斯坦距离]([https://zh.wikipedia.org/wiki/%E8%90%8A%E6%96%87%E6%96%AF%E5%9D%A6%E8%B7%9D%E9%9B%A2](https://zh.wikipedia.org/wiki/萊文斯坦距離))**中，可以**删除、加入、取代（替换）**字符串中的任何一个字元，也是较常用的编辑距离定义，常常提到编辑距离时，指的就是莱温斯坦距离。
- 也存在其他编辑距离的定义方式，例如Damerau-Levenshtein距离时一种莱温斯坦距离的变种，但允许以单一操作交换相邻的两个字符（称为字符转置），如AB->BA的距离时1（交换）而非2（先删除再插入、或者两次替换）。
- LCS（最长公共子序）距离只允许删除、加入字元。
- 汉明距离只允许取代字元。

## 莱温斯坦距离

莱温斯坦距离，又称为Levenshtein距离，是编辑距离的一种。指两个字符串之间，由一个转为另外一个所需的最少编辑操作次数。允许编辑操作包括将一个字符串替换成另一个字符，插入一个字符，删除一个字符。

例如将kitten转为sitting

1. sitten	(k->s)
2. sittin	(e->i)
3. sitting	(->g)

## 例子

kiten和sitting的莱温斯坦距离是3.将kitten变为sitting的最小处理方式如下：
1. **k**itten →**s**itten（将k改为s）
2. sitt**e**n → sitt**i**n（将e改为i）
3. sittin → sittin**g**（最后加入g）

若是考虑LCS距离（只考虑加入及删除），CLS距离是5；

1. 删除位在第一个字的**k**
2. 在第一个字之前加入**s**
3. 删除位在第4个字的**e**
4. 在第4个字之前加入**i**
5. 在第6个字之后加入**g**

## python实现

1. str1或str2的长度为0返回另一个字符串的长度
```
if len(str1)==0:
	return len(str2)
if len(str2)==0:
	return len(str1)
```
2. 初始化(n+1)(m+1)的矩阵d，并让第一行和列的值从0开始增长。扫描两字符串（nm级的），如果：`str1[i] == str2[j]`，用`temp`记录它，为0。否则`temp`记为1。然后在矩阵`d[i,j]`赋于`d[i-1,j]+1` 、`d[i,j-1]+1`、`d[i-1,j-1]+temp`三者的最小值。
```
for i in range(1,m):
	for j in range(1,n):
		if sm[i-1]==sn[j-1]:
			cost = 0  # 也就是上面提到的temp
		else:
			cost = 1
		matrix[i][j] = min(matrix[i-1][j]+1, matrix[i][j-1]+1, matrix[i-1][j-1]+cost)
		# 上面表示取三者较小值赋予matrix[i][j]
```
3. 扫描完后，返回矩阵的最后一个值`d[n][m]`即是它们的距离。

4. 计算相似度公式：1-它们的距离/两个字符串长度的最大值。

----
### 算法过程
以字符串“ivan1”和“ivan2”举例：

1. 第一行和第一列的值从0开始增长

![img](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTYxMjMxMjAzODE5NTY4)

首先我们先创建一个矩阵，或者说是我们的二维数列，假设有两个字符串，我们的字符串的长度分别是m和n，那么，我们矩阵的维度就应该是`(m+1)*(n+1)`.

注意，**我们先给数列的第一行第一列赋值，从0开始递增赋值。我们就得到了图一的这个样子。**

之后我们计算第一列，第二列，依次类推，算完整个矩阵。

我们的计算规则就是：
`d[i,j]=min(d[i-1,j]+1 、d[i,j-1]+1、d[i-1,j-1]+temp)` 这三个当中的最小值。
其中：`str1[i] == str2[j]`，用`temp`记录它，为0。否则`temp`记为1

> 我们用d[i-1,j]+1表示增加操作
d[i,j-1]+1 表示我们的删除操作
d[i-1,j-1]+temp表示我们的替换操作

2. 举证元素的产生 Matrix[i - 1, j] + 1 ; Matrix[i, j - 1] + 1 ; Matrix[i - 1, j - 1] + t 三者当中的最小值

![img](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTYxMjMxMjExNjA3Njc2)

3. 依次类推直到矩阵全部生成

![img](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTYxMjMxMjExODE1OTE3)

![img](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTYxMjMxMjExODMwNjgz)

最后右下角的`matrix[n][m]`就是最后要求的编辑距离。

---
### python 代码实现

```python
# coding=utf-8


def minEditDist(sm, sn):
    m, n = len(sm) + 1, len(sn) + 1
    # m为第一个字符串长度+1 ， n为第二个字符串长度+1

    # 创建一个m*n的全零矩阵 matrix (m*n)
    matrix = [[0] * n for i in range(m)]
	
    matrix[0][0] = 0 
    # 创建第一个字符串构成的列
    for i in range(1, m):
        matrix[i][0] = matrix[i - 1][0] + 1
        
	# 创建第二个字符串构成的行
    for j in range(1, n):
        matrix[0][j] = matrix[0][j - 1] + 1
	
	# 逐行输出matrix(m*n)
    for i in range(m):
        print(matrix[i])

    print("********分界线**********")

    cost = 0

    for i in range(1, m):
        for j in range(1, n):
            if sm[i - 1] == sn[j - 1]:
                cost = 0  # 也就是上文提到的temp值
            else:
                cost = 1

            matrix[i][j] = min(matrix[i - 1][j] + 1, matrix[i][j - 1] + 1, matrix[i - 1][j - 1] + cost)
        	''' 
        	我们用matrix[i-1,j]+1表示增加操作
    		matrix[i,j-1]+1 表示我们的删除操作
    		matrix[i-1,j-1]+cost表示我们的替换操作
            '''
    for i in range(m):
        print(matrix[i])
    
    return matrix[m - 1][n - 1]
    # return matrix[-1][-1]


mindist = minEditDist("ivan1", "ivan2")
print(mindist)

```
