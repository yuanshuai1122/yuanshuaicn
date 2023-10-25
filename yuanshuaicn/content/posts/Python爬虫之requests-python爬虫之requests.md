---
title: Python爬虫之requests
date: 2021-posts-26 21:51:21.002
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/v2-2103a9f9659baf7be26d40fe5b4e9f1f_1440w.jpg'
theme: 'light'
tags: 
- Python
- 爬虫
---

# Python爬虫之requests

### 什么是requests？

**Requests** is an elegant and simple HTTP library for Python, built for human beings. You are currently looking at the documentation of the development release.

通过pip install requests 可以帮你安装它。request可以帮助我们发送网络请求，传递URL参数，响应内容，定制请求头以及发送post请求等等。

```python
import requests

headers = {
"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) Ap\
pleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.99 Sa\
fari/537.36"
}

wd={"wd":"中国"}

response=requests.get("http://www.baidu.com/s?",params=wd,headers=headers)  #get请求的参数为params

data=response.text #返回一个字符串形式的数据

data2=response.content #返回一个二进制形式的数据

print(data2.decode())
```

```python
import requests
import re

#构造请求头信息
header={
"User-Agent":"Mozilla/5.0 (Linux; U; An\
droid 8.1.0; zh-cn; BLA-AL00 Build/HUAW\
EIBLA-AL00) AppleWebKit/537.36 (KHTML, l\
ike Gecko) Version/4.0 Chrome/57.0.2987.13\
2 MQQBrowser/8.9 Mobile Safari/537.36"
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


response=requests.post(url,headers=header,data=formdata)   #post请求的参数为data

#正则表达式 提取"tgt":"和"}]]中间的任意内容
pat=r'"tgt":"(.*?)"}]]'

result=re.findall(pat,response.text)

print(result)
```

```python
import requests

#设置ip地址
proxy={
"http":"http://101.248.64.72:80",
"http":"http://101.248.64.68:80",
"https":"https://101.248.64.72:80",
}

response=requests.get("http://www.baidu.com",proxies=proxy)

print(response.content.decode())
```

```python
import requests

response=requests.get("http://www.baidu.com")

#1.获取返回的cookiejar对象
cookiejar=response.cookies

#2.将cookiejar转换成字典
cookiedict=requests.utils.dict_from_cookiejar(cookiejar)

#print(cookiejar)
print(cookiedict)
```

```python
#使用session实现登陆

# import requests
#
# headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.99 Safari/537.36"}
#
# #创建session对象
# ses=requests.session()
#
# #构造登陆需要的参数
# data={"email":"3254272716@qq.com","password":"123321a"}
#
# #通过传递用户名密码得到cookie信息
# ses.post("http://www.renren.com/PLogin.do",data=data)
#
# #请求需要的页面
# response=ses.get("http://www.renren.com/880151247/profile")
#
# print(response.text)


import requests

headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.99 Safari/537.36"}

#创建session对象
ses=requests.session()

#构造登陆需要的参数
data={"email":"18232336174@163.com","password":"18232336174..."}

#通过传递用户名密码得到cookie信息
ses.post("http://mail.163.com",data=data)

#请求需要的页面
response=ses.get("http://mail.163.com/js6/main.jsp?sid=KAOGBblSlxmIGlAAmDSSXTjrBseNxEhE&df=mail163_letter#module=contact.ContactModule%7C%7B%7D")

print(response.content.decode())
```

```python
import re   # python 的正则库
import requests     # python 的requests库
import time


# page=int(input("请输入您要爬取的页数："))

songID=[]
songName=[]

page_size = int(input("请问要爬取第几页呢："))

for i in range(0,page_size):
	url="http://www.htqyy.com/top/musicList/hot?pageIndex="+str(i)+"&pageSize=20"

#$url = "http://www.htqyy.com/top/hot"

#构造请求头信息
header = {
"Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9",
"Accept-Encoding": "gzip, deflate",
"Accept-Language": "zh-CN,zh;q=0.9",
"Cache-Control": "max-age=0",
"Connection": "keep-alive",
#"Cookie":" __cfduid=d54ff2470d42e999d942b3b64cc266a9f1594821751; BAIDU_SSP_lcr=https://www.baidu.com/link?url=zPqjAbMqG9O52ECGWPxIoo5nIDVDpw6DO0i0JHZqbgy&wd=&eqid=e3b52e830007c46a000000035f0f0c70; blk=0; Hm_lvt_74e11efe27096f6ef1745cd53f168168=1594821752; isPlay=0; jploop=false; Hm_lpvt_74e11efe27096f6ef1745cd53f168168=1594828497",
"Host": "www.htqyy.com",
"Referer": "http://www.htqyy.com/top/hot",
"Upgrade-Insecure-Requests": "1",
"User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/83.0.4103.116 Safari/537.36"
}

#获取音乐榜单的网页信息
html=requests.get(url,headers = header)

strr=html.text

pat1=r'title="(.*?)" sid'
pat2=r'sid="(.*?)"'

idlist=re.findall(pat2,strr)
titlelist=re.findall(pat1,strr)

songID.extend(idlist)
songName.extend(titlelist)


for i in range(0,len(songID)):
	songurl="http://f2.htqyy.com/play8/"+str(songID[i])+"/mp3/7"
	songname=songName[i]

	data=requests.get(songurl).content

	print("正在下载第",i+1,"首,""歌曲名为：",songName[i])

	with open("/Users/yuanshuai/Downloads/music/{}.mp3".format(songname),"wb") as f:
		f.write(data)

	time.sleep(0.5)
```