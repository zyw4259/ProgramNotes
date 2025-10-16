---
title: Python01-基础
date: 2025-03-12 22:07:18
tags:
categories:
- [Python]
---

## 1. Python 注释

注释可以用来解释 Python 代码，用于提高代码的可读性，或者在测试代码时阻止部分代码的执行。

### 1.1 创建注释

- **单行注释**
   注释以 `#` 开头，Python 将忽略该行中 `#` 右侧的内容。
   **实例：**

  ```python
  #This is a comment
  print("Hello, World!")
  ```

- **行尾注释**
   注释可以放在一行代码的末尾，Python 同样会忽略 `#` 右侧的内容。
   **实例：**

  ```python
  print("Hello, World!") #This is a comment
  ```

- **使用注释阻止代码执行**
   通过将代码前加上 `#`，可以阻止该行代码的执行。
   **实例：**

  ```python
  #print("Hello, World!")
  print("Cheers, Mate!")
  ```

### 1.2 多行注释

Python 实际上没有专门的多行注释语法，但可以有两种方式实现多行注释效果：

- **方式一：每行均以 `#` 开头**
   **实例：**

  ```python
  #This is a comment
  #written in
  #more than just one line
  print("Hello, World!")
  ```

- **方式二：使用多行字符串**
   利用三引号（`"""` 或 `'''`）书写多行字符串，且未将该字符串赋值给任何变量时，Python 会忽略该字符串。
   **实例：**

  ```python
  """
  This is a comment
  written in
  more than just one line
  """
  print("Hello, World!")
  ```

------

​			

## 2. Python 变量

变量是存放数据值的容器。Python 与其他编程语言不同，不需要声明变量类型，变量在首次赋值时自动创建，并且可以在之后随时更改其类型。

### 2.1 创建变量

**实例：**

```python
x = 5
y = "John"
print(x)
print(y)
```

变量可以随时更改类型：

```python
x = 4       # x 是 int 类型
x = "Sally" # x 现在是 str 类型
print(x)
```

字符串变量可以使用单引号或双引号声明：

```python
x = "John"
# 或者
x = 'John'
```

在后续的章节中，将介绍更多关于数据类型的信息，如 `str`（字符串）和 `int`（整数）。

### 2.2 变量名称

变量名称可以采用简短的名称（如 `x` 和 `y`）或更具描述性的名称（如 `age`、`carname`、`total_volume`）。
 **Python 变量命名规则：**

- 变量名必须以字母或下划线字符开头
- 变量名称不能以数字开头
- 变量名只能包含字母数字字符和下划线（A-z、0-9 和 _）
- 变量名称区分大小写（例如：`age`、`Age` 和 `AGE` 是三个不同的变量）

**实例：**

```python
# 合法的变量名:
myvar = "John"
my_var = "John"
_my_var = "John"
myVar = "John"
MYVAR = "John"
myvar2 = "John"

# 非法变量名:
2myvar = "John"   # 数字不能开头
my-var = "John"   # 含有非法字符 -
my var = "John"   # 含有空格
```

请记住，变量名称区分大小写。

### 2.3 向多个变量赋值

- **同时赋予不同的值：**

  ```python
  x, y, z = "Orange", "Banana", "Cherry"
  print(x)
  print(y)
  print(z)
  ```

- **同时赋予相同的值：**

  ```python
  x = y = z = "Orange"
  print(x)
  print(y)
  print(z)
  ```

### 2.4 输出变量

Python 的 `print` 语句用于输出变量。

- 连接文本和变量时，可以使用 

  ```python
  +
  ```

   字符：

  ```python
  x = "awesome"
  print("Python is " + x)
  ```

- 连接两个字符串变量：

  ```python
  x = "Python is "
  y = "awesome"
  z = x + y
  print(z)
  ```

- 数字运算

  使用 

  ```python
  +
  ```

   作为数学运算符：

  ```python
  x = 5
  y = 10
  print(x + y)
  ```

- 注意：

   如果尝试将字符串与数字相加，会出现错误：

  ```python
  x = 5
  y = "John"
  print(x + y)  # 会报错
  ```

### 2.5 全局变量

在函数外部创建的变量称为全局变量，全局变量在函数内部和外部均可使用。

**实例：** 在函数外部创建变量，并在函数内部使用它：

```python
x = "awesome"

def myfunc():
  print("Python is " + x)

myfunc()
```

如果在函数内部创建与全局变量同名的变量，则该变量为局部变量，只在函数内部使用，而全局变量保持不变。

**实例：** 在函数内部创建一个与全局变量同名的变量：

```python
x = "awesome"

def myfunc():
  x = "fantastic"
  print("Python is " + x)

myfunc()
print("Python is " + x)
```

### 2.6 global 关键词

通常，在函数内部创建的变量是局部变量，如果需要在函数内部创建或更改全局变量，则需要使用 `global` 关键词。

- **创建全局变量：**

  ```python
  def myfunc():
    global x
    x = "fantastic"
  
  myfunc()
  print("Python is " + x)
  ```

- **在函数内部更改全局变量：**

  ```python
  x = "awesome"
  
  def myfunc():
    global x
    x = "fantastic"
  
  myfunc()
  print("Python is " + x)
  ```

------

​						

## 3. Python 数据类型

数据类型是编程中的一个重要概念，变量可以存储不同类型的数据，并且不同类型可以执行不同的操作。
 Python 内置的数据类型包括：

- **文本类型：** `str`
- **数值类型：** `int`, `float`, `complex`
- **序列类型：** `list`, `tuple`, `range`
- **映射类型：** `dict`
- **集合类型：** `set`, `frozenset`
- **布尔类型：** `bool`
- **二进制类型：** `bytes`, `bytearray`, `memoryview`

### 3.1 Python 数值类型

Python 中有三种数值类型：

- `int`
- `float`
- `complex`

为变量赋值时，即会创建相应的数值类型变量。

**实例：**

```python
x = 1    # int
y = 2.8  # float
z = 1j   # complex
```

使用 `type()` 函数可以验证变量的数据类型：

```python
print(type(x))
print(type(y))
print(type(z))
```

#### 3.1.1 Int

整数（Int）表示完整的数字，没有小数，长度不限。

**实例：**

```python
# Integers:
x = 1
y = 35656222554887711
z = -3255522

print(type(x))
print(type(y))
print(type(z))
```

#### 3.1.2 Float

浮点数（Float）是包含小数的数字，可以是正数或负数。

**实例：**

```python
# 浮点:
x = 1.10
y = 1.0
z = -35.59

print(type(x))
print(type(y))
print(type(z))
```

浮点数也可以使用科学计数法表示：

```python
# 浮点:
x = 35e3
y = 12E4
z = -87.7e100

print(type(x))
print(type(y))
print(type(z))
```

#### 3.1.3 Complex

复数使用 `j` 表示虚部。

**实例：**

```python
# 复数:
x = 3+5j
y = 5j
z = -5j

print(type(x))
print(type(y))
print(type(z))
```

### 3.2 类型转换

可以使用 `int()`、`float()`、`complex()` 等方法在不同数据类型之间进行转换。

**实例：**

```python
x = 1    # int
y = 2.8  # float
z = 1j   # complex

# 从 int 转换为 float:
a = float(x)

# 从 float 转换为 int:
b = int(y)

# 从 int 转换为 complex:
c = complex(x)

print(a)
print(b)
print(c)

print(type(a))
print(type(b))
print(type(c))
```

> **注意：** 您无法将复数转换为其他数字类型。

### 3.3 随机数

Python 没有内置的 `random()` 函数来生成随机数，但提供了一个名为 `random` 的内置模块，可用于生成随机数。

**实例：**

```python
import random

print(random.randrange(1, 10))
```

### 3.4 Python 类型转换（指定变量类型）

有时您可能需要为变量指定特定的数据类型，这可以通过类型转换完成。Python 是一门面向对象的语言，使用类来定义数据类型，因此可以使用构造函数进行转换。

- **int()** - 用整数字面量、浮点字面量（会进行下舍入），或者用表示完整数字的字符串字面量构造整数。
- **float()** - 用整数字面量、浮点字面量，或字符串字面量构造浮点数。
- **str()** - 用各种数据类型构造字符串，包括字符串、整数字面量和浮点字面量。

**实例：**

- **整数转换：**

  ```python
  x = int(1)   # x 将是 1
  y = int(2.8) # y 将是 2
  z = int("3") # z 将是 3
  ```

- **浮点数转换：**

  ```python
  x = float(1)     # x 将是 1.0
  y = float(2.8)   # y 将是 2.8
  z = float("3")   # z 将是 3.0
  w = float("4.2") # w 将是 4.2
  ```

- **字符串转换：**

  ```python
  x = str("s1") # x 将是 's1'
  y = str(2)    # y 将是 '2'
  z = str(3.0)  # z 将是 '3.0'
  ```

------

​						

## 4. Python 布尔值

布尔值表示两种状态：`True` 或 `False`。
 在编程中，布尔值通常用于判断表达式的真假，Python 计算任何表达式后都将返回 `True` 或 `False`。

**实例：**

```python
print(10 > 9)
print(10 == 9)
print(10 < 9)
```

------

​				

## 5. Python 运算符

运算符用于对变量和值执行各种操作，Python 中的运算符可以分为以下几组：

- 算术运算符
- 赋值运算符
- 比较运算符
- 逻辑运算符
- 身份运算符
- 成员运算符
- 位运算符

### 5.1 Python 算术运算符

算术运算符用于对数值进行常见的数学运算：

| 运算符 | 名称   | 实例   | 试一试 |
| ------ | ------ | ------ | ------ |
| +      | 加法   | x + y  |        |
| -      | 减法   | x - y  |        |
| *      | 乘法   | x * y  |        |
| /      | 除法   | x / y  |        |
| %      | 取模   | x % y  |        |
| **     | 幂     | x ** y |        |
| //     | 地板除 | x // y |        |

### 5.2 Python 赋值运算符

赋值运算符用于给变量赋值：

| 运算符 | 实例    | 等同于     | 试一试 |
| ------ | ------- | ---------- | ------ |
| =      | x = 5   | x = 5      |        |
| +=     | x += 3  | x = x + 3  |        |
| -=     | x -= 3  | x = x - 3  |        |
| *=     | x *= 3  | x = x * 3  |        |
| /=     | x /= 3  | x = x / 3  |        |
| %=     | x %= 3  | x = x % 3  |        |
| //=    | x //= 3 | x = x // 3 |        |
| **=    | x **= 3 | x = x ** 3 |        |
| &=     | x &= 3  | x = x & 3  |        |
| \|=    | x \|= 3 | x = x \| 3 |        |
| ^=     | x ^= 3  | x = x ^ 3  |        |
| >>=    | x >>= 3 | x = x >> 3 |        |
| <<=    | x <<= 3 | x = x << 3 |        |

### 5.3 Python 比较运算符

比较运算符用于比较两个值：

| 运算符 | 名称       | 实例   | 试一试 |
| ------ | ---------- | ------ | ------ |
| ==     | 等于       | x == y |        |
| !=     | 不等于     | x != y |        |
| >      | 大于       | x > y  |        |
| <      | 小于       | x < y  |        |
| >=     | 大于或等于 | x >= y |        |
| <=     | 小于或等于 | x <= y |        |

### 5.4 Python 逻辑运算符

逻辑运算符用于组合条件语句：

| 运算符 | 描述                                    | 实例                  | 试一试 |
| ------ | --------------------------------------- | --------------------- | ------ |
| and    | 如果两个语句都为真，则返回 True         | x < 5 and x < 10      |        |
| or     | 如果其中一个语句为真，则返回 True       | x < 5 or x < 4        |        |
| not    | 反转结果，如果结果为 True，则返回 False | not(x < 5 and x < 10) |        |

### 5.5 Python 身份运算符

身份运算符用于比较两个对象是否为同一对象（即内存位置是否相同），而不是简单地比较它们的值。

| 运算符 | 描述                                    | 实例       | 试一试 |
| ------ | --------------------------------------- | ---------- | ------ |
| is     | 如果两个变量是同一个对象，则返回 True   | x is y     |        |
| is not | 如果两个变量不是同一个对象，则返回 True | x is not y |        |

### 5.6 Python 成员运算符

成员运算符用于测试序列中是否存在具有指定值的元素：

| 运算符 | 描述                                          | 实例       | 试一试 |
| ------ | --------------------------------------------- | ---------- | ------ |
| in     | 如果对象中存在具有指定值的序列，则返回 True   | x in y     |        |
| not in | 如果对象中不存在具有指定值的序列，则返回 True | x not in y |        |

### 5.7 Python 位运算符

位运算符用于按位（即二进制）比较数字：

| 运算符 | 名称                 | 描述                                                   |
| ------ | -------------------- | ------------------------------------------------------ |
| &      | AND                  | 如果两个位均为 1，则将每个位设为 1                     |
| \|     | OR                   | 如果两位中的一位为 1，则将每个位设为 1                 |
| ^      | XOR                  | 如果两个位中只有一位为 1，则将每个位设为 1             |
| ~      | NOT                  | 反转所有位                                             |
| <<     | Zero fill left shift | 通过从右侧推入零来向左移动，推出最左边的位             |
| >>     | Signed right shift   | 通过从左侧推入最左边的位的副本向右移动，推出最右边的位 |

​			

​				

## 6. Python 列表

列表是一种有序且可更改的集合，允许重复的成员。在 Python 中，列表用方括号 `[]` 表示。

### 6.1 列表简介

- **定义：**
   列表是一种有序、可变的集合，用于存储一系列项目。

- 示例：

  ```python
  thislist = ["apple", "banana", "cherry"]
  print(thislist)
  ```

### 6.2 访问列表项目

- 通过索引访问：

  列表索引从 0 开始。

  ```python
  thislist = ["apple", "banana", "cherry"]
  print(thislist[1])  # 打印第二项
  ```

- 使用负索引：

  -1 表示最后一项，-2 表示倒数第二项，依此类推。

  ```python
  thislist = ["apple", "banana", "cherry"]
  print(thislist[-1])  # 打印最后一项
  ```

### 6.3 列表切片（索引范围）

- 指定范围：

  返回从起始索引（包含）到结束索引（不包含）的新列表。

  ```python
  thislist = ["apple", "banana", "cherry", "orange", "kiwi", "melon", "mango"]
  print(thislist[2:5])  # 返回第三到第五项
  ```

- 省略起始值：

  切片从列表开头开始。

  ```python
  thislist = ["apple", "banana", "cherry", "orange", "kiwi", "melon", "mango"]
  print(thislist[:4])  # 返回前 4 项，包含 "orange"
  ```

- 省略结束值：

  切片一直延续到列表末尾。

  ```python
  thislist = ["apple", "banana", "cherry", "orange", "kiwi", "melon", "mango"]
  print(thislist[2:])  # 返回从第三项到末尾的所有项目
  ```

- 负索引范围：

  使用负索引来指定切片范围。

  ```python
  thislist = ["apple", "banana", "cherry", "orange", "kiwi", "melon", "mango"]
  print(thislist[-4:-1])  # 返回从倒数第四项（包含）到倒数第一项（不包含）
  ```

### 6.4 修改列表

- 更改项目值：

  通过索引修改指定位置的值。

  ```python
  thislist = ["apple", "banana", "cherry"]
  thislist[1] = "blackcurrant"
  print(thislist)
  ```

### 6.5 遍历列表

- 使用 for 循环：

  ```python
  thislist = ["apple", "banana", "cherry"]
  for x in thislist:
      print(x)
  ```

  （关于 for 循环的详细内容，请参见后面的章节）

### 6.6 检查项目是否存在

- 使用 in 关键字：

  ```python
  thislist = ["apple", "banana", "cherry"]
  if "apple" in thislist:
      print("Yes, 'apple' is in the fruits list")
  ```

### 6.7 列表长度

- 使用 len() 函数：

  ```python
  thislist = ["apple", "banana", "cherry"]
  print(len(thislist))
  ```

### 6.8 添加列表项目

- 追加项目到末尾（append）：

  ```python
  thislist = ["apple", "banana", "cherry"]
  thislist.append("orange")
  print(thislist)
  ```

- 在指定索引处插入项目（insert）：

  ```python
  thislist = ["apple", "banana", "cherry"]
  thislist.insert(1, "orange")
  print(thislist)
  ```

### 6.9 删除列表项目

- 使用 remove() 删除指定项目：

  ```python
  thislist = ["apple", "banana", "cherry"]
  thislist.remove("banana")
  print(thislist)
  ```

- 使用 pop() 删除指定索引（默认删除最后一项）：

  ```python
  thislist = ["apple", "banana", "cherry"]
  thislist.pop()
  print(thislist)
  ```

- 使用 del 关键字删除指定索引或整个列表：

  ```python
  thislist = ["apple", "banana", "cherry"]
  del thislist[0]
  print(thislist)
  
  # 删除整个列表:
  del thislist
  # print(thislist)  # 访问已删除的列表将引发错误
  ```

- 使用 clear() 方法清空列表：

  ```python
  thislist = ["apple", "banana", "cherry"]
  thislist.clear()
  print(thislist)
  ```

### 6.10 复制列表

- 注意直接赋值仅创建引用。

  使用 copy() 方法或 list() 构造函数来复制列表。

  ```python
  # 使用 copy() 方法：
  thislist = ["apple", "banana", "cherry"]
  mylist = thislist.copy()
  print(mylist)
  
  # 使用 list() 构造函数：
  mylist = list(thislist)
  print(mylist)
  ```

### 6.11 合并两个列表

- 使用 + 运算符：

  ```python
  list1 = ["a", "b", "c"]
  list2 = [1, 2, 3]
  list3 = list1 + list2
  print(list3)
  ```

- 使用 for 循环逐个追加：

  ```python
  list1 = ["a", "b", "c"]
  list2 = [1, 2, 3]
  for x in list2:
      list1.append(x)
  print(list1)
  ```

- 使用 extend() 方法：

  ```python
  list1 = ["a", "b", "c"]
  list2 = [1, 2, 3]
  list1.extend(list2)
  print(list1)
  ```

### 6.12 使用 list() 构造函数

- 创建新列表：

  ```python
  thislist = list(("apple", "banana", "cherry"))  # 注意双圆括号
  print(thislist)
  ```

### 6.13 列表常用方法

| 方法      | 描述                                       |
| --------- | ------------------------------------------ |
| append()  | 在列表的末尾添加一个元素                   |
| clear()   | 删除列表中的所有元素                       |
| copy()    | 返回列表的副本                             |
| count()   | 返回指定值在列表中出现的次数               |
| extend()  | 将一个可迭代对象中的所有元素添加到列表末尾 |
| index()   | 返回指定值的第一个匹配项的索引             |
| insert()  | 在指定位置插入元素                         |
| pop()     | 删除并返回指定位置的元素（默认最后一个）   |
| remove()  | 删除列表中第一个匹配的指定值               |
| reverse() | 颠倒列表中元素的顺序                       |
| sort()    | 对列表中的元素进行排序                     |

------

​			

## 7. Python 元组

元组是有序且不可更改的集合，使用圆括号 `()` 表示。元组允许重复的成员。

### 7.1 元组简介

- **定义：**
   元组是有序的但不可变（不可修改）的集合。

- 示例：

  ```python
  thistuple = ("apple", "banana", "cherry")
  print(thistuple)
  ```

### 7.2 访问元组项目

- 通过索引访问：

  ```python
  thistuple = ("apple", "banana", "cherry")
  print(thistuple[1])  # 打印第二项
  ```

- 使用负索引：

  ```python
  thistuple = ("apple", "banana", "cherry")
  print(thistuple[-1])  # 打印最后一项
  ```

### 7.3 元组切片

- 指定索引范围：

  ```python
  thistuple = ("apple", "banana", "cherry", "orange", "kiwi", "melon", "mango")
  print(thistuple[2:5])  # 返回第三到第五项（索引2到4）
  ```

- 使用负索引范围：

  ```python
  thistuple = ("apple", "banana", "cherry", "orange", "kiwi", "melon", "mango")
  print(thistuple[-4:-1])  # 返回倒数第四项到倒数第二项
  ```

### 7.4 修改元组

- 注意：

  元组创建后不能修改。如果需要修改，可以将元组转换为列表，修改后再转换回元组。

  ```python
  x = ("apple", "banana", "cherry")
  y = list(x)
  y[1] = "kiwi"
  x = tuple(y)
  print(x)
  ```

### 7.5 遍历元组

- 使用 for 循环：

  ```python
  thistuple = ("apple", "banana", "cherry")
  for x in thistuple:
      print(x)
  ```

### 7.6 检查元组中是否存在项目

- 使用 in 关键字：

  ```python
  thistuple = ("apple", "banana", "cherry")
  if "apple" in thistuple:
      print("Yes, 'apple' is in the fruits tuple")
  ```

### 7.7 元组长度

- 使用 len() 函数：

  ```python
  thistuple = ("apple", "banana", "cherry")
  print(len(thistuple))
  ```

### 7.8 添加项目

- 说明：

  元组创建后不可添加项目。如果尝试修改会报错：

  ```python
  thistuple = ("apple", "banana", "cherry")
  thistuple[3] = "orange"  # 这将引发错误
  print(thistuple)
  ```

### 7.9 创建单项元组

- 注意单项元组需要在元素后加逗号：

  ```python
  thistuple = ("apple",)
  print(type(thistuple))
  
  # 以下不是元组
  thistuple = ("apple")
  print(type(thistuple))
  ```

### 7.10 删除元组

- 无法删除元组中的单个项目，但可以删除整个元组：

  ```python
  thistuple = ("apple", "banana", "cherry")
  del thistuple
  # print(thistuple)  # 这将引发错误，因为元组已被删除
  ```

### 7.11 合并元组

- 使用 + 运算符连接元组：

  ```python
  tuple1 = ("a", "b", "c")
  tuple2 = (1, 2, 3)
  tuple3 = tuple1 + tuple2
  print(tuple3)
  ```

### 7.12 使用 tuple() 构造函数

- 创建元组：

  ```python
  thistuple = tuple(("apple", "banana", "cherry"))  # 注意双圆括号
  print(thistuple)
  ```

### 7.13 元组内建方法

- **count()：** 返回指定值在元组中出现的次数
- **index()：** 返回指定值第一次出现的位置

------

​	

## 8. Python 集合

集合是一种无序、无索引的集合，不能包含重复的成员。集合使用花括号 `{}` 表示。

### 8.1 集合简介

- **定义：**
   集合是无序且不允许重复的元素集合。

- 示例：

  ```python
  thisset = {"apple", "banana", "cherry"}
  print(thisset)
  ```

  注：由于集合无序，输出项目的顺序可能与定义时不同。

### 8.2 访问集合项目

- 说明：

  不能通过索引访问集合中的元素，但可以使用 for 循环或 in 关键字检查是否存在某个值。

  ```python
  thisset = {"apple", "banana", "cherry"}
  for x in thisset:
      print(x)
  
  print("banana" in thisset)
  ```

### 8.3 更改集合

- 添加单个项目：

  使用 add() 方法。

  ```python
  thisset = {"apple", "banana", "cherry"}
  thisset.add("orange")
  print(thisset)
  ```

- 添加多个项目：

  使用 update() 方法，可以添加列表、元组或其他可迭代对象中的多个元素。

  ```python
  thisset = {"apple", "banana", "cherry"}
  thisset.update(["orange", "mango", "grapes"])
  print(thisset)
  ```

### 8.4 集合长度

- 使用 len() 函数：

  ```python
  thisset = {"apple", "banana", "cherry"}
  print(len(thisset))
  ```

### 8.5 删除集合项目

- remove() 方法：

  删除指定项目，如果项目不存在则引发错误。

  ```python
  thisset = {"apple", "banana", "cherry"}
  thisset.remove("banana")
  print(thisset)
  ```

- discard() 方法：

  删除指定项目，如果项目不存在则不报错。

  ```python
  thisset = {"apple", "banana", "cherry"}
  thisset.discard("banana")
  print(thisset)
  ```

- pop() 方法：

  随机删除一个项目（由于集合无序，不能确定删除哪一项），并返回该项目。

  ```python
  thisset = {"apple", "banana", "cherry"}
  x = thisset.pop()
  print(x)
  print(thisset)
  ```

- clear() 方法：

  清空集合。

  ```python
  thisset = {"apple", "banana", "cherry"}
  thisset.clear()
  print(thisset)
  ```

- del 关键字：

  删除整个集合。

  ```python
  thisset = {"apple", "banana", "cherry"}
  del thisset
  # print(thisset)  # 此处将引发错误，因为集合已被删除
  ```

### 8.6 合并两个集合

- 使用 union() 方法：

  返回一个新集合，包含两个集合中的所有不重复项目。

  ```python
  set1 = {"a", "b", "c"}
  set2 = {1, 2, 3}
  set3 = set1.union(set2)
  print(set3)
  ```

- 使用 update() 方法：

  将一个集合中的所有项目添加到另一个集合中。

  ```python
  set1 = {"a", "b", "c"}
  set2 = {1, 2, 3}
  set1.update(set2)
  print(set1)
  ```

  注：union() 和 update() 都会自动排除重复项。

### 8.7 使用 set() 构造函数

- 创建集合：

  ```python
  thisset = set(("apple", "banana", "cherry"))  # 注意双圆括号
  print(thisset)
  ```

### 8.8 集合常用方法

| 方法                          | 描述                                           |
| ----------------------------- | ---------------------------------------------- |
| add()                         | 向集合中添加元素                               |
| clear()                       | 删除集合中的所有元素                           |
| copy()                        | 返回集合的副本                                 |
| difference()                  | 返回两个或更多集合之间差异的集合               |
| difference_update()           | 删除集合中在另一个指定集合也存在的项目         |
| discard()                     | 删除指定项目                                   |
| intersection()                | 返回多个集合交集的集合                         |
| intersection_update()         | 删除集合中不在其他指定集合中的项目             |
| isdisjoint()                  | 检查两个集合是否没有交集                       |
| issubset()                    | 检查一个集合是否为另一个集合的子集             |
| issuperset()                  | 检查一个集合是否包含另一个集合                 |
| pop()                         | 随机删除并返回集合中的一个元素                 |
| remove()                      | 删除指定项目                                   |
| symmetric_difference()        | 返回两个集合的对称差集（只保留非共同项）的集合 |
| symmetric_difference_update() | 更新集合为两个集合的对称差集                   |
| union()                       | 返回包含所有集合项目的集合（并集）             |
| update()                      | 用其他集合的项目更新集合                       |

------

​	

## 9. Python 字典

字典是一种无序、可变且带索引的集合，以键-值对的形式存储数据，使用花括号 `{}` 表示。

### 9.1 字典简介

- **定义：**
   字典由键和值组成，每个键必须是唯一的。

- 示例：

  ```python
  thisdict = {
      "brand": "Ford",
      "model": "Mustang",
      "year": 1964
  }
  print(thisdict)
  ```

### 9.2 访问字典项目

- 通过键访问：

  ```python
  x = thisdict["model"]
  ```

- 使用 get() 方法：

  ```python
  x = thisdict.get("model")
  ```

### 9.3 修改字典

- 更改值：

  ```python
  thisdict = {
      "brand": "Ford",
      "model": "Mustang",
      "year": 1964
  }
  thisdict["year"] = 2018
  ```

### 9.4 遍历字典

- 遍历键：

  ```python
  for x in thisdict:
      print(x)
  ```

- 遍历值（通过键）：

  ```python
  for x in thisdict:
      print(thisdict[x])
  ```

- 直接遍历值：

  ```python
  for x in thisdict.values():
      print(x)
  ```

- 同时遍历键和值：

  ```python
  for x, y in thisdict.items():
      print(x, y)
  ```

### 9.5 检查键是否存在

- 使用 in 关键字：

  ```python
  if "model" in thisdict:
      print("Yes, 'model' is one of the keys in the thisdict dictionary")
  ```

### 9.6 字典长度

- 使用 len() 函数：

  ```python
  print(len(thisdict))
  ```

### 9.7 添加字典项目

- 通过新键赋值添加：

  ```python
  thisdict = {
      "brand": "Ford",
      "model": "Mustang",
      "year": 1964
  }
  thisdict["color"] = "red"
  print(thisdict)
  ```

### 9.8 删除字典项目

- 使用 pop() 方法：

  ```python
  thisdict = {
      "brand": "Ford",
      "model": "Mustang",
      "year": 1964
  }
  thisdict.pop("model")
  print(thisdict)
  ```

- 使用 popitem() 方法：

  删除最后插入的键值对（在 3.7 之前版本中为随机删除）。

  ```python
  thisdict = {
      "brand": "Ford",
      "model": "Mustang",
      "year": 1964
  }
  thisdict.popitem()
  print(thisdict)
  ```

- 使用 del 关键字删除指定键或整个字典：

  ```python
  thisdict = {
      "brand": "Ford",
      "model": "Mustang",
      "year": 1964
  }
  del thisdict["model"]
  print(thisdict)
  
  # 删除整个字典:
  del thisdict
  # print(thisdict)  # 访问已删除的字典将引发错误
  ```

- 使用 clear() 方法清空字典：

  ```python
  thisdict = {
      "brand": "Ford",
      "model": "Mustang",
      "year": 1964
  }
  thisdict.clear()
  print(thisdict)
  ```

### 9.9 复制字典

- 注意直接赋值只会创建引用。

  使用 copy() 方法或 dict() 构造函数复制字典。

  ```python
  # 使用 copy() 方法：
  thisdict = {
      "brand": "Ford",
      "model": "Mustang",
      "year": 1964
  }
  mydict = thisdict.copy()
  print(mydict)
  
  # 使用 dict() 构造函数：
  mydict = dict(thisdict)
  print(mydict)
  ```

### 9.10 嵌套字典

- 字典中包含其他字典：

  ```python
  myfamily = {
      "child1": {
          "name": "Emil",
          "year": 2004
      },
      "child2": {
          "name": "Tobias",
          "year": 2007
      },
      "child3": {
          "name": "Linus",
          "year": 2011
      }
  }
  ```

- 或先定义单个字典，再将其嵌套：

  ```python
  child1 = {"name": "Emil", "year": 2004}
  child2 = {"name": "Tobias", "year": 2007}
  child3 = {"name": "Linus", "year": 2011}
  
  myfamily = {
      "child1": child1,
      "child2": child2,
      "child3": child3
  }
  ```

### 9.11 使用 dict() 构造函数

- 通过关键字参数创建字典：

  ```python
  thisdict = dict(brand="Ford", model="Mustang", year=1964)
  print(thisdict)
  ```

### 9.12 字典常用方法

| 方法         | 描述                                         |
| ------------ | -------------------------------------------- |
| clear()      | 删除字典中的所有元素                         |
| copy()       | 返回字典的副本                               |
| fromkeys()   | 返回一个具有指定键和值的新字典               |
| get()        | 返回指定键的值                               |
| items()      | 返回包含每个键值对的元组列表                 |
| keys()       | 返回字典中所有键的列表                       |
| pop()        | 删除具有指定键的元素                         |
| popitem()    | 删除最后插入的键值对                         |
| setdefault() | 返回指定键的值；如果键不存在，则将其插入字典 |
| update()     | 使用指定键值对更新字典                       |
| values()     | 返回字典中所有值的列表                       |

------

​		

## 10. Python If ... Else 语句

Python 使用条件语句来控制程序流程，支持常见的比较操作符及逻辑运算。

### 10.1 基本条件

常用比较操作符：

- 等于：`a == b`
- 不等于：`a != b`
- 小于：`a < b`
- 小于等于：`a <= b`
- 大于：`a > b`
- 大于等于：`a >= b`

### 10.2 if 语句

- 语法：

  ```python
  a = 33
  b = 200
  if b > a:
      print("b is greater than a")
  ```

  注意缩进在 Python 中非常重要，否则会引发错误。

### 10.3 elif 语句

- 用于多个条件判断：

  ```python
  a = 33
  b = 33
  if b > a:
      print("b is greater than a")
  elif a == b:
      print("a and b are equal")
  ```

### 10.4 else 语句

- 捕获未被前面条件捕获的情况：

  ```python
  a = 200
  b = 33
  if b > a:
      print("b is greater than a")
  elif a == b:
      print("a and b are equal")
  else:
      print("a is greater than b")
  ```

### 10.5 单行 if 语句

- 如果只有一条语句要执行，可以写在一行：

  ```python
  if a > b: print("a is greater than b")
  ```

### 10.6 单行 if ... else 语句（三元运算符）

- 示例：

  ```python
  a = 2
  b = 330
  print("A") if a > b else print("B")
  ```

- 多个条件：

  ```python
  a = 330
  b = 330
  print("A") if a > b else print("=") if a == b else print("B")
  ```

### 10.7 逻辑运算

- and 关键字：

  ```python
  a = 200
  b = 33
  c = 500
  if a > b and c > a:
      print("Both conditions are True")
  ```

- or 关键字：

  ```python
  a = 200
  b = 33
  c = 500
  if a > b or a > c:
      print("At least one of the conditions is True")
  ```

### 10.8 嵌套 if

- 在 if 语句内部嵌套 if：

  ```python
  x = 41
  if x > 10:
      print("Above ten,")
      if x > 20:
          print("and also above 20!")
      else:
          print("but not above 20.")
  ```

### 10.9 pass 语句

- 占位符，避免空的 if 语句引发错误：

  ```python
  a = 33
  b = 200
  if b > a:
      pass
  ```

------

​	

​				

## 11. Python While 循环

while 循环用于在给定条件为真时重复执行代码块。

### 11.1 基本语法

- 示例：

  ```python
  i = 1
  while i < 6:
      print(i)
      i += 1  # 记得更新计数变量，避免无限循环
  ```

### 11.2 break 语句

- 在满足特定条件时退出循环：

  ```python
  i = 1
  while i < 6:
      print(i)
      if i == 3:
          break
      i += 1
  ```

### 11.3 continue 语句

- 跳过当前循环的剩余部分，进入下一次迭代：

  ```python
  i = 0
  while i < 6:
      i += 1
      if i == 3:
          continue
      print(i)
  ```

### 11.4 while 循环的 else 子句

- 当循环条件不再成立时执行 else 中的代码：

  ```python
  i = 1
  while i < 6:
      print(i)
      i += 1
  else:
      print("i is no longer less than 6")
  ```

------

​			

## 12. Python For 循环

for 循环用于遍历序列（如列表、元组、字典、集合或字符串）。

### 12.1 基本语法

- 遍历列表：

  ```python
  fruits = ["apple", "banana", "cherry"]
  for x in fruits:
      print(x)
  ```

### 12.2 遍历字符串

- 示例：

  ```python
  for x in "banana":
      print(x)
  ```

### 12.3 break 语句

- 在循环中提前退出：

  ```python
  fruits = ["apple", "banana", "cherry"]
  for x in fruits:
      print(x)
      if x == "banana":
          break
  ```

- 或先判断再打印：

  ```python
  fruits = ["apple", "banana", "cherry"]
  for x in fruits:
      if x == "banana":
          break
      print(x)
  ```

### 12.4 continue 语句

- 跳过当前迭代：

  ```python
  fruits = ["apple", "banana", "cherry"]
  for x in fruits:
      if x == "banana":
          continue
      print(x)
  ```

### 12.5 range() 函数

- 生成数字序列：

  ```python
  for x in range(6):
      print(x)  # 输出 0 到 5
  ```

- 指定起始值：

  ```python
  for x in range(2, 6):
      print(x)  # 输出 2 到 5
  ```

- 指定步长：

  ```python
  for x in range(2, 30, 3):
      print(x)  # 每次增加 3
  ```

### 12.6 for 循环中的 else 子句

- 当循环正常结束（非被 break 终止）时执行：

  ```python
  for x in range(6):
      print(x)
  else:
      print("Finally finished!")
  ```

### 12.7 嵌套循环

- 在循环内部嵌套循环：

  ```
  adj = ["red", "big", "tasty"]
  fruits = ["apple", "banana", "cherry"]
  for x in adj:
      for y in fruits:
          print(x, y)
  ```

### 12.8 pass 语句

- for 循环中占位，避免空循环引发错误：

  ```
  for x in [0, 1, 2]:
      pass
  ```

  ​							

## 13. Python 函数

函数是一段只有在调用时才运行的代码块。函数可以接收数据（称为参数），并返回数据作为结果。

### 13.1 创建函数

- 定义函数

  使用 

  ```
  def
  ```

   关键字来定义函数：

  ```
  def my_function():
      print("Hello from a function")
  ```

### 13.2 调用函数

- 调用方式

  直接使用函数名后加括号即可调用函数：

  ```
  def my_function():
      print("Hello from a function")
  
  my_function()
  ```

### 13.3 参数

- 传递参数

  参数在函数名后括号内指定，可传递任意数量，用逗号分隔：

  ```
  def my_function(fname):
      print(fname + " Refsnes")
  
  my_function("Emil")
  my_function("Tobias")
  my_function("Linus")
  ```

- 形式参数与实际参数

  - *形式参数（parameter）：* 在函数定义时括号中列出的变量。
  - *实际参数（argument）：* 调用函数时传入的值。

### 13.4 参数个数

- 必须传递正确数量的参数

  如果函数需要 2 个参数，则调用时必须传 2 个，否则会出错：

  ```
  def my_function(fname, lname):
      print(fname + " " + lname)
  
  my_function("Emil", "Refsnes")  # 正确调用
  # my_function("Emil")          # 错误示例
  ```

### 13.5 任意参数 *args

- 不确定传入参数数量时

  在参数名前加星号 

  ```
  *
  ```

  ，函数会将参数作为元组接收：

  ```
  def my_function(*kids):
      print("The youngest child is " + kids[2])
  
  my_function("Emil", "Tobias", "Linus")
  ```

  在 Python 文档中，任意参数通常缩写为 *args。

### 13.6 关键字参数

- 使用 key = value 的语法传参

  参数顺序无关紧要：

  ```
  def my_function(child3, child2, child1):
      print("The youngest child is " + child3)
  
  my_function(child1="Emil", child2="Tobias", child3="Linus")
  ```

  关键字参数在文档中通常缩写为 kwargs。

### 13.7 任意关键字参数 **kwargs

- 当不确定关键字参数数量时

  在参数名前加两个星号 

  ```
  **
  ```

  ，函数会接收一个字典：

  ```
  def my_function(**kid):
      print("His last name is " + kid["lname"])
  
  my_function(fname="Tobias", lname="Refsnes")
  ```

  任意关键字参数通常缩写为 **kwargs。

### 13.8 默认参数值

- 为参数指定默认值

  如果调用时未传入参数，则使用默认值：

  ```
  def my_function(country="Norway"):
      print("I am from " + country)
  
  my_function("Sweden")
  my_function("India")
  my_function()
  my_function("Brazil")
  ```

### 13.9 以 List 传参

- 传入任何数据类型

  参数可以是列表，在函数内部仍然是列表：

  ```
  def my_function(food):
      for x in food:
          print(x)
  
  fruits = ["apple", "banana", "cherry"]
  my_function(fruits)
  ```

### 13.10 返回值

- 使用 return 返回结果

  函数可以通过 

  ```
  return
  ```

   语句返回数据：

  ```
  def my_function(x):
      return 5 * x
  
  print(my_function(3))
  print(my_function(5))
  print(my_function(9))
  ```

### 13.11 pass 语句

- 空函数占位

  如果函数体为空，使用 

  ```
  pass
  ```

   避免错误：

  ```
  def myfunction():
      pass
  ```

### 13.12 递归

- 函数调用自身

  递归是一种常见的数学与编程概念，使用递归时应注意设置终止条件：

  ```
  def tri_recursion(k):
      if k > 0:
          result = k + tri_recursion(k - 1)
          print(result)
      else:
          result = 0
      return result
  
  print("\n\nRecursion Example Results")
  tri_recursion(6)
  ```

------

​				

## 14. Python Lambda 函数

Lambda 函数是一种小型匿名函数，可接受任意数量的参数，但只能有一个表达式。

### 14.1 语法

- **基本格式：**
   `lambda arguments : expression`

### 14.2 示例

- 单个参数：

  ```
  x = lambda a: a + 10
  print(x(5))
  ```

- 多个参数：

  ```
  x = lambda a, b: a * b
  print(x(5, 6))
  
  x = lambda a, b, c: a + b + c
  print(x(5, 6, 2))
  ```

### 14.3 为何使用 Lambda 函数

- 作为匿名函数传递给其他函数

  例如，根据传入参数生成一个函数：

  ```
  def myfunc(n):
      return lambda a: a * n
  
  mydoubler = myfunc(2)
  print(mydoubler(11))
  
  mytripler = myfunc(3)
  print(mytripler(11))
  ```

------

​				

## 15. Python 数组

请注意，Python 没有内置的数组类型，但通常使用列表来代替数组。

### 15.1 数组简介

- **定义：**
   数组用于在单个变量中存储多个值。

- 示例：

  ```
  python
  
  
  复制
  cars = ["Ford", "Volvo", "BMW"]
  ```

### 15.2 数组的概念

- 传统写法（多个变量）：

  ```
  car1 = "Ford"
  car2 = "Volvo"
  car3 = "BMW"
  ```

- 使用数组：

  使用数组可以在单个名称下保存多个值，并通过索引访问：

  ```
  python
  
  
  复制
  x = cars[0]
  ```

### 15.3 访问和修改数组元素

- 访问第一个元素：

  ```
  python
  
  
  复制
  x = cars[0]
  ```

- 修改第一个元素：

  ```
  python
  
  
  复制
  cars[0] = "Toyota"
  ```

### 15.4 数组长度

- 使用 len() 函数：

  ```
  python
  
  
  复制
  x = len(cars)
  ```

### 15.5 遍历数组元素

- 使用 for in 循环：

  ```
  for x in cars:
      print(x)
  ```

### 15.6 添加数组元素

- 使用 append() 方法：

  ```
  python
  
  
  复制
  cars.append("Honda")
  ```

### 15.7 删除数组元素

- 使用 pop() 方法删除指定索引的元素：

  ```
  python
  
  
  复制
  cars.pop(1)
  ```

- 使用 remove() 方法删除指定值的元素：

  ```
  python
  
  
  复制
  cars.remove("Volvo")
  ```

  注：remove() 方法只删除首次出现的指定值。

### 15.8 数组常用方法

| 方法      | 描述                                       |
| --------- | ------------------------------------------ |
| append()  | 在列表的末尾添加一个元素                   |
| clear()   | 删除列表中的所有元素                       |
| copy()    | 返回列表的副本                             |
| count()   | 返回指定值在列表中出现的次数               |
| extend()  | 将其他可迭代对象中的所有元素添加到列表末尾 |
| index()   | 返回指定值首次出现的索引                   |
| insert()  | 在指定位置插入元素                         |
| pop()     | 删除并返回指定索引的元素                   |
| remove()  | 删除列表中第一个匹配的指定值               |
| reverse() | 颠倒列表中元素的顺序                       |
| sort()    | 对列表中的元素进行排序                     |

------

​				

## 16. Python 类和对象

Python 是一种面向对象的编程语言，几乎所有内容都是对象，都拥有属性和方法。类是用于创建对象的蓝图或构造函数。

### 16.1 创建类

- 定义类：

  ```
  class MyClass:
      x = 5
  ```

### 16.2 创建对象

- 使用类创建对象，并访问属性：

  ```
  p1 = MyClass()
  print(p1.x)
  ```

### 16.3 **init**() 函数

- 作用：

  每次创建对象时自动调用，用于初始化对象属性。

  ```
  class Person:
      def __init__(self, name, age):
          self.name = name
          self.age = age
  
  p1 = Person("John", 36)
  print(p1.name)
  print(p1.age)
  ```

### 16.4 对象方法

- 在类中定义方法，使用 self 参数引用当前对象：

  ```
  class Person:
      def __init__(self, name, age):
          self.name = name
          self.age = age
  
      def myfunc(self):
          print("Hello my name is " + self.name)
  
  p1 = Person("John", 36)
  p1.myfunc()
  ```

### 16.5 self 参数

- self 表示当前对象，可用其他名称，但约定使用 self：

  ```
  class Person:
      def __init__(mysillyobject, name, age):
          mysillyobject.name = name
          mysillyobject.age = age
  
      def myfunc(abc):
          print("Hello my name is " + abc.name)
  
  p1 = Person("John", 36)
  p1.myfunc()
  ```

### 16.6 修改和删除对象属性

- 修改属性：

  ```
  python
  
  
  复制
  p1.age = 40
  ```

- 删除属性：

  ```
  python
  
  
  复制
  del p1.age
  ```

- 删除对象：

  ```
  python
  
  
  复制
  del p1
  ```

### 16.7 空类定义

- 如果类体为空，使用 pass 语句：

  ```
  class Person:
      pass
  ```

------

​					

## 17. Python 继承

继承允许定义子类，子类继承父类的所有属性和方法。

### 17.1 创建父类

- 示例：

  ```
  class Person:
      def __init__(self, fname, lname):
          self.firstname = fname
          self.lastname = lname
  
      def printname(self):
          print(self.firstname, self.lastname)
  
  x = Person("John", "Doe")
  x.printname()
  ```

### 17.2 创建子类

- 定义子类并继承父类：

  ```
  class Student(Person):
      pass
  
  x = Student("Mike", "Olsen")
  x.printname()
  ```

### 17.3 添加 **init**() 到子类

- 注意：

   添加子类的 

  init

  () 会覆盖父类的 

  init

  ()，需调用父类 

  init

  () 保留继承：

  ```
  class Student(Person):
      def __init__(self, fname, lname):
          Person.__init__(self, fname, lname)
  ```

### 17.4 使用 super() 函数

- 更简洁的方式调用父类方法：

  ```
  class Student(Person):
      def __init__(self, fname, lname):
          super().__init__(fname, lname)
  ```

### 17.5 添加额外属性

- 在子类 **init**() 中添加新属性：

  ```
  class Student(Person):
      def __init__(self, fname, lname, year):
          super().__init__(fname, lname)
          self.graduationyear = year
  
  x = Student("Mike", "Olsen", 2019)
  ```

### 17.6 添加子类方法

- 示例：

  ```
  class Student(Person):
      def __init__(self, fname, lname, year):
          super().__init__(fname, lname)
          self.graduationyear = year
  
      def welcome(self):
          print("Welcome", self.firstname, self.lastname, "to the class of", self.graduationyear)
  
  x = Student("Mike", "Olsen", 2019)
  x.welcome()
  ```

- **注意：** 如果在子类中定义了与父类同名的方法，则会覆盖父类的方法。

------

​				

## 18. Python 迭代器

迭代器是包含可计数值的对象，必须实现迭代器协议，即定义 `__iter__()` 和 `__next__()` 方法。

### 18.1 迭代器与可迭代对象

- **可迭代对象：** 列表、元组、字典和集合等。
   每个可迭代对象都有 `iter()` 方法来返回一个迭代器。

- 示例：

   从元组中返回迭代器：

  ```
  mytuple = ("apple", "banana", "cherry")
  myit = iter(mytuple)
  
  print(next(myit))
  print(next(myit))
  print(next(myit))
  ```

- 字符串示例：

  ```
  mystr = "banana"
  myit = iter(mystr)
  
  print(next(myit))
  print(next(myit))
  print(next(myit))
  print(next(myit))
  print(next(myit))
  print(next(myit))
  ```

### 18.2 遍历迭代器

- 使用 for 循环遍历：

  ```
  mytuple = ("apple", "banana", "cherry")
  for x in mytuple:
      print(x)
  
  mystr = "banana"
  for x in mystr:
      print(x)
  ```

### 18.3 自定义迭代器

- **创建自定义迭代器需要实现 `__iter__()` 和 `__next__()` 方法。**

  - **示例：** 创建一个返回数字的迭代器，从 1 开始，每次递增 1：

    ```
    class MyNumbers:
        def __iter__(self):
            self.a = 1
            return self
      
        def __next__(self):
            x = self.a
            self.a += 1
            return x
      
    myclass = MyNumbers()
    myiter = iter(myclass)
    
    print(next(myiter))
    print(next(myiter))
    print(next(myiter))
    print(next(myiter))
    print(next(myiter))
    ```

  - **使用 StopIteration 终止迭代：**

    ```
    class MyNumbers:
        def __iter__(self):
            self.a = 1
            return self
      
        def __next__(self):
            if self.a <= 20:
                x = self.a
                self.a += 1
                return x
            else:
                raise StopIteration
      
    myclass = MyNumbers()
    myiter = iter(myclass)
    
    for x in myiter:
        print(x)
    ```

------

​				

## 19. Python 作用域

变量的作用范围决定了变量在哪里可用。

### 19.1 局部作用域

- **定义：**
   在函数内部创建的变量，只在该函数内部可用。

- 示例：

  ```
  def myfunc():
      x = 300
      print(x)
  
  myfunc()
  ```

- 在函数内部嵌套函数中也可访问外部函数的局部变量：

  ```
  def myfunc():
      x = 300
      def myinnerfunc():
          print(x)
      myinnerfunc()
  
  myfunc()
  ```

### 19.2 全局作用域

- **定义：**
   在代码主体中创建的变量，是全局变量，在任何地方都可用。

- 示例：

  ```
  x = 300
  
  def myfunc():
      print(x)
  
  myfunc()
  print(x)
  ```

### 19.3 同名变量

- 局部变量与全局变量相同名称互不干扰：

  ```
  x = 300
  
  def myfunc():
      x = 200
      print(x)   # 输出局部变量 200
  
  myfunc()
  print(x)       # 输出全局变量 300
  ```

### 19.4 global 关键词

- 创建或修改全局变量

  使用 

  ```
  global
  ```

   关键字将变量声明为全局：

  ```
  def myfunc():
      global x
      x = 300
  
  myfunc()
  print(x)
  ```

- 在函数内部修改全局变量：

  ```
  x = 300
  
  def myfunc():
      global x
      x = 200
  
  myfunc()
  print(x)
  ```

------

​		

​				

## 20. Python 模块

模块类似于代码库，是包含一组函数和变量的文件，便于在其他程序中重用代码。

### 20.1 什么是模块？

- **定义：**
   模块是一个包含函数、变量或类的 .py 文件，可以在应用程序中导入和使用。

### 20.2 创建模块

- **方法：**
   将所需代码保存在扩展名为 `.py` 的文件中。

- 示例 (mymodule.py)：

  ```
  def greeting(name):
      print("Hello, " + name)
  ```

### 20.3 使用模块

- 导入并调用模块中的函数：

  ```
  import mymodule
  
  mymodule.greeting("Jonathan")
  ```

  注：使用时需写作 `module_name.function_name` 的形式。

### 20.4 模块中的变量

- 模块不仅包含函数，还可以包含变量（例如字典、列表、对象等）：

  - 示例 (mymodule.py)：

    ```
    person1 = {
        "name": "John",
        "age": 36,
        "country": "Norway"
    }
    ```

  - 导入后访问变量：

    ```
    import mymodule
    
    a = mymodule.person1["age"]
    print(a)
    ```

### 20.5 为模块命名

- **规则：**
   文件名可以自由命名，但扩展名必须为 `.py`。

### 20.6 重命名模块

- 使用 as 关键字创建别名：

  ```
  import mymodule as mx
  
  a = mx.person1["age"]
  print(a)
  ```

### 20.7 内建模块

- Python 内建了许多模块，可直接导入使用。

  - 示例：

    ```
    import platform
    
    x = platform.system()
    print(x)
    ```

### 20.8 使用 dir() 函数

- 列出模块中的所有已定义名称：

  ```
  import platform
  
  x = dir(platform)
  print(x)
  ```

  注：dir() 可用于所有模块，包括自定义模块。

### 20.9 从模块导入

- 使用 from … import 语句，可只导入模块的特定部分：

  - 示例 (mymodule.py 内容)：

    ```
    def greeting(name):
        print("Hello, " + name)
    
    person1 = {
        "name": "John",
        "age": 36,
        "country": "Norway"
    }
    ```

  - 仅导入 person1 字典：

    ```
    from mymodule import person1
    
    print(person1["age"])
    ```

  注：使用 from … import 导入后，引用时直接用变量名即可，无需再写模块名。

------

​					

## 21. Python 日期时间

通过内置的 `datetime` 模块，Python 可以把日期时间作为对象进行操作。

### 21.1 Python 日期

- **说明：**
   日期本身不是独立的数据类型，而是通过 `datetime` 模块处理的日期对象。

### 21.2 日期输出

- 示例：

  ```
  import datetime
  
  x = datetime.datetime.now()
  print(x)
  ```

  输出示例：

  ```
  2025-03-12 22:23:07.023151
  ```

  日期包含年、月、日、小时、分钟、秒和微秒。

### 21.3 创建日期对象

- 使用 datetime 类的构造函数，至少需要年、月、日：

  ```
  import datetime
  
  x = datetime.datetime(2020, 5, 17)
  print(x)
  ```

- **其他可选参数：**
   小时、分钟、秒、微秒及时区（默认值均为 0 或 None）。

### 21.4 strftime() 方法

- **用途：**
   将日期对象格式化为可读字符串。

- **示例：** 显示月份的名称：

  ```
  import datetime
  
  x = datetime.datetime(2018, 6, 1)
  print(x.strftime("%B"))
  ```

- **常用格式代码：**

  | 指令 | 描述                        | 示例                     |
  | ---- | --------------------------- | ------------------------ |
  | %a   | Weekday，短版本             | Wed                      |
  | %A   | Weekday，完整版本           | Wednesday                |
  | %w   | Weekday，数字 0-6，0 为周日 | 3                        |
  | %d   | 日，数字 01-31              | 31                       |
  | %b   | 月名称，短版本              | Dec                      |
  | %B   | 月名称，完整版本            | December                 |
  | %m   | 月，数字 01-12              | 12                       |
  | %y   | 年，短版本，无世纪          | 18                       |
  | %Y   | 年，完整版本                | 2018                     |
  | %H   | 小时，00-23                 | 17                       |
  | %I   | 小时，00-12                 | 05                       |
  | %p   | AM/PM                       | PM                       |
  | %M   | 分，00-59                   | 41                       |
  | %S   | 秒，00-59                   | 08                       |
  | %f   | 微秒，000000-999999         | 548513                   |
  | %z   | UTC 偏移                    | +0100                    |
  | %Z   | 时区                        | CST                      |
  | %j   | 年中的第几天，001-366       | 365                      |
  | %U   | 一年中的周数，周日为第一天  | 52                       |
  | %W   | 一年中的周数，周一为第一天  | 52                       |
  | %c   | 本地日期和时间              | Mon Dec 31 17:41:00 2018 |
  | %x   | 本地日期                    | 12/31/18                 |
  | %X   | 本地时间                    | 17:41:00                 |
  | %%   | 字符 %                      | %                        |

------

​					

## 22. Python 数学运算

Python 提供内置的数学函数以及扩展的 math 模块，方便执行各种数学计算。

### 22.1 内置数学函数

- min() 和 max()：

  可用于一个可迭代对象，返回最小和最大值。

  ```
  x = min(5, 10, 25)
  y = max(5, 10, 25)
  
  print(x)
  print(y)
  ```

- abs()：

  返回指定数字的绝对值。

  ```
  x = abs(-7.25)
  print(x)
  ```

- pow(x, y)：

  返回 x 的 y 次方。

  ```
  x = pow(4, 3)
  print(x)
  ```

### 22.2 math 模块

- 导入 math 模块后，可使用更多数学方法和常量。

  - 示例：计算平方根

    ```
    import math
    
    x = math.sqrt(64)
    print(x)
    ```

  - 向上舍入与向下舍入：

    ```
    import math
    
    x = math.ceil(1.4)   # 返回 2
    y = math.floor(1.4)  # 返回 1
    print(x)
    print(y)
    ```

  - 常量 PI：

    ```
    import math
    
    x = math.pi
    print(x)
    ```

------

​				

## 23. Python Try Except 异常处理

异常处理使得程序在遇到错误时不会崩溃，而是执行相应的处理代码。

### 23.1 基本异常处理

- 使用 try/except 捕获异常：

  ```
  try:
      print(x)
  except:
      print("An exception occurred")
  ```

  注：若 try 块中出现错误，则执行 except 块中的代码。

### 23.2 多个异常

- 针对不同错误类型处理：

  ```
  try:
      print(x)
  except NameError:
      print("Variable x is not defined")
  except:
      print("Something else went wrong")
  ```

### 23.3 else 子句

- 当没有异常时执行 else 块：

  ```
  try:
      print("Hello")
  except:
      print("Something went wrong")
  else:
      print("Nothing went wrong")
  ```

### 23.4 finally 子句

- **无论是否发生异常，finally 块都会执行：**

  ```
  try:
      print(x)
  except:
      print("Something went wrong")
  finally:
      print("The 'try except' is finished")
  ```

  *常用于关闭文件、释放资源等。*

- **示例：操作文件**

  ```
  try:
      f = open("demofile.txt")
      f.write("Lorum Ipsum")
  except:
      print("Something went wrong when writing to the file")
  finally:
      f.close()
  ```

### 23.5 引发异常

- 使用 raise 关键字主动引发异常：

  - 示例 1：当 x 小于 0 时引发异常

    ```
    x = -1
    if x < 0:
        raise Exception("Sorry, no numbers below zero")
    ```

  - 示例 2：当 x 不是整数时引发 TypeError

    ```
    x = "hello"
    if not type(x) is int:
        raise TypeError("Only integers are allowed")
    ```

------

​				

## 24. Python 用户输入

用户输入允许程序从命令行获取数据，方法在 Python 3.x 与 2.7 中略有不同。

### 24.1 Python 3.x 用户输入

- 使用 input() 方法：

  ```
  username = input("Enter username:")
  print("Username is: " + username)
  ```

### 24.2 Python 2.7 用户输入

- 使用 raw_input() 方法：

  ```
  username = raw_input("Enter username:")
  print("Username is: " + username)
  ```

*注：当执行 input() 或 raw_input() 时，程序会暂停等待用户输入。*

------

​					

## 25. Python 字符串格式化

字符串格式化允许您控制字符串中变量的显示格式。

### 25.1 使用 format() 方法

- 在字符串中添加占位符 {}，然后使用 format() 方法替换：

  ```
  price = 49
  txt = "The price is {} dollars"
  print(txt.format(price))
  ```

- 指定格式：

  如将价格格式化为两位小数：

  ```
  txt = "The price is {:.2f} dollars"
  print(txt.format(price))
  ```

### 25.2 多个值的格式化

- 传入多个值，对应多个占位符：

  ```
  quantity = 3
  itemno = 567
  price = 49
  myorder = "I want {} pieces of item number {} for {:.2f} dollars."
  print(myorder.format(quantity, itemno, price))
  ```

### 25.3 使用索引号

- 在占位符中添加索引号，确保顺序对应：

  ```
  quantity = 3
  itemno = 567
  price = 49
  myorder = "I want {0} pieces of item number {1} for {2:.2f} dollars."
  print(myorder.format(quantity, itemno, price))
  ```

- 重复引用同一值：

  ```
  age = 36
  name = "John"
  txt = "His name is {1}. {1} is {0} years old."
  print(txt.format(age, name))
  ```

### 25.4 命名索引

- 在占位符中使用名称，并在 format() 方法中指定关键字：

  ```
  myorder = "I have a {carname}, it is a {model}."
  print(myorder.format(carname="Ford", model="Mustang"))
  ```

  ​				

  ​			

## 26. Python 文件处理

文件处理是开发中非常重要的一部分，允许程序对文件进行创建、读取、更新和删除操作。Python 提供了内置的 open() 函数以及 os 模块来处理文件。

### 26.1 文件处理基础

- open() 函数：

  用于打开文件，主要参数包括文件名和模式。

  常用模式：

  - `"r"`：读取（默认），打开文件进行读取，文件不存在则报错。
  - `"a"`：追加，打开文件以便追加内容，文件不存在则创建。
  - `"w"`：写入，打开文件写入内容，会覆盖原有内容，文件不存在则创建。
  - `"x"`：创建，创建指定文件，若文件已存在则报错。
     同时还可以指定文件以文本 (`"t"`) 或二进制 (`"b"`) 模式打开。默认是文本模式，因此以下代码：

  ```
  f = open("demofile.txt")
  ```

  等价于：

  ```
  f = open("demofile.txt", "rt")
  ```

  注：确保文件存在，否则会引发错误。

### 26.2 打开和读取文件

- 读取整个文件：

  ```
  f = open("demofile.txt", "r")
  print(f.read())
  f.close()
  ```

- 指定文件路径：

  如果文件不在当前文件夹，需指定完整路径：

  ```
  f = open("D:\\myfiles\\welcome.txt", "r")
  print(f.read())
  ```

- 读取部分内容：

  通过指定 read() 方法的字符数：

  ```
  f = open("demofile.txt", "r")
  print(f.read(5))  # 返回文件前5个字符
  f.close()
  ```

- 按行读取：

  - 使用 readline() 方法一次读取一行：

    ```
    f = open("demofile.txt", "r")
    print(f.readline())
    f.close()
    ```

  - 连续调用 readline() 可读取多行：

    ```
    f = open("demofile.txt", "r")
    print(f.readline())
    print(f.readline())
    f.close()
    ```

  - 使用 for 循环逐行遍历文件：

    ```
    f = open("demofile.txt", "r")
    for line in f:
        print(line)
    f.close()
    ```

### 26.3 文件写入

- 写入已有文件（追加与覆盖）：

  - 追加模式 ("a")：

    在文件末尾追加内容，如果文件不存在则创建文件。

    ```
    f = open("demofile2.txt", "a")
    f.write("Now the file has more content!")
    f.close()
    
    # 验证写入结果：
    f = open("demofile2.txt", "r")
    print(f.read())
    f.close()
    ```

  - 写入模式 ("w")：

    覆盖原有内容。如果文件不存在则创建。

    ```
    f = open("demofile3.txt", "w")
    f.write("Woops! I have deleted the content!")
    f.close()
    
    f = open("demofile3.txt", "r")
    print(f.read())
    f.close()
    ```

  注：使用 "w" 模式会覆盖文件中原有的所有内容。

### 26.4 创建新文件

- 通过 "x" 模式创建：

  ```
  f = open("myfile.txt", "x")
  # 成功执行后，将创建一个新的空文件
  f.close()
  ```

- 使用 "w" 或 "a" 模式：

  若指定文件不存在，会自动创建文件：

  ```
  f = open("myfile.txt", "w")
  f.close()
  ```

### 26.5 删除文件和文件夹

- 删除文件：

  使用 os 模块中的 os.remove() 函数删除文件：

  ```
  import os
  os.remove("demofile.txt")
  ```

- 检查文件是否存在：

  为避免删除时出错，可先检查文件是否存在：

  ```
  import os
  if os.path.exists("demofile.txt"):
      os.remove("demofile.txt")
  else:
      print("The file does not exist")
  ```

- 删除文件夹：

  删除空文件夹使用 os.rmdir() 方法：

  ```
  import os
  os.rmdir("myfolder")
  ```

  注：os.rmdir() 只能删除空文件夹。