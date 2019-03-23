---
layout: post
#标题配置
title: Python笔记（for python 3.7）
#时间配置
date:   2019-01-27 20:43:00 +0800
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

### Dictionaries（字典）

> 字典（dictionary）存储`(key,value)` “键-值对”，这个Java中的`Map`相似；可以这样使用：

```python
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
**如果要访问键及其对应的值，请使用`items`方法：**👇

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

### Sets（集合）
- 集合（Sets）是**不同元素**的**无序**集合。使用`{}`标识，简单例子如下：

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
- 集合的一些操作

```python
a = {1,2,3,4}
b = {2,3,4,5}
print(a.union(b))        #print {1,2,3,4,5};print(b.union(a))效果相同，同理print(a|b)也是同样效果
print(a.intersection(b)) #print {2,3,4}; 同理b.intersection(a)或者a&by也是
print(a.difference(b))   #print {1}; 相当于a-b ；elements in a{} but not in b{};
print(b.difference(a))   #print {5}; 相当于b-a ；elements in b{} but not in a{};
#a.issubset(b) 或者 a<=b   判断a是不是b的子集
#
```
sets详细用法见[python开发文档-Sets v3.7](https://docs.python.org/3.7/library/stdtypes.html#set)
- 循环Loops：迭代集合（Sets）与迭代列表（Lists）具有相同的语法;但是由于集合是无序的，因此无法对访问集合元素的顺序进行假设：

```python
animals = {'cat', 'dog', 'fish'}		#新建一个集合
for idx, animal in enumerate(animals):		#使用枚举方法
    print('#%d: %s' % (idx + 1, animal))	#进行迭代输出
# Prints "#1: fish", "#2: dog", "#3: cat"
```
集合推导（Sets comprehensions）：如同列表（lists）和字典（dictionaries）一样，可以用集合推导轻松创建集合
```python
from math import sqrt		#导入平方根方法sqrt()
nums = {int(sqrt(x)) for x in range(30)}
print(nums)  # Prints "{0, 1, 2, 3, 4, 5}"
```

### Tuple（元组）
> 元组是一组**不可变**,**有序**值的列表。元组在很多方法都和列表（Lists）相似，最大的区别在于：元组能够被用于字典（dictionary）的值（key）和集合（Sets）的元素；而列表list不能。
> 下面是一个简单的例子

```python
d = {(x, x + 1): x for x in range(10)}  # Create a dictionary with tuple keys
t = (5, 6)        # Create a tuple
print(type(t))    # Prints "<class 'tuple'>"
print(d[t])       # Prints "5"
print(d[(1, 2)])  # Prints "1"
```
Tuples元组详细用法见[python开发文档-Tuples v3.7](https://docs.python.org/3.7/tutorial/datastructures.html#tuples-and-sequences)
## 赋值机制

```python
a = 10000
b = a
id(a) is id(b)     #print 'True'

b = 89898
id(a) is id(b)     #print 'False'

b = 10000
id(a) is id(b)     #print 'False'（较大的数对应不同的内存位置）

a = 1
b = 1
id(a) is id(b)     #print 'True'（为了提高内存的效率，较小的数不会在内存中重复创建）
```
## 判断结构
> 通过**缩进**控制判断结构

```python
a = 100
if a > 100:
	print('100')   #这里是一个if判断的结构
	
elif a < 50:
	print('50')

else:
	print('others')

lists = [123,456,789]
if 123 in lists:
	print('ok')     #Lists中的判断结构

dicts = {'num1':123,'num2':456}
if 'num1' in dicts:
	print('ok')     #Dictionaries中的判断结构
```
## 循环结构

```python
a = 0
while a<=10:
	print(a)
	a += 1

a = set(['s','a','m'])
while a:    #只要集合set中还有值，就可以一直执行while
	b = a.pop()
	print(b)  #无序输出's','a','m' 效果同for name in a: print(name)
```
> 一般的遍历循环

```python
a = [212,432,543,312,12342,543412,3241]
for i in range(len(a)):
	print(a[i])
# 遍历list并输出
```

## Function（函数）
> Python中使用关键字`def`来定义函数，例如：

```python
def sign(x):		#用def定义一个sign()函数
    if x > 0:
        return 'positive'
    elif x < 0:
        return 'negative'
    else:
        return 'zero'

for x in [-1, 0, 1]:
    print(sign(x))
# Prints "negative", "zero", "positive"
```
> 我们通常会定义函数来接受可选的关键字参数，如下所示

```python
def hello(name, loud=False):		#定义一个参数个数可变的函数hello()给定其中一个参数loud默认赋值；
    if loud:
        print('HELLO, %s!' % name.upper())
    else:
        print('Hello, %s' % name)

hello('Bob') # Prints "Hello, Bob"
hello('Fred', loud=True)  # Prints "HELLO, FRED!"

def add_numbers(a,*args):
	for i in args: 
		a += i
	return a
add_numbers(3,43,23,1)    #Prints result of (3+43+23+1)（输出多个参数的和）
```
Functions函数详细用法见[python开发文档-Functions v3.7](https://docs.python.org/3.7/tutorial/controlflow.html#defining-functions)

## 异常（Exceptions）

```python
import math

for i in range(10):
	try:      # 尝试
		input_number = input('write a number:')
		if input_number == 'q'
			break
		result = math.log(float(input_number))  # 若该处出现异常直接跳转到异常except处
		print(result)
	except ValueError:   # 如果发现异常，打印异常(ValueError是异常类型)，然后重复执行try的内容
		print(‘ValueError: input must > 0’)  # log()函数的输入必须大于零
		# break （可以通过break结束异常）
```
另一种异常
```python
import math
for i in range(10):
	try:      # 尝试
		input_number = input('write a number:')
		if input_number == 'q'
			break
		result = 1/math.log(float(input_number))  #分母不能为零，为零则异常
		print(result)
	except ValueError:   # 如果发现异常，打印异常(ValueError是异常类型)，然后重复执行try的内容
		print('ValueError: input must > 0')  # log()函数的输入必须大于零
		# break （可以通过break结束异常）
		
	except ZeroDivisionError:   # 如果发现异常，打印异常(ZeroDivisionError是异常类型)，然后重复执行try的内容
		print('log(value) must != 0')     # 1/log() 分母必须大于零
		# break （可以通过break结束异常）
		except Exception:   # 如果发现异常，打印异常(Exception是‘ 通用 ’异常类型，所有类型的异常都可以处理抛出)，然后重复执行try的内容
		print('unknown error')  # 打印输出错误的内容
		# break （可以通过break结束异常）
```
自定义的异常👇
```python
class MyError(ValueError):
	pass
cur_list['s','a','m']
while True:
	cur_input = input()
	if cur_input not in cur_list:
		raise MyError('Invalid input:%s' %cur_input)
	# 当输入非's''a''m'时，例如输入’‘sam’‘时，打印错误信息 MyError:Invalid input: sam?
```
finally的用法：无论有没有异常finally都会执行：
```python
try:
	print('hello')
finally:
	print('finally')    # 输出hello
	                    #     finally

try:
	1/0             # 先遇到异常
finally：
	print('finally')  # 先输出 finally ，在打印异常信息 ZeroDivisionError

try:
	1/0             # 先遇到异常
expect：            # 有异常expect，输出异常，再输出finally
	print('====0')
finally：
	print('finally')  # 先输出异常 ====0 ，再输出 finally 
```

## Python文件操作
python中文件操纵不同于numpy中，此处环境为jupter note
- 新建文件

```python
%%writefile samgggg.py
# 将下面内容写入到新建文件’samgggg.py‘文件中并保存（也可以保存成txt等文件）
name = input('input your id:')
print(name)
```
- 读文件

```python
sam = open('./samgggg.py')  # 注意路径可以是当前路径’./‘，或者完整路径
sam_read = sam.read()
print(sam_read)
# 打印输出name = input('input your id:')
#        print(name)
```
- 逐行读文件

```python
lines = sam.readlines()
print(type(lines))   # 输出lines类型为 class list 列表
print(lines)         # 输出['name = input('input your id:')\n','print(name)']
for line in lines:
	print('cur_line:'line) # 输出：cur_line:name = input('input your id:')
                             #    cur_line:print(name)
sam.close()                # 读完文件要关闭，这样完成一个完整的读文件操作
```
- 写文件

```python
txt = open('new_file.txt','w')  # 'w'模式，新写的会覆盖原始的内容；'a'模式会追加；'r'模式读
txt.write('jin tian tian qi bu cuo')
txt.write('\n')    # 记得加换行
txt.write('samgggg')
txt.close()        # close完成整个操作
```
- 其他文件操作

```python
with open('new_file.txt','w') as f:
	f.write('jin tian tian qi bu cuo')  # 操作会自动close（省事，不会忘记close，最多的用法）

# 相当于如下操作
txt = open('new_file.txt','w')
try:
	for i in range(50):
		10/(i-50)
		txt.write(str(i)+'\n')
except Exception:
	print('error:',i)
finally:
	close()                             # 在finally中加close
```
## Classes（类）
> 在Python中定义类的语法很简单

```python
class Greeter(object):

    # Constructor（构造函数）初始化的方法，当创建一个类的时候，首先会调用它
    def __init__(self, name):
        self.name = name  # Create an instance variable（创建实例变量）

    # Instance method（实例方法）
    def greet(self, loud=False):
        if loud:
            print('HELLO, %s!' % self.name.upper())
        else:
            print('Hello, %s' % self.name)

g = Greeter('Fred')  # Construct an instance of the Greeter class（构造Greeter类的实例）
g.greet()            # Call an instance method; prints "Hello, Fred"（调用实例方法）
g.greet(loud=True)   # Call an instance method; prints "HELLO, FRED!"（调用实例方法）
```
```python
class people:
	'帮助信息：xxxxx'
	# 所有实例都会共享
	number = 100
	# 构造函数，初始化的方法，当创建一个类的时候，首先会调用它
	def __init__(self,name,age):
		self.name = name
		self.age = age
	def display(self):
		print('number = ',people.number)
	def display_name(self):
		print(self.name)

p1 = people('sam',18)
p2 = people('python','40')

p1.name      # 将会打印  'sam'
p1.display() # 将会打印  'number = :100'
p2.display() # 将会打印  'number = :100'

p2.name = 'hello'
p2.name      # 将会打印  'hello'

del p2.name  # 删除p2.name

hasattr(p2,'name')#用hasattr判断p2中是否存在 name，将会输出 False

getattr(p1,'name')#将会输出 'sam'

setattr(p1,'name','gggg')
getattr(p1,'name') # 将会输出 'gggg'

delattr(p1,'name') # 删除属性

```
> print(people.__doc__)  # 将会打印'帮助信息：xxxxx'
> print(people.__name__)  # 将会打印'people'
> print(people.__module__)  # 将会打印'__main__'
> print(people.__bases__)  # 将会打印'(<class 'object'>)'
> print(people.__dict__)  # 将会打印''__module__','__main__','__doc__''

**类的继承** 、**方法的重写**
```python
class Parent:# 定义父类
	number = 100
	def __init__(self):
		print('调用父类构造函数')
	def parentMethod():
		print('调用父类方法')
	def setAttr(self,attr):  #设置一个属性值，用于验证子类能否继承父类的属性（能）
		Parent.parentAttr = attr
	def getAttr(self):
		print('父类的属性:',Parent.parentAttr)
	def newMethod(self):
		print('父类要被重写的方法')   # 可以在子类中重写父类中的方法

class child(Parent): # 定义子类继承父类Parent
	def __init__(self):
		print('调用子类构造函数')
	def childMethod():
		print('调用子类方法')
	# 在子类中定义与父类 同名 的方法会重写父类方法
	def newMethod(self):
		print('子类重写了父类方法')

c = child()      # 实例化子类，会调用子类构造函数
c.childMethod()  # 调用子类方法
c.parentMethod() # 调用父类方法
c.setAttr(1000)  # 设置属性值
c.getAttr()      # 检查能够get到上一步设置的属性
c.newMethod()    # 子类将会重写父类方法
# 输出 ： 调用子类构造函数
#		 调用子类方法
#		 调用父类方法
#		 父类的属性：1000
#        子类重写了父类方法
```
Classes ’类‘详细用法见[python开发文档-Functions v3.7](https://docs.python.org/3.7/tutorial/classes.html)

## 时间操作
```python
# 导入time
import time

print(time.time()) # 时间戳，距离1970年过去的时间

print(time.localtime(time.time())) #打印当前时间
#  输出：time.struct_time(tm_year=2019, tm_mon=1, tm_mday=30, tm_hour=23, tm_min=49, tm_sec=24, tm_wday=2, tm_yday=30, tm_isdst=0)

print(time.asctime(time.localtime(time.time()))) # 时间格式化
# 输出：Wed Jan 30 23:51:53 2019

print(time.strftime('%Y-%m-%d %H:%M:%S',time.localtime()))
# 格式化输出：2019-01-30 23:54:32
```
- 日历

```python
import calendar
print(calendar.month(2019,1)) # 输出日历
#    输出：    January 2019
#		Mo Tu We Th Fr Sa Su
#		    1  2  3  4  5  6
#		 7  8  9 10 11 12 13
#		14 15 16 17 18 19 20
#		21 22 23 24 25 26 27
#		28 29 30 31
```