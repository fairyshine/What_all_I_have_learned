# *args和**kwargs——魔法变量

https://blog.csdn.net/qq_34062683/article/details/124727412



其实并不是必须写成\*args ( arguments ,位置参数)和\*\*kwargs(keyword arguments ,关键字参数)。 只有变量前面的 * (星号)才是必须的. 你也可以写成\*var 和\*\* vars. 而写成\*args 和\*\*kwargs只是一个通俗的命名约定。

基本使用：

```python
def receive_args(*args, **kwargs):
    print(f'args type is: {type(args)}, value is: {args}')
    print(f'kargs type is: {type(kwargs)}, value is: {kwargs}')

if __name__ == '__main__':
    receive_args(0,name='abc')
```
输出：

```python
args type is: <class 'tuple'>, value is: (0,)
kargs type is: <class 'dict'>, value is: {'name': 'jyz'}
```

由此我们得出：`args`本质是一个`tuple`，`kwargs`本质是`dict`，**值得注意的是，`*`是在定义函数的形式参数时用来标志参数的类型（位置参数还是关键字参数），在函数体内要拿到真正的参数需要将`*`去掉。**

---

而如果想要将字典类型作为关键字变量传到函数中去，需要在变量前使用`**`做额外的标志：

```python
def receive_args(*args, **kwargs):
    print('args is: ',args)
    print('kwargs is: ',kwargs)

if __name__ == '__main__':
    a = 0
    b = 1
    d = {'name':'jyz','gender':'male'}
    receive_args(a,b,**d) # d前用**做标志
```
输出：

```python
args is:  (0, 1)
kwargs is:  {'name': 'jyz', 'gender': 'male'}
```



## 结论

1. `args`本质是一个`tuple`，`kwargs`本质是`dict`；
1. 标准参数、\*args、\*\*kwargs  三者顺序是固定的
2. 传入关键字参数时，要么使用`key=value`形式传递；要么先定义`dict`再使用`**dict`传递。



# @,decorator——装饰器/修饰器



## 无参装饰器

函数：func()

加装饰器后 

func=decorator(func)

### 无参函数

```python
def decorator(func):
    def wrapper(): # 引用外部作用域的变量func
        #装饰器内容1
        #。。。
        result=func() #无返回值直接func()
        #装饰器内容2
        #。。。
        return result  #可选，针对有返回值的func
    return wrapper
 
#错误举例：
def decorator(func): # 通过参数接收外部的值
		#装饰器内容
    return func()
#这样会导致原参数的调用方式从func()变成了decorator(func)，不能使用@
#违反了不能修改被装饰对象调用方式的原则
```

### 有参函数

函数：func(*args,**kwargs)

```python
def decorator(func):
    def wrapper(*args,**kwargs): # 引用外部作用域的变量func
        #装饰器内容1
        #。。。
        result=func(*args,**kwargs)#无返回值则func(*args,**kwargs)
        #装饰器内容2
        #。。。
        return result  #可选，针对有返回值的func
    wrapper.__doc__=func.__doc__   #9,10行不加的话不影响使用
    wrapper.__name__=func.__name__ #但是查看fun的对应信息会变成wrapper
    return wrapper
```

多个装饰器可叠加

```python
@deco3
@deco2
@deco1
def func():
    pass
```

代码语义为：`func=deco3(deco2(deco1(func)))`

## 有参装饰器

```python
def auth(name):
    def decorator(func):
        def wrapper(*args,**kwargs): # 引用外部作用域的变量func
            #装饰器内容
            #。。。
            if name=='hi':
                print("Hi ! ")
            if name=='hello':
                print("Hello ! ")
            return func(*args,**kwargs)
        wrapper.__doc__=func.__doc__   #11、12行不加的话不影响使用
        wrapper.__name__=func.__name__ #但是查看fun的对应信息会变成wrapper
        return wrapper
    return decorator
  
@auth(name='hi')
def fun():
  		...
```

按照上述方式来实现保留原函数属性过于麻烦，functools模块下提供一个装饰器wraps专门用来帮我们实现这件事，用法如下

```python
from functools import wraps

def auth(name):
    def decorator(func):
        @wraps(func)
        def wrapper(*args,**kwargs): # 引用外部作用域的变量func
            #装饰器内容
            #。。。
            if name=='hi':
                print("Hi ! ")
            if name=='hello':
                print("Hello ! ")
            return func(*args,**kwargs)
        return wrapper
    return decorator
```



参考：
	https://zhuanlan.zhihu.com/p/109078881

# 关于类的细节

```python
class ...():
		def __init__(self,):
      
    def ... (self,):
   		...
    return 
  	
    @staticmethod
  	def ... ( , ):
      ...
    return 
```

1. 方法=类中的函数

2. 关于self

   所有类中的方法都需要添加默认参数self(预定俗成，代表类自己。用其他名称也行，在函数内起作用)，静态方法除外。

3. 静态方法

​		（1）不需要约定的默认参数self

​		（2）不可以引用类中的属性或方法

​		（3）可以在不创建类实例的情况下调用+



​				

# copy() & deepcopy()

对于简单的 object：
	用 shallow copy 和 deep copy 没区别
对于复杂的 object：
	如 list 中套着 list 的情况，shallow copy 中的 子list，并未从原 object 真的「独立」出来。也就是说，如果你改变原 object 的子 list 中的一个元素，你的 copy 就会跟着一起变。这跟我们直觉上对「复制」的理解不同。
原文链接：https://blog.csdn.net/qq_32907349/article/details/52190796



# lambda表达式

本质：仅需一行的函数

```python
def f(x):
	return x * x
f = lambda x: x*x

def g(x,y):
	return x + y
g = lambda x,y: x+y
```

