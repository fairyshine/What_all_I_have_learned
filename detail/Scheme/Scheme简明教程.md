# 前言



# 第一章 进入Scheme

创建一个名为`hello.scm`的文件，并在里面输入以下内容：

```scheme
;The first program
(begin
	(display "Hello, World!")
  (newline))
```

第一行是一个注释，当Scheme发现一个分号，就把分号和这一行分号后面的文字都忽略了。`begin`语句（原文为form）是Scheme用来包括子语句的方式，这个例子里有两个子语句。

第一句调用了`display`过程，该过程会输出它的参数（字符串`"Hello, World!"`）到控制台（或者叫“标准输出”）

后面一句调用了`newline`过程，该过程输出一个换行。

## 进入scheme命令行

这将调用Scheme listener程序，这个程序读取你的输入，求值，打印结果（如果有的话），然后等待你的下一次输入。由此这通常被称为“读取-求值-打印”的循环。

MzScheme 输入 mzscheme

chicken 输入 csi

1. `(load "hello.scm")`  加载执行文件
2. `(exit)` 退出命令行

# 第二章 数据类型

## 2.1 简单数据类型

Scheme中的简单数据类型包含 `booleans` (布尔类型) , `number`(数字类型), `characters`(字符类型) 和 `symbols`(标识符类型)。

### 2.1.1 Booleans

`#t`、`#f`来分别表示true和false。Scheme拥有一个叫`boolean?`的过程，可以用来检测它的参数是否为boolean类型。

```scheme
(boolean? #t)              =>  #t
(boolean? "Hello, World!") =>  #f

(not #f)              =>  #t
(not #t)              =>  #f
(not "Hello, World!") =>  #f  ;最后一个表达式清晰的显示出了Scheme的一个便捷性：在一个需要boolean类型的上下文中，Scheme会将任何非 #f的值看成true。
```

### 2.1.2 Numbers

Scheme的numbers类型可以是`integers`(整型，例如`42`)，`rationals`(有理数，例如`22/7`)，`reals`(实数，例如`3.14159`)，或`complex`(复数，`2+3i`)。一个整数是一个有理数，一个有理数是一个实数，一个实数是一个复数，一个复数是一个数字。

```scheme
(number? 42)       =>  #t
(number? #t)       =>  #f
(complex? 2+3i)    =>  #t
(real? 2+3i)       =>  #f
(real? 3.1416)     =>  #t
(real? 22/7)       =>  #t
(real? 42)         =>  #t
(rational? 2+3i)   =>  #f
(rational? 3.1416) =>  #t
(rational? 22/7)   =>  #t
(integer? 22/7)    =>  #f
(integer? 42)      =>  #t

(eqv? 42 42)   =>  #t ;判断是否相等
(eqv? 42 #f)   =>  #f
(eqv? 42 42.0) =>  #f

(= 42 42)   =>  #t ;判断数字是否相等，还有<,<=,>,>=
(= 42 #f)   -->ERROR!!!
(= 42 42.0) =>  #t

(+ 1 2 3)    =>  6  ;基本运算 +, -,*,/, expt
(- 5.3 2)    =>  3.3
(- 5 2 1)    =>  2
(* 1 2 3)    =>  6
(/ 6 3)      =>  2
(/ 22 7)     =>  22/7
(expt 2 3)   =>  8
(expt 4 1/2) =>  2.0

(max 1 3 4 2 3) =>  4
(min 1 3 4 2 3) =>  1

(abs  3) =>  3
(abs -4) =>  4
```

Scheme的integers(整型)不需要一定是10进制格式。可以通过在数字前加前缀 `#b` 来规定实现2进制。这样 `#b1100`就是10进制数字12了。实现8进制和16进制格式的前缀分别是 `#o` 和`#x`。(decimal前缀 `#d`是可选项)



这些还只是冰山一角。Scheme提供一整套丰富数学和三角运算过程。比如`atan`, `exp`, 和 `sqrt`等过程分别返回参数的余切、自然反对数和开方值。

其它更具体的数学运算过程信息请参阅Revised^5 Report on the Algorithmic Language Scheme

### 2.1.3 Characters

Scheme中字符型数据通过在字符前加 `#\`前缀来表示。像`#\c`就表示字符`c`。那些非可视字符会有更多的描述名称，例如，`#\newline`, `#\tab`。空格字符可以写成 `#\`，或者可读性更好一些的`#\space`。

```scheme
(char? #\c) =>  #t ;字符类型判断
(char? 1)   =>  #f
(char? #\;) =>  #t

(char=? #\a #\a)  =>  #t  ;字符类型数据比较
(char<? #\a #\b)  =>  #t
(char>=? #\a #\b) =>  #f
       
(char-ci=? #\a #\A) =>  #t  ;忽略大小写的比较
(char-ci<? #\a #\B) =>  #t
       
(char-downcase #\A) =>  #\a ;大小写类型转换
(char-upcase #\a)   =>  #\A       
```

### 2.1.4 Symbols

前面我们所见到的简单数据类型的求值结果都是自己。Symbols的求值结果与上面的简单数据类型不同。

Symbols通常在Scheme程序中被用来当做变量的标识，求值后可以得到变量所代表的值。

创建一个单纯的symbol而非变量时，你需要使用`quote`过程：

```scheme
(quote xyz)=>  xyz
```

因为在Scheme中经常要引用这种类型，我们有一种更简便的方式。==表达式 `'E`和`(quote E)`在Scheme中是等价的。==

用来检查symbols类型数据的过程是==`symbol?`==

```scheme
(symbol? 'xyz) =>  #t(symbol? 42)   =>  #f
```

Scheme的symbols类型通常都是==不区分大小写==的。因此`Calorie` 和`calorie`是等价的

```scheme
(eqv? 'Calorie 'calorie)=>  #t
```

我们还可以使用==`define`== 将symbol 类型的数据 如`xyz`当成一个全局的变量来使用：

```scheme
(define xyz 9)
```

这样可以就创建了一个值为9的变量`xyz`。

如果想改变`xyz`中的值可以用==`==set!==`==来实现：

```scheme
(set! xyz #\c)
```

现在`xyz`中的值就是字符`#\c`了。

## 2.2 复合数据类型

### 2.2.1 Strings

字符串类型是由字符组成的序列。Strings是自运算类型。

```scheme
"Hello, World!"
=>  "Hello, World!"

(string #\h #\e #\l #\l #\o) ;还可以通过向string过程传递一组字符并返回由它们合并成的字符串：
=>  "hello"

(define greeting "Hello; Hello!") ;现在让我们定义一个全局字符串变量 greeting
(string-ref greeting 0) ;通过向string-ref过程传递一个字符串和一个从0开始的索引号，可以返回该字符串指定索引号位置的字符。
=>  #\H

(string-append "E "  		;可以通在一个现有的字符串上追加其它字符串的方式来获得新字符串
               "Pluribus "
               "Unum")
=>  "E Pluribus Unum"

(define a-3-char-long-string (make-string 3)) ;你可以定义一个指定长度的字符串，然后用期望的字符来填充它。

;检测一个值是否是字符串类型的过程是string?。

;通过调用string， make-string 和string-append获得的字符串结果都是可修改的。而过程string-set!就可以替换字符串指定索引处的字符。
(define hello (string #\H #\e #\l #\l #\o)) 
hello
=>  "Hello"
(string-set! hello 1 #\a)
hello
=>  "Hallo"
```

### 2.2.2 Vectors(向量)

Vectors是像strings一样的序列，但它们的元素可以是任何类型，而不仅仅是字符，当然元素也可以是Vetors类型，这是一种生成多维向量的好方式。

这使用五个整数创建了一个vector：

```scheme
(vector 0 1 2 3 4)
=>  #(0 1 2 3 4)
```

和`make-string`过程类似，过程`make-vectors`可以构建一个指定长度的向量：

```scheme
(define v (make-vector 5))
```

而过程`vector-ref` 和`vector-set!`分别可以访问和修改向量元素。

检测值是否是一个向量的过程是`vector?`。

### 2.2.3 Dotted pairs(点对)和(列表)

点对

点对是一种复合类型。把两个任意类型的值有序地连起来，即为一个点对。点对的第一个值被称作car，第二个值被称作cdr，而将两个值组合成点值对的过程是cons。

```scheme
(cons 1 #t)
=>  (1 . #t)
```

点对不能自运算，因此直接以值的方式来定义它们（即不通过调用`cons`来创建），必须显式的使用引号：

```scheme
'(1 . #t) 
=>  (1 . #t)

(1 . #t)  
-->ERROR!!!
```

访问点值对值的过程分别是`car` (`car`访问点值对的第一个元素)和 `cdr`(`cdr`访问点值对的非一个元素)：

```scheme
(define x (cons 1 #t))

(car x)
=>  1

(cdr x)
=>  #t
```

---

列表

诸如像`(1 . (2 . (3 . (4 . ()))))`这样形式的点值对被简化成`(1 2 3 4)`。像这样第二元素都是一个点值对特殊形式的嵌套点值对就称作列表list。

但Scheme提供了一个list过程可以更方便的创建列表。List可以将任意个数的参数变成列表返回：

```scheme
(list 1 2 3 4)
=>  (1 2 3 4)
```

实际上，如果我们知道列表所包含的所有元素，我们还可以用`quote` 来定义一个列表：

```scheme
'(1 2 3 4)
=>  (1 2 3 4)
```

列表的元素可以通过指定索引号来访问。

```scheme
(define y (list 1 2 3 4))
(list-ref y 0) =>  1
(list-ref y 3) =>  4
(list-tail y 1) =>  (2 3 4)
(list-tail y 3) =>  (4)
```

`list-tail`返回了给定索引号后的所有元素。

`pair?`， `list?` 和`null?`判断过程可以分别用来检查它们的参数是不是一个点对，列表或空列表。

```scheme
(pair? '(1 . 2)) =>  #t
(pair? '(1 2))   =>  #t
(pair? '())      =>  #f
(list? '())      =>  #t
(null? '())      =>  #t
(list? '(1 2))   =>  #t
(list? '(1 . 2)) =>  #f
(null? '(1 2))   =>  #f
(null? '(1 . 2)) =>  #f
```

## 2.3 数据类型转换

Scheme提供了许多可以进行数据类型转换的过程。我们已经知道可以通过`char-downcase` 和 `char-upcase`过程来进字符大小写的转换。字符还可以通过使用`char->integer`来转换成整型，同样的整型也可以通过`integer->char`被转换成字符。(字符转换成整型得到的结果通常是这个字符的ascii码值。)

其它的转换过程也都是一样的风格`list->string`， `vector->list` 和 `list->vector`。

`string->number`第二个参数是可选参数，指示以几进制来转换。

## 2.4 其他数据类型

Scheme还包含了一些其它数据类型。一个是 *procedure* (过程)。我们已经见过了许多过程了，例如，`display`， `+`， `cons`等。实际上，它们是一些承载了过程值的变量，过程本身内部的数值和字符并不可见：

还有另外一种数据类型是port端口。一个端口是为输入输出提供执行的通道。端口通常会和文件和控制台操作相关联。

在我们的`"Hello，World！"`程序中，我们使用`display`过程向控制台输出了一个字符串。`display`可以接受两个参数，第一个参数值是将输出的值，另一个值则表示了即将承载显示结果的输出port(端口)。

在我们的程序中，`display`的第二参数是隐式参数。这时候`display`会采用标准输出端口作为它的默认输出端口。我们可以通过调用`current-output-port`过程来取得当前的标准输出端口。我们可以更清楚的写出:

```scheme
(display "Hello, World!" (current-output-port))
```

## 2.5 S-expressions (S表达式)

所有这些已经被讨论过的数据类型可以被统一成一种通用的叫作s-expression(符号表达式或s-表达式)的数据类型(s代表符号)。像 `42`，`#\c`，`(1 . 2)` ， `#(a b c)` ，`"Hello"`， `(quote xyz)` ， `(string->number "16")`， 和 `(begin (display "Hello, World!") (newline))`都是s-表达式。

# 第三章 Forms代码结构

读者们会发现迄今为止我们提供的Scheme示例程序也都是s-表达式。这对所有的Scheme程序来说都适用：程序是数据。

如果这个代码结构的列表头是一个特殊的代码结构，则将会采用一种特殊的方式来运行。我们已经碰到过的特殊的代码结构有`begin`， `define`和 `set!`。

`begin`可以让它的子结构可以有序的运算，而最后一个子结构的结果将成为整个代码结构的运行结果。`define`会声明并会初始化一个变量。`set!` 可以给已经存在的变量重新赋值。

## 3.1 procedures(过程)

我们已经见过了许多系统过程，比如，`cons`， `string->list`等。用户可以使用代码结构`lambda`来创建自定义的过程。例如，下面定义了一个过程可以在它的参数上加上2：

```scheme
(lambda (x) (+ x 2))
```

使用变量承载

```scheme
(define add2 (lambda (x) (+ x 2)))  ;如果我们希望能够多次调用这个相同的过程，我们可以每次使用`lambda`重新创建一个复制品，但我们有更好的方式。我们可以使用一个变量来承载这个过程

(add2 4) 
=>  6
(add2 9) 
=>  11

(define (add2 x) (+ x 2)) ;译者注:定义过程还可以有另一种简单的方式，直接用define而不使用lambda来创建
```

### 3.1.1 过程的参数

### 3.1.2 可变数量的参数(不定长参数)

## 3.2 apply过程

`apply`过程允许我们直接传递一个装有参数的list 给一个过程来完成对这个过程的批量操作。

```scheme
(define x '(1 2 3))

(apply + x)
=>  6
```

## 3.3 顺序执行

我们使用`begin`这个特殊的结构来对一组需要有序执行的子结构来进行打包。许多Scheme的代码结构都隐含了`begin`。在Scheme中，==lambda的语句体都是隐式的`begin`代码结构。==因此，其间的begin不是必须的，不写时也不会有什么影响。

# 第四章 条件语句

和其它的编程语句一样，Scheme 也包含条件语句。

最基本的结构就是if：

```scheme
(if 测试条件
		then-分支    
		else-分支)
```

如果测试条件运算的结果是真(即，非`#f`的任何其它值)，`then`分支将会被运行(即满足条件时的运行分支)。否则，`else`分支会被运行。`else`分支是可选的。

```scheme
(define p 80)
(if (> p 70) 
    'safe
    'unsafe)
=>  safe 
(if (< p 90)
    'low-pressure) ;no else-branch
=>  low-pressure
```

## 4.1 when和unless

当我们只需要一个基本条件语句分支时（”then”分支或”else”分支），使用when 和 unless会更方便。

```scheme
(define a 10)
(define b 20)
(when (< a b)
       (display “a是”)
       (display a)
       (display “b是”)
       (display b)
       (display “a大于b” ) )
```

注意`when`的分支是一个隐式的`begin`语句结构，
而如果`if`的分支有多个代码结构时，需要一个显式的`begin`代码结构。

## 4.2 cond

`cond`结构在表示多重`if`表达式时很方便，
多重`if`结构除了最后一个`else`分支以外的其余分支都会包含一个新的`if`条件。因此，

```scheme
(if (char<? c #\c) -1    
		(if (char=? c #\c) 0        
				1))
```

这样的结构都可以使用`cond`来这样写：

```scheme
(cond ((char<? c #\c) -1)      
		((char=? c #\c) 0)      
				(else 1))
```

`cond`就是这样的一种多分支条件结构。每个从句都包含一个判断条件和一个相关的操作。第一个判断成立的从句将会引发它相关的操作执行。如果任何一个分支的条件判断都不成立则最后一个`else`分支将会执行(`else`分支语句是可选的)。

cond的分支操作都是`begin`结构。

## 4.3 case

当`cond`结构的每个测试条件是一个测试条件的分支条件时，可以缩减为一个`case`表达式。

```scheme
(define c #\c)
(case c
  ((#\a) 1)
  ((#\b) 2)
  ((#\c) 3)
  (else 4))
=>  3
```

分支头值是`#\c` 的分支将被执行。

## 4.4 and和or

Scheme提供了对boolean值进行逻辑与`and`和逻辑或`or`运算的结构。(我们已经见过了布尔类型的求反运算not过程。)

```scheme
(and 1 2)  =>  2
(and #f 1) =>  #f
```

`and`和`or`都是从左向右运算。当某个子结构可以决定最终结果时，`and`和`or`会忽略剩余的子结构，即它们是“短路”的。

```scheme
(and 1 #f expression-guaranteed-to-cause-error)
=>  #f
(or 1 #f expression-guaranteed-to-cause-error)
=>  1
```

# 第五章 词法变量

Scheme的变量有一定的词法作用域，即它们在程序代码中只对特定范围的代码结构可见。

迄今为止我们所见过的**全局变量**也没有例外的：它们的作用域是整个程序，这也是一种特定的作用范围。

我们也碰见过一些示例包含**局部变量**。它们都是lambda过程的参数，当过程被调用时这些变量会被赋值，而它们的作用域仅限于在过程的内部。

## 5.1 let和let*

并不是一定要显式的创建过程才可以创建局部变量。有个特殊的代码结构let可以创建一列局部变量以便在其结构体中使用:

```scheme
(let ((x 1)
      (y 2)
      (z 3))
  (list x y z))
=>  (1 2 3)
```

有时候，用`let`依次的创建局变量非常的方便，如果在初始化区域中可以用先创建的变量来为后创建的变量赋值也会非常方便。`let*`结构就可以这样做：

```scheme
(let* ((x 1)
       (y x))
  (+ x y))
=>  2
```

## 5.2 fluid-let （方言）

一个词法变量如果没有被隐藏，在它的作用域内一直都为可见状态。有时候，我们有必要将一个词法变量临时的设置为一个固定的值。为此我们可使用`fluid-let`结构(`fluid-let`是一个非标准的特殊结构。可参见8.3，在Scheme中定义fluid-let)。

# 第六章 递归

一个过程体中可以包含对其它过程的调用，特别的是也可以调用自己。互递归过程也是可以的。

## 6.1 letrec

## 6.2 命名let

## 6.3 迭代

## 6.4 用自定义过程映射整个列表

# 第七章 输入输出

