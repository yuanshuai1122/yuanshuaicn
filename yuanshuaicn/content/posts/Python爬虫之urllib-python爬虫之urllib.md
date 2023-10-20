---
title: Python爬虫之urllib
date: 2021-11-26 21:51:25.15
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/v2-2103a9f9659baf7be26d40fe5b4e9f1f_1440w.jpg'
theme: 'light'
tags: 
- Python
- 爬虫
---

（编码encode()）

pat=r"<title>(.*?)</title>"

data=re.findall(pat,reponse)


print(data[0])
```

```python
#创建自定义opener

from urllib import request

#构建HTTP处理器对象（专门处理HTTP请求的对象）
http_hander=request.HTTPHandler()

#创建自定义opener
opener=request.build_opener(http_hander)

#创建自定义请求对象
req=request.Request("http://www.baidu.com")

#发送请求，获取响应
#reponse=opener.open(req).read()

#把自定义opener设置为全局，这样用urlopen发送的请求也会使用自定义的opener;
request.install_opener(opener)

reponse=request.urlopen(req).read()


print(reponse)
```

```python
from urllib import request
import random

#反爬虫2：判断请求来源的ip地址
#措施：使用代理IP

	

proxylist=[
	{"http":"101.248.64.82:80"}
]

proxy=random.choice(proxylist)

#构建代理处理器对象
proxyHandler=request.ProxyHandler(proxy)

#创建自定义opener
opener=request.build_opener(proxyHandler)

#创建请求对象
req=request.Request("http://www.baidu.com")

res=opener.open(req)

print(res.read())
```

```python
#处理get请求

from urllib import request
import urllib
#http://www.baidu.com/s?wd=%E5%8C%97%E4%BA%AC #url编码

wd={"wd":"北京"}

url="http://www.baidu.com/s?"

#构造url编码
wdd=urllib.parse.urlencode(wd)

url=url+wdd

req=request.Request(url)

reponse=request.urlopen(req).read().decode()

print(reponse)


```

```python
# 实战：贴吧爬虫

from urllib import request
import urllib
import time

#构造请求头信息
header={
"User-Agent":"Mozilla/5.0 (Linux; U; An\
droid 8.1.0; zh-cn; BLA-AL00 Build/HUAW\
EIBLA-AL00) AppleWebKit/537.36 (KHTML, l\
ike Gecko) Version/4.0 Chrome/57.0.2987.13\
2 MQQBrowser/8.9 Mobile Safari/537.36"
}

#url规律

# http://tieba.baidu.com/f?kw=python&ie=utf-8&pn=0  #第一页 （1-1）*50

# http://tieba.baidu.com/f?kw=python&ie=utf-8&pn=50 #第二页 （2-1）*50

# http://tieba.baidu.com/f?kw=python&ie=utf-8&pn=100 #第三页 （3-1）*50

# http://tieba.baidu.com/f?kw=python&ie=utf-8&pn=150 #第四页（4-1）*50

# for i in range(1,4):
# 	print("http://tieba.baidu.com/f?kw=python&ie=utf-8&pn="+str((i-1)*50))

def loadpage(fullurl,filename):
	print("正在下载：",filename)
	req=request.Request(fullurl,headers=header)
	resp=request.urlopen(req).read()
	return resp


def writepage(html,filename):
	print("正在保存：",filename)

	with open(filename,"wb") as f:
		f.write(html)

	print("---------------------------")

#构造url
def tiebaSpider(url,begin,end):
	for page in range(begin,end+1):
		pn=(page-1)*50
		fullurl=url+"&pn="+str(pn) #每次请求的完整url
		filename="c:/第"+str(page)+"页.html" #每次请求后保存的文件名

		html=loadpage(fullurl,filename) #调用爬虫，爬取网页
		writepage(html,filename) #把获取到的网页信息写入本地

 


if __name__ == '__main__':

	kw=input("请输入贴吧名：")
	begin=int(input("请输入起始页："))
	end=int(input("请输入结束页："))

	url="http://tieba.baidu.com/f?"

	key=urllib.parse.urlencode({"kw":kw})

	url=url+key

	tiebaSpider(url,begin,end)

	time.sleep(10)
```

```python
# 实战：贴吧爬虫(完善)

from urllib import request
import urllib
import re
import random
#反爬虫1 ： 伪装浏览器的爬虫
#构造请求头信息
agent1="Mozilla/5.0 (Linux; U; Android 8.1.0; zh-cn; BLA-AL00 Build/HUAWEIBLA-AL00) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/57.0.2987.132 MQQBrowser/8.9 Mobile Safari/537.36"
agent2="Mozilla/5.0 (Linux; Android 8.1; EML-AL00 Build/HUAWEIEML-AL00; wv) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/53.0.2785.143 Crosswalk/24.53.595.0 XWEB/358 MMWEBSDK/23 Mobile Safari/537.36 MicroMessenger/6.7.2.1340(0x2607023A) NetType/4G Language/zh_CN"
agent3="Mozilla/5.0 (Linux; U; Android 8.0.0; zh-CN; MHA-AL00 Build/HUAWEIMHA-AL00) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/57.0.2987.108 UCBrowser/12.1.4.994 Mobile Safari/537.36"
agent4="Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.132 Safari/537.36"
agent5="Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.102 Safari/537.36"
list1=[agent1,agent2,agent3,agent4,agent5]

agent=random.choice(list1)
print(agent)

# 反爬虫2：判断请求来源的ip地址
# 措施：使用代理IP


# proxylist = [
# 	#{"http": "101.248.64.82:80"}
# 	#{"http": "49.89.87.40:3000"},
# 	#{"http": "36.249.109.42:9999"},
# 	#{"http": "58.254.220.116:53579"},
# 	#{"http": "183.166.136.49:8888"}
# ]
#
# proxy = random.choice(proxylist)
# print(proxy)
#
# # 构建代理处理器对象
# proxyHandler = request.ProxyHandler(proxy)
#
# # 创建自定义opener
# opener = request.build_opener(proxyHandler)
# #把自定义opener设置为全局，这样用urlopen发送的请求也会使用自定义的opener;
# request.install_opener(opener)


header={
"User-Agent":agent
}

url="http://fanyi.youdao.com/translate?smartresult=dict&smartresult=rule"

#key="自学"
key = input("请输入要翻译的文字：")

#post请求需要提交的参数
formdata={
	"i":key,
	"from":"AUTO",
	"to":"AUTO",
	"smartresult":"dict",
	"client":"fanyideskweb",
	"salt":"15503049709404",
	"sign":"3da914b136a37f75501f7f31b11e75fb",
	"ts":"1550304970940",
	"bv":"ab57a166e6a56368c9f95952de6192b5",
	"doctype":"json",
	"version":"2.1",
	"keyfrom":"fanyi.web",
	"action":"FY_BY_REALTIME",
	"typoResult":"false"
}
#转码
data=urllib.parse.urlencode(formdata).encode(encoding='utf-8')

req=request.Request(url,data=data,headers=header)

resp=request.urlopen(req).read().decode()

#正则表达式 提取"tgt":"和"}]]中间的任意内容
pat=r'"tgt":"(.*?)"}]]'

result=re.findall(pat,resp)

print(result[0])
```

```python
#异常处理
from urllib import request

list1=[

"http://www.baidu.com",
"http://www.baidu.com",
"http://www.jiswiswissnduehduehd.com",
"http://www.baidu.com",
"http://www.baidu.com",

]

i=0
for url in list1:
	i=i+1
	
	try:
		request.urlopen(url)
		print("第",i,"次请求完成")
	except Exception as e:
		print(e)
```

```python
# Cookie
from urllib import request


url = "http://i.baidu.com/"

headers = {
    "User-Agent" : "Mozilla/5.0 (Windows NT 10.0; Win64; x64) App\
    leWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.99 Safari/537.36",

    "Cookie":"BAIDUID=F1676F6D91D12CF987A81C35B5683EF5:FG=1; PSTM=1543068715; BIDUPSID=F1676F6D91D12CF987A81C35B5683EF5; BDUSS=k4VllQdW9-ZDR6fmo1LWxIeEo5LWd1OTMxLUhCQkY5eUZlZXY0OEtKSzdVSEZjQVFBQUFBJCQAAAAAAAAAAAEAAABATb2F1~OwttK76ti0s8zs0cQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAALvDSVy7w0lca3; BDORZ=B490B5EBF6F3CD402E515D22BCDA1598; PHPSESSID=e1sso19h9qnjugdq974cpofk50; __guid=62687476.678578182179940700.1550386788528.5847; Hm_lvt_4010fd5075fcfe46a16ec4cb65e02f04=1550386790; monitor_count=4; Hm_lpvt_4010fd5075fcfe46a16ec4cb65e02f04=1550387997"
}

req = request.Request(url, headers = headers)

response = request.urlopen(req)

print(response.read().decode())
```