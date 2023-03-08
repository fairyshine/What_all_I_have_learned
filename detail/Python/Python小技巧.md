#### 字典的key和value互换

```python
{value:key for key,value in a_dict.items()}
```

#### 数字转化为列表

```python
(map(int, str(i))
```



#### 修改多层表格内的字符串

==字符串本身不可修改！需要替换！==

TableEE时用到

```python
for i in range(len(dataset)):
    dataset[i]["doc_id"] = dataset[i]["doc_id"][:-1]
    for j in range(len(dataset[i]['table_content'])):
        for k in range(len(dataset[i]['table_content'][j])):
                line = []
                for l in range(len(dataset[i]['table_content'][j][k])):
                     line.append(dataset[i]['table_content'][j][k][l].replace('（','(').replace('）',')'))
                dataset[i]['table_content'][j][k] = line
```

