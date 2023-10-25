---
title: Python爬虫（全）
date: 2021-posts-26 21:52:17.227
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
# 实战：贴吧爬虫

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

## 二、requests

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

## 三、正则表达式

```python
import re

#原子：正则表达式中实现匹配的基本单位
#元字符：正则表达式中具有特殊含义的字符

#以普通字符作为原子（匹配一个普通字符）
a="湖南湖北广东广西"
pat="湖北"
result=re.search(pat,a)
print(result)


#匹配通用字符
#\w 任意字母/数字/下划线 
#\W 和小写w相反 
#\d 十进制数字
#\D 除了十进制数以外的值
#\s 空白字符 
#\S 非空白字符 

b="136892763900"
pat2="1\d\d\d\d\d\d\d\d\d\d"
print(re.search(pat2,b))

c="@@@@@@@@@@##@!_tdyuhdihdiw"
pat3=r"\W\w\w"
print(re.search(pat3,c))


#匹配数字、英文、中文
# 数字 [0-9]
# 英文 [a-z][A-Z]
# 中文 [\u4e00-\u9fa5]

d="!@#$@#@##$张三%$^%$%#@$boy#@%##$%$$@#@#23@#@#@#@##$%$%$"

pat1=r"[\u4e00-\u9fa5][\u4e00-\u9fa5]"
pat2=r"[a-z][a-z][a-z]"
pat3=r"[0-9][0-9]"

result1=re.search(pat1,d)
result2=re.search(pat2,d)
result3=re.search(pat3,d)

print(result1,result2,result3)


#原子表
#定义一组平等的原子
b="18689276390"
pat2="1[3578]\d\d\d\d\d\d\d\d\d"
print(re.search(pat2,b))

c="nsiwsoiwpythonjsoksosj"
pat3=r"py[abcdt]hon"

print(re.search(pat2,b))





#元字符--正则表达式中具有特殊含义的字符
# . 匹配任意字符 \n除外
# ^ 匹配字符串开始位置  ^136
# $ 匹配字符串中结束的位置 6666$
# * 重复0次1次多次前面的原子 \d*
# ? 重复一次或者0次前面的原子 \d?
# + 重复一次或多次前面的原子  \d+

d="135738484941519874888813774748687"
pat1="..."
pat2="^135\d\d\d\d\d\d\d\d"
pat3=".*8687$"
pat4="8*"
pat5="8+"
print(re.search(pat5,d))


#匹配固定次数
#{n}前面的原子出现了n次
#{n,}至少出现n次
#{n,m}出现次数介于n-m之间

a="234ded65de45667888991jisw"
pat1=r"\d{8,10}"

print(re.search(pat1,a))



# #多个表达式 | 
a="13699998888"
b="027-1234567"

pat1=r"1[3578]\d{9}|\d{3}-\d{7}"

print(re.search(pat1,a))





#分组 ()
a="jiwdjeodjo@$#python%$$^^&*&^%$java#@!!!!!!!!!!!!!!13688889999!!!!!!!!!!!!!!!!!#@#$#$"
pat=r"(python).{0,}(java).{0,}(1[3578]\d{9})"
print(re.search(pat,a).group(3))


a="jiwdjeodjo@$#python%$$^^&*&^%$java#@!!!!!!!!!!!!!!aaa我要自学网bbb!!!!!!!!!!!!!!!!!#@#$#$"
pat=r"aaa(.*?)bbb"
print(re.findall(pat,a))





#贪婪模式和非贪婪模式
#贪婪模式：在整个表达式匹配成功的前提下，尽可能多的匹配；
#非贪婪模式：在整个表达式匹配成功的前提下，尽可能少的匹配 ( ? )；
#Python里默认是贪婪的。
strr='aa<div>test1</div>bb<div>test2</div>cc'
pat1=r"<div>.*</div>"
print(re.search(pat1,strr)) #贪婪模式


strr='aa<div>test1</div>bb<div>test2</div>cc'
pat1=r"<div>.*?</div>"
print(re.findall(pat1,strr)) #非贪婪模式

```

```python
import re

#compile函数---将正则表达式转换成内部格式，提高执行效率

strr="PYTHON666Java"

pat=re.compile(r"Python",re.I) #模式修正符：忽略大小写


print(pat.search(strr))
```

```python
import re

#match函数和search函数

# match函数--匹配开头
# search函数--匹配任意位置

#这两个函数都是一次匹配，匹配到一次就不再往后继续匹配了

strr="javapythonjavahtmlpythonjs"

pat=re.compile(r"python")

print(pat.search(strr).group())
```

```python
import re

#findall()   查找所有匹配的内容，装到列表中
#finditer()  查找所有匹配的内容，装到迭代器中

strr="hello--------hello-----------\
---------hello-----------------\
---------hello--hello----------------\
----------hello---------hello----hello----------"

pat=re.compile(r"hello")

#print(pat.findall(strr))
data=pat.finditer(strr)

list1=[]

for i in data:
	list1.append(i.group())

print(list1)
```

```python
import re

#split()  按照能够匹配的子串将字符串分割后返回列表
#sub()    sub方法 用于替换

strr1="张三,,,李四,,,,,,,,,王五,,,,,,,,赵六"

pat1=re.compile(r",+")

result1=pat1.split(strr1)



strr2="hello 123,hello 456!"

pat2=re.compile(r"\d+")

result2=pat2.sub("666",strr2)



print(result2)
```

```python
import re
import requests

headers = {
"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) Ap\
pleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.99 Sa\
fari/537.36"
}

response=requests.get("http://changyongdianhuahaoma.51240.com/",headers=headers).text

pat1=r'<tr bgcolor="#EFF7F0">[\s\S]*?<td>(.*?)</td>[\s\S]*?<td>[\s\S]*?</td>[\s\S]*?</tr>'
pat2=r'<tr bgcolor="#EFF7F0">[\s\S]*?<td>[\s\S]*?</td>[\s\S]*?<td>(.*?)</td>[\s\S]*?</tr>'

pattern1=re.compile(pat1)
pattern2=re.compile(pat2)

data1=pattern1.findall(response)
data2=pattern2.findall(response)

resultlist=[]
for i in range(0,len(data1)):
	resultlist.append(data1[i]+data2[i])

print(resultlist)
```

```python
import urllib.request
import re
# 全局取消证书验证
import ssl
ssl._create_default_https_context = ssl._create_unverified_context


headers = {"User-Agent" : "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/\
537.36 (KHTML, like Gecko) Chrome/54.0.2840.99 Safari/537.36"}

page_num = int(input("请问要爬取第几页呢："))
page=str((page_num-1)*20)

url="https://movie.douban.com/j/chart/top_list?type=11&interval_id=100%3A90&action=&start="+page+"&limit=20"

req=urllib.request.Request(url,headers=headers)

data=urllib.request.urlopen(req).read().decode()

pat1=r'"rating":\["(.*?)","\d+"\]'
pat2=r'"title":"(.*?)"'

pattern1=re.compile(pat1,re.I)
pattern2=re.compile(pat2,re.I)

data1=pattern1.findall(data)
data2=pattern2.findall(data)

for x in range(len(data1)):
	print("排名：",x+1,"电影名：",data2[x],"豆瓣评分:",data1[x])
```

## 四、xpath表达式

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

## 五、BeautifulSoup

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

## 六、多线程

```python
import threading
import time


def run(name):
    print(name,"线程执行了！")
    time.sleep(5)

#创建2个线程对象
t1=threading.Thread(target=run,args=("t1",))
t2=threading.Thread(target=run,args=("t2",))

#启动线程
t1.start()
t2.start()

#等待子线程执行完毕后再执行主线程后面的内容
t1.join()
t2.join()

print("执行完毕")
```

```python
# 使用 threading 模块创建线程 
# 创建一个新的子类继承threading.Thread.
# 并实例化后调用 start() 方法启动新线程，即它调用了线程的 run() 方法

import threading
import time

#线程类
class myThread(threading.Thread):

    def __init__(self,name):
        threading.Thread.__init__(self)
        self.name = name

    def run(self):
        print("开始线程",self.name)
        print("线程执行中---1")
        time.sleep(1)
        print("线程执行中---2")
        time.sleep(1)
        print("线程执行中---3")
        time.sleep(1)
        print("线程执行中---4")
        time.sleep(1)
        print("线程执行中---5")
        time.sleep(1)
        print("结束线程",self.name)

#创建线程
t1=myThread("t1")
t2=myThread("t2")
t3=myThread("t3")

#开启线程
t1.start()
t2.start()
t3.start()

t1.join()
t2.join()
t3.join()

print("执行完毕")
```

```python
#队列Queue
import queue

#Queue是python标准库中的线程安全的实现,
#提供了一个适用于多线程编程的先进先出的数据结构，
#即队列，用来在生产者和消费者线程之间的信息传递。

#对于资源，加锁是个重要的环节。
#因为python原生的list,dict等，都是非线程安全的。
#而Queue，是线程安全的，因此在满足使用条件下，建议使用队列。


#创建队列
q=queue.Queue(maxsize=10)


for i in range(1,11):
    q.put(i) #往队列里面放值


# print(q.get())
# print(q.get())
# print(q.get())
# print(q.get())
# print(q.get())
# print(q.get())

#判断队列是否为空，循环取出所有值
while not q.empty():
    print(q.get())
```

```python
#多线程爬取糗事百科

# 使用了线程库
import threading
# 队列
import queue
import requests
import time
from lxml import etree

# https://www.qiushibaike.com/8hr/page/1/
# https://www.qiushibaike.com/8hr/page/2/
# https://www.qiushibaike.com/8hr/page/3/
#'//div/a[@class="recmd-content"]'

#采集网页线程--爬取段子列表所在的网页，放进队列
class Thread1(threading.Thread):
    def __init__(self, threadName,pageQueue,dataQueue):
        threading.Thread.__init__(self)
        self.threadName = threadName #线程名
        self.pageQueue = pageQueue #页码队列
        self.dataQueue = dataQueue #数据队列
        self.headers = {"User-Agent" : "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0;"}


    def run(self):
        print("启动线程"+self.threadName)
        while not flag1:
            try:
                page=self.pageQueue.get()
                url="https://www.qiushibaike.com/8hr/page/"+str(page)+"/"
                content=requests.get(url,headers=self.headers).text
                time.sleep(0.5)
                self.dataQueue.put(content) #将数据放入数据队列中
            except Exception as e:
                pass
        print("结束线程"+self.threadName)

#解析网页线程--从对、队列中拿到列表网页，进行解析，并存储到本地
class Thread2(threading.Thread):
    def __init__(self, threadName,dataQueue,filename):
        threading.Thread.__init__(self)
        self.threadName = threadName
        self.dataQueue = dataQueue
        self.filename = filename


    def run(self):
        print("启动线程"+self.threadName)
        
        while not flag2:
            try:
                data1=self.dataQueue.get()
                html=etree.HTML(data1)
                node_list=html.xpath('//div/a[@class="recmd-content"]')
                for node in node_list:
                    data=node.text
                    self.filename.write(data+"\n")

            except Exception as e:
                pass

        print("结束线程"+self.threadName)



flag1=False #判断页码队列中是否为空
flag2=False #判断数据队列中是否为空


def main():
    #页码队列
    pageQueue=queue.Queue(10)

    for i in range(1,11):
        pageQueue.put(i)
    #存放采集结果的数据队列
    dataQueue=queue.Queue()

    #保存到本地的文件
    filename=open(r"C:\file\dianzi.txt","a")

    #启动线程
    t1=Thread1("采集线程",pageQueue,dataQueue)
    t1.start()
    t2=Thread2("解析线程",dataQueue,filename)
    t2.start()

    #当pageQueue为空时，结束采集线程
    while not pageQueue.empty():
        pass
    global flag1
    flag1=True


    #当dataQueue为空时，结束解析线程
    while not dataQueue.empty():
        pass
    global flag2
    flag2=True

    t1.join()
    t2.join()

    filename.close()

    print("结束！")



if __name__ == '__main__':
    main()
```

## 七、验证码识别

```python
#识别车牌号
from aip import AipOcr
import re

APP_ID = '15469265'
API_KEY = 'rAGFtOChXtO7mnRPiwXg1Frf'
SECRET_KEY = 'Ailvoijh4X7lQIAoZ58UsGPlaDCmLIt7'

client = AipOcr(APP_ID, API_KEY, SECRET_KEY)


""" 读取图片 """
def get_file_content(filePath):
    with open(filePath, 'rb') as fp:
        return fp.read()

image = get_file_content(r'C:\Users\Administrator\Desktop\img\ee.jpg')

""" 调用通用文字识别, 图片参数为本地图片 """
data=str(client.basicGeneral(image)).replace(' ','')

pat=re.compile(r"{'words':'(.*?)'}")

result=pat.findall(data)[0]

print(result)
```

```python
#人工智能接口申请
from aip import AipOcr
import re

APP_ID="15725370"
API_KEY="t85bppstXXudNNSU0klALWgj"
SECRET_KEY="Zt7z61AXutINgWS1lqWe3xsWp9uePSFF"

client=AipOcr(APP_ID,API_KEY,SECRET_KEY)

with open(r"C:\Users\Administrator\Desktop\img\ee.jpg","rb") as f:
    image=f.read()

data=str(client.basicGeneral(image)).replace(" ","")

pat=re.compile(r"{'words':'(.*?)'}")

result=pat.findall(data)[0]

print(result)
```

```python
#文字识别
from aip import AipOcr
import re
import requests

APP_ID="15725370"
API_KEY="t85bppstXXudNNSU0klALWgj"
SECRET_KEY="Zt7z61AXutINgWS1lqWe3xsWp9uePSFF"

client=AipOcr(APP_ID,API_KEY,SECRET_KEY)

data=requests.get(r"http://127.0.0.1:8020/登陆验证码/login.html").text

pat=re.compile(r'<img src="(.*?)" style')

url="http://127.0.0.1:8020/登陆验证码/"+pat.findall(data)[0]

image=requests.get(url).content


data=str(client.basicGeneral(image)).replace(" ","")

pat=re.compile(r"{'words':'(.*?)'}")

result=pat.findall(data)[0]

print(result)
```

```python
#模拟验证码识别
from aip import AipOcr
import re
import requests

APP_ID="15725370"
API_KEY="t85bppstXXudNNSU0klALWgj"
SECRET_KEY="Zt7z61AXutINgWS1lqWe3xsWp9uePSFF"

client=AipOcr(APP_ID,API_KEY,SECRET_KEY)

data=requests.get(r"http://127.0.0.1:8020/登陆验证码/login.html").text

pat=re.compile(r'<img src="(.*?)" style')

url="http://127.0.0.1:8020/登陆验证码/"+pat.findall(data)[0]

image=requests.get(url).content


data=str(client.basicGeneral(image)).replace(" ","")

pat=re.compile(r"{'words':'(.*?)'}")

result=pat.findall(data)[0]

print(result)
```

## 八、scrapy框架

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

阳光问政平台爬虫（Scrapy实现：2020.7.21） sunSpider文件夹

## 九、fiddler手机抓包

- fiddler官网：https://www.telerik.com/fiddler

通过Fiddler抓包工具，可以抓取手机的网络通信，但前提是手机和电脑处于同一局域网内（WI-FI或热点），然后进行以下设置：

- ### 用Fiddler对Android应用进行抓包

1. 打开Fiddler设置

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_spider/fiddler1.png)

在`Connections`里设置允许连接远程计算机，确认后重新启动Fiddler

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_spider/fiddler2.png)

2. 在命令提示符下输入`ipconfig`查看本机IP

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_spider/fiddler3.png)

3.  打开Android设备的“设置”->“WLAN”，找到你要连接的网络，在上面长按，然后选择“修改网络”，弹出网络设置对话框，然后勾选“显示高级选项”。 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_spider/fiddler4.png)

4.  在“代理”后面的输入框选择“手动”，在“代理服务器主机名”后面的输入框输入电脑的ip地址，在“代理服务器端口”后面的输入框输入8888，然后点击“保存”按钮。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_spider/fiddler5.png)

5. 启动Android设备中的浏览器，访问网页即可在Fiddler中可以看到完成的请求和响应数据。

- ### 用Fiddler对iPhone手机应用进行抓包

基本流程差不多，只是手机设置不太一样：

iPhone手机：点击设置 > 无线局域网 > 无线网络 > HTTP代理 > 手动：

代理地址(电脑IP)：192.168.xx.xxx

端口号：8888

## 十、数据写入

```python
#写入到Excel
import xlsxwriter

#创建文件，并添加一个工作表
workbook=xlsxwriter.Workbook('demo.xlsx')
worksheet=workbook.add_worksheet()

#在指定位置写入数据
worksheet.write("A1","这是A1的数据")
worksheet.write("A2","这是A2的数据")

#关闭表格文件
workbook.close()
```

```python
#爬取便民查询网常用号码，并写入到Excel
import re
import requests
import xlsxwriter

headers = {
"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) Ap\
pleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.99 Sa\
fari/537.36"
}

response=requests.get("http://changyongdianhuahaoma.51240.com/",headers=headers).text

pat1=r'<tr bgcolor="#EFF7F0">[\s\S]*?<td>(.*?)</td>[\s\S]*?<td>[\s\S]*?</td>[\s\S]*?</tr>'
pat2=r'<tr bgcolor="#EFF7F0">[\s\S]*?<td>[\s\S]*?</td>[\s\S]*?<td>(.*?)</td>[\s\S]*?</tr>'

pattern1=re.compile(pat1)
pattern2=re.compile(pat2)

data1=pattern1.findall(response)
data2=pattern2.findall(response)

resultlist=[]

#创建表格
workbook=xlsxwriter.Workbook("demo2.xlsx")
worksheet=workbook.add_worksheet()

for i in range(0,len(data1)):
	resultlist.append(data1[i]+data2[i])

	#写入数据
	worksheet.write("A"+str(i+1),data1[i])
	worksheet.write("B"+str(i+1),data2[i])

print(resultlist)
# 关闭表格资源，这样才会完成创建
workbook.close()
```

```python
#爬取便民查询网常用号码，并写入到Mysql 
#注意：需要提前创建对应字段的数据库
import re
import requests
import pymysql

#建立数据库连接
db=pymysql.Connect(host="localhost",port=3306,user="root",passwd="AA123456",db="spider_test",charset="utf8")
cursor=db.cursor()


#爬取数据
headers = {
"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) Ap\
pleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.99 Sa\
fari/537.36"
}

response=requests.get("http://changyongdianhuahaoma.51240.com/",headers=headers).text


#处理数据
pat1=r'<tr bgcolor="#EFF7F0">[\s\S]*?<td>(.*?)</td>[\s\S]*?<td>[\s\S]*?</td>[\s\S]*?</tr>'
pat2=r'<tr bgcolor="#EFF7F0">[\s\S]*?<td>[\s\S]*?</td>[\s\S]*?<td>(.*?)</td>[\s\S]*?</tr>'

pattern1=re.compile(pat1)
pattern2=re.compile(pat2)

data1=pattern1.findall(response)
data2=pattern2.findall(response)

#清空数据库原来的内容
sqll="delete from tel"
cursor.execute(sqll)
db.commit()

resultlist=[]
for i in range(0,len(data1)):
	resultlist.append(data1[i]+data2[i])

	sql="insert into tel(name,phone) values('"+data1[i]+"','"+data2[i]+"')"
	cursor.execute(sql)

print(resultlist)

db.commit()
db.close()
```





完结撒花~

2020.7.22 下午5：11