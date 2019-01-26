---
layout: post
#标题配置
title: Python笔记（for python 3.7）
#时间配置
date:   2019-01-27 00:43:00 +0800
#大类配置
categories: python
#小类配置
tag: 笔记
---

* content
{:toc}
# python note(python笔记 python版本3.7)
## Basic data type（基本数据类型）
###整型和浮点型 
-（和其他语言同样）
```python
x = 3
print(type(x)) # Prints "<class 'int'>"
print(x)       # Prints "3"
print(x + 1)   # Addition; prints "4"
print(x - 1)   # Subtraction; prints "2"
print(x * 2)   # Multiplication; prints "6"
print(x ** 2)  # Exponentiation;(幂运算) prints "9"
x += 1
print(x)  # Prints "4"
x *= 2
print(x)  # Prints "8"
y = 2.5
print(type(y)) # Prints "<class 'float'>"
print(y, y + 1, y * 2, y ** 2) # Prints "2.5 3.5 5.0 6.25"
```
    注意：python不像其他语言没有单一的自增`x++`或者自减`X--`
    python内嵌复数，同样可以操作复数（complex numbers）

### 布尔型 Booleans
	python能够实现布尔逻辑运算，但是使用单词`and`、`or`而不是符号`&&`、`||`;
```python
t = True
f = False
print(type(t)) # Prints "<class 'bool'>"
print(t and f) # Logical AND; prints "False"
print(t or f)  # Logical OR; prints "True"
print(not t)   # Logical NOT; prints "False"
print(t != f)  # Logical XOR; prints "True"
```

### 字符串 Strings

    python对于字符串操作很方便
```python
hello = 'hello'    # String literals can use single quotes
world = "world"    # or double quotes; it does not matter.
print(hello)       # Prints "hello"
print(len(hello))  # String length; prints "5"
hw = hello + ' ' + world  # String concatenation（级联或者拼接）
print(hw)  # prints "hello world"
hw12 = '%s %s %d' % (hello, world, 12)  # sprintf style string formatting
print(hw12)  # prints "hello world 12"
```
    String对象有很多有用的方法
```python
s = "hello"
print(s.capitalize())  # Capitalize a string;（使字符串首字母大写）prints "Hello"
print(s.upper())       # Convert a string to uppercase;（大写转换） prints "HELLO"
print(s.rjust(7))      # Right-justify a string,（右适应，左边空格）padding with spaces; prints "  hello"
print(s.center(7))     # Center a string, padding with spaces（居中，左右空格）; prints " hello "
print(s.replace('l', '(ell)'))  # Replace all instances of one substring with another;（部分字符替换）
                                # prints "he(ell)(ell)o"
print('  world '.strip())  # Strip leading and trailing whitespace;（去掉前后的空格）prints "world"
```
详细用法见[python开发文档 v3.7]（https://docs.python.org/3.7/library/stdtypes.html#string-methods）

## Containers（容器）
-Python包含几种内置的容器类型：列表，字典，集合，元组；
###List（列表）
-列表：是Python中的数组，但是可以调整大小和包含不同类型的元素
```python
xs = [3, 1, 2]    # Create a list(创建list)
print(xs, xs[2])  # Prints "[3, 1, 2] 2"（list中包含list）
print(xs[-1])     # Negative indices count from the end of the list; prints "2"（负数：从后向前索引）
xs[2] = 'foo'     # Lists can contain elements of different types（list中可以包含不同的元素 修改list）
print(xs)         # Prints "[3, 1, 'foo']"
xs.append('bar')  # Add a new element to the end of the list
print(xs)         # Prints "[3, 1, 'foo', 'bar']"
x = xs.pop()      # Remove and return the last element of the list（从list尾移除一个元素 修改list）
print(x, xs)      # Prints "bar [3, 1, 'foo']"
```
详细用法见[python开发文档 v3.7]（https://docs.python.org/3.7/tutorial/datastructures.html#more-on-lists）
-List切片（Slicing）
	除了一次访问一个列表元素之外，Python还提供了访问子列表的简明语法;这被称为切片
```python
nums = list(range(5))     # range is a built-in function that creates a list of integers(range是python中内建的函数，自动生成一列整数)
print(nums)               # Prints "[0, 1, 2, 3, 4]"
print(nums[2:4])          # Get a slice from index 2 to 4 (exclusive); prints "[2, 3]"
print(nums[2:])           # Get a slice from index 2 to the end; prints "[2, 3, 4]"
print(nums[:2])           # Get a slice from the start to index 2 (exclusive); prints "[0, 1]"
print(nums[:])            # Get a slice of the whole list; prints "[0, 1, 2, 3, 4]"（相当于复制，注：不同于赋值用法）
print(nums[:-1])          # Slice indices can be negative; prints "[0, 1, 2, 3]"
nums[2:4] = [8, 9]        # Assign a new sublist to a slice（将新的子列表分配给切片，会改变list）
print(nums)               # Prints "[0, 1, 8, 9, 4]"
```
--Loop 可以循环遍历数组中的元素
```python
animals = ['cat', 'dog', 'monkey']
for animal in animals:
    print(animal)
# Prints "cat", "dog", "monkey", each on its own line.
```
--如果要访问循环体内每个元素的索引，请使用内置枚举`enumerate`函数
```python
animals = ['cat', 'dog', 'monkey']
for idx, animal in enumerate(animals):
    print('#%d: %s' % (idx + 1, animal))
# Prints "#1: cat", "#2: dog", "#3: monkey", each on its own line
```
-List 理解：编程时，我们经常想要将一种数据转换为另一种数据。举个简单的例子，考虑以下计算平方数的代码：
```nums = [0, 1, 2, 3, 4]
squares = []
for x in nums:
    squares.append(x ** 2)
print(squares)   # Prints [0, 1, 4, 9, 16]
```

###Dictionaries（字典）
###Sets（集合）
###Tuple（元组）
##Function（函数）
##Classes（类）