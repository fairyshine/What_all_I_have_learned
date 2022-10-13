# 1 torch 向量(tensor)

Each torch.Tensor has a torch.dtype, torch.device, and torch.layout.

requires_grad:

- 在用户手动定义tensor时，参数requires_grad默认值是False。

- 而在Module中的层在定义时，相关tensor的requires_grad参数默认是True。

- 在计算图中，如果有一个输入的requires_grad是True，那么输出的requires_grad也是True。只有在所有输入的requires_grad都为False时，输出的requires_grad才为False。

backward():

- 只能对标量求导数（梯度）


detach():

- 返回一个新的tensor，并且这个tensor是从当前的计算图中分离出来的（截断计算图）。但是返回的tensor和原来的tensor是共享内存空间的。

### 向量操作

变形：

❌  view() （已过时）

✅  reshape()

- reshape方法更强大，可以认为a.reshape = a.view() + a.contiguous().view()。

- https://blog.csdn.net/Flag_ing/article/details/109129752

# 2 基础网络 torch.nn

## 2.1 线性层

CLASS torch.nn.Linear(
in_features, 
out_features, 
bias=True, 
device=None, dtype=None
)



## 2.2 其他基础层

### 池化层

CLASS torch.nn.MaxPool2d(kernel_size, stride=None, padding=0, dilation=1, return_indices=False, ceil_mode=False)



(N,C,H,W) 批量大小，通道数，图片高度，图片宽度

kernel_size ：表示做最大池化的窗口大小，可以是单个值，也可以是tuple元组

stride ：步长，可以是单个值，也可以是tuple元组

padding ：填充，可以是单个值，也可以是tuple元组

dilation ：控制窗口中元素步幅

return_indices ：布尔类型，返回最大值位置索引

ceil_mode ：布尔类型，为True，用向上取整的方法，计算输出形状；默认是向下取整。



### 归一化层

CLASS torch.nn.BatchNorm2d(num_features, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True, device=None, dtype=None)



num_features：一般输入参数为batch_size×num_features×height×width，即为其中特征的数量

eps：分母中添加的一个值，目的是为了计算的稳定性，默认为：1e-5

momentum：一个用于运行过程中均值和方差的一个估计参数（我的理解是一个稳定系数，类似于SGD中的momentum的系数）

affine：当设为true时，会给定可以学习的系数矩阵gamma和beta

## 2.3 卷积层

CLASS torch.nn.Conv2d(in_channels, out_channels, kernel_size, stride=1, padding=0, dilation=1, groups=1, bias=True, padding_mode='zeros', device=None, dtype=None)



groups:分组，输入、输出通道均分组，均需被其整除



![卷积层groups](./pic/卷积层groups.jpg)

## 2.4 循环层

nn.LSTM

初始化：

``` python
CLASS torch.nn.LSTM(*args, **kwargs)
```
![LSTM参数](pic/LSTM参数.png)

- input_size – The number of expected features in the input x
- hidden_size – The number of features in the hidden state h
- num_layers – Number of recurrent layers. E.g., setting num_layers=2 would mean stacking two LSTMs together to form a stacked LSTM, with the second LSTM taking in outputs of the first LSTM and - computing the final results. Default: 1
- bias – If False, then the layer does not use bias weights b_ih and b_hh. Default: True
- batch_first – If True, then the input and output tensors are provided as (batch, seq, feature) instead of (seq, batch, feature). Note that this does not apply to hidden or cell states. See the - Inputs/Outputs sections below for details. Default: False
- dropout – If non-zero, introduces a Dropout layer on the outputs of each LSTM layer except the last layer, with dropout probability equal to dropout. Default: 0
- bidirectional – If True, becomes a bidirectional LSTM. Default: False
- proj_size – If > 0, will use LSTM with projections of corresponding size. Default: 0

输入：input, (h_0, c_0)
- input: (L,H_in)  (L,N,H_in)or(N,L,H_in) depending on batch_first
- h_0:   (D*num_layers,H_out)   (D*num_layers,N,H_out)
- c_0:   (D*num_layers,H_cell)   (D*num_layers,N,H_cell)

N=batch size       L=sequence length         D=2 if 双向 else 1

输出：output, (h_n, c_n)
- output: (L,D*H_out)         (L,N,D*H_out)or(N,L,D*H_out)
- h_n:   (D*num_layers,H_out)    (D*num_layers,N,H_out)
- c_n:   (D*num_layers,H_cell)   (D*num_layers,N,H_cell)

- H_in=input size
- H_cell=hidden size
- H_out=hidden size  OR  proj_size if it>0

## 2.5 Transformer层

![Transformer整体结构](pic/Transformer整体结构.jpg)

整体：

【🌟】nn.Transformer

多层：

- nn.TransformerEncoder N个编码层
- nn.TransformerDecoder N个解码层

单层：

- nn.TransformerEncoderLayer  编码层  多头注意力+前馈
- nn.TransformerDecoderLayer 解码层  多头/Masked多头注意力+前馈
