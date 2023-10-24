---
title: xpath表达式
date: 2021-post-26 21:51:32.12
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/v2-2103a9f9659baf7be26d40fe5b4e9f1f_1440w.jpg'
theme: 'light'
tags: 
- Python
- 爬虫
---

## Python爬虫之xpath表达式

```python
#xpath表达式

#有同学说，我正则用的不好，处理HTML文档很累，有没有其他的方法？
#有！那就是XPath，我们可以先将 HTML文件 转换成 XML文档，
#然后用 XPath 查找 HTML 节点或元素。

#我们需要安装lxml模块来支持xpath的操作。

#使用 pip 安装：pip install lxml
```

```python
#解析字符串形式html
text ='''
<div>
    <ul>
         <li class="item-0"><a href="link1.html">张三</a></li>
         <li class="item-1"><a href="link2.html">李四</a></li>
         <li class="item-inactive"><a href="link3.html">王五</a></li>
         <li class="item-1"><a href="link4.html">赵六</a></li>
         <li class="item-0"><a href="link5.html">老七</a> 
     </ul>
 </div>
'''

from lxml import etree

#etree.HTML()将字符串解析成了特殊的html对象
html=etree.HTML(text)

#将html对象转成字符串
result=etree.tostring(html,encoding="utf-8").decode()

print(result)
```

```python
#解析本地html
#爬虫中网页处理方式：
#1，在爬虫中，数据获取和数据清洗一体，HTML()
#2、数据获取和数据清洗分开，parse()

from lxml import etree 

#获取本地html文档
html=etree.parse(r"C:\file\hello.html")

result=etree.tostring(html,encoding="utf-8").decode()

print(result)
```

```python
#获取一类标签
from lxml import etree

html=etree.parse(r"C:\file\hello.html")

result=html.xpath("//a") #获取所有span标签的信息


print(result[0].text)
```

```python
#获取指定属性的标签

from lxml import etree 

html = etree.parse("c:/file/hello.html")

# result1=html.xpath("//li[@class='item-88']")

result2=html.xpath("//li/a[@href='link2.html']")

print(result2)
```

```python
from lxml import etree 
#获取标签的属性
html = etree.parse("c:/file/hello.html")
# result1=html.xpath("//li/@class")
result2=html.xpath("//li/a/@href")

for i in result2:
	requests.get(i)
```

```python
#获取子标签

from lxml import etree 

html = etree.parse("c:/file/hello.html")

result1=html.xpath("//li/a") #获取下一级子标签
result2=html.xpath("//li//span") #获取所有符合条件子标签

#print(result2[1].text)

#获取li标签下a标签里所有的class

result3=html.xpath("//li/a//@class")

print(result3)
```

```python
#获取标签名和内容

from lxml import etree 

html = etree.parse("c:/file/hello.html")

#获取倒数第二个li元素下a的内容

#result1=html.xpath("//li[last()-1]/a")

# result2=html.xpath("//li/a")
# print(result1[-2].text) #.text获取标签内容


#获取 class 值为 bold 的标签名
result3=html.xpath("//*[@class='bold']")

print(result3[0].tag) #.tag表示获取标签名
```

```python
#爬取糗事百科段子
import requests
from lxml import etree 

url = 'https://www.qiushibaike.com/'
headers = {
	'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64)\
	AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0\
	.2743.116 Safari/537.36',
	'Accept-Language': 'zh-CN,zh;q=0.8'}


response=requests.get(url,headers=headers).text

html=etree.HTML(response)

result1=html.xpath('//div//a[@class="recmd-content"]/@href')

#print(result1)

#https://www.qiushibaike.com/article/121207030

for site in result1:
	xurl="https://www.qiushibaike.com"+site
	response2=requests.get(xurl).text
	html2=etree.HTML(response2)
	result2=html2.xpath("//div[@class='content']")

	print(result2[0].text)
```

```python
#百度贴吧图片爬虫
import urllib
import urllib.request
from lxml import etree
# 全局取消证书验证
import ssl
ssl._create_default_https_context = ssl._create_unverified_context


class Spider(object):
    def __init__(self):
        self.beginPage=1
        self.endPage=3
        self.url="http://tieba.baidu.com/f?"
        self.ua_header = {"User-Agent" : "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1 Trident/5.0;"}
        self.fileName=1

    #构造url
    def tiebaSpider(self):
        for page in range(self.beginPage,self.endPage+1):
            pn=(page-1)*50
            wo={'pn':pn,'kw':tiebaName}
            word=urllib.parse.urlencode(wo)
            myurl=self.url+word
            self.loadPage(myurl)

    #爬取页面内容
    def loadPage(self,url):
        req=urllib.request.Request(url,headers=self.ua_header)
        data=urllib.request.urlopen(req).read()

        html=etree.HTML(data)
        links=html.xpath('//div[@class="threadlist_lz clearfix"]/div/a/@href')

        for link in links:
            link="http://tieba.baidu.com"+link
            self.loadImages(link)
            

    #爬取帖子详情页，获得图片的链接
    def loadImages(self,link):
        req=urllib.request.Request(link,headers=self.ua_header)
        data=urllib.request.urlopen(req).read()
        html=etree.HTML(data)
        links=html.xpath('//img[@class="BDE_Image"]/@src')
        for imageslink in links:
            self.writeImages(imageslink)


    #通过图片所在链接，爬取图片并保存图片到本地：
    def writeImages(self,imagesLink):
        print("正在存储图片：",self.fileName,"....")

        image=urllib.request.urlopen(imagesLink).read()

        #保存图片到本地
        file=open(r"/Users/yuanshuai/Desktop/img//"+str(self.fileName)+".jpg","wb")
        file.write(image)

        file.close()

        self.fileName+=1


if __name__ == '__main__':

    tiebaName = input("请输入要爬取的贴吧名：")

    mySpider=Spider()

    mySpider.tiebaSpider()
```

##