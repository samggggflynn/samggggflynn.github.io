---
layout: post
#标题配置
title: Python-Numpy笔记
#时间配置
date:   2019-01-28 22:43:00 +0800
#大类配置
categories: python-numpy
#小类配置
tag: 笔记
---

* content
{:toc}
# Numpy
> **Numpy是Python的科学计算的核心库**，它提供了一个高性能的多维数组对象，以及用于处理这些数组的工具。

## Arrays（数组）
> 一个numpy数组是一个由不同数值组成的网格。网格中的数据都是同一种数据类型，可以通过非负整型数的元组来访问。维度的数量被称为数组的阶，数组的大小是一个由整型数构成的元组，可以描述数组不同维度上的大小。

- 我们可以从列表创建数组，然后利用方括号访问其中的元素：

```python
import numpy as np        #导入numpy包

a = np.array([1, 2, 3])   # Create a rank 1 array（创建一维数组）
print(type(a))            # Prints "<class 'numpy.ndarray'>"
print(a.shape)            # Prints "(3,)"
print(a[0], a[1], a[2])   # Prints "1 2 3"（通过[]中的索引访问输出数组）
a[0] = 5                  # Change an element of the array（改变某一元素的值）
print(a)                  # Prints "[5, 2, 3]"

b = np.array([[1,2,3],[4,5,6]])    # Create a rank 2 array（创建二维数组）
print(b.shape)                     # Prints "(2, 3)"（数组的行数、列数）
print(b[0, 0], b[0, 1], b[1, 0])   # Prints "1 2 4"（访问输出数组指定行列位置的元素值）
```
- Numpy还提供了很多其他创建数组的方法：

```python
import numpy as np   #导入numpy包

a = np.zeros((2,2))  # Create an array of all zeros（创建一个2x2的全’0‘数组）
print a              # Prints "[[ 0.  0.]
                     #          [ 0.  0.]]"

b = np.ones((1,2))   # Create an array of all ones（创建一个1x2的全’1‘数组）
print b              # Prints "[[ 1.  1.]]"

c = np.full((2,2), 7) # Create a constant array（创建一个2x2，所有元素都是’7‘的数组）
print c               # Prints "[[ 7.  7.]
                      #          [ 7.  7.]]"

d = np.eye(2)        # Create a 2x2 identity matrix（创建一个2x2的单位矩阵（二阶单位矩阵））
print d              # Prints "[[ 1.  0.]
                     #          [ 0.  1.]]"

e = np.random.random((2,2)) # Create an array filled with random values（创建一个全部由随机数构成的2x2的数组）
print e                     # Might print "[[ 0.91940167  0.08143941]
                            #               [ 0.68744134  0.87236687]]"
```

其他创建数组的方法见：[创建数组]（https://docs.scipy.org/doc/numpy/user/basics.creation.html#arrays-creation）

### Array indexing（数组索引）
> Numpy提供了多种索引数组的方法

- 切片（Slicing）：和lists相似，numpy数组也可以切片。
- 由于数组可能是多维的，因此必须为数组的每个维指定一个切片。
```python
import numpy as np #导入numpy包

# Create the following rank 2 array with shape (3, 4)（创建如下的3x4的二阶数组）
# [[ 1  2  3  4]
#  [ 5  6  7  8]
#  [ 9 10 11 12]]
a = np.array([[1,2,3,4], [5,6,7,8], [9,10,11,12]])

# Use slicing to pull out the subarray consisting of the first 2 rows
# and columns 1 and 2; b is the following array of shape (2, 2):
#（使用切片来拉出由前两行的第1（+1）列和第2（+1）列组成的2x2的子阵列）
# [[2 3]
#  [6 7]]
b = a[:2, 1:3]   #分别对行、列使用’：2‘和’1：3‘的切片操作

# A slice of an array is a view into the same data, so modifying it
# will modify the original array.
#由于切片操作是对原有相同的数据进行的，因此修改切片就会修改原有数组
print(a[0, 1])   # Prints "2"
b[0, 0] = 77     # b[0, 0] is the same piece of data as a[0, 1]
print(a[0, 1])   # Prints "77"
```
 还可以将整数索引与切片索引混合使用。但是，这样做会产生比原始数组更低级别的数组。请注意，这与MATLAB处理数组切片的方式完全不同：

```python
# 导入numpy包
import numpy as np

# Create the following rank 2 array with shape (3, 4)
#创建如下的数组
# [[ 1  2  3  4]
#  [ 5  6  7  8]
#  [ 9 10 11 12]]
a = np.array([[1,2,3,4], [5,6,7,8], [9,10,11,12]])

# Two ways of accessing the data in the middle row of the array.
#两种访问数组中间行数据的方法
# Mixing integer indexing with slices yields an array of lower rank,
#将整数索引与切片混合会产生较低等级的数组，而只使用切片产生一个与原数组相同等级的数组
# while using only slices yields an array of the same rank as the
# original array:
row_r1 = a[1, :]    # Rank 1 view of the second row of a
row_r2 = a[1:2, :]  # Rank 2 view of the second row of a
print(row_r1, row_r1.shape)  # Prints "[5 6 7 8] (4,)"
print(row_r2, row_r2.shape)  # Prints "[[5 6 7 8]] (1, 4)"

# We can make the same distinction when accessing columns of an array:
col_r1 = a[:, 1]
col_r2 = a[:, 1:2]
print(col_r1, col_r1.shape)  # Prints "[ 2  6 10] (3,)"
print(col_r2, col_r2.shape)  # Prints "[[ 2]
                             #          [ 6]
                             #          [10]] (3, 1)"
```
**整数数组索引：**使用切片索引到numpy数组时，生成的数组始终是原始数组的子数组（子阵列）。相反，整数数组索引 允许我们利用其它数组的数据构建一个新的数组。
> 整数索引数组又叫’花式索引‘ 。花式索引与切片索引不同，会把数据复制到新数组中

```python
# 导入numpy包
import numpy as np

# 新建一个数组
a = np.array([[1,2], [3, 4], [5, 6]])

# An example of integer array indexing.
# The returned array will have shape (3,) and
print(a[[0, 1, 2], [0, 1, 0]])  # Prints "[1 4 5]"#多个索引数组
#（👆* 这里比较难理解：在花式索引中，依次取第0行、第1行、第2行的对应第0列、第1列、第0列，即取a[0][0],a[1][2],a[2][1]）

# The above example of integer array indexing is equivalent to this:
# 上面的整数数组索引示例与此等效
print(np.array([a[0, 0], a[1, 1], a[2, 0]]))  # Prints "[1 4 5]"

# When using integer array indexing, you can reuse the same
# element from the source array:
# 使用整数数组索引，您可以重用相同的 来自源数组的元素
print(a[[0, 0], [1, 1]])  # Prints "[2 2]"

# Equivalent to the previous integer array indexing example
# 等效于前一个整数数组索引示例
print(np.array([a[0, 1], a[0, 1]]))  # Prints "[2 2]"
```
详细解释**整数索引数组**又叫’花式索引‘ 。花式索引与切片索引不同，会把数据复制到新数组中
```python
import numpy as np

# 新建一个数组
#array([[ 0,  1,  2,  3],
#      [ 4,  5,  6,  7],
#       [ 8,  9, 10, 11],
#       [12, 13, 14, 15],
#       [16, 17, 18, 19],
#       [20, 21, 22, 23],
#       [24, 25, 26, 27],
#       [28, 29, 30, 31]])

arr=np.arange(32).reshape((8,4))

arr[[4,2,1,7]]
# 将会输出array([[16, 17, 18, 19],
#       [ 8,  9, 10, 11],
#       [ 4,  5,  6,  7],
#       [28, 29, 30, 31]])

# 倒序整数索引
arr[[-4,-2,-1,-7]] #同上输出
```
……未完待续