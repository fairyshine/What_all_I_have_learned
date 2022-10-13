正则表达式在线测试工具：https://regex101.com/



# 快速入门

https://www.bilibili.com/video/BV1da4y1p7iZ

perl风格

## 限定符 (Quantifier)

a* 	a出现0次或多次
a+ 	a出现1次或多次
a？	a出现o次或1次
a{6}	 a出现6次
a{2，6} 	a出现2-6次
a{2,}	a出现两次以上

## 或运算符 (OR Operator)
（alb）匹配a或者b
(ab)1(cd）匹配ab或者cd
## 字符类 (Character Classes)
[abc]		匹配a或者b或者c
[a-c] 		同上
[a-fA-F0-9] 	匹配小写＋大写英文字符以及数字
[ ^0-9 ]	 匹配非数字字符

## 元字符 (Meta-characters)

\d	匹配数字字符
\D	匹配非数字字符
\w 	匹配单词宇行（英文、数宇、下划线，
\W 	匹配非单词字符
\s	  匹配空白符（包含换行符、Tab)
\S	  匹配非空白字符
.		匹配任意字符（换行符除外）
\bword\b \b标注字符的边界(全字匹配）
^	匹配行首
$	匹配行尾

## 贪婪/懒惰匹配 (Greedy / Lazy Match)

<.+＞默认贪梦匹配 “任意字符”
＜.+＞懒惰匹配 “任意字符”

# 详细教程

正则表达式30分钟入门教程 作者：deerchao
https://deerchao.cn/tutorials/regex/regex.htm
Regex tutorial — A quick cheatsheet by examples(英文） 作者：Jonny Fox
https://medium.com/factory-mind/regex-tutorial-a-simple-cheatsheet-by-examples-649dc1c3f285
Regular Expressions Tutorial（英文）
https://www.regular-expressions.info/tutorial.html

