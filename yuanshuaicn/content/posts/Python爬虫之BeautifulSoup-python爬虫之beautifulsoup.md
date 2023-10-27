---
title: Python爬虫之BeautifulSoup
date: 2021-10-24 18:38:23.971
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
  avatar: "https://aabb-2023.oss-cn-beijing.aliyuncs.com/hjscijg3uw.png"
tags: 
- Python
- 爬虫
---

## Python爬虫之BeautifulSoup

```python
#BeautifulSoup模块简介和安装

from bs4 import BeautifulSoup

#CSS 选择器：BeautifulSoup4
#和lxml 一样，Beautiful Soup 也是一个HTML/XML的解析器
#主要的功能也是如何解析和提取 HTML/XML 数据。


#模块下载安装：pip install bs4

#基础例子
html = """
<html><head><title>The Dormouse's story</title></head>
<body>
<p class="title" name="dromouse"><b>The Dormouse's story</b></p>
<p class="story">Once upon a time there were three little sisters; and their names were
<a href="http://example.com/elsie" class="sister" id="link1"><!-- Elsie --></a>,
<a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> and
<a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>;
and they lived at the bottom of a well.</p>
<p class="story">...</p>
"""

#解析字符串形式的html
soup=BeautifulSoup(html,"lxml")

# #解析本地html文件
# soup2=BeautifulSoup(open("index.html"))


#格式化输出soup对象
print(soup.prettify())

# #根据标签名获取标签信息 soup.标签名
# print(soup.title)

# #获取标签内容
# print(soup.title.string)

# #获取标签名
# print(soup.title.name)

# #获取标签内所有属性
# print(soup.p.attrs["name"])

#获取直接子标签，结果是一个列表
# print(soup.head.contents)

#获取直接子标签，结果是一个生成器
# for i in soup.head.children:
# 	print(i)


#获取所有子标签，结果是一个生成器
for i in soup.p.descendants:
	print(i)
  
#根据字符串查找所有的a标签，返回一个结果集，里面装的是标签对象
# data=soup.find_all("a")
# for i in data:
# 	print(i.string)

#根据正则表达式查找标签
# data2=soup.find_all(re.compile("^b"))
# for i in data2:
# 	print(i.string)

#根据属性查找标签
# data3=soup.find_all(id="link2")
# for i in data3:
# 	print(i)

#根据标签内容获取标签内容
data4=soup.find_all(text="Lacie")
data5=soup.find_all(text=["Lacie","Tillie"])
data6=soup.find_all(text=re.compile("Do"))
print(data6)

#CSS选择器类型：标签选择器、类选择器、id选择器

#通过标签名查找
# data=soup.select("a")

#通过类名查找
# data=soup.select(".sister")

#通过id查找
# data=soup.select("#link2")

#组合查找
# data=soup.select("p #link1")

#通过其他属性查找
data=soup.select('a[href="http://example.com/tillie"]')


print(data)
```

##