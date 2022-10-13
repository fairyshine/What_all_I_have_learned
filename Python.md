# Python基础

[【🍰总结】Python知识点](detail/Python/Python知识点.md)

[【🍔总结】Python小技巧](detail/Python/Python小技巧.md)

# 常用库

## 标准库

[【📖总结】Python标准库研究](detail/Python/Python标准库研究.md)

## Collections



## NumPy & Pandas、SciPy、Matplotlib

数据处理+科学运算+数据可视化

| Logo                                                         | 库名                      | 简介                                                         |
| ------------------------------------------------------------ | ------------------------- | ------------------------------------------------------------ |
| <img src="pic/numpy.png" alt="numpy" style="zoom:50%;" />    | NumPy (Numerical Python)  | 支持大量的维度数组与矩阵运算，此外也针对数组运算提供大量的数学函数库。 |
| ![pandas](pic/pandas.png)                                    | Pandas                    | Pandas 一个强大的分析结构化数据的工具集，基础是 Numpy（提供高性能的矩阵运算）。Pandas 名字衍生自术语 "panel data"（面板数据）和 "Python data analysis"（Python 数据分析）。Pandas 可以从各种文件格式比如 CSV、JSON、SQL、Microsoft Excel 导入数据。 |
| <img src="pic/scipy.png" alt="scipy" style="zoom:50%;" />    | SciPy (Scientific Python) | 包含的模块有最优化、线性代数、积分、插值、特殊函数、快速傅里叶变换、信号处理和图像处理、常微分方程求解和其他科学与工程中常用的计算。 |
| <img src="pic/Matplotlib.png" alt="Matplotlib" style="zoom:50%;" /> | Matplotilb                | 是 Python 编程语言及其数值数学扩展包 NumPy 的可视化操作界面。它为利用通用的图形用户界面工具包，如 Tkinter, wxPython, Qt 或 GTK+ 向应用程序嵌入式绘图提供了应用程序接口（API）。 |

### 笔记

#### NumPy

[【🚀教程】视频—一个10分钟的numpy入门教程](detail/Python/一个10分钟的numpy入门教程.md)  B站up 奇乐编程学院

#### Pandas

[【🚀教程】菜鸟教程—Pandas](detail/Python/菜鸟教程—Pandas.md)

### 电子书

《From Python to Numpy》https://www.labri.fr/perso/nrougier/from-python-to-numpy/

## 包管理系统：conda

创建虚拟环境：conda create -n [环境名称] [安装库包列表]
查看环境列表：conda env list
删除环境：conda remove -n [环境名称] --all

## 依赖导出：pipreqs

`pipreqs ./ --encoding=utf8`

## 终端显示：Rich

[【🚀文档】Rich参考文档](detail/Python/Rich参考文档.md)

## 机器学习：scikit-learn（即sklearn）

[【🚀资料】sklearn资料汇总](detail/Python/sklearn资料汇总.md)

[【🚀文档】sklearn官方文档](detail/Python/sklearn官方文档.md)

- 🔗：https://scikit-learn.org/stable/index.html  /  https://scikit-learn.org.cn

## 文本表示：Gensim 

[【🚀教程】Gensim使用](detail/Python/Gensim使用.md)

## 绘制图表：Pyecharts

## 命令行工具：Typer

## 参数管理：OmegaConf、Hydra

## 图形库

Pyside6 做GUI



OpenGL 底层

## Module分发：setuptools

[【🐰教程】setuptools研究](detail/Python/setuptools研究.md)

## 打包： Pyinstaller

## 网络交互

### 爬虫库

[【🚀】beautifulsoup4使用](detail/Python/beautifulsoup4使用.md)

### Selenium-WEB自动化

[【🚀资料】Selenium使用](detail/Python/Selenium使用.md)

# 项目代码的组织管理

文章：

- https://docs.python-guide.org/writing/structure/
  - 【软件设计】【Structuring your Python project】Python项目结构的组织方法；以及实例演示：从零开始组织自己的Python项目代码 https://blog.csdn.net/weixin_39278265/article/details/118223182
- https://guicommits.com/organize-python-code-like-a-pro/
  - https://www.bilibili.com/video/BV1rG4y1e7qT

