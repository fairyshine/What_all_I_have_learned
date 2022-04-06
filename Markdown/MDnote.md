# 怎么写Markdown笔记？

[toc]

## 1 MD基本语法

Markdown 是一种轻量级标记语言，它允许人们使用易读易写的纯文本格式编写文档。

### 标题

```markdown
# 这是一级标题
## 这是二级标题
### 这是三级标题
```

### 强调

_这会是 斜体 的文字_
__这会是 粗体 的文字__
_你也 __组合__ 这些符号_
~~这个文字将会被横线删除~~

### 列表

无序列表

- Item 1
- Item 2
  - Item 2a
  - Item 2b

有序列表

1. Item 1
1. Item 2
1. Item 3
   1. Item 3a
   1. Item 3b

### 图片

```markdown
Format: ![Alt Text](url)
```

### 链接

[GitHub](https://github.com)

### 引用

正如 Kanye West 所说：
> We're living the future so
> the present is our past.

### 分割线

---
***
___

### 代码

```markdown
行内：`code`

行间：```language  {.line-numbers}可显示代码行数
    code
			```
```

### 任务列表

- [x] @mentions, #refs, [links](http://baidu.com), __formatting__, and <del>tags</del> supported
- [x] list syntax required (any unordered or ordered list supported)
- [x] this is a complete item
- [ ] this is an incomplete item

### 表格

First Header | Second Header
------------ | -------------
Content from cell 1 | Content from cell 2
Content in the first column | Content in the second column

## 2 扩展语法（MPE中可用）

### 表格扩展

a|b
-|-
>|c

a|b
-|-
c|e
d|^

### 上、下标

30^th^
H~2~O

### 脚注

Content [^1]
[^1]: Hi! This is a footnote

### 缩略 ？

_[HTML]: Hyper Text Markup Language
_[W3C]: World Wide Web Consortium
The HTML specification
is maintained by the W3C.

### 标记

==marked==

### CriticMarkup ？

添加 {++1++}
删除 {--2--}
替换 {~~1~>2~~}
注释 {>> 1 <<}
高亮 {==1==}{>>1<<}

### Admonition

!!! note This is the admonition title
    This is the admonition body

## 数学

$行内公式$
$$行间公式$$

### 导入外部文件

@import "https://需要raw.githubusercontent.网速com/shd101wyy/markdown-preview-enhanced/master/LICENSE.md"
