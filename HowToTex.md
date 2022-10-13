# Latex的使用速查笔记

## 简介

### 排版语言

LaTeX是使用TeX这种宏语言所编写的一集宏，并作为一种格式固定下来。
使用 LaTeX 进行排版的理念是==「样式与内容分离」==。

### 排版引擎(编译器)

是编译源代码并生成文档的程序，如 pdfTeX、XeTeX 和 LuaTeX 等。有时也称其为 **编译器**。

如果我们将 plain TeX 与 LaTeX 的关系比作 C 和 C++ 的关系，那么 pdfTeX、XeTeX 和 LuaTeX 之间的关系就可以比作是 GCC、LLVM 和 Clang 之间的关系。

- **pdfTeX**：直接生成 PDF，支持 micro-typography；
- **XeTeX**：支持 Unicode、OpenType 与复杂文字编排（CTL），**支持中文**；
- **LuaTeX**：支持 Unicode、OpenType，内联 Lua，**支持中文**。

### TeX发行版

一个 TeX 发行版是 TeX 排版引擎、支持排版的文件（基本格式、LaTeX 宏包、字体等）以及一些辅助工具的集合。

各式各样的 TeX 发行版经过十多年的发展，大浪淘沙，现今的两个主流发行版为：

- **TeX Live** 由类 UNIX 系统上的 teTeX 发展并取而代之，最终成为「跨平台」的 TeX 发行版。TeX Live 自 2011 年起以年份作为发行版的版本号，保持了一年一更的频率。**MacTeX** 是 macOS（OS X）系统下的一个定制化的 TeX Live 版本，与 TeX Live 同步更新。
- **MiKTeX** 是主要用于 Windows 平台的一个稳定发展的 TeX 发行版，和 TeX Live 相比，它只会安装用得到的宏包，所以体积较小。在中国大陆曾经发行过“CTeX 套装”，它是一个经过本地化配置的 MiKTeX，不过其配置较为过时，也不再有更新支持，使用起来可能有诸多问题，现已不推荐使用。

## LaTeX文档基本结构

LaTeX 源代码以一个 `\documentclass` 命令作为开头，它指定了文档使用的 **文档类**。`\begin{document}...\end{document}` 环境[^注1]当中的内容是文档正文。

在 `\documentclass` 和 `\begin{document}` 之间的位置称为 **导言区**。在导言区中常会使用 `\usepackage` 命令调用宏包，还会进行文档的全局设置，例如设置页面大小、页眉页脚样式、章节标题样式等等。

接在 `%` 符号后面的内容是 **注释**，它不会在文档中被渲染出来。如果你想在文档中显示出 `%` 这个字符，可以在前面加 **反斜杠**，也就是 `\%`。

以 **反斜杠** 开头，以第一个 **空格或非字母** 字符结束的一串文字，被称为 **控制序列**，也可以称为 **命令** 或 **标记**，请注意命令是 **区分大小写** 的。

```latex
\documentclass{...} % ... 为某文档类
% 导言区
\begin{document}
% 正文内容
\end{document}
% 此后内容会被忽略
```

[^注1]:\begin{}...\end{}之间的内容属于同一个环境

### 经典布局

#### 单文档

一篇经典的包含各级标题的文章的结构就像这样。

```latex
\documentclass[UTF8]{ctexart}

% 导言区，只是定义一些信息，不会被渲染
\title{你好，world!}  % 标题
\author{Liam}  % 作者
\date{\today}  % 时间设为今天

% 正文内容
\begin{document}

\maketitle  % 渲染标题、作者和时间等信息
\tableofcontents  % 目录

% 各级标题和内容
\section{你好中国}
中国在East Asia.

\subsection{Hello Beijing}
北京是capital of China.

\subsubsection{Hello Dongcheng District}

\paragraph{Tian'anmen Square}
is in the center of Beijing

\subparagraph{Chairman Mao}
is in the center of 天安门广场。

\subsection{Hello 苏州}

\paragraph{苏州大学} is one of the best university in 江苏。

\end{document}
```

#### 多文档

我们也可以使用 `\include` 之类的命令进行 **分文档编译**。

```latex
\documentclass{book}
% 导言区，加载宏包和各项设置，包括参考文献、索引等
\usepackage{makeidx} % 调用 makeidx 宏包，用来处理索引
\makeindex % 开启索引的收集
\bibliographystyle{plain} % 指定参考文献样式为 plain
\begin{document}
\frontmatter % 前言部分
\maketitle % 标题页
\include{preface} % 前言章节 preface.tex
\tableofcontents
\mainmatter % 正文部分
\include{chapter1} % 第一章 chapter1.tex
\include{chapter2} % 第二章 chapter2.tex
...
\appendix % 附录
\include{appendixA} % 附录 A appendixA.tex
...
\backmatter % 后记部分
\include{epilogue} % 后记 epilogue.tex
\bibliography{books} % 利用 BibTeX 工具从数据库文件 books.bib 生成参考文献
\printindex % 利用 makeindex 工具生成索引
\end{document}
```

### 排版策略

==[htbp]==浮动格式：
『h』当前位置。将图形放置在正文文本中给出该图形环境的地方。如果本页所剩的页面不够，这一参数将不起作用。
『t』顶部。将图形放置在页面的顶部。
『b』底部。将图形放置在页面的底部。
『p』浮动页。将图形放置在一只允许有浮动对象的页面上。

在table或者figure 后加 ==[!htb]== 是系统忽略“美学”标准,把表格和图片插入到你的代码中,是动的,但是不加感叹号,它就是按顺序选择h(此处),t(上方),b(下方),所以为了让图片随着你的代码移动,最好加一个[!htb]

==[H]==

## 文档类 \documentclass[]{}

\documentclass[(编码类型)]{文档类型}

\documentclass[UTF8]{ctexart}

一个最简单的英文文档是使用了 `\documentclass{article}` 文档类的 latex 源码。

```text
\documentclass{article}
\begin{document}
``Hello world!'' from \LaTeX.
\end{document}
```

一个最简单的中文文档是使用了 `\documentclass[UTF8]{ctexart}` 文档类的 latex 源码。

```text
\documentclass[UTF8]{ctexart}
\begin{document}
“你好，世界！”来自 \LaTeX{} 的问候。
\end{document}
```

其中 `[UTF8]` 是 **文档可选参数**，用以全局规定一些排版参数。

另外的例子是，`[11pt,twoside,a4paper]` 指定纸张为 A4 大小，基本字号为 11pt，双面
排版。

## 宏包 \usepackage[]{}

`\usepackage[⟨options⟩]{⟨package-name⟩}`

![LaTeX常见宏包](pic/LaTeX常见宏包.jpg)

## 常用环境速查

#### 列表和枚举：

```latex
\begin{enumerate} %有序列表
  \item Da Vinci
        \begin{itemize} %无序列表
          \item \textit{The Last Supper}
          \item \textit{Salvator Mundi}
          \item \textit{Mona Lisa}
        \end{itemize}
  \item Shakespeare
        \begin{itemize}
          \item \textit{Macbeth}
          \item \textit{Hamlet}
          \item \textit{King Lear}
          \item \textit{Othello}
        \end{itemize}
\end{enumerate}
```

#### 图片：

```latex
% 不是 graphics，放到导言区
\usepackage{graphicx}
% 可以统一指定图片路径
\graphicspath{{./images/}}

\begin{figure}
  \centering
  % 可指定宽度、高度等选项
  \includegraphics[...]{fudan-logo.pdf}
  \caption{Logo of Fudan University}
  \label{fig:fudan-logo}
\end{figure}
```

#### 表格：

```latex
\usepackage{booktabs}  % 三线表
\begin{table}
  \caption{Population Census of China}
  \label{tab:china-population}
  % 列格式：c 居中，l 左对齐，r 右对齐
  \begin{tabular}{cc}
    \toprule
    Year     & Population \\
    \midrule
    1953     & 6.0        \\
    ... 2020 & 14.1       \\
    \bottomrule
  \end{tabular}
\end{table}
```

#### 公式、定理：

```latex
$ $  %单行公式

%多行公式  无编号/右侧打印编号
\begin{displaymath}...\end{displaymath}  简写为 \[...\]
\begin{equation}...\end{equation} 
```

```latex
\usepackage{amsthm}
% 需要预先定义
\newtheorem{theorem}{定理}
\newtheorem*{remark}{评论}  % 不编号

\begin{theorem}[Fermat]
  $a^n+b^n=c^n$ has no positive...
\end{theorem}
\begin{proof}
  % 证明后面会有 QED 符号 
  It's obvious.
\end{proof}
\begin{remark}
  The cases $n=1$ and $n=2$...
\end{remark}
```

#### 代码高亮：

```latex
\usepackage{listings}
\usepackage{xcolor}
\lstset{
    columns=fixed,       
    numbers=left,                                        % 在左侧显示行号
    frame=none,                                          % 不显示背景边框
    backgroundcolor=\color[RGB]{245,245,244},            % 设定背景颜色
    keywordstyle=\color[RGB]{40,40,255},                 % 设定关键字颜色
    numberstyle=\footnotesize\color{darkgray},           % 设定行号格式
    commentstyle=\it\color[RGB]{0,96,96},                % 设置代码注释的格式
    stringstyle=\rmfamily\slshape\color[RGB]{128,0,0},   % 设置字符串格式
    showstringspaces=false,                              % 不显示字符串中的空格
    language=c++,                                        % 设置语言
}

% 引入文件的写法
% \lstinputlisting[language=c++]{main.cpp}
% 直接写法
\begin{lstlisting}
  #include <iostream>
  int main()
  {
      std::cout << "Hello, World!" << std::endl;
  }  
\end{lstlisting}
```

## 语法详解

### 文本格式

\textbf 加粗
\textit 斜体
\underline 下划线
两个回车  换行

### 章 和 节

\section{}
\subsection{}
\subsubsection{}

文档类为book时，可加入 \part > \chapter > \section

### 图片

\usepackage{graphicx}
\includegraphics[]{}

---

# MacTeX 安装相关

在线使用LaTeX https://www.overleaf.com/

官网下载 / brew

1. TeXShop：这是一个 TeX 的前端，是 TeX 的编辑器和预览器。新手可以使用这个来学习。（老手一般都有自己喜欢的编辑器）
2. TeX Live Utility：这个实用工具可以通过网络更新 TeX Live 2022 包，以及配置 TeX 中的页面大小。
3. LaTeXiT：这是一个简单的 LaTeX 图形应用程序，但是一般被当作方程编辑器，用于写数学方程，因为可以很快看到写的公式的样式。
4. BibDesk：这是一个用于编辑和管理参考书目的工具。
5. 在“应用程序”里多了一个“Docs and Spell Utilities”，这个是一些介绍文档，有空可以看看。
   

额外安装TexStudio作为编辑器。









# References

LaTeX入门（1） - 小流星的文章 - 知乎 https://zhuanlan.zhihu.com/p/101804051

🌟第 4 期、写作：TeX、LaTeX 与排版系统 - 一只方橙的文章 - 知乎 https://zhuanlan.zhihu.com/p/496078810

LaTeX 与 TeX 有什么本质区别？ - 刘海洋的回答 - 知乎 https://www.zhihu.com/question/49681542/answer/117303280

MacTeX 小笔记 - ZhongUncle的博客 - CSDN https://blog.csdn.net/qq_33919450/category_11786609.html
