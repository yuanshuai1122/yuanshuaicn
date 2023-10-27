---
title: Python爬虫之验证码识别
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

## Python爬虫之验证码识别

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

##