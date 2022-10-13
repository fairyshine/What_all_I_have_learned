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
