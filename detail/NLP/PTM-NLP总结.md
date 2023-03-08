# TRANSFORMER

## 结构

图解结构

https://towardsdatascience.com/understanding-transformers-the-data-science-way-e4670a4ee076



### 整体结构

![Data Flow in a Transformer](pic/Data Flow in a Transformer.png)



### Encoder 结构

![Encoder — Input and Output shapes are the same](pic/Encoder — Input and Output shapes are the same.png)



#### position embedding

Positional encoding matrix for the first 300 and 3000 positions (*Image by author*)

![transformer300positions](pic/transformer300positions.png)

![transformer3000positions](pic/transformer3000positions.png)

#### A）Self-attention layer

![How Self-Attention Works ](pic/How Self-Attention Works .png)

![The Full multi-headed self-attention Layer](pic/The Full multi-headed self-attention Layer.png)

#### B）Position-wise feed-forward network

![Each word goes into the feed-forward network](pic/Each word goes into the feed-forward network.png)

### Decoder 结构

![Decoder Architecture](pic/Decoder Architecture.png)



## 预测流程动画

![Predicting with a greedy search using the Transformer](pic/Predicting with a greedy search using the Transformer.gif)



https://ai.googleblog.com/2017/08/transformer-novel-neural-network.html

The animation below illustrates how we apply the Transformer to machine translation. 

![transform20fps](pic/transform20fps.gif)

---



