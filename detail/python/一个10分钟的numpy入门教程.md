https://www.bilibili.com/video/BV1Wy4y1h7ii

### 数组对象 numpy.array()

import numpy as np

np.array([1,2,3,4,5])

a=np.zeros((3,2)) 三行二列的全零数组

a.shape 获取a数组的尺寸

np.ones((2,4)) 全一数组

np.arrange(3,7) 创建递增、递减数列

np.linspace(0,1,5) 等差数列

np.random.rand(2,4) 生成随机数组



dtype属性指定数据类型

np.int8/16/32/64 整形

np.uint8/16/32/64 无符号整型

np.float32/64 浮点数

bool 布尔值

 str 字符串

现有数组通过astype方法转换数据类型



### 基本运算

加减乘除（对应元素）、数乘

np.dot(a,b) 点乘

a @ b 矩阵乘法

np.sqrt() 求方根

np.sin() np.cos() 三角函数

np.log() np.power() 对数和指数运算



a.min() a,max() 求最值

a.argmin() a,argmax() 求最值的索引

a.sum a,mediam d.mean 求和 均值

a.var a.std 方差 标准差



参数axis 指定维度运算

---

#### 广播机制

---

a[0,1]  获取指定索引的元素

a[a<3] 筛选满足条件的元素

a[0,0:2] 切片，获取一块元素，冒号包前不包后

 起点：终点：步长（可正可负）

### 应用：图片处理

from PIL import Image

图片转化为rgb矩阵









