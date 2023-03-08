# 资料汇总

![NLP范式变迁](pic/NLP范式变迁.jpg)

1. Feature Engineering：即使用文本特征，例如词性，长度等，在使用机器学习的方法进行模型训练。（无预训练语言模型）
2. Architecture Engineering：在W2V基础上，利用深度模型，加上固定的embedding。（有固定预训练embedding，但与下游任务无直接关系）
3. Objective Engineering：在bert 的基础上，使用动态的embedding，在加上fine-tuning。（有预训练语言模型，但与下游任务有gap）
4. Prompt Engineering：直接利用与训练语言模型辅以特定的prompt。（有预训练语言模型，但与下游任务无gap）



# 1 自编码、自回归的语言模型

🔗：https://zhuanlan.zhihu.com/p/483785458

## 一、预训练简介

预训练主要分为两大分支，一支是**自编码语言模型**（**Autoencoder Language Model**），**自回归语言模型（Autoregressive** **Language Model）。**

**1 自编码语言模型（Autoencoder Language Model）**

BERT、ALBert、RoBERTa

自编码语言模型是对输入的句子随机Mask其中的单词，然后预训练过程的主要任务之一是根据上下文单词来预测这些被Mask掉的单词，那些被Mask掉的单词就是在输入侧加入的噪音。BERT就是典型的自编码类语言模型。

**优点**

它能比较自然地融入双向语言模型，同时看到被预测单词的上文和下文。

**缺点**

主要在输入侧引入[Mask]标记，导致预训练阶段和Fine-tuning阶段不一致的问题，因为Fine-tuning阶段是看不到[Mask]标记的。而Bert这种自编码模式，在生成类NLP任务中，就面临训练过程和应用过程不一致的问题，导致生成类的NLP任务到目前为止都做不太好。

**2 自回归语言模型（Autoregressive Language Model）**

ELMO、XLnet、GPT1、GPT2

**自回归语言模型**是根据上文内容预测下一个可能的单词，就是常说的自左向右的语言模型任务，或者反过来也行，就是根据下文预测前面的单词。GPT 就是典型的自回归语言模型。

**优点**

其实跟下游NLP任务有关，比如生成类NLP任务，比如文本摘要，机器翻译等，在实际生成内容的时候，就是从左向右的，自回归语言模型天然匹配这个过程。

**缺点**

只能利用上文或者下文的信息，不能同时利用上文和下文的信息。

# 2 NLP中预训练模型的综述

NLP中预训练模型的综述1

🔗：https://zhuanlan.zhihu.com/p/353054197

NLP中预训练模型的综述2

🔗：https://zhuanlan.zhihu.com/p/353638297

## PTM历史、两代

第一代PTMs: 目标是学习到好的word embeddings（词向量，例如一个词对应到一个200维度的向量），然后模型本身就被丢弃了。例如Skip-Gram，GloVe。虽然这些pre-trained embeddings可以捕捉到词的“部分”语义信息，它们大多数是上下文无关的，无法捕捉到文脉中的更高级别的抽象和概念，例如**多义词，语法结构，语义角色，以及指代分析**。

第二代PTMs: 集中在学习基于文脉的词向量表示，保留模型本身。例如，CoVe, ELMo, OpenAI GPTx以及BERT。在应用到下游任务的时候，模型本身（例如多层Transformer's Encoder Layers)会被继续使用。

## 8种预训练任务类型

1. LM = 自回归语言模型, auto-regressive language models
2. MLM = 遮掩语言模型，masked language models
3. Seq2Seq MLM=序列到序列的遮掩语言模型，Sequence-to-Sequence Masked Language Model;
4. PLM = Permutated language model, 重新排列语言模型
5. DAE = 去噪自编码器，denoising auto-encoder
6. DIM = Deep InfoMax，深度-信息-最大化？不好翻译
7. NSP/SOP=next sentence prediction, sentence ordering prediction, 下一个句子预测，句子顺序预测；
8. RTD = replaced token detection, 被替换掉的token的检测

![预训练任务的损失函数](pic/预训练任务的损失函数.png)

第一，LM-Language Model，语言模型，自回归（从左向右）的基于马尔可夫假设的（目前的事实，只和之前的和历史上的事实相关，和未来的无关）。是一般在句子上建模，把一个序列的概率按照各个词的概率进行拆解；例如，p(I love you) = p(I)p(love|I)p(you|I, love)。

第二，MLM-Masked Language Model，掩码/遮掩语言模型（双向的），在输入序列（一般是句子为基本单位）上随机mask掉（单词用[MASK]替换掉），然后让被改造后的序列去预测被mask掉的是哪些词；例如，假设love被mask掉，那么是p(love|I [mask] you)的概率最大化为目标，即最小化-logp(love|I [mask] you)。

第三，Seq2Seq MLM，序列到序列遮掩语言模型（前一个序列双向，后一个序列单向），被遮掩的是一个phrase，即一个word span。例如，I live in New York. 这个句子，假设New York被遮掩，则要计算的是p(New|I live in [mask] [mask]) * p(York| I live in New [mask])。可以看到剩余的序列I live in都参与了，而当预测New的时候，输入条件是I live in [mask] [mask]，继续当预测York的时候，输入就变成了I live in New [mask]。这和MLM不同，MLM是类似p(New|I live in [mask] [mask] * p(York|I live in [mask] [mask])[其隔断了New York之间的依赖关系...]。

第四，PLM-permuted language model，(全)排列语言模型，基于一个新的排列（可能是随机生成的），然后在该排列的指导下，构建自回归形式下的概率分解。例如I love you，假设有个全排列是 2 1 3，即新的序列是love I you，那么概率就是p(love)p(I|love)p(you|love,I)。

第五，DAE-denoising autoencoder，去噪自编码器，DAE的损失函数和前面的MLM，Seq2Seq MLM，甚至PLM有一定的交集。参考图片自编码器中的，把一个区域抠掉，然后让剩下的图片去预测这个被遮掩的区域；或者是让图片经过down-sampling之后再up-sampling去比较自己和经过这个网络之后的输出（重建）。类似的，NLP中的DAE，可以是随机删除句子中的一些词，然后去预测哪些词被删除了，同样的，“增加，删除，修改，改变顺序”等方法下，对原来的序列进行扰乱（加噪声），然后通过神经网络去恢复原来的序列，从而让神经网络识别噪声并去噪。例如，I live in New York，不单单是New York被遮掩，I live in的顺序也可能被打乱为in I live，这样就是p(New| in I live [mask] [mask])p(York| in I live New [mask])这样的概率分解了。

第六，DIM-deep infomax，深度信息最大化，举例子说明，I live in New York，还是New York被mask掉，而且从语料库中sample出一个新的phrase，Ice Cream。则训练的目标一则是让预测出来的phrase无限接近真实短语New York，一则是让预测出来的短语和负例Ice Cream越远越好。负例可以不止一个，所以上面的公式中有个求和操作。

第七，NSP/SOP=next sentence prediction, sentence order prediction；下个句子的预测，以及句子顺序的预测；这两个都是document级别的。例如有三个句子s1, s2, s3，那么可以构建正例如<s1, s2>, <s2, s3>代表的是s2在s1的后面，s3在s2的后边。负例的话，可以有若干构造方法，例如随机从语料库中选择一个句子作为s2'。鉴于BERT里面这个目标的设定有些偏颇，后续有若干改进，例如ALBERT就认为，随机抽取的负例，因为和当前句子，如s1，可能topic都不一样（s1是关于养猪的，而s2'是关于美妆的），这种随机抽取的负例，难于很好的捕捉句子之间的内在的逻辑蕴含关系。好的负例可以是<s2, s1>，这样就保证了不受topic不一致的干扰，而专注于学习“逻辑蕴含关系”。哎呀，突然想到十多年前在吉林大学当老师，教离散数学的场景，当时的一个理解“蕴含”的例子是“如果食堂的饭菜好吃，那么太阳从西边出来了”-如果前提为假，那么你可以推论任何东西-而整个“命题”是True的。多么神奇。

第八，RTD = replaced token detection，被替换的token的检测。根据当前上下问，判定当前的token是否被替换掉了。输入的序列是双向可见的。和前面的DAE其实也有一定的交集。当一个序列中有若干被随机替换掉的token的时候，就有意思了，可以参考MLM或者Seq2Seq MLM的方法，一次性或者step-by-step判定（二元分类：被替换了=1vs. 是原来的=0）句子中的每个词的1/0标签。

![典型预训练模型列表](pic/典型预训练模型列表.png)

###  MLM: 掩码语言模型（遮盖语言模型）masked language model - MLM：

MLM最早是Taylor等人提出的，受的是”完形填空“的启发。之后BERT将其发扬光大了。为了可以双向训练==BERT==，Devlin等人提出在Transformer的Encoder的基础上，把输入的句子中的若干词用[MASK]遮盖起来，然后通过其他可见的词，来预测被遮盖的到底是哪些词。这里有个”脱节“的问题在于，在实际下游fine-tuning的时候，并没有[MASK]这个token出现在下游数据集中。为了缓解这个问题，BERT中提出：在80%的概率下，使用[MASK]这个token；然后在10%的概率下，使用任意token；以及在剩下的10%的概率下，使用原来的token。（缓兵之计，并不是完美的）。MLM常被当成分类问题（预测每个被遮掩位置的具体的token的种类，例如词表是30000的时候，则是类似multi-class classification并且|y|=30000。

### Seq2Seq MLM: 序列到序列的MLM (sequence-to-sequence MLM, or, Seq2Seq MLM)

除了上面的MLM之外（只使用Transformer的Encoder的部分），还有另外一种基于”**自回归**生成式模型“的方法，即把”被mask的sequence“扔给Encoder，然后再用一个decoder去逐个预测被mask的token。例如，==MASS, T5==属于此范畴。

### PLM: 排列语言模型 Permuted Language Modeling 

因为很多下游应用任务中，并没有包括[MASK]的文本序列，所以传统MLM和下游任务之间存在一定的沟壑。为了缓解该问题，Permuted Language Modeling (**PLM**)被提出（来自==XLNet==，neurips 2019论文，[https://arxiv.org/pdf/1906.08237.pdf](https://link.zhihu.com/?target=https%3A//arxiv.org/pdf/1906.08237.pdf)）。PLM这个任务是，首先对一个输入序列构造所有可能的全排列，然后选择其中一个全排列，此后，该全排列中的若干词被选择为target，而模型被训练为预测这些targets, 基于的是：剩余的tokens以及targets的原始的正确的位置信息。

## PTMs分类标准

1. 表示类型：上下文无关和上下文相关；
2. 架构：LSTM （例如ELMo), Transformer的encoder (例如BERT)，Transformer的Decoder （例如GPT)，以及同时使用Transformer的Encoder和Decoder （例如, Seq2Seq MLM)。
3. 预训练任务类型，如传统自回归LM，Masked-LM，Permuted-LM, DAE, CTL, 等。
4. 扩展：例如，知识强化的PTMs，多语言或者单语言PTMs，多模态PTMs，特定领域PTMs(例如Healthcare, Finance等），以及压缩PTMs。

# 3 生成式(预测式)  or  对比式——的预训练

https://zhuanlan.zhihu.com/p/432950958

![生成式-对比式预训练](pic/生成式对比式预训练.jpg)ps:这图来自CV界，但用在NLP这里也行，都是互通的。

## Predictive(Generative)

预测式无监督预训练简介（Predictive Pre-Training）

🔗：https://zhuanlan.zhihu.com/p/463354320

> 🌟个人理解：
>
> 预测式可大致再分为判别式(抽取式)和生成式。
>
> - 判别式(抽取式)：输出结果一定来自输入文本（context）
> - 生成式：输出结果来源未知，在词库中寻找最优结果生成



## Constractive

对比式无监督预训练简介（Contrastive Pre-training）

🔗：https://zhuanlan.zhihu.com/p/463840977



# PTM分类资料

## BERT

为什么 Bert 的三个 Embedding 可以进行相加？ - 苏剑林的回答 - 知乎 https://www.zhihu.com/question/374835153/answer/1042845667
