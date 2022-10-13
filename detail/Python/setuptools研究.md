参考资料：

setuptools简介：https://blog.csdn.net/weixin_38384296/article/details/112715321

# 简介

distutils：标准库中负责建立Python第三方库的安装器，能进行Python模块的安装和发布。

setuptools：distutils增强版，不包括在标准库中。

## 安装使用

两种方式

pip install -e .（或python setup.py develop）：实质建立到该文件夹中包的链接。”`site-packages`”目录里创建一个指向当前项目位置的链接。这样如果当前位置的源码被改动，就会马上反映到”`site-packages`”里。

​						-e 理解为 editable，修改本地文件，调用的模块以最新文件为准。

pip install .（或python setup.py install）：正常安装，文件移动到conda对应env包目录。

​						将安装后的模块freeze在pip/conda依赖下，换句话说，再修改本地的原项目文件，不会导致对应模块发生变化。

===

安装后，

pip list ：查看已安装包列表

pip show 包名：查看该包相关信息

# 文件结构

项目目录/

 ├ setup.py

 └ 包目录（包名）/

​     ├ __ __ init__.py

​                   ...

# 特性文件

setup.py

MANIFEST.in

setup.cfg

# setup.py

```python
#coding:utf8
from setuptools import setup
setup(
   name='MyApp',        # 项目名
   version='1.0',       # 版本号
   packages=['myapp']   # 包括在安装包内的Python包
   include_package_data=True,   # 启用清单文件MANIFEST.in
   exclude_package_data={'':['.gitignore']},
   install_requires=[   # 依赖列表
       'Flask>=0.10',
       'Flask-SQLAlchemy>=1.5,<=2.1'
   ]
)
```

## steup.cfg

可在该文件填写信息

https://setuptools.pypa.io/en/latest/userguide/declarative_config.html#

例子：

```python
# setup.py；会自动读取setup.cfg中的设置
import setuptools
setuptools.setup() # 也可有参调用，则会覆盖.cfg的对应条目

# setup.cfg；
[metadata]
name = hellokpg
version = 1.0
author = xxx
long_description = file: Readme.md # 从文件中读取
license = MIT
url = https://github.com/user/repo
classifiers = # PyPI的分类，类似于标签，所有条目见 https://pypi.org/pypi?%3Aaction=list_classifiers
    Development Status :: 3 - Alpha
    Programming Language :: Python :: 3

[options]
packages = find: # 自动搜索存在__init__.py的文件夹作为包
install_requires = # 依赖，pip安装时靠的就是这个而不是requirements.txt
    requests

[options.entry_points]
console_scripts =
    pyhello = hello.__main__:main

# hello_impl.py
def say(to):
    print('hello', to)

# __init__.py
from .hello_impl import say

# __main__.py
from . import say
import sys
def main():
    say(sys.argv[1])
if __name__ == '__main__':
    main()
```



# MANIFEST.in

打包非py的静态资源

setup.py中输入`include_package_data=True`启用



The manifest template commands are:

|                 Command                 |                         Description                          |
| :-------------------------------------: | :----------------------------------------------------------: |
|        **include pat1 pat2 ...**        |    include all files matching any of the listed patterns     |
|        **exclude pat1 pat2 ...**        |    exclude all files matching any of the listed patterns     |
| **recursive-include dir pat1 pat2 ...** | include all files under *dir* matching any of the listed patterns |
| **recursive-exclude dir pat1 pat2 ...** | exclude all files under *dir* matching any of the listed patterns |
|    **global-include pat1 pat2 ...**     | include all files anywhere in the source tree matching — & any of the listed patterns |
|    **global-exclude pat1 pat2 ...**     | exclude all files anywhere in the source tree matching — & any of the listed patterns |
|              **prune dir**              |                exclude all files under *dir*                 |
|              **graft dir**              |                include all files under *dir*                 |

参考自：

- https://blog.csdn.net/weixin_43590796/article/details/121122850
- https://docs.python.org/2/distutils/sourcedist.html

# 技巧

## find_packages()——自动搜索Python包

**自动搜索可以引入的Python包**，它默认在与 setup.py 文件同一目录下搜索各个含有 `__init__.py` 的目录做为要添加的包，不包含子文件夹。

`packages=setuptools.findpackages()`

另一种方式：

**find_namespace_packages**

不包含__ init__.py的命名空间包，可使a.basic和a.extend在不同的重名a文件夹（子文件夹仍需包含init，即basic、extend）

## entry_points 

https://setuptools.pypa.io/en/latest/userguide/entry_point.html#

例子：

`entry_points={"console_scripts": ["rex = rex.cmds:main"]},`

在上面这段代码里， `console_scripts` 是一种特殊的 entry_points 。 `setuptools` 从中读取 `"<console_script_name> = <python_package:object_name>"` 。当把这个 Python 包安装好后，它会自动创建一个命令行工具。

### console_scripts

can use `sys.stdin`, `sys.stdout`

### gui_scripts

only affects Windows systems

### 句法

```
<name> = <package_or_module>[:<object>[.<attr>[.<nested-attr>]*]]
```

## 打包

```shell
#源代码包
python setup.py sdist

# 二进制分发包
python setup.py bdist
# wheel二进制分发包
python setup.py bdist_wheel
# egg二进制分发包（旧式）
python setup.py bdist_egg
```

