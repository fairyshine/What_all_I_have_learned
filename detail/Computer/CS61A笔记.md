# 1-1 Welcome！

## Lecture : 

课程主旨：

管理程序复杂度

深入理解编程理念

计算机怎样解释程序

## Disc 0 : Getting Started

/

## Lab 0 : Getting Started

下载 lab00.zip

安装 终端、python3、文本编辑器

基本操作

ok的使用：`python3 ok -q python-basics -u`

# 1-2 Functions

## Lecture : 

值（Values）、表达式（Expressions）、函数（Functions）

## Textbook : Ch1.1 Getting Started

The high productivity of computer science is only possible because **the discipline is built upon an elegant and powerful set of fundamental ideas**. All computing begins with representing information, specifying logic to process it, and designing abstractions that manage the complexity of that logic. Mastering these fundamentals will require us to understand precisely how computers interpret computer programs and carry out computational processes.

These fundamental ideas have long been taught using the classic textbook *==Structure and Interpretation of Computer Programs==* ([SICP](http://mitpress.mit.edu/sicp)) by Harold Abelson and Gerald Jay Sussman with Julie Sussman. This text borrows heavily from that textbook, which the original authors have kindly licensed for adaptation and reuse under a Creative Commons license. These notes are published under the [Creative Commons attribution non-commericial share-alike license version 3](http://creativecommons.org/licenses/by-nc-sa/3.0/deed.en_US).

## Textbook : Ch1.2 Elements of Programming

### 1.2.4  Names and the Environment

**Changing the value of one name does not affect other names**. Below, even though the name `area`was bound to a value defined originally in terms of `radius`, the value of `area` has not changed. Updating the value of `area` requires another assignment statement.

```python
>>> area, circumference = pi * radius * radius, 2 * pi * radius
>>> area
314.1592653589793
>>> circumference
62.83185307179586
>>> radius = 11
>>> area
314.1592653589793
>>> area = pi * radius * radius
380.132711084365
```

### 1.2.6  The Non-Pure Print Function

**Pure functions.** Functions have some input (their arguments) and return some output (the result of applying them).

**Non-pure functions.** In addition to returning a value, applying a non-pure function can generate *side effects*, which make some change to the state of the interpreter or computer. A common side effect is to generate additional output beyond the return value, using the `print` function.

## HW 1 : Control

Q2、Q3、Q4、Q5、Q6、Q7

Q7中quine的概念很有意思

```python
quine = 'print("quine =",repr(quine),"; eval(quine)")' ; eval(quine)
quine = 'print(repr(quine),end="")' ; eval(quine)
```

# 2-1 Control

## Lecture : 

add、sub、mul、from operator import truediv, floordiv

#### The None Value

没有显式return的函数，将会return None

#### Side effects

除去return外，还带来其他作用，如print

#### 布尔数

#### 迭代

while 、 break

## Textbook : Ch1.3 Defining New Functions

#### 1.3.1  Environments

- Global
  - 变量
  - import的函数
  - 自定义的函数
- 函数内环境
  - 函数内变量

## Textbook : Ch1.4 Designing Functions

#### 1.4.1  Documentation

加入函数的说明文档

```python
def pressure(v, t, n):
    """Compute the pressure in pascals of an ideal gas.

    Applies the ideal gas law: http://en.wikipedia.org/wiki/Ideal_gas_law

    v -- volume of gas, in cubic meters
    t -- absolute temperature in degrees kelvin
    n -- particles of gas
    """
    k = 1.38e-23  # Boltzmann's constant
    return n * k * t / v
  
help(pressure)
```

#### 1.4.2  Default Argument Values

为函数参数设置默认值

`def pressure(v, t, n=6.022e23):`

## Textbook : Ch1.5 Control

函数体内加入if、while等语句

测试：assert语句、doctest库

## Lab 1 : Variables & Functions , Control

Q4、Q5

# 2-2 Higher-Order Functions

## Lecture :

编写函数：

​		Don‘t Repeat Yourself(DRY) : Implement a process just once, execute it many times.

高阶函数（Higher-Order Functions）：

​		有其他函数作为参数；A function that takes a function as an argument value

​		返回函数作为结果 A function that returns a function as a return value

lambda表达式：

​		**lambda** <parameters>: <expression>

条件表达式：

​		<consequent> **if** <predicate> **else** <alternative>

## Textbook : Ch1.6 Higher-Order Functions

### 1.6.8  Abstractions and First-Class Functions

在计算机科学中，如果一门编程语言声称拥有第一类函数(first-class functions)，那么这门编程语言将函数视为其第一类成员(first-class citizens也译为头等公民、一等公民)。

一个一等公民(即类型、对象、实体、或值等等)在一门特定的编程语言中能够支持其他大多数实体所支持的操作。这些操作一般来说有：被当做参数传递、能够作为函数返回值、能够被修改、能够被赋给一个变量。

First-class object的定义

- 在运行时间（runtime）创建
- 通过数据结构赋值给变量或元素
- 可以作为参数传递给函数
- 可以作为函数的返回值

### 1.6.9  Function Decorators @

使用High-order function作为函数的装饰器

## Disc 1 : Control, Environment Diagrams

Control structures

​		conditional statements

​		boolean operators

​		while loops

Environment diagrams

​		assignment statements

​		def statements

​		call expressions

## Pj 1 : The Game of Hog

👌

# 2-3 Environments

## Lecture : 

An environment is a sequence of frames.

- Global frame
- Function1 frame
- Function2 frame

Names have different meanings in different environments.

===

嵌套函数

```python
def make_texter(emoji): 
	def texter(text):
		return emoji + text + emoji 
	return texter
happy_text = make_texter("􏰀")
result = happy_text("lets go to the beach!")
```

frame:

​	当一个函数被定义，它的parent是当前frame

​	当一个函数被调用，以函数名命名的frame被添加

Function  currying：

​	currying：Converting a function that takes multiple arguments into a single-argument higher-order function.

## HW 2 : Higher-Order Functions

Q1、Q2

# 3-1

## Lab 2 : Higher-Order Functions, Lambda Expressions

Q3、Q4、Q7、Q8

lambda的嵌套使用

```python
def lambda_curry2(func):
    """
    Returns a Curried version of a two-argument function FUNC.
    >>> from operator import add, mul, mod
    >>> curried_add = lambda_curry2(add)
    >>> add_three = curried_add(3)
    >>> add_three(5)
    8
    >>> curried_mul = lambda_curry2(mul)
    >>> mul_5 = curried_mul(5)
    >>> mul_5(42)
    210
    >>> lambda_curry2(mod)(123)(10)
    3
    """
    "*** YOUR CODE HERE ***"
    return lambda x: lambda y: func(x,y)
```

# 3-2 Design

## Lecture : 

#### Functional abstractions

#### Naming things

#### Debugging & errors

## Disc 2 : Higher-Order Functions, Self Reference

/

# 3-3 Function Examples

## Lecture : 

#### Currying  函数柯里化

柯里化，名字听起来很高大上，这其实是一个函数式编程里的一种写法，又叫部分求值。
官方的解释是，把接受多个参数的函数变换成只接后一个单一参数的函数，并返回接受余下的参数和结果的新函数的技术。
其实通俗点说，就是把一个需要传递多参的函数转换成传单参的形式。

#### Decorators

#### Review

## Lab 3 : Midterm Review(Opt)

/

# 4-1

None

# 4-2 Recursion 递归

## Lecture : 

#### Recursive functions

A function is **recursive** if the body of that function calls itself, either directly or indirectly.

#### Recursion in environment diagrams

递归函数每一层都会分别开辟新frame

#### Mutual recursion

递归函数之间相互调用实现功能

#### Recursion vs. iteraton

观点：迭代可以看作是一种特殊的递归。

## Textbook : Ch1.7 Recursive Functions

/

## Disc 3 : Recursion

/

# 4-3 Tree Recursion

## Lecture : 

#### order of recursive calls 递归调用的顺序

#### Tree recursion

#### 例子：Counting partitions 计算隔断问题

## Textbook : Ch1.7 Recursive Functions

#### 1.7.4  Tree Recursion

Another common pattern of computation is called tree recursion, in which **a function calls itself more than once.**

## HW 3 : Recursion, Tree Recursion

Q1、Q2、Q3、Q4

# 5-1 Containers

## Lecture : 

/

## Textbook : Chapter2 Building Abstractions with Data

## Textbook : Ch2.1 Introduction

原生数据类型 Native Data Types

int、float

boolean

## Textbook : Ch2.3 Sequences 序列

Sequences共同的特征

- Length
- Element selection

2.3.1 Lists 列表

2.3.2 Sequence Iteration

2.3.3 Sequence Processing

2.3.4 Sequence Abstraction

2.3.5 Strings 

2.3.6 Trees

2.3.7 Linked Lists

## Lab 4 : Recursion, Tree Recursion, Python Lists

Q2、Q3、Q4、Q5

# 5-2 Sequences

## Lecture : 

#### Box+pointer

嵌套列表结构展示

#### Slicing

[:]

#### Recursive exercises

#### Built-ins for iterables

sum、all、any、max、min

## Textbook : Ch2.3

/

## Disc 4 : Tree Recursion, Python Lists

/

## Pj 2 : Cats

🈳️

# 5-3 Data Abstraction

## Lecture : 

#### Lecture 11 follow-ups

#### Data abstraction

#### Dictionaries

## Textbook : Ch2.2

2.2.1 Example: Rational Numbers

2.2.2 Pairs

2.2.3 Abstraction Barriers

2.2.4 The Properties of Data

# 6-1 Trees

## Lecture : 

/

## Textbook : Ch2.3

/

## Lab 5 :Python Lists, Trees

Q1、Q2、Q3、Q4、(Q5)、Q6、Q7、(Q8)

# 6-2 Mutability

## Lecture : 

#### Objects & methods

An **object** is a bundle of data and behavior.

A type of object is called a **class**.

Every value in Python is an object.

- All objects have attributes
- Objects often have associated methods

#### List mutation & methods

列表的方法：

append()添加单个元素   extend()添加一个列表中所有元素

pop()弹出最后一个元素 remove(a) 移除值为a的第一个元素

#### Tuples

#### Mutability

Immutable types：int,float,string,tuple

mutable types: list,dict

表达式的值改变，共有两种情况：

​		Name Change；Object mutation

#### Beware of mutation

## Textbook : Ch2.4 Mutable Data 可变数据

/

## Disc 5 : Trees, Data Abstraction, Sequences

/

# 6-3 Syntax

## Lecture : 

#### 例子：Syntax trees

#### Data abstractions

#### Parsing syntax trees

#### Sentence generation

## HW 4 : Data Abstraction, Trees

# 7-1 Iterators 迭代器

## Lecture :

#### Iterators

iter(iterable)  next(iterable)

#### For loops with iterators

#### Built-in functions for iterators

## Textbook : Ch4.2

## Lab 6 : Mutability, Itrerators

# 7-2 Generators

## Lecture :

## Textbook : Ch4.2

## Disc 6 : Mutability, Iterators and Generators

# 7-3 Objects

## Lecture :

#### Object-oriented programming 面向对象编程

#### The class statement

#### Class methods

#### Instance variables

#### Class variables

## Textbook : Ch2.5

## HW 5 : Iterators and Generators

# 8-1 Inheritance 继承

## Lecture :

## Textbook : Ch2.5

## Lab 7 : Object-Oriented Programming

## Pj 3 : Ants

# 8-2 Representation

## Lecture :

## Textbook : Ch2.7

## Disc 7 : Object-Oriented Programming, String Representation

# 8-3 Recursive Objects

## Lecture :

## Textbook : Ch2.9

## HW 6 : Object-Oriented Programming, Linked Lists

# 9-1 Efficiency

## Lecture :

#### Exponentiation 取幂、乘方

#### Orders of Growth 增长级

#### Memoization 对函数返回值进行缓存

## Textbook : Ch2.8

## Lab 8 : Linked Lists, Mutable Trees

# 9-2 Decomposition 分解

## Lecture :

#### Modules

#### Packages

#### modularity 模块化

#### Modular design

## Disc 8 : Linked Lists, Trees

# 9-3 Data Example

## Lecture

# 10

# -----------

# 11-1 Scheme(LISP语言的一个方言)

## Lecture :

#### scheme expressions

#### Special forms

#### Quotation

## Textbook : Ch3.1 Introduction

Chapters 1 and 2 describe the close connection between two fundamental elements of programming: functions and data. 

This chapter focuses on the third fundamental element of programming: programs themselves. 

## Textbook : Ch3.2 Functional Programming

编程范式：命令式编程(Imperative)、声明式编程(Declarative)和函数式编程(Functional)

#### 3.2.1 Expressions

Scheme exclusively uses prefix notation.

`(quotient 10 2)`

#### 3.2.2 Definitions

```scheme
(define pi 3.14)
(* pi 2)
;得到6.28
```

The general form of a procedure definition is:

```scheme
(define (<name> <formal parameters>) <body>)
```

Anonymous functions are created using the `lambda` special form. `Lambda` is used to create procedures in the same way as `define`, except that no name is specified for the procedure:

```scheme
(lambda (<formal-parameters>) <body>)
```

#### 3.2.3 Compound values

#### 3.2.4 Symbolic Data

#### 3.2.5 Turtle graphics

## Lab 10 : Scheme

## HW 7 : Scheme

# 11-2 Exceptions

## Lecture :

## Textbook : Ch3.3

## Disc 10 : Scheme, Scheme Lists

# 11-3 Calculator

## Lecture :

## Textbook : Ch3.4

## HW 8 : Scheme Lists

# 12-1 Intepreters

## Lecture :

## Textbook : Ch3.5

## Lab 11 : Intepreters

# 12-2 Tail Calls

## Lecture :

## Disc 11 : Interpreters

## Pj 4 : Scheme

# 12-3 Regular Expressions

## Lecture :

## HW 9 : Regular Expressions

# 13-1 Backus-Naur Form 巴克斯范式

## Lecture :

## Lab 12 : Regular Expressions, BNF

# 13-2 SQL

## Lecture :

## Textbook : Ch4.3 Declarative Programming 声明式编程

## Disc 12 : Regular Expressions, BNF, SQL

# 13-3 Tables

## Lecture :

## Textbook : Ch4.3

## HW 10 : BNF, SQL

# 14-1 Aggregation

## Lecture :

## Textbook : Ch4.3

## Lab 13 : SQL

# 15-1 Web Apps(Optional)

## Lecture :

## Lab 14 : Final Review

# 15-2 Final Examples

## Lecture :

## Disc 14 : Final Review

# 15-3 : Conclusion

## Lecture :

## HW 11 : Finale

