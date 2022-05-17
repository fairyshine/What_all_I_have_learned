# 说明

## 自动求导机制

概述Autograd如何工作并记录操作。

### 从后向中排除子图

每个变量都有两个标志：`requires_grad`和`volatile`。

它们都允许从梯度计算中精细地排除子图，可以提高效率。

（1）`requires_grad`

**只有所有输入都不需要梯度（均视为常量？），输出才不需要梯度。**

想要冻结部分模型时，或明确不会使用某些参数的梯度，则可以切换对应模型中的`requires_grad`标志。

注：新构造modules的参数默认`requires_gradj=True`

```python
>>> x = Variable(torch.randn(5, 5))
>>> y = Variable(torch.randn(5, 5))
>>> z = Variable(torch.randn(5, 5), requires_grad=True)
>>> a = x + y
>>> a.requires_grad
False
>>> b = a + z
>>> b.requires_grad
True
```

（2）`volatile` (释义：易挥发的；变化无常的，不稳定的)

纯粹的inference模式下推荐使用`volatile`,明确不会调用`.backward()`时。

它将使用绝对最小的内存来评估模型，不会保存任何中间状态。

`volatile=True` 决定了`requires_grad=False`

一个操作只要有一个`volatile`的输入，它的输出也必然是`volatile`。

```python
>>> regular_input = Variable(torch.randn(5, 5))
>>> volatile_input = Variable(torch.randn(5, 5), volatile=True)
>>> model = torchvision.models.resnet18(pretrained=True)
>>> model(regular_input).requires_grad
True
>>> model(volatile_input).requires_grad
False
>>> model(volatile_input).volatile
True
>>> model(volatile_input).creator is None
True
```

### 自动求导如何编码历史信息

变量的`.creator`属性，指向把它作为输出的函数。这是一个由`Function`对象作为节点组成的有向无环图(DAG)的入口点，它们之间的引用就是图的边。

（变量是图的入口，函数是图的出口。）

每次执行一个操作，一个表示它的新的`Function`对象就被实例化，它的`forward()`方法被调用，并且它输出的`variable`的创建者被设置为这个`Function`。

通过跟踪变量到叶节点的路径，可重建创建数据的操作序列，并自动计算梯度。

> 算梯度时，方向传播，可能用到了链式法则？

补充：需要注意的一点是，整个图在每次迭代时都是从头开始重新创建的，这就允许使用任意的Python控制流语句，这样可以在每次迭代时改变图的整体形状和大小。在启动训练之前不必对所有可能的路径进行编码—— what you run is what you differentiate.

### Variable上的In-place操作

在自动求导中支持in-place操作是一件很困难的事情，除非您的内存压力很大，否则您可能永远不需要使用它们。

限制in-place操作适用性主要有两个原因：

１．覆盖梯度计算所需的值。这就是为什么变量不支持`log_`。

２．每个in-place操作实际上需要实现重写计算图。

### In-place正确性检查

每个变量保留有version counter，它每次都会递增，当在任何操作中被使用时。当`Function`保存任何用于后向的tensor时，还会保存其包含变量的version counter。一旦访问`self.saved_tensors`，它将被检查，如果它大于保存的值，则会引起错误。

## CUDA语义

`torch.cuda`会记录当前选择的GPU，并且分配的所有CUDA张量将在上面创建。可以使用`torch.cuda.device`上下文管理器更改所选设备。

一旦张量被分配，对其进行操作的结果将始终放在与张量相同的设备上。

默认情况下，不支持跨GPU操作，唯一的例外是`copy_()`。 除非启用对等存储器访问，否则对分布不同设备上的张量任何启动操作的尝试都将会引发错误。

```python
x = torch.cuda.FloatTensor(1)
# x.get_device() == 0
y = torch.FloatTensor(1).cuda()
# y.get_device() == 0

with torch.cuda.device(1):
    # allocates a tensor on GPU 1
    a = torch.cuda.FloatTensor(1)
    # transfers a tensor from CPU to GPU 1
    b = torch.FloatTensor(1).cuda()
    # a.get_device() == b.get_device() == 1

    c = a + b
    # c.get_device() == 1
    z = x + y
    # z.get_device() == 0

    # even within a context, you can give a GPU id to the .cuda call
    d = torch.randn(2).cuda(2)
    # d.get_device() == 2
```

### 最佳实践

#### 使用固定的内存缓存区

当副本来自固定（页锁）内存时，主机到GPU的复制速度要快很多。CPU张量和存储开放了一个`pin_memory()`方法，它返回该对象的副本，而它的数据放在固定区域中。

另外，一旦固定了张量或存储，就可以使用异步的GPU副本。只需传递一个额外的`async=True`参数到`cuda()`的调用。这可以用于将数据传输与计算重叠。

通过将`pin_memory=True`传递给其构造函数，可以使`DataLoader`将batch返回到固定内存中。

#### 使用nn.DataParallel 替代 multiprocessing

大多数涉及批量输入和多个GPU的情况应默认使用`DataParallel`来使用多个GPU。尽管有GIL的存在，单个python进程也可能使多个GPU饱和。

## 扩展PyTorch

本章包含如何扩展 `torch.nn`, `torch.autograd`和 使用我们的 `C 库`编写自定义的`C`扩展。

### 扩展torch.autograd

如果你想要添加一个新的 `Operation` 到`autograd`的话，你的`Operation`需要继承 `class Function`。`autograd`使用`Function`计算结果和梯度，同时编码 `operation`的历史。每个新的 `operation(function)` 都需要实现三个方法：

- `__init__ (optional)` - 如果你的`operation`包含非`Variable`参数，那么就将其作为`__init__`的参数传入到`operation`中。例如：`AddConstant Function`加一个常数，`Transpose Function`需要指定哪两个维度需要交换。如果你的`operation`不需要额外的参数，你可以忽略`__init__`。
- `forward()` - 在里面写执行此`operation`的代码。可以有任意数量的参数。如果你对某些参数指定了默认值，则这些参数是可传可不传的。记住：`forward()`的参数只能是`Variable`。函数的返回值既可以是 `Variable`也可以是`Variables`的`tuple`。同时，请参考 `Function`的 `doc`，查阅有哪些 方法是只能在`forward`中调用的。
- `backward()` - 梯度计算公式。 参数的个数和`forward`返回值的个数一样，每个参数代表传回到此`operation`的梯度. `backward()`的返回值的个数应该和此`operation`输入的个数一样，每个返回值对应了输入值的梯度。如果`operation`的输入不需要梯度，或者不可导，你可以返回`None`。 如果`forward()`存在可选参数，你可以返回比输入更多的梯度，只是返回的是`None`。

```python
# Inherit from Function
class Linear(Function):

    # bias is an optional argument
    def forward(self, input, weight, bias=None):
        self.save_for_backward(input, weight, bias)
        output = input.mm(weight.t())
        if bias is not None:
            output += bias.unsqueeze(0).expand_as(output)
        return output

    # This function has only a single output, so it gets only one gradient
    def backward(self, grad_output):
        # This is a pattern that is very convenient - at the top of backward
        # unpack saved_tensors and initialize all gradients w.r.t. inputs to
        # None. Thanks to the fact that additional trailing Nones are
        # ignored, the return statement is simple even when the function has
        # optional inputs.
        input, weight, bias = self.saved_tensors
        grad_input = grad_weight = grad_bias = None

        # These needs_input_grad checks are optional and there only to
        # improve efficiency. If you want to make your code simpler, you can
        # skip them. Returning gradients for inputs that don't require it is
        # not an error.
        if self.needs_input_grad[0]:
            grad_input = grad_output.mm(weight)
        if self.needs_input_grad[1]:
            grad_weight = grad_output.t().mm(input)
        if bias is not None and self.needs_input_grad[2]:
            grad_bias = grad_output.sum(0).squeeze(0)

        return grad_input, grad_weight, grad_bias
```

现在，为了可以更简单的使用自定义的`operation`，我们建议将其用一个简单的 `helper function`包装起来。 functions:

```python
def linear(input, weight, bias=None):
    # First braces create a Function object. Any arguments given here
    # will be passed to __init__. Second braces will invoke the __call__
    # operator, that will then use forward() to compute the result and
    # return it.
    return Linear()(input, weight, bias)
```

### 扩展torch.nn

`nn` 包含两种接口 - `modules`和他们的`functional`版本。通过这两个接口，你都可以扩展`nn`。但是我们建议，在扩展`layer`的时候，使用`modules`， 因为`modules`保存着参数和`buffer`。如果不需要参数的话，那么建议使用`functional`(激活函数，pooling，这些都不需要参数)。

增加一个`operation`的 `functional`版本已经在上面一节介绍完毕。

增加一个模块(`module`)。 由于`nn`重度使用`autograd`。所以，添加一个新`module`需要实现一个 用来执行 计算 和 计算梯度 的`Function`。从现在开始，假定我们想要实现一个`Linear module`，记得之前我们已经实现了一个`Linear Funciton`。 只需要很少的代码就可以完成这个工作。 现在，我们需要实现两个方法：

- `__init__ (optional)` - 输入参数，例如`kernel sizes`, `numbers of features`, 等等。同时初始化 `parameters`和`buffers`。
- `forward()` - 实例化一个执行`operation`的`Function`，使用它执行`operation`。和`functional wrapper(上面实现的那个简单的wrapper)`十分类似。

```python
class Linear(nn.Module):
    def __init__(self, input_features, output_features, bias=True):
        self.input_features = input_features
        self.output_features = output_features

        # nn.Parameter is a special kind of Variable, that will get
        # automatically registered as Module's parameter once it's assigned
        # as an attribute. Parameters and buffers need to be registered, or
        # they won't appear in .parameters() (doesn't apply to buffers), and
        # won't be converted when e.g. .cuda() is called. You can use
        # .register_buffer() to register buffers.
        # nn.Parameters can never be volatile and, different than Variables,
        # they require gradients by default.
        self.weight = nn.Parameter(torch.Tensor(input_features, output_features))
        if bias:
            self.bias = nn.Parameter(torch.Tensor(output_features))
        else:
            # You should always register all possible parameters, but the
            # optional ones can be None if you want.
            self.register_parameter('bias', None)

        # Not a very smart way to initialize weights
        self.weight.data.uniform_(-0.1, 0.1)
        if bias is not None:
            self.bias.data.uniform_(-0.1, 0.1)

    def forward(self, input):
        # See the autograd section for explanation of what happens here.
        return Linear()(input, self.weight, self.bias)
        #注意这个Linear是之前实现过的Linear
```

### 编写自定义C扩展

Coming soon. For now you can find an example at [GitHub](https://github.com/pytorch/extension-ffi).

## 多进程最佳实践

`torch.multiprocessing`是Python`multiprocessing`的替代品。它支持完全相同的操作，但扩展了它以便通过`multiprocessing.Queue`发送的所有张量将其数据移动到共享内存中，并且只会向其他进程发送一个句柄。

> **Note**
>
> 当`Variable`发送到另一个进程时，`Variable.data`和`Variable.grad.data`都将被共享。

这允许实现各种训练方法，如Hogwild，A3C或需要异步操作的任何其他方法。

### 共享CUDA张量

仅在Python 3中使用`spawn`或`forkserver`启动方法才支持在进程之间共享CUDA张量。

> **Warning**
>
> CUDA API要求导出到其他进程的分配，只要它们被使用就要一直保持有效。您应该小心，确保您共享的CUDA张量只要有必要就不要超出范围。这不是共享模型参数的问题，但传递其他类型的数据应该小心。注意，此限制不适用于共享CPU内存。

参考：[使用 nn.DataParallel 替代 multiprocessing](https://pytorch-cn.readthedocs.io/zh/latest/notes/cuda/)

### 最佳实践和提示

#### 避免和抵制死锁

#### 重用经过队列的缓冲区

#### 异步多进程训练（例如Hogwild）

## 序列化语义

### 保存模型的推荐方法

这主要有两种方法序列化和恢复模型。

第一种（推荐）：只保存和加载模型参数🌟

保存：

```python
torch.save(the_model.state_dict(), PATH)
```

加载：

```python
the_model = TheModelClass(*args, **kwargs)
the_model.load_state_dict(torch.load(PATH))
```

---

第二种：保存和加载整个模型

保存：

```python
torch.save(the_model, PATH)
```

加载：

```python
the_model = torch.load(PATH)
```

然而，在这种情况下，序列化的数据被绑定到特定的类和固定的目录结构，所以当在其他项目中使用时，或者在一些严重的重构器之后它可能会以各种方式break。

# Package参考

## torch

### 张量 Tensors

#### torch.is_tensor

```python
torch.is_tensor(obj)
```

如果*obj* 是一个pytorch张量，则返回True

- 参数： obj (Object) – 判断对象

#### torch.is_storage

```python
torch.is_storage(obj)
```

如何*obj* 是一个pytorch storage对象，则返回True

- 参数： obj (Object) – 判断对象

#### torch.set_default_tensot_type

#### torch.numel

```python
torch.numel(input)->int
```

返回`input` 张量中的元素个数

- 参数: input (*Tensor*) – 输入张量

例子:

```python
>>> a = torch.randn(1,2,3,4,5)
>>> torch.numel(a)
120
>>> a = torch.zeros(4,4)
>>> torch.numel(a)
16
```

#### torch.set_printoptions

```python
torch.set_printoptions(precision=None, threshold=None, edgeitems=None, linewidth=None, profile=None)
```

设置打印选项。 完全参考自[ Numpy](https://docs.scipy.org/doc/numpy/reference/generated/numpy.set_printoptions.html)。

参数: 

- precision – 浮点数输出的精度位数 (默认为8 )
- threshold – 阈值，触发汇总显示而不是完全显示(repr)的数组元素的总数 （默认为1000）
- edgeitems – 汇总显示中，每维（轴）两端显示的项数（默认值为3）
- linewidth – 用于插入行间隔的每行字符数（默认为80）。Thresholded matricies will ignore this parameter.
- profile – pretty打印的完全默认值。 可以覆盖上述所有选项 (默认为short, full)

### 创建操作 Creation Ops

#### torch.eye

```
torch.eye(n, m=None, out=None)
```

返回一个2维张量，对角线位置全1，其它位置全0

参数: 

- n (int) – 行数
- m (int, *optional*) – 列数.如果为None,则默认为*n*
- out (*Tensor*, *optinal*) - Output tensor

#### torch.from_numpy

```
torch.from_numpy(ndarray) → Tensor
```

Numpy桥，将`numpy.ndarray` 转换为pytorch的 `Tensor`。 返回的张量tensor和numpy的ndarray共享同一内存空间。修改一个会导致另外一个也被修改。返回的张量不能改变大小。

例子:

```
>>> a = numpy.array([1, 2, 3])
>>> t = torch.from_numpy(a)
>>> t
torch.LongTensor([1, 2, 3])
>>> t[0] = -1
>>> a
array([-1,  2,  3])
```

#### torch.linspace

```python
torch.linspace(start, end, steps=100, out=None) → Tensor
```

返回一个1维张量，包含在区间`start` 和 `end` 上均匀间隔的`steps`个点。 输出1维张量的长度为`steps`。

参数:

- start (float) – 序列的起始点
- end (float) – 序列的最终值
- steps (int) – 在`start` 和 `end`间生成的样本数
- out (Tensor, optional) – 结果张量

例子:

```
>>> torch.linspace(3, 10, steps=5)

  3.0000
  4.7500
  6.5000
  8.2500
 10.0000
[torch.FloatTensor of size 5]

>>> torch.linspace(-10, 10, steps=5)

-10
 -5
  0
  5
 10
[torch.FloatTensor of size 5]

>>> torch.linspace(start=-10, end=10, steps=5)

-10
 -5
  0
  5
 10
[torch.FloatTensor of size 5]
```

#### torch.logspace

```
torch.logspace(start, end, steps=100, out=None) → Tensor
```

返回一个1维张量，包含在区间 10start 和 10end上以对数刻度均匀间隔的`steps`个点。 输出1维张量的长度为`steps`。 

参数:

- start (float) – 序列的起始点
- end (float) – 序列的最终值
- steps (int) – 在`start` 和 `end`间生成的样本数
- out (Tensor, optional) – 结果张量

例子:

```
>>> torch.logspace(start=-10, end=10, steps=5)

 1.0000e-10
 1.0000e-05
 1.0000e+00
 1.0000e+05
 1.0000e+10
[torch.FloatTensor of size 5]

>>> torch.logspace(start=0.1, end=1.0, steps=5)

  1.2589
  2.1135
  3.5481
  5.9566
 10.0000
[torch.FloatTensor of size 5]
```

#### torch.ones

```
torch.ones(*sizes, out=None) → Tensor
```

返回一个全为1 的张量，形状由可变参数`sizes`定义。

参数:

- sizes (int...) – 整数序列，定义了输出形状
- out (Tensor, optional) – 结果张量 例子:

```
>>> torch.ones(2, 3)

 1  1  1
 1  1  1
[torch.FloatTensor of size 2x3]

>>> torch.ones(5)

 1
 1
 1
 1
 1
[torch.FloatTensor of size 5]
```

#### torch.rand

```
torch.rand(*sizes, out=None) → Tensor
```

返回一个张量，包含了从区间[0,1)的均匀分布中抽取的一组随机数，形状由可变参数`sizes` 定义。

参数:

- sizes (int...) – 整数序列，定义了输出形状
- out ([*Tensor*](http://pytorch.org/docs/tensors.html#torch.Tensor), *optinal*) - 结果张量 例子：

```
>>> torch.rand(4)

 0.9193
 0.3347
 0.3232
 0.7715
[torch.FloatTensor of size 4]

>>> torch.rand(2, 3)

 0.5010  0.5140  0.0719
 0.1435  0.5636  0.0538
[torch.FloatTensor of size 2x3]
```

#### torch.randn

```
torch.randn(*sizes, out=None) → Tensor
```

返回一个张量，包含了从标准正态分布(均值为0，方差为 1，即高斯白噪声)中抽取一组随机数，形状由可变参数`sizes`定义。 参数:

- sizes (int...) – 整数序列，定义了输出形状
- out ([*Tensor*](http://pytorch.org/docs/tensors.html#torch.Tensor), *optinal*) - 结果张量

例子：:

```
>>> torch.randn(4)

-0.1145
 0.0094
-1.1717
 0.9846
[torch.FloatTensor of size 4]

>>> torch.randn(2, 3)

 1.4339  0.3351 -1.0999
 1.5458 -0.9643 -0.3558
[torch.FloatTensor of size 2x3]
```

#### torch.randperm

```
torch.randperm(n, out=None) → LongTensor
```

给定参数`n`，返回一个从`0` 到`n -1` 的随机整数排列。

参数:

- n (int) – 上边界(不包含)

例子：

```
>>> torch.randperm(4)

 2
 1
 3
 0
[torch.LongTensor of size 4]
```

#### torch.arange

```
torch.arange(start, end, step=1, out=None) → Tensor
```

返回一个1维张量，长度为 floor((end−start)/step)。包含从`start`到`end`，以`step`为步长的一组序列值(默认步长为1)。

参数:

- start (float) – 序列的起始点
- end (float) – 序列的终止点
- step (float) – 相邻点的间隔大小
- out (Tensor, optional) – 结果张量

例子：

```
>>> torch.arange(1, 4)

 1
 2
 3
[torch.FloatTensor of size 3]

>>> torch.arange(1, 2.5, 0.5)

 1.0000
 1.5000
 2.0000
[torch.FloatTensor of size 3]
```

#### torch.zeros

```
torch.zeros(*sizes, out=None) → Tensor
```

返回一个全为标量 0 的张量，形状由可变参数`sizes` 定义。

参数:

- sizes (int...) – 整数序列，定义了输出形状
- out ([Tensor](http://pytorch.org/docs/tensors.html#torch.Tensor), *optional*) – 结果张量

例子：

```python
>>> torch.zeros(2, 3)

 0  0  0
 0  0  0
[torch.FloatTensor of size 2x3]

>>> torch.zeros(5)

 0
 0
 0
 0
 0
[torch.FloatTensor of size 5]
```

### 索引，切片，连接，换位 Indexing，Slicing，Joining，Ops



### 随机抽样 Random sampling

### 序列化 Serialization

### 数学操作 Math operations

### Pointwise Ops

### Reduction Ops

### 比较操作 Comparison Ops

### 其他操作 Other Operations

### BLAS and LAPACK Operations



## torch.Tensor



## torch.Storage



## torch.nn



## torch.nn.functional



## torch.nn.init



## torch.optim



## torch.autograd



## torch.multiprocessing



## torch.legacy



## torch.cuda



## torch.utils.ffi



## torch.utils.data



## torch.utils.model_zoo



# torchvision参考

## torchvision



## torchvision.datasets



## torchvision.models



## torchvision.transforms



## torchvision.utils

