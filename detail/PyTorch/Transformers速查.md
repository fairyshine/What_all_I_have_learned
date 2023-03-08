# 基本流程

以**BERT**为例

~~~python
import torch
from transformers import AutoTokenizer
from transformers import AutoModel

tokenizer = AutoTokenizer.from_pretrained('bert')

self.PreTrained = AutoModel.from_pretrained('bert')

PTM = self.PreTrained(input_ids=input_ids,)#& attention_mask,token_type_ids,position_ids
PTM = PTM.last_hidden_state
~~~

模型相关api可查阅文档：
（1）模型的**输入**：
	https://huggingface.co/docs/transformers/v4.22.2/en/model_doc/bert#transformers.BertModel
（2）模型的**输出**：
	https://huggingface.co/docs/transformers/v4.22.2/en/main_classes/output#transformers.modeling_outputs.BaseModelOutputWithPoolingAndCrossAttentions

# accelerate库

## 基本流程

```python
from accelerate import Accelerator

accelerator = Accelerator(split_batches=True)

model, optimizer, data = accelerator.prepare(model, optimizer, data)

-         source = source.to(device)  不需要指定device
-         targets = targets.to(device)

-         loss.backward()
+         accelerator.backward(loss)
```

1.移除代码中所有的**to(device)**或者**cuda()**,accelerator会自动帮你处理。

2.你的训练dataloader会被分配到可用的GPU或TPU中，和原生的分布式一样，实际的batch size是你在dataloader中定义的batch size乘以GPU/TPU的数量。比如args.batch_size=16，有四张卡，一个训练loop中共有64个batch。如果你想和DataParallel一样，保持总体batch size不变，在初始化Accelerator时要加上**split_batches=True**

3.和训练相关的对象都要传递到**prepare**方法中

4.最后，用**accelerator.backward(loss)**替代**loss.backward()**

### 如何运行

配置

`accelerate config`

`accelerate env`

运行

`accelerate launch {script_name.py} --arg1 --arg2 ...`

`CUDA_VISIBLE_DEVICES=0 accelerate launch {script_name.py} --arg1 --arg2 ...`



## 细节补充

关于device

```python
- device = 'cuda'
+ device = accelerator.device
  model.to(device)
```
