**Machine Reading Comprehension 机器阅读理解**



参考：

[《Neural Machine Reading Comprehension: Methods and Trends》](https://www.mdpi.com/2076-3417/9/18/3698#cite)

https://zhuanlan.zhihu.com/p/443203758

https://blog.csdn.net/sdu_hao/category_9626814.html

# 定义

给定上下文 C 和问题 Q，

机器阅读理解任务要求模型通过学习函数 F 使得 A = F (C, Q) 给出问题 Q 的正确答案 A。

# 分类

## 完形填空

其特点为：

(i) 答案 A 是给定上下文 C 中的一个词或实体；

(ii) 问题 Q 是通过从给定的上下文 C 中删除一个单词或实体来生成的，使得 Q = C-A

## 多项选择

其特点为：

多项选择题的答案不限于上下文中的单词或实体，也就是可以不是出现在上下文中的。因此答案形式更加灵活，但该任务需要提供候选答案。

## 跨度提取

其特点为：

因为有些时候单词或者实体构成的答案还不够解答问题，因此需要从上下文中提取一些片段作为答案。

并且这些片段必须要从给定的上下文中提取出来的。

## 自由问答

其特点为：

答案A非常的开放，可以不来自于上下文C

## 举例

![MRC几种类别举例](pic/MRC几种类别举例.png)

# 通常的模型结构

Embeddings

特征提取：针对上下文C和问题Q，分别挖掘特征

上下文—问题交互：注意力机制提取C和Q之间的相关性

答案预测：

# BERT-MRC模型

