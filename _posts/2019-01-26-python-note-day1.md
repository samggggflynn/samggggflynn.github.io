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
### 整型和浮点型 
**（和其他语言同样）**
```python
x = 3
print(type(x)) # Prints "<class 'int'>"
print(x)       # Prints "3"
print(x + 1)   # Addition; prints "4"
print(x - 1)   # Subtraction; prints "2"
print(x * 2)   # Multiplication; prints "6"
print(x ** 2)  # Exponentiation;（幂运算） prints "9"
x += 1
print(x)  # Prints "4"
x *= 2
print(x)  # Prints "8"
y = 2.5
print(type(y)) # Prints "<class 'float'>"
print(y, y + 1, y * 2, y ** 2) # Prints "2.5 3.5 5.0 6.25"
```
>注意：python不像其他语言没有单一的自增`x++`或者自减`X--`
>python内嵌复数，同样可以操作复数（complex numbers）

### 布尔型 Booleans
> **python能够实现布尔逻辑运算，但是使用单词`and`、`or`而不是符号`&&`、`||`;**

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

- python对于字符串操作很方便

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
- String对象有很多有用的方法

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
详细用法见[python开发文档-string v3.7](https://docs.python.org/3.7/library/stdtypes.html#string-methods)

## Containers（容器）
- Python包含几种内置的容器类型：列表，字典，集合，元组；
### Lists（列表）
- 列表：是Python中的数组，但是可以调整大小和包含不同类型的元素

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
详细用法见[python开发文档-lists v3.7](https://docs.python.org/3.7/tutorial/datastructures.html#more-on-lists)
- **List切片（Slicing）**

>除了一次访问一个列表元素之外，Python还提供了访问子列表的简明语法;这被称为切片

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
- 循环Loops ：可以循环遍历数组中的元素

```python
animals = ['cat', 'dog', 'monkey']
for animal in animals:
    print(animal)
# Prints "cat", "dog", "monkey", each on its own line.
```
- **如果要访问循环体内每个元素的索引，请使用内置枚举`enumerate`函数**

```python
animals = ['cat', 'dog', 'monkey']
for idx, animal in enumerate(animals):
    print('#%d: %s' % (idx + 1, animal))
# Prints "#1: cat", "#2: dog", "#3: monkey", each on its own line
```
- List推导（list comprehensions）：编程时，我们经常想要将一种数据转换为另一种数据。举个简单的例子，考虑以下计算平方数的代码：

```python
nums = [0, 1, 2, 3, 4]
squares = []
for x in nums:
    squares.append(x ** 2)
print(squares)   # Prints [0, 1, 4, 9, 16]
```
- 可以使用下面更精简的方法

```python
nums = [0, 1, 2, 3, 4]
squares = [x ** 2 for x in nums]
print(squares)   # Prints [0, 1, 4, 9, 16]
```
- 列表推导（List comprehensions）也能够包含条件（condition）

```python
nums = [0, 1, 2, 3, 4]
even_squares = [x ** 2 for x in nums if x % 2 == 0]
print(even_squares)  # Prints "[0, 4, 16]"
```
======================================
### Dictionaries（字典）

> 字典（dictionary）存储`(key,value)` “键-值对”，这个Java中的`Map`相似；可以这样使用：

```Python
d = {'cat': 'cute', 'dog': 'furry'}  # （创建一个字典）Create a new dictionary with some data
print(d['cat'])       # Get an entry from a dictionary（从字典中获取一个条目）; prints "cute"
print('cat' in d)     # Check if a dictionary has a given key（检查字典中是否包含给定的值）; prints "True"
d['fish'] = 'wet'     # Set an entry in a dictionary（给字典添加一个新的条目）
print(d['fish'])      # Prints "wet"
# print(d['monkey'])  # KeyError: 'monkey' not a key of d（若字典中不存在则报错）
print(d.get('monkey', 'N/A'))  # Get an element with a default;（获取具有默认值的元素）prints "N/A"
print(d.get('fish', 'N/A'))    # Get an element with a default; prints "wet"
del d['fish']         # Remove an element from a dictionary（从字典中删除元素）
print(d.get('fish', 'N/A')) # "fish" is no longer a key; prints "N/A"
```
dictionary详细用法见[python开发文档-dict v3.7](https://docs.python.org/3.7/library/stdtypes.html#dict)
* 在字典中用“键”迭代（iterate over）很容易
```python
d = {'person': 2, 'cat': 4, 'spider': 8}	#定义一个字典
for animal in d:
    legs = d[animal]
    print('A %s has %d legs' % (animal, legs))
# Prints "A person has 2 legs", "A cat has 4 legs", "A spider has 8 legs"（依次对元素迭代按照格式输出）
```
** 如果要访问键及其对应的值，请使用`items`方法：**👇
```python
d = {'person': 2, 'cat': 4, 'spider': 8}
for animal, legs in d.items():
    print('A %s has %d legs' % (animal, legs))	#比上面更简单
# Prints "A person has 2 legs", "A cat has 4 legs", "A spider has 8 legs"
```
* 字典推导（Dictionary comprehensions）：和List comprehensions相似，但允许更容易的构造词典，如：
```python
nums = [0, 1, 2, 3, 4]
even_num_to_square = {x: x ** 2 for x in nums if x % 2 == 0}
print(even_num_to_square)  # Prints "{0: 0, 2: 4, 4: 16}"(输出平方)
```
=======================================
### Sets（集合）
- 集合（Sets）是不同元素的**无序**集合。使用`{}`标识，简单例子如下：
```python
animals = {'cat', 'dog'}  #新建一个集合
print('cat' in animals)   # Check if an element is in a set;（检查集合中是否存在给定的元素）prints "True"
print('fish' in animals)  # prints "False"
animals.add('fish')       # Add an element to a set（在集合中添加一个新的元素）
print('fish' in animals)  # Prints "True"（检查已经添加的元素）
print(len(animals))       # Number of elements in a set;（输出集合长度，即集合中元素个数）prints "3"
animals.add('cat')        # Adding an element that is already in the set does nothing（添加重复的元素没有用）
print(len(animals))       # Prints "3"（即不能添加重复的元素）
animals.remove('cat')     # Remove an element from a set（移除一个指定元素）
print(len(animals))       # Prints "2"
```
sets详细用法见[python开发文档-Sets v3.7](https://docs.python.org/3.7/library/stdtypes.html#set)
- 循环Loops：迭代集合（Sets）与迭代列表（Lists）具有相同的语法;但是由于集合是无序的，因此无法对访问集合元素的顺序进行假设：
```python
animals = {'cat', 'dog', 'fish'}
for idx, animal in enumerate(animals):
    print('#%d: %s' % (idx + 1, animal))
# Prints "#1: fish", "#2: dog", "#3: cat"
```
========================================
### Tuple（元组）

## Function（函数）
## Classes（类）