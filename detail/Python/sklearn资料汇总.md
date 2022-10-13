

# 一文上手sklearn机器学习库

https://zhuanlan.zhihu.com/p/190049765

## 一、准备工作

**Python的基本语法和Python中的面向对象的概念与操作**。

**Numpy的基本数据结构和操作方法**。

**Pandas - 读写数据的利器**。

**Matplotlib - 绘图工具。**

## 二、线性回归

```python
>>> import numpy as np
>>> from sklearn.linear_model import LinearRegression
>>> X = np.array([[1, 1], [1, 2], [2, 2], [2, 3]])
>>> # y = 1 * x_0 + 2 * x_1 + 3
>>> y = np.dot(X, np.array([1, 2])) + 3
>>> reg = LinearRegression().fit(X, y)


>>> reg.coef_
array([1., 2.])
>>> reg.intercept_
3.0000...
>>> reg.predict(np.array([[3, 5]]))
array([16.])
```

小结一下，根据官方文档，要拟合一个线性回归模型并且预测出新值的话，其实只需要进行四个步骤：

- 格式化数据，输入为`n*d`的数组，其中`n`表示数据的个数，`d`是维度；输出值是一维数组。
- 初始化模型`LinearReregression()`
- 拟合`fit(X,y)`
- 预测`predict(X_test)`

## 三、机器学习的通用模式及实现方法

模型如何拟合和如何预测。

从上章中我们已经看到，在调用线性回归时，我们只需要找到它具体对应的`class`，然后再分别调用`fit`和`predict`两个函数即可完成拟合(训练)和预测的步骤。而事实上，在sklearn中绝大多数模型都是用这样的方式封装的，因此在掌握了线性回归的基础上，要学会这些方法就非常容易了。

## 四、模型自动调参

### **1、 调参的基本思想--交叉验证(Cross Validation)**

### **2、 实现交叉验证需要考虑哪些细节**

- 子集应该如何划分？
  - k-fold: 随机将训练集划分为![[公式]](https://www.zhihu.com/equation?tex=k)个
  - leave-one-out: 将子集划分为![[公式]](https://www.zhihu.com/equation?tex=n) 个，即每个子集只有一个样本
  - hold-out: 将![[公式]](https://www.zhihu.com/equation?tex=n)个样本中的![[公式]](https://www.zhihu.com/equation?tex=m)个随机挑出作为测试集（当然也可以指定测试集）

- 备选的参数组合应该如何生成？
  - grid-search: 即给出每个超参数的备选范围，对所有组合进行穷举
  - random search: 以某种方式生成多种超参数的组合，穷举所有随机生成的结果
- 评价模型性能的指标选什么？
  - 分类问题：分类精度、平衡精度、平均精度等
  - 回归问题：最大误差、均方误差、均方根误差等

### **3、 sklearn中的实现方式（以GridSearchCV和SVR为例）**

`GridSearchCV`即网格搜索的交叉验证法，这是最为常用的方法之一。

# 五、集成学习

### **1、集成学习简述**

集成学习是目前各类竞赛和工程中应用最广泛的模型提升方法。比如在kaggle中就有关于集成学习的介绍（**[Kaggle模型融合原文](https://mlwave.com/kaggle-ensembling-guide/)**）。这里所谓的模型融合实际上主要就是集成学习方法。

常见的集成学习有以下几种：

- Boosting
- Bagging
- Stacking

其中boosting和bagging应用最多。比如目前流行的**[xgboost](https://xgboost.readthedocs.io/en/latest/python/python_api.html)**就是(gradient)boosting的一种，而random forest则是bagging的一种。

### **2、集成学习的两种主要思路**

- 采用同一基模型，在样本的不同子集上进行训练，从而得到对应的多个不同的具体模型。这类学习方法的代表就是boosting, bagging。
- 采用多种基模型，在同一样本上进行训练，将多个模型组合起来得到具体模型。voting和stacking都是采用这类方式。

### **3、单一模型集成 - - 以Adaboost为例**

### **4、多模型集成 - - 以Voting例**
