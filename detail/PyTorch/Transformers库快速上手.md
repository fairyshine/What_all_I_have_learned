

数据集：https://huggingface.co/datasets

模型：https://huggingface.co/models

1. 自回归：GPT2、Transformer-XL、XLNet
2. 自编码：BERT、ALBERT、RoBERTa、ELECTRA
3. Seq2Seq：BART、Pegasus、T5

官方文档：https://huggingface.co/docs

# 安装

共有transformers、datasets、tokenizers三个库，前两个必装，用pip即可。

# 框架介绍

![Transformers库框架应用流程](/Users/wumengsong/Documents/note/QuickNote/detail/PyTorch/pic/Transformers库框架应用流程.jpg)

*数据处理 process data*            **Tokenizers**

*模型应用 apply a model*         **Transformer**

*做出预测 make predictions*     **Head**



# 使用字典和分词工具

## 加载tokenizer准备语料

### 通用(Auto Classes)

```python
from transformers import AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained('bert-base-chinese')
```

### BertModel

一般使用通用方法即可，基于tokenizers库的rust实现方式，效率更高。

```python
from transformers import BertTokenizer

tokenizer = BertTokenizer.from_pretrained('bert-base-chinese')
```

## 运行tokenizer分词操作

encode、decode操作

### .encode

```python
#编码两个句子
out = tokenizer.encode(
    string1,
    string2,

    #当句子长度大于max_length时,截断
    truncation=True,

    #一律补pad到max_length长度
    padding='max_length',
    add_special_tokens=True,
    max_length=30,
    return_tensors=None,
)


tokenizer.decode(out)
```

### .encode_plus

```python
#增强的编码函数
out = tokenizer.encode_plus(
    text=sents[0],
    text_pair=sents[1],

    #当句子长度大于max_length时,截断
    truncation=True,

    #一律补零到max_length长度
    padding='max_length',
    max_length=30,
    add_special_tokens=True,

    #可取值tf,pt,np,默认为返回list
    return_tensors=None,

    #返回token_type_ids
    return_token_type_ids=True,

    #返回attention_mask
    return_attention_mask=True,

    #返回special_tokens_mask 特殊符号标识
    return_special_tokens_mask=True,

    #返回offset_mapping 标识每个词的起止位置,这个参数只能BertTokenizerFast使用
    #return_offsets_mapping=True,

    #返回length 标识长度
    return_length=True,
)

#input_ids 就是编码后的词
#token_type_ids 第一个句子和特殊符号的位置是0,第二个句子的位置是1
#special_tokens_mask 特殊符号的位置是1,其他位置是0
#attention_mask pad的位置是0,其他位置是1
#length 返回句子长度
for k, v in out.items():
    print(k, ':', v)

tokenizer.decode(out['input_ids'])
```

input_ids : [101, 6848, 2885, 4403, 3736, 5709, 1736, 4638, 1333, 1728, 2218, 3221, 3175, 912, 511, 102, 5011, 6381, 3315, 4638, 7241, 4669, 4802, 2141, 4272, 511, 102, 0, 0, 0] token_type_ids : [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 0, 0] special_tokens_mask : [1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1] attention_mask : [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 0, 0] length : 30

'[CLS] 选 择 珠 江 花 园 的 原 因 就 是 方 便 。 [SEP] 笔 记 本 的 键 盘 确 实 爽 。 [SEP] [PAD] [PAD] [PAD]'

### .batch_encode_plus

```python
#批量编码句子
out = tokenizer.batch_encode_plus(
    batch_text_or_text_pairs=[sents[0], sents[1]],
    add_special_tokens=True,

    #当句子长度大于max_length时,截断
    truncation=True,

    #一律补零到max_length长度
    padding='max_length',
    max_length=15,

    #可取值tf,pt,np,默认为返回list
    return_tensors=None,

    #返回token_type_ids
    return_token_type_ids=True,

    #返回attention_mask
    return_attention_mask=True,

    #返回special_tokens_mask 特殊符号标识
    return_special_tokens_mask=True,

    #返回offset_mapping 标识每个词的起止位置,这个参数只能BertTokenizerFast使用
    #return_offsets_mapping=True,

    #返回length 标识长度
    return_length=True,
)

#input_ids 就是编码后的词
#token_type_ids 第一个句子和特殊符号的位置是0,第二个句子的位置是1
#special_tokens_mask 特殊符号的位置是1,其他位置是0
#attention_mask pad的位置是0,其他位置是1
#length 返回句子长度
for k, v in out.items():
    print(k, ':', v)

tokenizer.decode(out['input_ids'][0]), tokenizer.decode(out['input_ids'][1])
```

input_ids : [[101, 6848, 2885, 4403, 3736, 5709, 1736, 4638, 1333, 1728, 2218, 3221, 3175, 912, 102], [101, 5011, 6381, 3315, 4638, 7241, 4669, 4802, 2141, 4272, 511, 102, 0, 0, 0]] token_type_ids : [[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0], [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]] special_tokens_mask : [[1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1], [1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1]] length : [15, 12] attention_mask : [[1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1], [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 0, 0]]

('[CLS] 选 择 珠 江 花 园 的 原 因 就 是 方 便 [SEP]', '[CLS] 笔 记 本 的 键 盘 确 实 爽 。 [SEP] [PAD] [PAD] [PAD]')

## 字典相关操作

### 获取字典

```python
#获取字典
zidian = tokenizer.get_vocab()

type(zidian), len(zidian), '月光' in zidian,
```

(dict, 21128, False)

### 向字典添加新词并编码

```python
#添加新词
tokenizer.add_tokens(new_tokens=['月光', '希望'])

#添加新符号
tokenizer.add_special_tokens({'eos_token': '[EOS]'})

zidian = tokenizer.get_vocab()

type(zidian), len(zidian), zidian['月光'], zidian['[EOS]']
```

(dict, 21131, 21128, 21130)

```python
#编码新添加的词
out = tokenizer.encode(
    text='月光的新希望[EOS]',
    text_pair=None,

    #当句子长度大于max_length时,截断
    truncation=True,

    #一律补pad到max_length长度
    padding='max_length',
    add_special_tokens=True,
    max_length=8,
    return_tensors=None,
)

print(out)

tokenizer.decode(out)
```

[101, 21128, 4638, 3173, 21129, 21130, 102, 0]

'[CLS] 月光 的 新 希望 [EOS] [SEP] [PAD]'

# 数据集操作

从官方Hub加载

```python
from datasets import load_dataset

dataset = load_dataset(path='seamew/ChnSentiCorp')

dataset
```

DatasetDict({    **train**: Dataset({        features: ['text', 'label'],        num_rows: 9600    })    **validation**: Dataset({        features: ['text', 'label'],        num_rows: 1200    })    **test**: Dataset({        features: ['text', 'label'],        num_rows: 1200    }) })

简单操作

```python
#取出训练集
dataset = dataset['train']

dataset

#查看一个数据
dataset[0]
```

Dataset({    features: ['text', 'label'],    num_rows: 9600 })

{'text': '选择珠江花园的原因就是方便，有电动扶梯直接到达海边，周围餐馆、食廊、商场、超市、摊位一应俱全。酒店装修一般，但还算整洁。 泳池在大堂的屋顶，因此很小，不过女儿倒是喜欢。 包的早餐是西式的，还算丰富。 服务吗，一般', 'label': 1}

## 加载本地数据文件

对于huggingface官方提供的数据集

```python
#加载
datasets.load_from_disk('')
#保存
.save_to_disk(dataset_dict_path='')
```

各种格式数据集

csv

```python
#保存
.to_csv(path_or_buf='')

#加载
datasets.load_dataset(path='csv',data_files='',split='')
```

json

```python
#保存
.to_json(path_or_buf='')

#加载
datasets.load_dataset(path='json',data_files='',split='')
```

# 使用评价函数

```python
from datasets import list_metrics

#列出评价指标
metrics_list = list_metrics()

len(metrics_list), metrics_list
```

```python
from datasets import load_metric

#加载一个评价指标
metric = load_metric('glue', 'mrpc')

print(metric.inputs_description)
```

```python
#计算一个评价指标
predictions = [0, 1, 0]
references = [0, 1, 1]

final_score = metric.compute(predictions=predictions, references=references)

final_score
```

# 使用管道函数

transformers库的最简单使用方式，不适合自定义、大批量操作

https://huggingface.co/docs/transformers/pipeline_tutorial

# Trainer-再训练api

1 数据集

dataset_train

dataset_test 

2 模型

```python
from transformers import AutoModelForSequenceClassification

#加载模型
model = AutoModelForSequenceClassification.from_pretrained('bert-base-cased',
                                                           num_labels=2)
```

3 评价函数

```python
import numpy as np
from datasets import load_metric
from transformers.trainer_utils import EvalPrediction

#加载评价函数
metric = load_metric('accuracy')

#定义评价函数
def compute_metrics(eval_pred):
    logits, labels = eval_pred
    logits = logits.argmax(axis=1)
    return metric.compute(predictions=logits, references=labels)
```

4 设置参数，并初始化Trainer

```python
from transformers import TrainingArguments, Trainer

#初始化训练参数
args = TrainingArguments(output_dir='./output_dir', evaluation_strategy='epoch')
args.num_train_epochs = 1
args.learning_rate = 1e-4
args.weight_decay = 1e-2
args.per_device_eval_batch_size = 32
args.per_device_train_batch_size = 16

#初始化训练器
trainer = Trainer(
    model=model,
    args=args,
    train_dataset=dataset_train,
    eval_dataset=dataset_test,
    compute_metrics=compute_metrics,
)
```

5 训练、保存模型

```python
#评价模型
trainer.evaluate()

#训练
trainer.train()

#评价模型
trainer.evaluate()

#保存模型
trainer.save_model(output_dir='./output_dir')
```

6 使用

```python
model.load_state_dict(torch.load('./output_dir/pytorch_model.bin'))
```



# 实战例子

## 1 文本情感分类

## 2 中文填空

## 3 中文句子关系推断

## 4 Trainer再训练



# 七个实战任务

## 1 预测最后一个词

## 2 预测中间词

## 3 阅读理解

## 4 长求总/文本摘要

## 5 分类

## 6 命名实体识别

## 7 翻译

