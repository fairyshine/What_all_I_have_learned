# 教程

https://scikit-learn.org.cn/lists/8.html

https://scikit-learn.org/stable/tutorial/index.html

## scikit-learn机器学习简介

`Scikit-learn`是一个开源的机器学习库，它支持有监督和无监督的学习。

它还提供了用于模型拟合，数据预处理，模型选择和评估以及许多其他实用程序的各种工具。

### 拟合和预测：估算器(estimator)基础

`Scikit-learn`提供了数十种内置的机器学习算法和模型，称为估算器。

这是一个简单的示例，其中我们使用一些非常基本的数据来训练 [`RandomForestClassifier`](https://scikit-learn.org.cn/view/633.html)：

```python
>>> from sklearn.ensemble import RandomForestClassifier
>>> clf = RandomForestClassifier(random_state=0)
>>> X = [[ 1,  2,  3],  #2个样本，3个特征
...      [11, 12, 13]]
>>> y = [0, 1]  #每一个样本的类别
>>> clf.fit(X, y)

```

### 转换器和预处理器

在中`scikit-learn`，预处理器和转换器遵循与estimator对象相同的API（实际上它们都继承自同一 `BaseEstimator`类）。转换对象没有预测方法，但是需要有一个输出新转换的样本矩阵`X`的转换方法：

```python
>>> from sklearn.preprocessing import StandardScaler
>>> X = [[0, 15],
...      [1, -10]]
>>> StandardScaler().fit(X).transform(X)
array([[-1.,  1.],
       [ 1., -1.]])
```

### 管道：连接预处理器和估算器

在以下示例中，我们[加载Iris数据集](https://scikit-learn.org.cn/view/121.html)，将其分为训练集和测试集，然后根据测试数据计算管道的准确性得分：

```python
>>> from sklearn.preprocessing import StandardScaler
>>> from sklearn.linear_model import LogisticRegression
>>> from sklearn.pipeline import make_pipeline

...
>>> # 创建一个pipeline对象
>>> pipe = make_pipeline(
...     StandardScaler(),
...     LogisticRegression(random_state=0)
... )
...

```

### 模型评估

使用[`cross_validate`](https://scikit-learn.org.cn/view/660.html)帮助程序执行5折交叉验证过程。

```python
>>> from sklearn.datasets import make_regression
>>> from sklearn.linear_model import LinearRegression
>>> from sklearn.model_selection import cross_validate
...
>>> X, y = make_regression(n_samples=1000, random_state=0)
>>> lr = LinearRegression()
...
>>> result = cross_validate(lr, X, y)  # 默认为5折交叉验证
>>> result['test_score']  # 此处R2得分很高的原因为数据集很简单
array([1., 1., 1., 1., 1.])
```

### 自动参数搜索

所有估算器都有可以调整的参数（在文献中通常称为超参数）。

`Scikit-learn`提供了自动查找最佳参数组合的工具（通过交叉验证）。

训练好最佳参数集的[`RandomForestRegressor`](https://scikit-learn.org.cn/view/650.html)。在[用户指南中](https://scikit-learn.org.cn/view/99.html)可以阅读更多内容：

```python
>>> from sklearn.datasets import fetch_california_housing
>>> from sklearn.ensemble import RandomForestRegressor
>>> from sklearn.model_selection import RandomizedSearchCV
>>> from sklearn.model_selection import train_test_split
>>> from scipy.stats import randint
...
>>> X, y = fetch_california_housing(return_X_y=True)
>>> X_train, X_test, y_train, y_test = train_test_split(X, y, random_state=0)
...
>>> # 定义要搜索的参数空间
>>> param_distributions = {'n_estimators': randint(1, 5),
...                        'max_depth': randint(5, 10)}
...
>>> # 现在创建一个searchCV 对象然后用数据训练它
>>> search = RandomizedSearchCV(estimator=RandomForestRegressor(random_state=0),
...                             n_iter=5,
...                             param_distributions=param_distributions,
...                             random_state=0)
>>> search.fit(X_train, y_train)
RandomizedSearchCV(estimator=RandomForestRegressor(random_state=0), n_iter=5,
                   param_distributions={'max_depth': ...,
                                        'n_estimators': ...},
                   random_state=0)
>>> search.best_params_
{'max_depth': 9, 'n_estimators': 4}

>>> # 搜索对象现在就像普通的随机森林估计器一样
>>> # max_depth=9 和 n_estimators=4
>>> search.score(X_test, y_test)
0.73...
```

| 注意                                                         |
| :----------------------------------------------------------- |
| 在实践中，你几乎总是想要用[搜索管道](https://scikit-learn.org.cn/view/99.html#3.2.3 参数搜索技巧)，而不是单个估计器。一个主要原因是，如果在不使用pipeline的情况下对整个数据集应用预处理步骤，然后执行任何类型的交叉验证，那么你将打破训练和测试数据之间独立性的基本假设。实际上，由于你使用了整个数据集对数据进行了预处理，因此有关测试集的一些信息可用于训练集。这将导致高估估算器的泛化能力（你可以在此[Kaggle帖子中](https://www.kaggle.com/alexisbcook/data-leakage)阅读更多内容）。  使用管道进行交叉验证和搜索将在很大程度上避免遇到这个常见的陷阱。 |

## 科学数据处理统计学习指南

本教程将探讨统计学习，以及将机器学习技术用于统计推断的目的：在手头的数据上得出结论。

Scikit-learn是一个Python模块，在紧密结合的科学Python程序包世界（NumPy，SciPy，matplotlib）中集成了经典的机器学习算法。

### 统计学习：scikit-learn中的设置和估计对象

数据集

估计对象  

- 拟合数据：estimator.fit(data)
- 估算器参数（超参数）

### 有监督的学习：从高维观测值预测输出变量

监督学习包括学习两个数据集之间的链接：观测数据X和我们试图预测的外部变量y，通常称为“目标”或“标签”。 y通常是长度为n_samples的一维数组。

scikit-learn中所有受监督的估计器都执行fit（X，y）方法以拟合模型，并执行predict（X）方法，给定未标记的观察值X，该方法返回预测的标记y。

### 模型选择：选择估计量及其参数

score方法打分

### 无监督学习：寻求数据表示

### 全部放在一起

管道

## 处理文本数据

在本节中，我们将看到如何：

- 加载文件内容和类别
- 提取适合机器学习的特征向量
- 训练线性模型以执行分类
- 使用网格搜索策略找到特征提取组件和分类器的良好配置

### 加载20个新闻组数据集

### 从文本文件中提取特征

### 训练一个分类器

### 建立管道

### 模型在测试集上的表现的评估

### 使用网格搜索调参

## 选择适合的估算器

