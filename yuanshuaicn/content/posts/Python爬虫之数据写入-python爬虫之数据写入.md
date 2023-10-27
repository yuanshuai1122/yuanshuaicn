---
title: Python爬虫之数据写入
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

## Python爬虫之数据写入

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