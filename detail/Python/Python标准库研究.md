# difflib——比较序列

举例：

```python
from difflib import SequenceMatcher#导入库
def similarity(a, b):
    return SequenceMatcher(None, a, b).ratio()#引用ratio方法，返回序列相似性的度量
print(similarity('苏州大学', '位于苏州的大学'))
```

