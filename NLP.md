[【🚀总结】NLP日常实践经验](detail/NLP/个人NLP实践经验.md)



[【🚀笔记】CS224N](detail/NLP/CS224N-2019/CS224N笔记.md)



# --PTMs--

Pre-training Models，NLP相关预训练语言模型

[【🚀资料】-PTM-NLP概览](detail/NLP/PTM概览.md)



![NLP范式变迁](pic/NLP范式变迁.jpg)

1. Feature Engineering：即使用文本特征，例如词性，长度等，在使用机器学习的方法进行模型训练。（无预训练语言模型）
2. Architecture Engineering：在W2V基础上，利用深度模型，加上固定的embedding。（有固定预训练embedding，但与下游任务无直接关系）
3. Objective Engineering：在bert 的基础上，使用动态的embedding，在加上fine-tuning。（有预训练语言模型，但与下游任务有gap）
4. Prompt Engineering：直接利用与训练语言模型辅以特定的prompt。（有预训练语言模型，但与下游任务无gap）



## 提示学习 prompt learning

[【🚀资料】提示学习概览](detail/NLP/提示学习概览.md)

[综述]鹏飞大神的Pre-train, Prompt, and Predict [1] - 迷途小书僮的文章 - 知乎 https://zhuanlan.zhihu.com/p/396098543
【NLP】Prompt Learning 超强入门教程 - sergio的文章 - 知乎 https://zhuanlan.zhihu.com/p/442486331
NLP 里 prompt engineering （设计一个问题的背景提示）有多重要? - 知乎 https://www.zhihu.com/question/439114659
一个小白如何学好prompt tuning? - 知乎 https://www.zhihu.com/question/509079916
Prompt 如何更好地应用于工业界？ - 知乎 https://www.zhihu.com/question/495040812
近代自然语言处理技术发展的“第四范式” - 刘鹏飞的文章 - 知乎 https://zhuanlan.zhihu.com/p/395115779

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
