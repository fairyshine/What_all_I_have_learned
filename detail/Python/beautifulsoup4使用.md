# 引用

```python
from bs4 import BeautifulSoup
import bs4
```



# 解析器

~~~python
soup = BeautifulSoup('data'，'html.parser')
~~~


(1)Python标准库中的HTML解析器'html.parser'
(2)lxml的HTML解析器'lxml'
	pip install lxml
(3)lxml的XML解析器'xml'
	pip install lxml
(4)html5lib的解析器'html5lib'
	pip install html5lib

# BeautifulSoup类的基本元素

Beautiful Soup将复杂HTML文档转换成一个复杂的树形结构,每个节点都是Python对象,所有对象可以归纳为4种:`Tag` , `NavigableString` , `BeautifulSoup` , `Comment` .

##  Tag

`Tag` 对象与XML或HTML原生文档中的tag相同:

### Name

### Attributes

## NavigableString

字符串常被包含在tag内.Beautiful Soup用 `NavigableString` 类来包装tag中的字符串:

## BeautifulSoup

`BeautifulSoup` 对象表示的是一个文档的全部内容.大部分时候,可以把它当作 `Tag` 对象

## Comment

注释，`Comment` 对象是一个特殊类型的 `NavigableString` 对象

# 遍历文档树

# 搜索文档树

`find()` 和 `find_all()`

# 修改文档树

`Tag.decompose()` 方法将当前节点移除文档树并完全销毁

# 输出

