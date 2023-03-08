# torch.tensor

## 新建张量

==torch.full(size,fill_value)==  以值fill_value创建大小为size的tensor

==torch.arange(start=0,end,step=1)== 以等差数列创建tensor

==torch.eq(input,other) /Tensor.eq== Computes input$=$other element-wise.

==torch.ne(input,other)  /Tensor.ne== Computes input$\ne$other element-wise.

==torch.ge(input,other)  /Tensor.ge== Computes input$\geq$other element-wise.

==torch.lt(input,other)  /Tensor.lt== Computes input$<$other element-wise.

==Tensor.new_full(size,fill_value)== 以值fill_value创建大小同当前tensor的新tensor

==Tensor.new_ones(size)== 以值1创建大小同当前tensor的新tensor

==Tensor.new_zeros(size)== 以值0创建大小同当前tensor的新tensor

## 查看信息

==torch.topk(input,k,dim=None,largest=True,sorted=True)== 返回tensor中最大/小的k个元素

==Tensor.size(dim=None)== 返回tensor维度元组

==torch.gather(input,dim,index)  /Tensor.gather()== 从原tensor中获取指定dim和指定index的数据

```python
out[i][j][k] = input[index[i][j][k]][j][k]  # if dim == 0
out[i][j][k] = input[i][index[i][j][k]][k]  # if dim == 1
out[i][j][k] = input[i][j][index[i][j][k]]  # if dim == 2
```

==Tensor.nonzero(as_tuple=)== 以二维点坐标/一维序号返回tensor中非零元素的位置(as_tuple=False/True)

## 操作张量

==torch.stack(tensor,dim=0)== 沿某一新维度拼接tensor**s**

==torch.cat(tensors,dim=0)==  沿某一现有维度拼接tensor**s**

==torch.where(condition,x,y)== 返回tensor，满足条件取tensor x对应位置元素，否则y



==Tensor.item()== 单元素tensor转化为标量

==Tensor.sum(dim)==

==Tensor.max(dim)==



==Tensor.masked_fill_(mask,value)== 将tesnsor中mask为True的部分填充为value

==Tensor.masked_select(mask)== 返回一维tensor，元素为原tesnor对应mask的tensor的bool值为True的那些

==Tensor.view(shape=)==  相当于np的reshape

==Tensor.repeat_interleave(repeats)== 重复tensor中的元素
	repeats: 重复次数 ,int or Tensor(为每个元素设置不同的重复次数)

==Tensor.index_select(input,dim,index)== 获得原tensor指定维度指定index的新tensor(占用新的存储空间，其他维度不变)

==Tensor.cumsum(dim)== 按某维度以前项和为新元素的tensor

==Tensor.scatter_(dim,index,src)== Writes all values from the tensor `src` into `self` at the indices specified in the `index` tensor. 

```
self[index[i][j][k]][j][k] = src[i][j][k]  # if dim == 0
self[i][index[i][j][k]][k] = src[i][j][k]  # if dim == 1
self[i][j][index[i][j][k]] = src[i][j][k]  # if dim == 2
```

![img](pic/torch scatter_.jpg)



### 类型转换

==Tensor.tolist()== 将tensor转化为list或数字 

### squeeze、unsqueeze——改变维度

==Tensor.squeeze==(input, dim=None, ***, out=None) → Tensor #在指定index减少维度
	另有：tensor.squeeze(dim)
	torch.squeeze(input)会自动去除size=1的维度

==Tensor.unsqueeze==(input, dim) → Tensor #在指定index增加维度
	另有：tensor.unsqueeze(dim)

# torch.nn

## Embedding

==torch.nn.Embedding.from_pretrained==(embeddings= ,freeze= ,)
==torch.nn.Embedding==(num_embeddings= ,embedding_dim= )

## 多卡-DP层/DDP层

推荐使用accelerate库，是基于ddp的封装

### DP

==torch.nn.DataParallel==

单机多卡；写法简单；不推荐

```python
model = torch.nn.DataParallel(model.cuda())
```

### DDP

==torch.nn.parallel.DistributedDataParallel==

```python
import torch
import torch.distributed as dist

from torch.utils.data import Dataloader

# local_rank这个参数是底层传的，我们只要预留好位置接受即可
parser = argparse.ArgumentParser()
parser.add_argument('--local_rank',
                    default=0,
                    type=int,
                    help='node rank for distributed training')
args = parser.parse_args()
print(args.local_rank)

# 
if args.local_rank != -1:
    torch.cuda.set_device(args.local_rank)
    device = torch.device("cuda", args.local_rank)
    dist.init_process_group(backend='nccl')  # 开启后端通信

...
# model采用DistributedDataParallel进行包装
model.to(device)  # 封装之前要把模型移到对应的gpu
num_gpus = torch.cuda.device_count()
if num_gpus > 1:
    print('use {} gpus!'.format(num_gpus))
    model = torch.nn.parallel.DistributedDataParallel(model,
                                                      device_ids=[args.local_rank],
                                                      output_device=args.local_rank,
                                                      find_unused_parameters=True)

...
# 改用DistributedSampler
train_sampler = torch.utils.data.distributed.DistributedSampler(tokenized_datasets["train"])
train_dataloader = DataLoader(tokenized_datasets["train"],
                              batch_size=32,
                              shuffle=(train_sampler is None),
                              sampler=train_sampler,
                              pin_memory=False)
```

在使用时，调用 torch.distributed.launch 启动器启动：

`CUDA_VISIBLE_DEVICES=0,1,2,3 python -m torch.distributed.launch --nproc_per_node=4 main.py`

#### 使用 torch.multiprocessing 取代启动器

```python
# main.py
import torch
import torch.distributed as dist
import torch.multiprocessing as mp

mp.spawn(main_worker, nprocs=4, args=(4, myargs))

def main_worker(proc, nprocs, args):

   dist.init_process_group(backend='nccl', init_method='tcp://127.0.0.1:23456', world_size=4, rank=gpu)
   torch.cuda.set_device(args.local_rank)

   train_dataset = ...
   train_sampler = torch.utils.data.distributed.DistributedSampler(train_dataset)

   train_loader = torch.utils.data.DataLoader(train_dataset, batch_size=..., sampler=train_sampler)

   model = ...
   model = torch.nn.parallel.DistributedDataParallel(model, device_ids=[args.local_rank])

   optimizer = optim.SGD(model.parameters())

   for epoch in range(100):
      for batch_idx, (data, target) in enumerate(train_loader):
          images = images.cuda(non_blocking=True)
          target = target.cuda(non_blocking=True)
          ...
          output = model(images)
          loss = criterion(output, target)
          ...
          optimizer.zero_grad()
          loss.backward()
          optimizer.step()
```

## train / eval

==torch.nn.Module.train()==
	-mode：bool,True,设置为训练模式（False时等价于eval()）
==torch.nn.Moduel.eval()==

> **(1) model.train()**
> **启用 Batch Normalization 和 Dropout。**
> model.train()作用：对BN层，保证BN层能够用到每一批数据的均值和方差，并进行计算更新；对于Dropout，model.train()是随机取一部分网络连接来训练更新参数。
> **(2) model.eval()**
> **不启用 Batch Normalization 和 Dropout。**
> model.eval()是保证BN层直接利用之前训练阶段得到的均值和方差，即测试过程中要保证BN层的均值和方差不变；对于Dropout，model.eval()是利用到了所有网络连接，即不进行随机舍弃神经元。
> **(3) with torch.no_grad()**
> 无论是train() 还是eval() 模式，各层的gradient计算和存储都在进行且完全一致，只是在eval模式下不会进行反向传播。而with torch.no_grad()则主要是用于停止autograd模块的工作，以起到加速和节省显存的作用。它的作用是将该with语句包裹起来的部分停止梯度的更新，从而节省了GPU算力和显存，但是并不会影响dropout和BN层的行为。若想节约算力，可在test阶段带上torch.no_grad()，示例代码：
>
> ```python
> def test(model,dataloader):
> 	model.eval()  # 切换到测试模式
> 	with torch.no_grad():  #with下内容不进行grad计算
> 		...
> ```
>
> 【PyTorch】搞定网络训练中的model.train()和model.eval()模式 - 未来达摩大师的文章 - 知乎 https://zhuanlan.zhihu.com/p/494060986



# torch.utils.data

## Dataset

### 自定义数据集

==torch.utils.data.Dataset==  表示Map式数据集的抽象类，自定义数据集需继承
torch.utils.data.IterableDataset 表示Iterable式数据集的抽象类

#### Map式数据集

​	常用。Map式的数据集重写**getitem**(self, index),**len**(self) 两个内建方法，用来表示从索引到样本的映射（Map）.这样一个数据集dataset，举个例子，当使用dataset[idx]命令时，可以在你的硬盘中读取你的数据集中第idx张图片以及其标签（如果有的话）;len(dataset)则会返回这个数据集的容量。
​	必须：def \_\_getitem\_\_(self,index)
​	可选：def \_\_len\_\_(self)

#### Iterable式数据集

​	一个Iterable（迭代）式数据集是抽象类**data.IterableDataset**的子类，并且覆写了**iter**方法成为一个迭代器。这种数据集主要用于数据大小未知，或者以流的形式的输入，本地文件不固定的情况，需要以迭代的方式来获取样本索引。
​	必须：def \_\_iter\_\_(self)

### 预置数据集

==torch.utils.data.TensorDataset==(*tensors）

## Dataloader

==torch.utils.data.DataLoader==(dataset,)
	-batch_size：int,1,批量大小
	-shuffle：bool,False,打乱顺序
	-num_workers：int,0,多线程
	-collate_fn：一个batch的样本数据进行合并操作(输入：由一条条样本数据组成的列表)

> 两文读懂PyTorch中Dataset与DataLoader（一）打造自己的数据集 - Chenllliang的文章 - 知乎 https://zhuanlan.zhihu.com/p/105507334
>
> 一文弄懂Pytorch的DataLoader, DataSet, Sampler之间的关系 - marsggbo的文章 - 知乎 https://zhuanlan.zhihu.com/p/76893455
