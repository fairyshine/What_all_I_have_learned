https://github.com/zergtant/pytorch-handbook

# 第一章 PyTorch 入门

## 1.1 Pytorch简介

Torch：一个与Numpy类似的张量（Tensor）操作库，与Numpy不同的是Torch对GPU支持的很好，Lua是Torch的上层包装。

PyTorch和Torch使用包含所有相同性能的C库：TH, THC, THNN, THCUNN，并且它们将继续共享这些库。

这样的回答就很明确了，其实PyTorch和Torch都使用的是相同的底层，只是使用了不同的上层包装语言。

注：LUA虽然快，但是太小众了，所以才会有PyTorch的出现。

---

PyTorch是一个基于Torch的Python开源机器学习库，用于自然语言处理等应用程序。 它主要由Facebook的人工智能研究小组开发。Uber的"Pyro"也是使用的这个库。

PyTorch是一个Python包，提供两个高级功能：

- 具有强大的GPU加速的张量计算（如NumPy）
- 包含自动求导系统的的深度神经网络

## 1.2 PyTorch环境搭建

略

## 1.3 PyTorch深度学习：60分钟快速入门（官方）

官方网站的 [Deep Learning with PyTorch: A 60 Minute Blitz] (https://pytorch.org/tutorials/beginner/deep_learning_60min_blitz.html) 

### 张量 Tensors

```python
import torch

x = torch.tensor([5.5, 3])

x = torch.empty(5, 3)
x = torch.rand(5, 3)
x = torch.zeros(5, 3, dtype=torch.long)
```

相关操作

看形状：

``torch.size`` 返回值是 tuple类型, 所以它支持tuple类型的所有操作.

**译者注：使用size方法与Numpy的shape属性返回的相同，张量也支持shape属性，后面会详细介绍**

```python
print(x.size())

```

运算：

```python
y = torch.rand(5, 3)
print(x + y)             #加法1

print(torch.add(x, y))   #加法2

result = torch.empty(5, 3)
torch.add(x, y, out=result)  #提供输出tensor作为参数
print(result)

# adds x to y
y.add_(x) #替换
print(y)
```

任何 以``_`` 结尾的操作都会用结果替换原变量. 例如: ``x.copy_(y)``, ``x.t_()``, 都会改变 ``x``.

变形：

`torch.view`: 可以改变张量的维度和大小

**译者注：torch.view 与Numpy的reshape类似**

```python
x = torch.randn(4, 4)
y = x.view(16)
z = x.view(-1, 8)  #  size -1 从其他维度推断
print(x.size(), y.size(), z.size())
```

如果你有只有一个元素的张量，使用`.item()`来得到Python数据类型的数值

**NumPy转换**

将一个Torch Tensor转换为NumPy数组是一件轻松的事，反之亦然。

Torch Tensor与NumPy数组共享底层内存地址，修改一个会导致另一个的变化。

```python
#Torch Tensor 转换为 NumPy数组  ————x.numpy()
a = torch.ones(5)
b = a.numpy()

#NumPy Array 转化成 Torch Tensor ————torch.from_numpy(x)
import numpy as np
a = np.ones(5)
b = torch.from_numpy(a)
np.add(a, 1, out=a)
```

**CUDA张量**

使用`.to` 方法 可以将Tensor移动到任何设备中

```python
# is_available 函数判断是否有cuda可以使用
# ``torch.device``将张量移动到指定的设备中
if torch.cuda.is_available():
    device = torch.device("cuda")          # a CUDA 设备对象
    y = torch.ones_like(x, device=device)  # 直接从GPU创建张量
    x = x.to(device)                       # 或者直接使用``.to("cuda")``将张量移动到cuda中
    z = x + y
    print(z)
    print(z.to("cpu", torch.double))       # ``.to`` 也会对变量的类型做更改
```

### Autograd：自动求导机制

PyTorch 中所有神经网络的核心是 `autograd` 包。 我们先简单介绍一下这个包，然后训练第一个简单的神经网络。

---

`autograd`包为张量上的所有操作提供了自动求导。 它是一个在运行时定义的框架，这意味着反向传播是根据你的代码来确定如何运行，并且每次迭代可以是不同的。

`torch.Tensor`是这个包的核心类。如果设置 `.requires_grad` 为 `True`，那么将会追踪所有对于该张量的操作。 当完成计算后通过调用 `.backward()`，自动计算所有的梯度， 这个张量的所有梯度将会自动积累到 `.grad` 属性。

为了防止跟踪历史记录（和使用内存），可以将代码块包装在`with torch.no_grad()：`中。 在评估模型时特别有用，因为模型可能具有`requires_grad = True`的可训练参数，但是我们不需要梯度计算。

---

在自动梯度计算中还有另外一个重要的类`Function`.

`Tensor` and `Function` are interconnected and build up an acyclic graph, that encodes a complete history of computation. Each tensor has a `.grad_fn` attribute that references a `Function` that has created the `Tensor` (except for Tensors created by the user - their `grad_fn is None`).

`Tensor` 和 `Function`互相连接并生成一个非循环图，它表示和存储了完整的计算历史。 每个张量都有一个`.grad_fn`属性，这个属性引用了一个创建了`Tensor`的`Function`（除非这个张量是用户手动创建的，即，这个张量的 `grad_fn` 是 `None`）。

如果需要计算导数，你可以在`Tensor`上调用`.backward()`。 如果`Tensor`是一个标量（即它包含一个元素数据）则不需要为`backward()`指定任何参数， 但是如果它有更多的元素，你需要指定一个`gradient` 参数来匹配张量的形状。

***译者注：在其他的文章中你可能会看到说将Tensor包裹到Variable中提供自动梯度计算，Variable 这个在0.41版中已经被标注为过期了，现在可以直接使用Tensor，官方文档在这里：\*** (https://pytorch.org/docs/stable/autograd.html#variable-deprecated)

---

**梯度**

反向传播 因为 `out`是一个纯量（scalar），`out.backward()` 等于`out.backward(torch.tensor(1))`。



---

**稍后阅读:**

`autograd` 和 `Function` 的官方文档 https://pytorch.org/docs/autograd

### 神经网络 Neural Networks

使用torch.nn包来构建神经网络。

上一讲已经讲过了`autograd`，`nn`包依赖`autograd`包来定义模型并求导。 一个`nn.Module`包含各个层和一个`forward(input)`方法，该方法返回`output`。

### 举例：训练一个分类器



### 选读：数据并行处理（多GPU）



## 1.4 相关资源介绍

暂无？

# 第二章 基础

## 第一节 PyTorch基础

### PyTorch 基础：张量

**基本类型**

Tensor的基本数据类型有五种：

- 32位浮点型：torch.FloatTensor。 (默认)
- 64位整型：torch.LongTensor。
- 32位整型：torch.IntTensor。
- 16位整型：torch.ShortTensor。
- 64位浮点型：torch.DoubleTensor。

除以上数字类型外，还有 byte和chart型

### 使用PyTorch计算梯度数值

PyTorch的Autograd模块实现了深度学习的算法中的向传播求导数，在张量（Tensor类）上的所有操作，Autograd都能为他们自动提供微分，简化了手动计算导数的复杂过程。

在0.4以前的版本中，Pytorch 使用 Variable 类来自动计算所有的梯度。Variable类主要包含三个属性： data：保存Variable所包含的Tensor；grad：保存data对应的梯度，grad也是个Variable，而不是Tensor，它和data的形状一样；grad_fn：指向一个Function对象，这个Function用来反向传播计算输入的梯度。

从0.4起， Variable 正式合并入Tensor类，通过Variable嵌套实现的自动微分功能已经整合进入了Tensor类中。虽然为了代码的兼容性还是可以使用Variable(tensor)这种方式进行嵌套，但是这个操作其实什么都没做。

所以，以后的代码建议直接使用Tensor类进行操作，因为官方文档中已经将Variable设置成过期模块。

要想通过Tensor类本身就支持了使用autograd功能，只需要设置.requires_grad=True

Variable类中的的grad和grad_fn属性已经整合进入了Tensor类中

---

**Autograd**

在张量创建时，通过设置 requires_grad 标识为Ture来告诉Pytorch需要对该张量进行自动求导，PyTorch会记录该张量的每一步操作历史并自动计算

```python
x = torch.rand(5, 5, requires_grad=True)
y = torch.rand(5, 5, requires_grad=True)
```

**简单的自动求导**

```python
z=torch.sum(x+y) 
z.backward() #此时z是标量，可以简单地自动求导
print(x.grad,y.grad)
```

如果Tensor类表示的是一个标量（即它包含一个元素的张量），则不需要为backward()指定任何参数，但是如果它有更多的元素，则需要指定一个gradient参数，它是形状匹配的张量。 以上的 `z.backward()`相当于是`z.backward(torch.tensor(1.))`的简写。 这种参数常出现在图像分类中的单标签分类，输出一个标量代表图像的标签。

**复杂的自动求导**

```python
z= x**2+y**3
z.backward(torch.ones_like(x)) #我们的返回值不是一个标量，所以需要输入一个大小相同的张量作为参数，这里我们用ones_like函数根据x生成一个张量
print(x.grad)
```

我们可以使用with torch.no_grad()上下文管理器临时禁止对已设置requires_grad=True的张量进行自动求导。这个方法在测试集计算准确率的时候会经常用到，例如：

```python
with torch.no_grad():
    print((x +y*2).requires_grad)
False
```

使用.no_grad()进行嵌套后，代码不会跟踪历史记录，也就是说保存的这部分记录会减少内存的使用量并且会加快少许的运算速度。

**Autograd过程解析**

为了说明Pytorch的自动求导原理，我们来尝试分析一下PyTorch的源代码，虽然Pytorch的 Tensor和 TensorBase都是使用CPP来实现的，但是可以使用一些Python的一些方法查看这些对象在Python的属性和状态。 Python的 `dir()` 返回参数的属性、方法列表。`z`是一个Tensor变量，看看里面有哪些成员变量。

```python
dir(z)
```

1. 当我们执行z.backward()的时候。这个操作将调用z里面的grad_fn这个属性，执行求导的操作。
2. 这个操作将遍历grad_fn的next_functions，然后分别取出里面的Function（AccumulateGrad），执行求导操作。这部分是一个递归的过程直到最后类型为叶子节点。
3. 计算出结果以后，将结果保存到他们对应的variable 这个变量所引用的对象（x和y）的 grad这个属性里面。
4. 求导结束。所有的叶节点的grad变量都得到了相应的更新

最终当我们执行完c.backward()之后，a和b里面的grad值就得到了更新。

**扩展Autograd**

如果需要自定义autograd扩展新的功能，就需要扩展Function类。因为Function使用autograd来计算结果和梯度，并对操作历史进行编码。 在Function类中最主要的方法就是`forward()`和`backward()`他们分别代表了前向传播和反向传播。

一个自定义的Function需要一下三个方法：

```
__init__ (optional)：如果这个操作需要额外的参数则需要定义这个Function的构造函数，不需要的话可以忽略。

forward()：执行前向传播的计算代码

backward()：反向传播时梯度计算的代码。 参数的个数和forward返回值的个数一样，每个参数代表传回到此操作的梯度。
```

```python
# 引入Function便于扩展
from torch.autograd.function import Function
```

```python
# 定义一个乘以常数的操作(输入参数是张量)
# 方法必须是静态方法，所以要加上@staticmethod 
class MulConstant(Function):
    @staticmethod 
    def forward(ctx, tensor, constant):
        # ctx 用来保存信息这里类似self，并且ctx的属性可以在backward中调用
        ctx.constant=constant
        return tensor *constant
    @staticmethod
    def backward(ctx, grad_output):
        # 返回的参数要与输入的参数一样.
        # 第一个输入为3x3的张量，第二个为一个常数
        # 常数的梯度必须是 None.
        return grad_output, None 
```

### PyTorch基础：神经网络包nn和优化器optm

神经网络的典型训练过程如下：

1. 定义包含一些可学习的参数(或者叫权重)神经网络模型； 
2. 在数据集上迭代； 
3. 通过神经网络处理输入； 
4. 计算损失(输出结果和正确值的差值大小)；
5. 将梯度反向传播回网络的参数； 
6. 更新网络的参数，主要使用如下简单的更新原则： `weight = weight - learning_rate * gradient`

**定义网络**

```python
import torch
import torch.nn as nn
import torch.nn.functional as F


class Net(nn.Module):

    def __init__(self):
        super(Net, self).__init__()
        # 1 input image channel, 6 output channels, 5x5 square convolution
        # kernel
        self.conv1 = nn.Conv2d(1, 6, 5)
        self.conv2 = nn.Conv2d(6, 16, 5)
        # an affine operation: y = Wx + b
        self.fc1 = nn.Linear(16 * 5 * 5, 120)
        self.fc2 = nn.Linear(120, 84)
        self.fc3 = nn.Linear(84, 10)

    def forward(self, x):
        # Max pooling over a (2, 2) window
        x = F.max_pool2d(F.relu(self.conv1(x)), (2, 2))
        # If the size is a square you can only specify a single number
        x = F.max_pool2d(F.relu(self.conv2(x)), 2)
        x = x.view(-1, self.num_flat_features(x))
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x

    def num_flat_features(self, x):
        size = x.size()[1:]  # all dimensions except the batch dimension
        num_features = 1
        for s in size:
            num_features *= s
        return num_features


net = Net()
print(net)
```

在模型中必须要定义 `forward` 函数，`backward` 函数（用来计算梯度）会被`autograd`自动创建。 可以在 `forward` 函数中使用任何针对 Tensor 的操作。

`net.parameters()`返回可被学习的参数（权重）列表和值

将所有参数的梯度缓存清零，然后进行随机梯度的的反向传播：

```
net.zero_grad()
out.backward(torch.randn(1, 10))
```

**注意：**``torch.nn`` 只支持小批量输入。整个 ``torch.nn`` 包都只支持小批量样本，而不支持单个样本。 例如，``nn.Conv2d`` 接受一个4维的张量， ``每一维分别是sSamples * nChannels * Height * Width（样本数*通道数*高*宽）``。 如果你有单个样本，只需使用 ``input.unsqueeze(0)`` 来添加其它的维数

在继续之前，我们回顾一下到目前为止用到的类。

**回顾:**

- `torch.Tensor`：一个用过自动调用 `backward()`实现支持自动梯度计算的 *多维数组* ， 并且保存关于这个向量的*梯度* w.r.t.
- `nn.Module`：神经网络模块。封装参数、移动到GPU上运行、导出、加载等。
- `nn.Parameter`：一种变量，当把它赋值给一个`Module`时，被 *自动* 地注册为一个参数。
- `autograd.Function`：实现一个自动求导操作的前向和反向定义，每个变量操作至少创建一个函数节点，每一个`Tensor`的操作都回创建一个接到创建`Tensor`和 *编码其历史* 的函数的`Function`节点。

**损失函数**

```python
output = net(input)
target = torch.randn(10)  # 随机值作为样例
target = target.view(1, -1)  # 使target和output的shape相同
criterion = nn.MSELoss()

loss = criterion(output, target)
```

**反向传播**

```python
net.zero_grad()     # 清除梯度

print('conv1.bias.grad before backward')
print(net.conv1.bias.grad)

loss.backward()

print('conv1.bias.grad after backward')
print(net.conv1.bias.grad)
```

**更新权重**

```python
import torch.optim as optim

# create your optimizer
optimizer = optim.SGD(net.parameters(), lr=0.01)

# in your training loop:
optimizer.zero_grad()   # zero the gradient buffers
output = net(input)
loss = criterion(output, target)
loss.backward()
optimizer.step()    # Does the update
```

### PyTorch基础：数据的加载和预处理

PyTorch通过torch.utils.data对一般常用的数据加载进行了封装，可以很容易地实现多线程数据预读和批量加载。 并且torchvision已经预先实现了常用图像数据集，包括前面使用过的CIFAR-10，ImageNet、COCO、MNIST、LSUN等数据集，可通过torchvision.datasets方便的调用

---

**Dataset**

Dataset是一个抽象类，为了能够方便的读取，需要将要使用的数据包装为Dataset类。 自定义的Dataset需要继承它并且实现两个成员方法：

1. `__getitem__()` 该方法定义用索引(`0` 到 `len(self)`)获取一条数据或一个样本
2. `__len__()` 该方法返回数据集的总长度

```python
#引用
from torch.utils.data import Dataset
import pandas as pd
```

```python
#定义一个数据集
class BulldozerDataset(Dataset):
    """ 数据集演示 """
    def __init__(self, csv_file):
        """实现初始化方法，在初始化的时候将数据读载入"""
        self.df=pd.read_csv(csv_file)
    def __len__(self):
        '''
        返回df的长度
        '''
        return len(self.df)
    def __getitem__(self, idx):
        '''
        根据 idx 返回一行数据
        '''
        return self.df.iloc[idx].SalePrice
```

至此，我们的数据集已经定义完成了，我们可以实例化一个对象访问它

```python
ds_demo= BulldozerDataset('median_benchmark.csv')
```

**Dataloader**

DataLoader为我们提供了对Dataset的读取操作，常用参数有：batch_size(每个batch的大小)、 shuffle(是否进行shuffle操作)、 num_workers(加载数据的时候使用几个子进程)。下面做一个简单的操作

```python
dl = torch.utils.data.DataLoader(ds_demo, batch_size=10, shuffle=True, num_workers=0)
```

**torchvision包**

略

## 第二节 深度学习基础及数学原理

### 监督学习和无监督学习

这里我们只着重介绍监督学习，因为我们后面的绝大部们课程都是使用的监督学习的方法，在训练和验证时输入的数据既包含输入x，又包含x对应的输出y，即学习数据已经事先给出了正确答案。

### 线性回归（Linear Regression）



### 损失函数（Loss Function）



### 梯度下降



### 方差/偏差



### 正则化



## 第三节 神经网络简介



## 第四节 卷积神经网络



## 第五节 循环神经网络



# 第三章 实战

## 第一节 logistic回归二元分类



## 第二节 CNN：MNIST数据集手写数字识别



## 第三节 RNN：通过sin预测cos



# 第四章 提高

## 第一节 Fine-tuning



## 第二节 可视化



## 第三节 Fast.ai



## 第四节 训练的一些技巧



## 第五节 多GPU并行训练



## 补充：在PyTorch中使用DistributedDataParallel进行多GPU分布式模型训练



# 第五章 应用

## 第一节 Kaggle介绍



## 第二节结构化数据



## 第三节 计算机视觉



## 第四节 自然语言处理



## 第五节 协同滤波



# 第六章 资源



# 第七章 附录



