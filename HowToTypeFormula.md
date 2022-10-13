# 简介

## 计算机中主要的公式书写方式

+ LaTeX
+ MathML
+ AsciiMath
+ UnicodeMath

==LaTeX==
[LaTeX——Mathematical Symbols速查](resources/symbols.pdf)

==MathML==
数学标记语言（Mathematical Markup Language），是一种基于XML（标准通用标记语言的子集）的标准，用来在互联网上书写数学符号和公式的置标语言。

==AsciiMath==
AsciiMath is an easy-to-write markup language for mathematics.
http://asciimath.org

==UnicodeMath==
应用于Microsoft Word的公式输入。

## 计算机中主要的公式渲染引擎

+ LaTeX
+ MathJax
+ KaTeX

==MathJax==
MathJax is an open-source JavaScript display engine for **LaTeX**, **MathML**, and **AsciiMath** notation that works in all modern browsers, with built-in support for assistive technology like screen readers.  
https://www.mathjax.org

==KaTeX==
KaTeX is a fast, easy-to-use JavaScript library for TeX math rendering on the web.
https://katex.org
https://github.com/KaTeX/KaTeX

> **Typora**中使用mathjax引擎渲染LaTeX数学公式。

# LaTeX 公式输入

amsmath amssymb

## 进入数学模式

### Markdown——Typora内

**行内公式**

```latex
$ f(x) = a+b $
```

**行间公式**

```latex
$$ f(x) = a+b $$
```

**手动编号**

```latex
$$ f(x) = a - b \tag{1.1} $$
```

**换行**

==\\\\== 或者 ==\newline==

### TeX——TexLive等

Latex中书写公式 - yolo的文章 - 知乎 https://zhuanlan.zhihu.com/p/480184909

(待整理)

\[ f(x) = a+b \]

\begin{displaymath}
f(x) = a+b
\end{displaymath}

\[ \lim \]

```latex
\documentclass{article}
\usepackage{amsmath}

\begin{document}
	
	
	\begin{equation}
	\begin{aligned}
	  \theta  \\
	   \theta  
	\end{aligned}
	\end{equation}
	
\[ 
\begin{align}   split
	f(x) = &a - b \\
&\theta 
\end{align}
\]

\end{document}

```

## 公式语法

### 简单运算

| 展示           | 语法         | 名称           |
| -------------- | ------------ | -------------- |
| $ + - * / =$   | + - * / =    | 四则运算,等于  |
| $\cdot$        | \cdot        | 点乘           |
| $\bmod$        | \bmod        | 取模           |
| $\neq \equiv $ | \neq  \equiv | 不等于，恒等于 |

### 上下标

上标：^{}	$x^{1}$
下标：\_{}	$x_{1}$

### 根号、分式

\sqrt表示平方根
\sqrt[n]表示n次方根
\frac表示分式

### 上下标记

\overline{}, \underline{} 分别在表达式上、下方画出水平线 
\overbrace{}^{}, \underbrace{}_{} 分别在表达式上、下方给出一个水平的大括号

$\overbrace{1+2+\cdots+n}^{n个}$   $\underbrace{a+b+\cdots+z}_{26}$

### 向量

\vec表示向量，\overrightarrow表示箭头向右的向量，\overleftarrow表示箭头向左的向量

### 积分、极限、求和、乘积

\int表示积分，\lim表示极限， \sum表示求和，\prod表示乘积，^、_表示上、下限
$$
\lim_{x \to \infty} x^2_{22} - \int_{1}^{5}x\mathrm{d}x + \sum_{n=1}^{20} n^{2} = \prod_{j=1}^{3} y_{j}  + \lim_{x \to -2} \frac{x-2}{x}
$$

 ### 三圆点

\ldots点位于基线上，\cdots点设置为居中，\vdots使其垂直，\ddots对角线排列

### 重音符号

$ \hat{x} $  $ \bar{x} $  $ \tilde{x} $

### 矩阵

| matrix  | $ \begin{matrix} 1 & 2 & \cdots \\ 3 & 4 & \cdots \\ \vdots  & \vdots & \ddots \\ \end{matrix} $ |
| :-----: | :----------------------------------------------------------: |
| bmatrix | $ \begin{bmatrix} 1 & 2 & \cdots \\ 3 & 4 & \cdots \\ \vdots  & \vdots & \ddots \\ \end{bmatrix} $ |
| vmatrix | $ \begin{vmatrix} 1 & 2 & \cdots \\ 3 & 4 & \cdots \\ \vdots  & \vdots & \ddots \\ \end{vmatrix} $ |
| pmatrix | $ \begin{pmatrix} 1 & 2 & \cdots \\ 3 & 4 & \cdots \\ \vdots  & \vdots & \ddots \\ \end{pmatrix} $ |

### 希腊字母

$ \alpha^{2} + \beta = \Theta  $

### 多行公式

#### 公式组合

通过cases环境实现公式的组合，&分隔公式和条件，还可以==通过\limits来让x→0位于lim的正下方而非默认在lim符号的右下方显示==[^注1]

$$
D(x) = \begin{cases}
\lim\limits_{x \to 0} \frac{a^x}{b+c}, & x<3 \\
\pi, & x=3 \\
\int_a^{3b}x_{ij}+e^2 \mathrm{d}x,& x>3 \\
\end{cases}
$$

[^注1]:tex中仅单行模式会这样

#### 拆分单个公式

通过split环境实现公式拆分

$$
\begin{split}
\cos 2x &= \cos^2x - \sin^2x \\
&=2\cos^2x-1
\end{split}
$$

#### 公式对齐

| 环境    | 是否进入数学模式 | 是否接受可选参数 | 是否占满整行 | 是否产生编号 | 备注                                 |
| ------- | ---------------- | ---------------- | ------------ | ------------ | ------------------------------------ |
| align   | 是               | 否               | 是           | 是           | align* 不产生编号，其他与 align 相同 |
| aligned | 否               | 是               | 否           | 不可用       |                                      |

align是环境，它和equation都是一样的地位，多行公式均占用公式编号

align*意味着对应的多行公式不标号

aligned通常和equation环境配合使用，其放在equation环境之中，意味着多行公式只占用当前equation环境的一个编号
$$
\begin{align}
x + y + z &= 0 \\
y - z &= 1
\end{align}
$$


# References

LaTeX 公式篇 - Aaron Zhu的文章 - 知乎 https://zhuanlan.zhihu.com/p/110756681

LaTeX中的algin，align*和aligned都有什么区别？ - 知乎 https://www.zhihu.com/question/478714967
