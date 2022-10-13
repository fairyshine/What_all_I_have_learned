# torch.tensor

==view==  #相当于np的reshape

## squeeze、unsqueeze——改变维度

==torch.squeeze==(input, dim=None, ***, out=None) → Tensor #在指定index减少维度
	另有：tensor.squeeze(dim)
	torch.squeeze(input)会自动去除size=1的维度

==torch.unsqueeze==(input, dim) → Tensor #在指定index增加维度
	另有：tensor.unsqueeze(dim)

# torch.nn

==torch.nn.Embedding.from_pretrained==(embeddings= ,freeze= ,)
==torch.nn.Embedding==(num_embeddings= ,embedding_dim= )



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

> 两文读懂PyTorch中Dataset与DataLoader（一）打造自己的数据集 - Chenllliang的文章 - 知乎 https://zhuanlan.zhihu.com/p/105507334
