---
title: Python爬虫之scrapy框架
date: 2021-posts-26 21:51:43.559
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/v2-2103a9f9659baf7be26d40fe5b4e9f1f_1440w.jpg'
theme: 'light'
tags: 
- Python
- 爬虫
---

## Python爬虫之scrapy框架

1. 创建项目

>scrapy startproject 项目名

2. 创建爬虫

> scrapy  genspider  爬虫识别名称   '要爬取的主机地址'

3. 运行爬虫

> scrapy crawl 爬虫识别名称

### 1.Scrapy框架的安装

> pip3 install scrapy

### 2.Scrapy框架的简单使用

- 常用命令

> 创建项目：scrapy startproject xxx
> 进入项目：cd xxx #进入某个文件夹下
> 创建爬虫：scrapy genspider xxx（爬虫名） xxx.com （爬取域）
> 生成文件：scrapy crawl xxx -o xxx.json (生成某种类型的文件)
> 运行爬虫：scrapy crawl XXX
> 列出所有爬虫：scrapy list
> 获得配置信息：scrapy settings [options]

- Scrapy项目下包含

> scrapy.cfg: 项目的配置文件
> tutorial/: 该项目的python模块。在此放入代码（核心）
> tutorial/items.py: 项目中的item文件.（这是创建容器的地方，爬取的信息分别放到不同容器里）
> tutorial/pipelines.py: 项目中的pipelines文件.
> tutorial/settings.py: 项目的设置文件.（我用到的设置一下基础参数，比如加个文件头，设置一个编码）
> tutorial/spiders/: 放置spider代码的目录. （放爬虫的地方）

- 容器（items）的定义，容器不一定是一开始全部都定义好的，可以跟随项目的更新一点点向里面添加

  也就是定义我们要爬取的内容

> import scrapy
> class DmozItem(scrapy.Item): #创建一个类，继承scrapy.item类，就是继承人家写好的容器
> title = scrapy.Field() # 需要取哪些内容，就创建哪些容器
> link = scrapy.Field()
> desc = scrapy.Field()

- 一个简单爬虫小例子

```python
import scrapy
class DmozSpider(scrapy.Spider): # 继承Spider类
    name = "dmoz" # 爬虫的唯一标识，不能重复，启动爬虫的时候要用
    allowed_domains = ["dmoz.org"] # 限定域名，只爬取该域名下的网页
    start_urls = [ # 开始爬取的链接
        "https://www.baidu.com/"
    ]
    def parse(self, response):
        filename = response.url.split("/")[-2] # 获取url，用”/”分段，获去倒数第二个字段
        with open(filename, 'a') as f:
            f.write(response.body) # 把访问的得到的网页源码写入文件
```

里面的parse方法，这个方法有两个作用
1.负责解析start_url下载的Response 对象，根据item提取数据（解析item数据的前提是parse里全部requests请求都被加入了爬取队列）
2.如果有新的url则加入爬取队列，负责进一步处理，URL的Request 对象
这两点简单来说就是编写爬虫的主要部分

那么爬虫编写完，我们需要启动爬虫

> cd XXX

进入到你的文件夹下
输入命令,启动爬虫

> scrapy crawl dmoz

那么启动爬虫时发生了什么？
Scrapy为Spider的 start_urls 属性中的每个url创建了Request 对象，并将 parse 方法作为回调函数(callback)赋值给了requests,而requests对象经过调度器的调度，执行生成response对象并送回给parse() 方法进行解析,所以请求链接的改变是靠回调函数实现的。

> yield scrapy.Request(self.url, callback=self.parse)

### 3.Scrapy框架的整体架构和组成

- 官方的Scrapy的架构图

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_spider/1.png)

>图中绿色的是数据的流向
>我们看到图里有这么几个东西，分别是
>Spiders：爬虫，定义了爬取的逻辑和网页内容的解析规则，主要负责解析响应并生成结果和新的请求
>Engine：引擎，处理整个系统的数据流处理，出发事物，框架的核心。
>Scheduler：调度器，接受引擎发过来的请求，并将其加入队列中，在引擎再次请求时将请求提供给引擎
>Downloader：下载器，下载网页内容，并将下载内容返回给spider
>ItemPipeline：项目管道，负责处理spider从网页中抽取的数据，主要是负责清洗，验证和向数据库中存储数据
>Downloader Middlewares：下载中间件，是处于Scrapy的Request和Requesponse之间的处理模块
>Spider Middlewares：spider中间件，位于引擎和spider之间的框架，主要处理spider输入的响应和输出的结果及新的请求middlewares.py里实现

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_spider/2.png)

- 最后我们来顺一下scrapy框架的整体执行流程：

> 1.spider的yeild将request发送给engine
> 2.engine对request不做任何处理发送给scheduler
> 3.scheduler，生成request交给engine
> 4.engine拿到request，通过middleware发送给downloader
> 5.downloader在\获取到response之后，又经过middleware发送给engine
> 6.engine获取到response之后，返回给spider，spider的parse()方法对获取到的response进行处理，解析出items或者requests
> 7.将解析出来的items或者requests发送给engine
> 8.engine获取到items或者requests，将items发送给ItemPipeline，将requests发送给scheduler（ps，只有调度器中不存在request时，程序才停止，及时请求失败scrapy也会重新进行请求）

### 4.中间件介绍

请查看xmind思维导图：Scrapy.xmind

### 5.附件

阳光问政平台爬虫（Scrapy实现：2020.7.21） sunSpider项目，详情查看关于我-我的项目

##