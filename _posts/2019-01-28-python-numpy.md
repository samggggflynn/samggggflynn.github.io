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

## Array indexing（数组索引）
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
整数数组索引的一个有用技巧是从 矩阵的每一行中选择或改变一个元素
```python
# 导入numpy包
import numpy as np

# Create a new array from which we will select elements
#创建一个新数组，从中选择元素
a = np.array([[1,2,3], [4,5,6], [7,8,9], [10, 11, 12]])

print(a)  # prints "array([[ 1,  2,  3],
          #                [ 4,  5,  6],
          #                [ 7,  8,  9],
          #                [10, 11, 12]])"

# Create an array of indices
# 创建一个索引数组👇
b = np.array([0, 2, 0, 1])

# Select one element from each row of a using the indices in b
# 使用b中的索引从a的每一行中选择一个元素：👇
print(a[np.arange(4), b])  # Prints "[ 1  6  7 11]"

# Mutate one element from each row of a using the indices in b
# 使用b中的索引来变换a的每一行中的一个元素：👇
a[np.arange(4), b] += 10

print(a)  # prints "array([[11,  2,  3],
          #                [ 4,  5, 16],
          #                [17,  8,  9],
          #                [10, 21, 12]])
```
**布尔数组索引：**布尔数组索引允许您选择数组中的任意元素。
通常，这种类型的索引（布尔型数组索引）用于选择满足某些条件的数组元素。这是一个例子：
```python
# 导入numpy包
import numpy as np

# 新建一个数组a
a = np.array([[1,2], [3, 4], [5, 6]])

bool_idx = (a > 2)   # Find the elements of a that are bigger than 2;
					 # 找出数组 a 中大于 2 的元素；操作会返回一个和数组 a 一样视图的数组，每个位置对应 a 中元素是否大于 2 的布尔值
                     # this returns a numpy array of Booleans of the same
                     # shape as a, where each slot of bool_idx tells
                     # whether that element of a is > 2.

print(bool_idx)      # Prints "[[False False]
                     #          [ True  True]
                     #          [ True  True]]"

# We use boolean array indexing to construct a rank 1 array
# consisting of the elements of a corresponding to the True values
# of bool_idx
# 我们用布尔型数组索引创建一个一阶数组，新数组由bool_idx数组中的对应True值的元素
print(a[bool_idx])  # Prints "[3 4 5 6]"

# We can do all of the above in a single concise statement:
# 还可以将上面的所有操作整合到一个简介的声明中
print(a[a > 2])     # Prints "[3 4 5 6]"
```
其他数组索引的方法见：[Numpy数组索引](https://docs.scipy.org/doc/numpy/reference/arrays.indexing.html)

## 数据类型（Datatypes）
每个Numpy数组都是由类型相同的元素组成的网格。Numpy提供了一组可用于构造数组的大量数值数据类型。Numpy尝试在创建数组时猜测数据类型，但构造数组的函数通常还包含一个可选参数来显式指定数据类型。下面是个例子：
```python
# 导入numpy包
import numpy as np

x = np.array([1, 2])   # Let numpy choose the datatype（让numpy自己选择数据类型）
print(x.dtype)         # Prints "int64"

x = np.array([1.0, 2.0])   # Let numpy choose the datatype（让numpy自己选择数据类型）
print(x.dtype)             # Prints "float64"

x = np.array([1, 2], dtype=np.int64)   # Force a particular datatype（强制使用特定的数据类型）
print(x.dtype)                         # Prints "int64"

```
更多numpy数据类型的方法见：[Numpy数据类型](https://docs.scipy.org/doc/numpy/reference/arrays.dtypes.html)
## 数组计算

基本数学计算函数会对数组中元素逐个进行计算，既可以利用操作符重载，也可以使用numpy模块中的函数方式：
```python
import numpy as np
# 新建数组并指定数据类型
x = np.array([[1,2],[3,4]], dtype=np.float64)
y = np.array([[5,6],[7,8]], dtype=np.float64)

# Elementwise sum; both produce the array
# 元素和;两者都产生阵列；两者都会输出如下结果
# [[ 6.0  8.0]
#  [10.0 12.0]]
print x + y
print np.add(x, y)

# Elementwise difference; both produce the array
# 元素差;两者都产生阵列 两者都会输出如下结果
# [[-4.0 -4.0]
#  [-4.0 -4.0]]
print x - y
print np.subtract(x, y)

# Elementwise product; both produce the array
# 元素的积；两者都产生阵列 两者都会输出如下结果
# [[ 5.0 12.0]
#  [21.0 32.0]]
print(x * y)
print(np.multiply(x, y))

# Elementwise division; both produce the array
# 元素的除法；两者都产生阵列 两者都会输出如下结果
# [[ 0.2         0.33333333]
#  [ 0.42857143  0.5       ]]
print(x / y)
print(np.divide(x, y))

# Elementwise square root; produces the array
# 元素的平方根；两者都产生阵列 两者都会输出如下结果
# [[ 1.          1.41421356]
#  [ 1.73205081  2.        ]]
print(np.sqrt(x))
```
- 和MATLAB不同，`*`是元素逐个相乘，而不是矩阵乘法。在Numpy中使用`dot`来进行矩阵乘法

```python
import numpy as np

x = np.array([[1,2],[3,4]])
y = np.array([[5,6],[7,8]])

v = np.array([9,10])
w = np.array([11, 12])

# Inner product of vectors; both produce 219
# 向量的内积；两者都产生219  (x1*x2 + y1*y2=9*11 + 10*12)
print(v.dot(w))
print(np.dot(v, w))

# Matrix / vector product; both produce the rank 1 array [29 67]
# (dot方法及模块中函数)矩阵/向量乘法 结果是2 x 1  矩阵[29 67]
print(x.dot(v))
print(np.dot(x, v))

# Matrix / matrix product; both produce the rank 2 array
# 矩阵乘法 
# [[19 22]
#  [43 50]]
print(x.dot(y))
print(np.dot(x, y))
```
Numpy提供了许多用于在数组上执行计算的有用函数;其中最有用的是`sum`求和函数
```python
import numpy as np

x = np.array([[1,2],[3,4]])

print(np.sum(x))  # Compute sum of all elements; prints "10"（计算所有元素的和）
print(np.sum(x, axis=0))  # Compute sum of each column; prints "[4 6]"（计算列和）
print(np.sum(x, axis=1))  # Compute sum of each row; prints "[3 7]"（计算行和）
```
[numpy提供的完整数学函数列表](https://docs.scipy.org/doc/numpy/reference/routines.math.html)

除了使用数组计算数学函数，我们经常需要重新整形或以其他方式处理数组中的数据。
这种操作的最简单的例子是转置矩阵,要转置矩阵，只需使用数组对象的 `T` 属性即可
```python
import numpy as np

x = np.array([[1,2], [3,4]])
print(x)    # Prints "[[1 2]
            #          [3 4]]"
print(x.T)  # 打印矩阵 x 的转置(x.t)
			# Prints "[[1 3]
            #          [2 4]]"

# Note that taking the transpose of a rank 1 array does nothing:
# 请注意，转置一阶数组不会产生任何结果：
v = np.array([1,2,3])
print(v)    # Prints "[1 2 3]"
print(v.T)  # Prints "[1 2 3]"
```
[Numpy提供了更多用于操作数组的函数](https://docs.scipy.org/doc/numpy/reference/routines.array-manipulation.html)

## 广播（Broadcasting）
广播是一种强有力的机制，它让Numpy可以让不同大小的矩阵在一起进行数学计算。我们常常会有一个小的矩阵和一个大的矩阵，然后我们会需要用小的矩阵对大的矩阵做一些计算。
举个例子，如果我们想要把一个固定向量加到矩阵的每一行，我们可以这样做：
```python
import numpy as np

# We will add the vector v to each row of the matrix x,
# storing the result in the matrix y
# 把向量v加到矩阵x的每一行，把结果存到矩阵y
x = np.array([[1,2,3], [4,5,6], [7,8,9], [10, 11, 12]])
v = np.array([1, 0, 1])
y = np.empty_like(x)   # Create an empty matrix with the same shape as x（创建一个和矩阵x同型的空矩阵）

# Add the vector v to each row of the matrix x with an explicit loop
# 用显式循环将向量v添加到矩阵x的每一行。
for i in range(4):
    y[i, :] = x[i, :] + v

# Now y is the following
# [[ 2  2  4]
#  [ 5  5  7]
#  [ 8  8 10]
#  [11 11 13]]
print(y)
```
上面的操作是有效的。但是当矩阵x很大，利用循环来计算就会变得很慢。
注意，将向量v添加到矩阵x的每一行等同于通过垂直堆叠v的多个副本来形成矩阵vv，然后执行x和vv的元素和求和。我们可以像这样实现这种方法：
```python
import numpy as np

# We will add the vector v to each row of the matrix x,
# storing the result in the matrix y

x = np.array([[1,2,3], [4,5,6], [7,8,9], [10, 11, 12]])
v = np.array([1, 0, 1])
vv = np.tile(v, (4, 1))   # Stack 4 copies of v on top of each other（将v的4个副本堆叠在一起）
print(vv)                 # Prints "[[1 0 1]
                          #          [1 0 1]
                          #          [1 0 1]
                          #          [1 0 1]]"
y = x + vv  # Add x and vv elementwise
print(y)  # Prints "[[ 2  2  4
          #          [ 5  5  7]
          #          [ 8  8 10]
          #          [11 11 13]]"
```
Numpy广播允许我们执行此计算而不实际创建v的多个副本。看看下面例子：
```python
import numpy as np

# We will add the vector v to each row of the matrix x,
# storing the result in the matrix y
# 把向量v加到矩阵x的每一行，把结果存到矩阵y
x = np.array([[1,2,3], [4,5,6], [7,8,9], [10, 11, 12]])
v = np.array([1, 0, 1])
y = x + v  # Add v to each row of x using broadcasting（用广播把v加到x的每一行）
print(y)  # Prints "[[ 2  2  4]
          #          [ 5  5  7]
          #          [ 8  8 10]
          #          [11 11 13]]"
```
即使x具有形状（4,3）并且v由于广播而具有形状（3，），y = x + v也起作用;这就好像v实际上有形状（4,3），其中每一行都是v的副本，并且总和是按元素执行的。
对两个数组使用**广播**机制要遵守下列规则：
	1.如果数组的秩不同，使用1来将秩较小的数组进行扩展，直到两个数组的尺寸的长度都一样。
	2.如果两个数组在某个维度上的长度是一样的，或者其中一个数组在该维度上长度为1，那么我们就说这两个数组在该维度上是相容的。
	3.如果两个数组在所有维度上都是相容的，他们就能使用广播。
	4.如果两个输入数组的尺寸不同，那么注意其中较大的那个尺寸。因为广播之后，两个数组的尺寸将和那个较大的尺寸一样。
	5.在任何一个维度上，如果一个数组的长度为1，另一个数组长度大于1，那么在该维度上，就好像是对第一个数组进行了复制。
如果上述解释看不明白，可以读一读[文档](https://docs.scipy.org/doc/numpy/user/basics.broadcasting.html)和这个[解释](http://wiki.scipy.org/EricsBroadcastingDoc)
支持广播机制的函数是全局函数。

下面是广播（broadcasting）的一些应用：
```python
import numpy as np

# Compute outer product of vectors
# 计算向量的外积
v = np.array([1,2,3])  # v has shape (3,)
w = np.array([4,5])    # w has shape (2,)
# To compute an outer product, we first reshape v to be a column
# vector of shape (3, 1); we can then broadcast it against w to yield
# an output of shape (3, 2), which is the outer product of v and w:
# 为了计算外积，我们首先要将v重塑为（3 x 1）的列向量，然后让他与w相乘产生（3 x 2）的矩阵，这就是v和w的输出
# [[ 4  5]
#  [ 8 10]
#  [12 15]]
print(np.reshape(v, (3, 1)) * w)# 将v重塑为（3 x 1）的列向量，然后让他与w相乘

# Add a vector to each row of a matrix
x = np.array([[1,2,3], [4,5,6]])
# x has shape (2, 3) and v has shape (3,) so they broadcast to (2, 3),
# giving the following matrix:
# x 为（2 x 3）矩阵，v 为（1 x 3）向量，所以它们广播结果为如下（2 x 3）矩阵
# [[2 4 6]
#  [5 7 9]]
print(x + v)

# Add a vector to each column of a matrix
# 把向量加到矩阵每一列
# x has shape (2, 3) and w has shape (2,).
# x 为（2 x 3）矩阵，w 为（1 x 2）向量
# If we transpose x then it has shape (3, 2) and can be broadcast
# against w to yield a result of shape (3, 2); transposing this result
# yields the final result of shape (2, 3) which is the matrix x with
# the vector w added to each column. Gives the following matrix:
# 如果我们转置x然后它有形状（3 x 2）可以与w广播产生（3 x 2）矩阵；把结果再转置得到最终结果是（2 x 3）的矩阵。这就是把向量w加到矩阵x的每一列
# [[ 5  6  7]
#  [ 9 10 11]]
print((x.T + w).T)
# Another solution is to reshape w to be a column vector of shape (2, 1);
# 另一种方法是将w重塑为（2 x 1）的列向量
# we can then broadcast it directly against x to produce the same output.
# 然后我们可以直接对x广播它，产生同样输出
print(x + np.reshape(w, (2, 1)))

# Multiply a matrix by a constant:
# 将矩阵乘以常数：Numpy将标量相乘不改变矩阵形状
# x has shape (2, 3). Numpy treats scalars as arrays of shape ();
# these can be broadcast together to shape (2, 3), producing the
# following array:
# [[ 2  4  6]
#  [ 8 10 12]]
print(x * 2)
```
广播通常会使您的代码更简洁，更快速，因此您应该尽可能地使用它。
这篇教程涉及了你需要了解的numpy中的一些**重要内容**，但是numpy远不止如此。可以[查阅numpy文档](http://docs.scipy.org/doc/numpy/reference/)来学习更多。