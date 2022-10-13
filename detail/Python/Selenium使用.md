```python
driver.find_element(By.XPATH, '//*[@id="kw"]') 
# 根据xpath选择元素(万金油)
driver.find_element(By.CSS_SELECTOR, '#kw') 
# 根据css选择器选择元素
driver.find_element(By.NAME, 'wd') 
# 根据name属性值选择元素
driver.find_element(By.CLASS_NAME, 's_ipt') 
# 根据类名选择元素
driver.find_element(By.LINK_TEXT, 'hao123') 
# 根据链接文本选择元素
driver.find_element(By.PARTIAL_LINK_TEXT, 'hao') 
# 根据包含文本选择
driver.find_element(By.TAG_NAME, 'title') 
# 根据标签名选择
# 目标元素在当前html中是唯一标签或众多标签第一个时候使用
driver.find_element(By.ID, 'su') 
# 根据id选择
```

