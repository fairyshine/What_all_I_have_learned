[【🚀总结】NLP日常实践经验](detail/NLP/个人NLP实践经验.md)

[【🚀笔记】CS224N](detail/NLP/CS224N-2019/CS224N笔记.md)



# --PTMs--

Pre-training Models，NLP相关预训练语言模型

[【🎩总结】PTM-NLP](detail/NLP/PTM-NLP总结.md)

[【🚀资料】-PTM-NLP概览](detail/NLP/PTM概览.md)



## 提示学习 prompt learning

[【🚀资料】提示学习概览](detail/NLP/提示学习概览.md)



# --任务--

## 事件模式归纳

Event Schema Induction

[事件模式归纳相关研究简述- CSDN](https://blog.csdn.net/qq_27590277/article/details/124958441)

## NER 命名实体识别

从Muti-head,Biaffine到Globalpointer细数近年来的命名实体识别 - 我想了很多事的文章 - 知乎 https://zhuanlan.zhihu.com/p/375805722

## MRC 机器阅读理解

[【🤖️资料】MRC个人总结](detail/NLP/MRC个人总结.md)

## KPE 关键词提取

[【👓资料】KPE概览](detail/NLP/KPE概览.md)





# --方法--

## Self-Attention 自注意力

在Transformer的论文《Attention is all you need》中被熟知

通俗的解释：对序列编码表示的一个全局优化

> Self-Attention的作用就是全局关联权重，然后做输入的加权和。 例如我有三个词 A B C ，首先将ABC编码成向量， 然后进Attention层， 然后对ABC分别洗脑，告诉你其他两个词对你也很重要，你的心里要有他们，就这样每个词的重组就是给自己一点权重（最多），给其他两个人一点权重，然后组合成新的自己。https://zhuanlan.zhihu.com/p/449028081
>
> 
>
> The Annotated Transformer——基于PyTorch的复现注释
> 	https://nlp.seas.harvard.edu/2018/04/03/attention.html

## FGM 梯度对抗

实现：师兄的版本：http://192.168.126.124:9999/spico1026/ccks2022fewshotese/blob/main/src/task.py
博客：https://zhuanlan.zhihu.com/p/103593948
实现：https://wmathor.com/index.php/archives/1537/

## CRF

https://github.com/Jianwei-Lv/chinese-event-extraction-pytorch
