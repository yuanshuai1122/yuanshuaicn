---
title: Python数据分析之Pandas（一）
date: 2021-post-26 21:52:58.514
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/v2-2103a9f9659baf7be26d40fe5b4e9f1f_1440w.jpg'
theme: 'light'
tags: 
- Python
- 数据分析
---

-: | :-----: | :----: | :-------: |
|  0   |   1    |    1    |  4.0   | 964982703 |
|  1   |   1    |    3    |  4.0   | 964981247 |
|  2   |   1    |    6    |  4.0   | 964982224 |
|  3   |   1    |   47    |  5.0   | 964983815 |
|  4   |   1    |   50    |  5.0   | 964982931 |

In [5]:

```python
# 查看数据的形状，返回(行数、列数)
ratings.shape
```

Out[5]:

```python
(100836, 4)
```

In [6]:

```python
# 查看列名列表
ratings.columns
```

Out[6]:

```python
Index(['userId', 'movieId', 'rating', 'timestamp'], dtype='object')
```

In [7]:

```python
# 查看索引列
ratings.index
```

Out[7]:

```python
RangeIndex(start=0, stop=100836, step=1)
```

In [8]:

```python
# 查看每列的数据类型
ratings.dtypes
```

Out[8]:

```python
userId         int64
movieId        int64
rating       float64
timestamp      int64
dtype: object
```

### 1.2 读取txt文件，自己指定分隔符、列名

In [9]:

```python
fpath = "./datas/crazyant/access_pvuv.txt"
```

In [10]:

```python
pvuv = pd.read_csv(
    fpath,
    sep="\t",
    header=None,
    names=['pdate', 'pv', 'uv']
)
```

In [11]:

```python
pvuv
```

Out[11]:

|      |   pdate    |  pv  |  uv  |
| :--: | :--------: | :--: | :--: |
|  0   | 2019-09-10 | 139  |  92  |
|  1   | 2019-09-09 | 185  | 153  |
|  2   | 2019-09-08 | 123  |  59  |
|  3   | 2019-09-07 |  65  |  40  |
|  4   | 2019-09-06 | 157  |  98  |
|  5   | 2019-09-05 | 205  | 151  |
|  6   | 2019-09-04 | 196  | 167  |
|  7   | 2019-09-03 | 216  | 176  |
|  8   | 2019-09-02 | 227  | 148  |
|  9   | 2019-09-01 | 105  |  61  |

## 2、读取excel文件

In [12]:

```python
fpath = "./datas/crazyant/access_pvuv.xlsx"
pvuv = pd.read_excel(fpath)
```

In [13]:

```python
pvuv
```

Out[13]:

|      |    日期    |  PV  |  UV  |
| :--: | :--------: | :--: | :--: |
|  0   | 2019-09-10 | 139  |  92  |
|  1   | 2019-09-09 | 185  | 153  |
|  2   | 2019-09-08 | 123  |  59  |
|  3   | 2019-09-07 |  65  |  40  |
|  4   | 2019-09-06 | 157  |  98  |
|  5   | 2019-09-05 | 205  | 151  |
|  6   | 2019-09-04 | 196  | 167  |
|  7   | 2019-09-03 | 216  | 176  |
|  8   | 2019-09-02 | 227  | 148  |
|  9   | 2019-09-01 | 105  |  61  |

## 3、读取MySQL数据库

In [14]:

```python
import pymysql
conn = pymysql.connect(
        host='127.0.0.1',
        user='root',
        password='12345678',
        database='test',
        charset='utf8'
    )
```

In [15]:

```python
mysql_page = pd.read_sql("select * from crazyant_pvuv", con=conn)
```

In [16]:

```python
mysql_page
```

Out[16]:

|      |   pdate    |  pv  |  uv  |
| :--: | :--------: | :--: | :--: |
|  0   | 2019-09-10 | 139  |  92  |
|  1   | 2019-09-09 | 185  | 153  |
|  2   | 2019-09-08 | 123  |  59  |
|  3   | 2019-09-07 |  65  |  40  |
|  4   | 2019-09-06 | 157  |  98  |
|  5   | 2019-09-05 | 205  | 151  |
|  6   | 2019-09-04 | 196  | 167  |
|  7   | 2019-09-03 | 216  | 176  |
|  8   | 2019-09-02 | 227  | 148  |
|  9   | 2019-09-01 | 105  |  61  |

# 03. Pandas数据结构

1. Series
2. DataFrame
3. 从DataFrame中查询出Series

In [1]:

```python
import pandas as pd
import numpy as np
```

### 1. Series

Series是一种类似于一维数组的对象，它由一组数据（不同数据类型）以及一组与之相关的数据标签（即索引）组成。

#### 1.1 仅有数据列表即可产生最简单的Series

In [2]:

```python
s1 = pd.Series([1,'a',5.2,7])
```

In [3]:

```python
# 左侧为索引，右侧是数据
s1
```

Out[3]:

```python
0      1
1      a
2    5.2
3      7
dtype: object
```

In [4]:

```python
# 获取索引
s1.index
```

Out[4]:

```python
RangeIndex(start=0, stop=4, step=1)
```

In [5]:

```python
# 获取数据
s1.values
```

Out[5]:

```python
array([1, 'a', 5.2, 7], dtype=object)
```

#### 1.2 创建一个具有标签索引的Series

In [6]:

```python
s2 = pd.Series([1, 'a', 5.2, 7], index=['d','b','a','c'])
```

In [7]:

```python
s2
```

Out[7]:

```python
d      1
b      a
a    5.2
c      7
dtype: object
```

In [8]:

```python
s2.index
```

Out[8]:

```python
Index(['d', 'b', 'a', 'c'], dtype='object')
```

#### 1.3 使用Python字典创建Series

In [9]:

```python
sdata={'Ohio':35000,'Texas':72000,'Oregon':16000,'Utah':5000}
```

In [10]:

```python
s3=pd.Series(sdata)
```

In [11]:

```python
s3
```

Out[11]:

```python
Ohio      35000
Texas     72000
Oregon    16000
Utah       5000
dtype: int64
```

#### 1.4 根据标签索引查询数据

类似Python的字典dict

In [12]:

```python
s2
```

Out[12]:

```python
d      1
b      a
a    5.2
c      7
dtype: object
```

In [13]:

```python
s2['a']
```

Out[13]:

```python
5.2
```

In [14]:

```python
type(s2['a'])
```

Out[14]:

```python
float
```

In [15]:

```python
s2[['b','a']]
```

Out[15]:

```python
b      a
a    5.2
dtype: object
```

In [16]:

```python
type(s2[['b','a']])
```

Out[16]:

```python
pandas.core.series.Series
```

### 2. DataFrame

DataFrame是一个表格型的数据结构

- 每列可以是不同的值类型（数值、字符串、布尔值等）
- 既有行索引index,也有列索引columns
- 可以被看做由Series组成的字典

创建dataframe最常用的方法，见02节读取纯文本文件、excel、mysql数据库

#### 2.1 根据多个字典序列创建dataframe

In [17]:

```python
data={
        'state':['Ohio','Ohio','Ohio','Nevada','Nevada'],
        'year':[2000,2001,2002,2001,2002],
        'pop':[1.5,1.7,3.6,2.4,2.9]
    }
df = pd.DataFrame(data)
```

In [18]:

```python
df
```

Out[18]:

|      | state  | year | pop  |
| :--: | :----: | :--: | :--: |
|  0   |  Ohio  | 2000 | 1.5  |
|  1   |  Ohio  | 2001 | 1.7  |
|  2   |  Ohio  | 2002 | 3.6  |
|  3   | Nevada | 2001 | 2.4  |
|  4   | Nevada | 2002 | 2.9  |

In [19]:

```python
df.dtypes
```

Out[19]:

```python
state     object
year       int64
pop      float64
dtype: object
```

In [20]:

```python
df.columns
```

Out[20]:

```python
Index(['state', 'year', 'pop'], dtype='object')
```

In [21]:

```python
df.index
```

Out[21]:

```python
RangeIndex(start=0, stop=5, step=1)
```

### 3. 从DataFrame中查询出Series

- 如果只查询一行、一列，返回的是pd.Series
- 如果查询多行、多列，返回的是pd.DataFrame

In [22]:

```python
df
```

Out[22]:

|      | state  | year | pop  |
| :--: | :----: | :--: | :--: |
|  0   |  Ohio  | 2000 | 1.5  |
|  1   |  Ohio  | 2001 | 1.7  |
|  2   |  Ohio  | 2002 | 3.6  |
|  3   | Nevada | 2001 | 2.4  |
|  4   | Nevada | 2002 | 2.9  |

#### 3.1 查询一列，结果是一个pd.Series

In [23]:

```python
df['year']
```

Out[23]:

```python
0    2000
1    2001
2    2002
3    2001
4    2002
Name: year, dtype: int64
```

In [24]:

```python
type(df['year'])
```

Out[24]:

```python
pandas.core.series.Series
```

#### 3.2 查询多列，结果是一个pd.DataFrame

In [25]:

```python
df[['year', 'pop']]
```

Out[25]:

|      | year | pop  |
| :--: | :--: | :--: |
|  0   | 2000 | 1.5  |
|  1   | 2001 | 1.7  |
|  2   | 2002 | 3.6  |
|  3   | 2001 | 2.4  |
|  4   | 2002 | 2.9  |

In [26]:

```python
type(df[['year', 'pop']])
```

Out[26]:

```python
pandas.core.frame.DataFrame
```

#### 3.3 查询一行，结果是一个pd.Series

In [27]:

```python
df.loc[1]
```

Out[27]:

```python
state    Ohio
year     2001
pop       1.7
Name: 1, dtype: object
```

In [28]:

```python
type(df.loc[1])
```

Out[28]:

```python
pandas.core.series.Series
```

#### 3.4 查询多行，结果是一个pd.DataFrame

In [29]:

```python
df.loc[1:3]
```

Out[29]:

|      | state  | year | pop  |
| :--: | :----: | :--: | :--: |
|  1   |  Ohio  | 2001 | 1.7  |
|  2   |  Ohio  | 2002 | 3.6  |
|  3   | Nevada | 2001 | 2.4  |

In [30]:

```python
type(df.loc[1:3])
```

Out[30]:

```python
pandas.core.frame.DataFrame
```

# 04、Pandas查询数据

## Pandas查询数据的几种方法

1. df.loc方法，根据行、列的标签值查询
2. df.iloc方法，根据行、列的数字位置查询
3. df.where方法
4. df.query方法

.loc既能查询，又能覆盖写入，强烈推荐！

## Pandas使用df.loc查询数据的方法

1. 使用单个label值查询数据
2. 使用值列表批量查询
3. 使用数值区间进行范围查询
4. 使用条件表达式查询
5. 调用函数查询

## 注意

- 以上查询方法，既适用于行，也适用于列
- 注意观察降维dataFrame>Series>值

In [23]:

```python
import pandas as pd
print(pd.__version__)
1.0.1
```

## 0、读取数据

数据为北京2018年全年天气预报
该数据的爬虫教程参见我的Python爬虫系列视频课程

In [2]:

```python
df = pd.read_csv("./datas/beijing_tianqi/beijing_tianqi_2018.csv")
```

In [3]:

```python
df.head()
```

Out[3]:

|      |    ymd     | bWendu | yWendu | tianqi  | fengxiang | fengli | aqi  | aqiInfo | aqiLevel |
| :--: | :--------: | :----: | :----: | :-----: | :-------: | :----: | :--: | :-----: | :------: |
|  0   | 2018-01-01 |   3℃   |  -6℃   | 晴~多云 |  东北风   | 1-2级  |  59  |   良    |    2     |
|  1   | 2018-01-02 |   2℃   |  -5℃   | 阴~多云 |  东北风   | 1-2级  |  49  |   优    |    1     |
|  2   | 2018-01-03 |   2℃   |  -5℃   |  多云   |   北风    | 1-2级  |  28  |   优    |    1     |
|  3   | 2018-01-04 |   0℃   |  -8℃   |   阴    |  东北风   | 1-2级  |  28  |   优    |    1     |
|  4   | 2018-01-05 |   3℃   |  -6℃   | 多云~晴 |  西北风   | 1-2级  |  50  |   优    |    1     |

In [4]:

```python
# 设定索引为日期，方便按日期筛选
df.set_index('ymd', inplace=True)
```

In [5]:

```python
# 时间序列见后续课程，本次按字符串处理
df.index
```

Out[5]:

```python
Index(['2018-01-01', '2018-01-02', '2018-01-03', '2018-01-04', '2018-01-05',
       '2018-01-06', '2018-01-07', '2018-01-08', '2018-01-09', '2018-01-10',
       ...
       '2018-12-22', '2018-12-23', '2018-12-24', '2018-12-25', '2018-12-26',
       '2018-12-27', '2018-12-28', '2018-12-29', '2018-12-30', '2018-12-31'],
      dtype='object', name='ymd', length=365)
```

In [6]:

```python
df.head()
```

Out[6]:

|            | bWendu | yWendu | tianqi  | fengxiang | fengli | aqi  | aqiInfo | aqiLevel |
| :--------: | :----: | :----: | :-----: | :-------: | :----: | :--: | :-----: | :------: |
|    ymd     |        |        |         |           |        |      |         |          |
| 2018-01-01 |   3℃   |  -6℃   | 晴~多云 |  东北风   | 1-2级  |  59  |   良    |    2     |
| 2018-01-02 |   2℃   |  -5℃   | 阴~多云 |  东北风   | 1-2级  |  49  |   优    |    1     |
| 2018-01-03 |   2℃   |  -5℃   |  多云   |   北风    | 1-2级  |  28  |   优    |    1     |
| 2018-01-04 |   0℃   |  -8℃   |   阴    |  东北风   | 1-2级  |  28  |   优    |    1     |
| 2018-01-05 |   3℃   |  -6℃   | 多云~晴 |  西北风   | 1-2级  |  50  |   优    |    1     |

In [7]:

```python
# 替换掉温度的后缀℃
df.loc[:, "bWendu"] = df["bWendu"].str.replace("℃", "").astype('int32')
df.loc[:, "yWendu"] = df["yWendu"].str.replace("℃", "").astype('int32')
```

In [8]:

```python
df.dtypes
```

Out[8]:

```python
bWendu        int32
yWendu        int32
tianqi       object
fengxiang    object
fengli       object
aqi           int64
aqiInfo      object
aqiLevel      int64
dtype: object
```

In [9]:

```python
df.head()
```

Out[9]:

|            | bWendu | yWendu | tianqi  | fengxiang | fengli | aqi  | aqiInfo | aqiLevel |
| :--------: | :----: | :----: | :-----: | :-------: | :----: | :--: | :-----: | :------: |
|    ymd     |        |        |         |           |        |      |         |          |
| 2018-01-01 |   3    |   -6   | 晴~多云 |  东北风   | 1-2级  |  59  |   良    |    2     |
| 2018-01-02 |   2    |   -5   | 阴~多云 |  东北风   | 1-2级  |  49  |   优    |    1     |
| 2018-01-03 |   2    |   -5   |  多云   |   北风    | 1-2级  |  28  |   优    |    1     |
| 2018-01-04 |   0    |   -8   |   阴    |  东北风   | 1-2级  |  28  |   优    |    1     |
| 2018-01-05 |   3    |   -6   | 多云~晴 |  西北风   | 1-2级  |  50  |   优    |    1     |

## 1、使用单个label值查询数据

行或者列，都可以只传入单个值，实现精确匹配

In [10]:

```python
# 得到单个值
df.loc['2018-01-03', 'bWendu']
```

Out[10]:

```python
2
```

In [11]:

```python
# 得到一个Series
df.loc['2018-01-03', ['bWendu', 'yWendu']]
```

Out[11]:

```python
bWendu     2
yWendu    -5
Name: 2018-01-03, dtype: object
```

## 2、使用值列表批量查询

In [12]:

```python
# 得到Series
df.loc[['2018-01-03','2018-01-04','2018-01-05'], 'bWendu']
```

Out[12]:

```python
ymd
2018-01-03    2
2018-01-04    0
2018-01-05    3
Name: bWendu, dtype: int32
```

In [13]:

```python
# 得到DataFrame
df.loc[['2018-01-03','2018-01-04','2018-01-05'], ['bWendu', 'yWendu']]
```

Out[13]:

|            | bWendu | yWendu |
| :--------: | :----: | :----: |
|    ymd     |        |        |
| 2018-01-03 |   2    |   -5   |
| 2018-01-04 |   0    |   -8   |
| 2018-01-05 |   3    |   -6   |

## 3、使用数值区间进行范围查询

注意：区间既包含开始，也包含结束

In [14]:

```python
# 行index按区间
df.loc['2018-01-03':'2018-01-05', 'bWendu']
```

Out[14]:

```python
ymd
2018-01-03    2
2018-01-04    0
2018-01-05    3
Name: bWendu, dtype: int32
```

In [15]:

```python
# 列index按区间
df.loc['2018-01-03', 'bWendu':'fengxiang']
```

Out[15]:

```python
bWendu        2
yWendu       -5
tianqi       多云
fengxiang    北风
Name: 2018-01-03, dtype: object
```

In [16]:

```python
# 行和列都按区间查询
df.loc['2018-01-03':'2018-01-05', 'bWendu':'fengxiang']
```

Out[16]:

|            | bWendu | yWendu | tianqi  | fengxiang |
| :--------: | :----: | :----: | :-----: | :-------: |
|    ymd     |        |        |         |           |
| 2018-01-03 |   2    |   -5   |  多云   |   北风    |
| 2018-01-04 |   0    |   -8   |   阴    |  东北风   |
| 2018-01-05 |   3    |   -6   | 多云~晴 |  西北风   |

## 4、使用条件表达式查询

bool列表的长度得等于行数或者列数

#### 简单条件查询，最低温度低于-10度的列表

In [17]:

```python
df.loc[df["yWendu"]<-10, :]
```

Out[17]:

|            | bWendu | yWendu | tianqi  | fengxiang | fengli | aqi  | aqiInfo | aqiLevel |
| :--------: | :----: | :----: | :-----: | :-------: | :----: | :--: | :-----: | :------: |
|    ymd     |        |        |         |           |        |      |         |          |
| 2018-01-23 |   -4   |  -12   |   晴    |  西北风   | 3-4级  |  31  |   优    |    1     |
| 2018-01-24 |   -4   |  -11   |   晴    |  西南风   | 1-2级  |  34  |   优    |    1     |
| 2018-01-25 |   -3   |  -11   |  多云   |  东北风   | 1-2级  |  27  |   优    |    1     |
| 2018-12-26 |   -2   |  -11   | 晴~多云 |  东北风   |  2级   |  26  |   优    |    1     |
| 2018-12-27 |   -5   |  -12   | 多云~晴 |  西北风   |  3级   |  48  |   优    |    1     |
| 2018-12-28 |   -3   |  -11   |   晴    |  西北风   |  3级   |  40  |   优    |    1     |
| 2018-12-29 |   -3   |  -12   |   晴    |  西北风   |  2级   |  29  |   优    |    1     |
| 2018-12-30 |   -2   |  -11   | 晴~多云 |  东北风   |  1级   |  31  |   优    |    1     |

In [18]:

```python
# 观察一下这里的boolean条件
df["yWendu"]<-10
```

Out[18]:

```python
ymd
2018-01-01    False
2018-01-02    False
2018-01-03    False
2018-01-04    False
2018-01-05    False
              ...  
2018-12-27     True
2018-12-28     True
2018-12-29     True
2018-12-30     True
2018-12-31    False
Name: yWendu, Length: 365, dtype: bool
```

#### 复杂条件查询，查一下我心中的完美天气

注意，组合条件用&符号合并，每个条件判断都得带括号

In [19]:

```python
## 查询最高温度小于30度，并且最低温度大于15度，并且是晴天，并且天气为优的数据
df.loc[(df["bWendu"]<=30) & (df["yWendu"]>=15) & (df["tianqi"]=='晴') & (df["aqiLevel"]==1), :]
```

Out[19]:

|            | bWendu | yWendu | tianqi | fengxiang | fengli | aqi  | aqiInfo | aqiLevel |
| :--------: | :----: | :----: | :----: | :-------: | :----: | :--: | :-----: | :------: |
|    ymd     |        |        |        |           |        |      |         |          |
| 2018-08-24 |   30   |   20   |   晴   |   北风    | 1-2级  |  40  |   优    |    1     |
| 2018-09-07 |   27   |   16   |   晴   |  西北风   | 3-4级  |  22  |   优    |    1     |

我哭，北京好天气这么稀少！！

In [20]:

```python
# 再次观察这里的boolean条件
(df["bWendu"]<=30) & (df["yWendu"]>=15) & (df["tianqi"]=='晴') & (df["aqiLevel"]==1)
```

Out[20]:

```python
ymd
2018-01-01    False
2018-01-02    False
2018-01-03    False
2018-01-04    False
2018-01-05    False
              ...  
2018-12-27    False
2018-12-28    False
2018-12-29    False
2018-12-30    False
2018-12-31    False
Length: 365, dtype: bool
```

## 5、调用函数查询

In [21]:

```python
# 直接写lambda表达式
df.loc[lambda df : (df["bWendu"]<=30) & (df["yWendu"]>=15), :]
```

Out[21]:

|            | bWendu | yWendu | tianqi  | fengxiang | fengli | aqi  | aqiInfo  | aqiLevel |
| :--------: | :----: | :----: | :-----: | :-------: | :----: | :--: | :------: | :------: |
|    ymd     |        |        |         |           |        |      |          |          |
| 2018-04-28 |   27   |   17   |   晴    |  西南风   | 3-4级  | 125  | 轻度污染 |    3     |
| 2018-04-29 |   30   |   16   |  多云   |   南风    | 3-4级  | 193  | 中度污染 |    4     |
| 2018-05-04 |   27   |   16   | 晴~多云 |  西南风   | 1-2级  |  86  |    良    |    2     |
| 2018-05-09 |   29   |   17   | 晴~多云 |  西南风   | 3-4级  |  79  |    良    |    2     |
| 2018-05-10 |   26   |   18   |  多云   |   南风    | 3-4级  | 118  | 轻度污染 |    3     |
|    ...     |  ...   |  ...   |   ...   |    ...    |  ...   | ...  |   ...    |   ...    |
| 2018-09-15 |   26   |   15   |  多云   |   北风    | 3-4级  |  42  |    优    |    1     |
| 2018-09-17 |   27   |   17   | 多云~阴 |   北风    | 1-2级  |  37  |    优    |    1     |
| 2018-09-18 |   25   |   17   | 阴~多云 |  西南风   | 1-2级  |  50  |    优    |    1     |
| 2018-09-19 |   26   |   17   |  多云   |   南风    | 1-2级  |  52  |    良    |    2     |
| 2018-09-20 |   27   |   16   |  多云   |  西南风   | 1-2级  |  63  |    良    |    2     |

64 rows × 8 columns

In [22]:

```python
# 编写自己的函数，查询9月份，空气质量好的数据
def query_my_data(df):
    return df.index.str.startswith("2018-09") & (df["aqiLevel"]==1)
    
df.loc[query_my_data, :]
```

Out[22]:

|            | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi | aqiInfo | aqiLevel |
| ---------: | -----: | -----: | ------: | --------: | -----: | ---: | ------: | -------: |
|        ymd |        |        |         |           |        |      |         |          |
| 2018-09-01 |     27 |     19 | 阴~小雨 |      南风 |  1-2级 |   50 |      优 |        1 |
| 2018-09-04 |     31 |     18 |      晴 |    西南风 |  3-4级 |   24 |      优 |        1 |
| 2018-09-05 |     31 |     19 | 晴~多云 |    西南风 |  3-4级 |   34 |      优 |        1 |
| 2018-09-06 |     27 |     18 | 多云~晴 |    西北风 |  4-5级 |   37 |      优 |        1 |
| 2018-09-07 |     27 |     16 |      晴 |    西北风 |  3-4级 |   22 |      优 |        1 |
| 2018-09-08 |     27 |     15 | 多云~晴 |      北风 |  1-2级 |   28 |      优 |        1 |
| 2018-09-15 |     26 |     15 |    多云 |      北风 |  3-4级 |   42 |      优 |        1 |
| 2018-09-16 |     25 |     14 | 多云~晴 |      北风 |  1-2级 |   29 |      优 |        1 |
| 2018-09-17 |     27 |     17 | 多云~阴 |      北风 |  1-2级 |   37 |      优 |        1 |
| 2018-09-18 |     25 |     17 | 阴~多云 |    西南风 |  1-2级 |   50 |      优 |        1 |
| 2018-09-21 |     25 |     14 |      晴 |    西北风 |  3-4级 |   50 |      优 |        1 |
| 2018-09-22 |     24 |     13 |      晴 |    西北风 |  3-4级 |   28 |      优 |        1 |
| 2018-09-23 |     23 |     12 |      晴 |    西北风 |  4-5级 |   28 |      优 |        1 |
| 2018-09-24 |     23 |     11 |      晴 |      北风 |  1-2级 |   28 |      优 |        1 |
| 2018-09-25 |     24 |     12 | 晴~多云 |      南风 |  1-2级 |   44 |      优 |        1 |
| 2018-09-29 |     22 |     11 |      晴 |      北风 |  3-4级 |   21 |      优 |        1 |
| 2018-09-30 |     19 |     13 |    多云 |    西北风 |  4-5级 |   22 |      优 |        1 |

## 05、Pandas怎样新增数据列？

在进行数据分析时，经常需要按照一定条件创建新的数据列，然后进行进一步分析。

1. 直接赋值
2. df.apply方法
3. df.assign方法
4. 按条件选择分组分别赋值 微信公众号：蚂蚁学Python

In [1]:

```python
import pandas as pd
```

### 0、读取csv数据到dataframe

In [2]:

```python
fpath = "./datas/beijing_tianqi/beijing_tianqi_2018.csv"
df = pd.read_csv(fpath)
```

In [3]:

```python
df.head()
```

Out[3]:

|      |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi | aqiInfo | aqiLevel |
| ---: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | ------: | -------: |
|    0 | 2018-01-01 |     3℃ |    -6℃ | 晴~多云 |    东北风 |  1-2级 |   59 |      良 |        2 |
|    1 | 2018-01-02 |     2℃ |    -5℃ | 阴~多云 |    东北风 |  1-2级 |   49 |      优 |        1 |
|    2 | 2018-01-03 |     2℃ |    -5℃ |    多云 |      北风 |  1-2级 |   28 |      优 |        1 |
|    3 | 2018-01-04 |     0℃ |    -8℃ |      阴 |    东北风 |  1-2级 |   28 |      优 |        1 |
|    4 | 2018-01-05 |     3℃ |    -6℃ | 多云~晴 |    西北风 |  1-2级 |   50 |      优 |        1 |

### 1、直接赋值的方法

实例：清理温度列，变成数字类型

In [4]:

```python
# 替换掉温度的后缀℃
df.loc[:, "bWendu"] = df["bWendu"].str.replace("℃", "").astype('int32')
df.loc[:, "yWendu"] = df["yWendu"].str.replace("℃", "").astype('int32')
```

In [6]:

```python
df.head()
```

Out[6]:

|      |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi | aqiInfo | aqiLevel |
| ---: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | ------: | -------: |
|    0 | 2018-01-01 |      3 |     -6 | 晴~多云 |    东北风 |  1-2级 |   59 |      良 |        2 |
|    1 | 2018-01-02 |      2 |     -5 | 阴~多云 |    东北风 |  1-2级 |   49 |      优 |        1 |
|    2 | 2018-01-03 |      2 |     -5 |    多云 |      北风 |  1-2级 |   28 |      优 |        1 |
|    3 | 2018-01-04 |      0 |     -8 |      阴 |    东北风 |  1-2级 |   28 |      优 |        1 |
|    4 | 2018-01-05 |      3 |     -6 | 多云~晴 |    西北风 |  1-2级 |   50 |      优 |        1 |

实例：计算温差

In [9]:

```python
# 注意，df["bWendu"]其实是一个Series，后面的减法返回的是Series
df.loc[:, "wencha"] = df["bWendu"] - df["yWendu"]
```

In [10]:

```python
df.head()
```

Out[10]:

|      |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi | aqiInfo | aqiLevel | wencha |
| ---: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | ------: | -------: | -----: |
|    0 | 2018-01-01 |      3 |     -6 | 晴~多云 |    东北风 |  1-2级 |   59 |      良 |        2 |      9 |
|    1 | 2018-01-02 |      2 |     -5 | 阴~多云 |    东北风 |  1-2级 |   49 |      优 |        1 |      7 |
|    2 | 2018-01-03 |      2 |     -5 |    多云 |      北风 |  1-2级 |   28 |      优 |        1 |      7 |
|    3 | 2018-01-04 |      0 |     -8 |      阴 |    东北风 |  1-2级 |   28 |      优 |        1 |      8 |
|    4 | 2018-01-05 |      3 |     -6 | 多云~晴 |    西北风 |  1-2级 |   50 |      优 |        1 |      9 |

### 2、df.apply方法

Apply a function along an axis of the DataFrame.

Objects passed to the function are Series objects whose index is either the DataFrame’s index (axis=0) or the DataFrame’s columns (axis=1). 

实例：添加一列温度类型： 

1. 如果最高温度大于33度就是高温
2. 低于-10度是低温
3. 否则是常温

In [11]:

```python
def get_wendu_type(x):
    if x["bWendu"] > 33:
        return '高温'
    if x["yWendu"] < -10:
        return '低温'
    return '常温'

# 注意需要设置axis==1，这是series的index是columns
df.loc[:, "wendu_type"] = df.apply(get_wendu_type, axis=1)
```

In [12]:

```python
# 查看温度类型的计数
df["wendu_type"].value_counts()
```

Out[12]:

```python
常温    328
高温     29
低温      8
Name: wendu_type, dtype: int64
```

### 3、df.assign方法

Assign new columns to a DataFrame.

Returns a new object with all original columns in addition to new ones. 

实例：将温度从摄氏度变成华氏度

In [13]:

```python
# 可以同时添加多个新的列
df.assign(
    yWendu_huashi = lambda x : x["yWendu"] * 9 / 5 + 32,
    # 摄氏度转华氏度
    bWendu_huashi = lambda x : x["bWendu"] * 9 / 5 + 32
)
```

Out[13]:

|      |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi | aqiInfo | aqiLevel | wencha | wendu_type | yWendu_huashi | bWendu_huashi |
| ---: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | ------: | -------: | -----: | ---------: | ------------: | ------------: |
|    0 | 2018-01-01 |      3 |     -6 | 晴~多云 |    东北风 |  1-2级 |   59 |      良 |        2 |      9 |       常温 |          21.2 |          37.4 |
|    1 | 2018-01-02 |      2 |     -5 | 阴~多云 |    东北风 |  1-2级 |   49 |      优 |        1 |      7 |       常温 |          23.0 |          35.6 |
|    2 | 2018-01-03 |      2 |     -5 |    多云 |      北风 |  1-2级 |   28 |      优 |        1 |      7 |       常温 |          23.0 |          35.6 |
|    3 | 2018-01-04 |      0 |     -8 |      阴 |    东北风 |  1-2级 |   28 |      优 |        1 |      8 |       常温 |          17.6 |          32.0 |
|    4 | 2018-01-05 |      3 |     -6 | 多云~晴 |    西北风 |  1-2级 |   50 |      优 |        1 |      9 |       常温 |          21.2 |          37.4 |
|  ... |        ... |    ... |    ... |     ... |       ... |    ... |  ... |     ... |      ... |    ... |        ... |           ... |           ... |
|  360 | 2018-12-27 |     -5 |    -12 | 多云~晴 |    西北风 |    3级 |   48 |      优 |        1 |      7 |       低温 |          10.4 |          23.0 |
|  361 | 2018-12-28 |     -3 |    -11 |      晴 |    西北风 |    3级 |   40 |      优 |        1 |      8 |       低温 |          12.2 |          26.6 |
|  362 | 2018-12-29 |     -3 |    -12 |      晴 |    西北风 |    2级 |   29 |      优 |        1 |      9 |       低温 |          10.4 |          26.6 |
|  363 | 2018-12-30 |     -2 |    -11 | 晴~多云 |    东北风 |    1级 |   31 |      优 |        1 |      9 |       低温 |          12.2 |          28.4 |
|  364 | 2018-12-31 |     -2 |    -10 |    多云 |    东北风 |    1级 |   56 |      良 |        2 |      8 |       常温 |          14.0 |          28.4 |

365 rows × 13 columns

### 4、按条件选择分组分别赋值

按条件先选择数据，然后对这部分数据赋值新列
实例：高低温差大于10度，则认为温差大

In [14]:

```python
# 先创建空列（这是第一种创建新列的方法）
df['wencha_type'] = ''

df.loc[df["bWendu"]-df["yWendu"]>10, "wencha_type"] = "温差大"

df.loc[df["bWendu"]-df["yWendu"]<=10, "wencha_type"] = "温差正常"
```

In [15]:

```python
df["wencha_type"].value_counts()
```

Out[15]:

```python
温差正常    187
温差大     178
Name: wencha_type, dtype: int64
```

## 06、Pandas数据统计函数

1. 汇总类统计
2. 唯一去重和按值计数
3. 相关系数和协方差

In [1]:

```python
import pandas as pd
```

### 0、读取csv数据

In [2]:

```python
fpath = "./datas/beijing_tianqi/beijing_tianqi_2018.csv"
df = pd.read_csv(fpath)
```

In [3]:

```python
df.head(3)
```

Out[3]:

|      |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi | aqiInfo | aqiLevel |
| ---: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | ------: | -------: |
|    0 | 2018-01-01 |     3℃ |    -6℃ | 晴~多云 |    东北风 |  1-2级 |   59 |      良 |        2 |
|    1 | 2018-01-02 |     2℃ |    -5℃ | 阴~多云 |    东北风 |  1-2级 |   49 |      优 |        1 |
|    2 | 2018-01-03 |     2℃ |    -5℃ |    多云 |      北风 |  1-2级 |   28 |      优 |        1 |

In [4]:

```python
# 替换掉温度的后缀℃
df.loc[:, "bWendu"] = df["bWendu"].str.replace("℃", "").astype('int32')
df.loc[:, "yWendu"] = df["yWendu"].str.replace("℃", "").astype('int32')
```

In [5]:

```python
df.head(3)
```

Out[5]:

|      |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi | aqiInfo | aqiLevel |
| ---: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | ------: | -------: |
|    0 | 2018-01-01 |      3 |     -6 | 晴~多云 |    东北风 |  1-2级 |   59 |      良 |        2 |
|    1 | 2018-01-02 |      2 |     -5 | 阴~多云 |    东北风 |  1-2级 |   49 |      优 |        1 |
|    2 | 2018-01-03 |      2 |     -5 |    多云 |      北风 |  1-2级 |   28 |      优 |        1 |

### 1、汇总类统计

In [6]:

```python
# 一下子提取所有数字列统计结果
df.describe()
```

Out[6]:

|       |     bWendu |     yWendu |        aqi |   aqiLevel |
| ----: | ---------: | ---------: | ---------: | ---------: |
| count | 365.000000 | 365.000000 | 365.000000 | 365.000000 |
|  mean |  18.665753 |   8.358904 |  82.183562 |   2.090411 |
|   std |  11.858046 |  11.755053 |  51.936159 |   1.029798 |
|   min |  -5.000000 | -12.000000 |  21.000000 |   1.000000 |
|   25% |   8.000000 |  -3.000000 |  46.000000 |   1.000000 |
|   50% |  21.000000 |   8.000000 |  69.000000 |   2.000000 |
|   75% |  29.000000 |  19.000000 | 104.000000 |   3.000000 |
|   max |  38.000000 |  27.000000 | 387.000000 |   6.000000 |

In [7]:

```python
## 查看单个Series的数据
df["bWendu"].mean()
```

Out[7]:

```python
18.665753424657535
```

In [8]:

```python
# 最高温
df["bWendu"].max()
```

Out[8]:

```python
38
```

In [9]:

```python
# 最低温
df["bWendu"].min()
```

Out[9]:

```python
-5
```

### 2、唯一去重和按值计数

#### 2.1 唯一性去重

一般不用于数值列，而是枚举、分类列

In [10]:

```python
df["fengxiang"].unique()
```

Out[10]:

```python
array(['东北风', '北风', '西北风', '西南风', '南风', '东南风', '东风', '西风'], dtype=object)
```

In [11]:

```python
df["tianqi"].unique()
```

Out[11]:

```python
array(['晴~多云', '阴~多云', '多云', '阴', '多云~晴', '多云~阴', '晴', '阴~小雪', '小雪~多云',
       '小雨~阴', '小雨~雨夹雪', '多云~小雨', '小雨~多云', '大雨~小雨', '小雨', '阴~小雨',
       '多云~雷阵雨', '雷阵雨~多云', '阴~雷阵雨', '雷阵雨', '雷阵雨~大雨', '中雨~雷阵雨', '小雨~大雨',
       '暴雨~雷阵雨', '雷阵雨~中雨', '小雨~雷阵雨', '雷阵雨~阴', '中雨~小雨', '小雨~中雨', '雾~多云',
       '霾'], dtype=object)
```

In [12]:

```python
df["fengli"].unique()
```

Out[12]:

```python
array(['1-2级', '4-5级', '3-4级', '2级', '1级', '3级'], dtype=object)
```

#### 2.2 按值计数

In [13]:

```python
df["fengxiang"].value_counts()
```

Out[13]:

```python
南风     92
西南风    64
北风     54
西北风    51
东南风    46
东北风    38
东风     14
西风      6
Name: fengxiang, dtype: int64
```

In [14]:

```python
df["tianqi"].value_counts()
```

Out[14]:

```python
晴         101
多云         95
多云~晴       40
晴~多云       34
多云~雷阵雨     14
多云~阴       10
阴~多云        8
小雨~多云       8
雷阵雨         8
雷阵雨~多云      7
小雨          6
多云~小雨       5
阴           4
雷阵雨~中雨      4
中雨~小雨       2
中雨~雷阵雨      2
阴~小雨        2
霾           2
阴~小雪        1
小雪~多云       1
大雨~小雨       1
小雨~雷阵雨      1
小雨~中雨       1
小雨~雨夹雪      1
雾~多云        1
雷阵雨~阴       1
暴雨~雷阵雨      1
小雨~阴        1
雷阵雨~大雨      1
阴~雷阵雨       1
小雨~大雨       1
Name: tianqi, dtype: int64
```

In [15]:

```python
df["fengli"].value_counts()
```

Out[15]:

```python
1-2级    236
3-4级     68
1级       21
4-5级     20
2级       13
3级        7
Name: fengli, dtype: int64
```

### 3、相关系数和协方差

用途（超级厉害）：

1. 两只股票，是不是同涨同跌？程度多大？正相关还是负相关？
2. 产品销量的波动，跟哪些因素正相关、负相关，程度有多大？

来自知乎，对于两个变量X、Y：

1. 协方差：***衡量同向反向程度\***，如果协方差为正，说明X，Y同向变化，协方差越大说明同向程度越高；如果协方差为负，说明X，Y反向运动，协方差越小说明反向程度越高。
2. 相关系数：***衡量相似度程度\***，当他们的相关系数为1时，说明两个变量变化时的正向相似度最大，当相关系数为－1时，说明两个变量变化的反向相似度最大

In [16]:

```python
# 协方差矩阵：
df.cov()
```

Out[16]:

|          |     bWendu |     yWendu |         aqi |  aqiLevel |
| -------: | ---------: | ---------: | ----------: | --------: |
|   bWendu | 140.613247 | 135.529633 |   47.462622 |  0.879204 |
|   yWendu | 135.529633 | 138.181274 |   16.186685 |  0.264165 |
|      aqi |  47.462622 |  16.186685 | 2697.364564 | 50.749842 |
| aqiLevel |   0.879204 |   0.264165 |   50.749842 |  1.060485 |

In [17]:

```python
# 相关系数矩阵
df.corr()
```

Out[17]:

|          |   bWendu |   yWendu |      aqi | aqiLevel |
| -------: | -------: | -------: | -------: | -------: |
|   bWendu | 1.000000 | 0.972292 | 0.077067 | 0.071999 |
|   yWendu | 0.972292 | 1.000000 | 0.026513 | 0.021822 |
|      aqi | 0.077067 | 0.026513 | 1.000000 | 0.948883 |
| aqiLevel | 0.071999 | 0.021822 | 0.948883 | 1.000000 |

In [18]:

```python
# 单独查看空气质量和最高温度的相关系数
df["aqi"].corr(df["bWendu"])
```

Out[18]:

```python
0.07706705916811077
```

In [19]:

```python
df["aqi"].corr(df["yWendu"])
```

Out[19]:

```python
0.02651328267296879
```

In [20]:

```python
# 空气质量和温差的相关系数
df["aqi"].corr(df["bWendu"]-df["yWendu"])
```

Out[20]:

```python
0.21652257576382047
```

In [ ]:

```python
# !! 这就是特征工程对于机器学习重要性的一个例子
```

In [21]:

```python
0.21/0.02
```

Out[21]:

```python
10.5
```

## 07、Pandas对缺失值的处理

Pandas使用这些函数处理缺失值：

- isnull和notnull：检测是否是空值，可用于df和series
- dropna：丢弃、删除缺失值
  - axis : 删除行还是列，{0 or ‘index’, 1 or ‘columns’}, default 0
  - how : 如果等于any则任何值为空都删除，如果等于all则所有值都为空才删除
  - inplace : 如果为True则修改当前df，否则返回新的df
- fillna：填充空值
  - value：用于填充的值，可以是单个值，或者字典（key是列名，value是值）
  - method : 等于ffill使用前一个不为空的值填充forword fill；等于bfill使用后一个不为空的值填充backword fill
  - axis : 按行还是列填充，{0 or ‘index’, 1 or ‘columns’}
  - inplace : 如果为True则修改当前df，否则返回新的df

In [1]:

```python
import pandas as pd
```

### 实例：特殊Excel的读取、清洗、处理

#### 步骤1：读取excel的时候，忽略前几个空行

In [2]:

```python
studf = pd.read_excel("./datas/student_excel/student_excel.xlsx", skiprows=2)
```

In [3]:

```python
studf
```

Out[3]:

|      | Unnamed: 0 | 姓名 | 科目 | 分数 |
| ---: | ---------: | ---: | ---: | ---: |
|    0 |        NaN | 小明 | 语文 | 85.0 |
|    1 |        NaN |  NaN | 数学 | 80.0 |
|    2 |        NaN |  NaN | 英语 | 90.0 |
|    3 |        NaN |  NaN |  NaN |  NaN |
|    4 |        NaN | 小王 | 语文 | 85.0 |
|    5 |        NaN |  NaN | 数学 |  NaN |
|    6 |        NaN |  NaN | 英语 | 90.0 |
|    7 |        NaN |  NaN |  NaN |  NaN |
|    8 |        NaN | 小刚 | 语文 | 85.0 |
|    9 |        NaN |  NaN | 数学 | 80.0 |
|   10 |        NaN |  NaN | 英语 | 90.0 |

#### 步骤2：检测空值

In [4]:

```python
studf.isnull()
```

Out[4]:

|      | Unnamed: 0 |  姓名 |  科目 |  分数 |
| ---: | ---------: | ----: | ----: | ----: |
|    0 |       True | False | False | False |
|    1 |       True |  True | False | False |
|    2 |       True |  True | False | False |
|    3 |       True |  True |  True |  True |
|    4 |       True | False | False | False |
|    5 |       True |  True | False |  True |
|    6 |       True |  True | False | False |
|    7 |       True |  True |  True |  True |
|    8 |       True | False | False | False |
|    9 |       True |  True | False | False |
|   10 |       True |  True | False | False |

In [5]:

```python
studf["分数"].isnull()
```

Out[5]:

```python
0     False
1     False
2     False
3      True
4     False
5      True
6     False
7      True
8     False
9     False
10    False
Name: 分数, dtype: bool
```

In [6]:

```python
studf["分数"].notnull()
```

Out[6]:

```python
0      True
1      True
2      True
3     False
4      True
5     False
6      True
7     False
8      True
9      True
10     True
Name: 分数, dtype: bool
```

In [7]:

```python
# 筛选没有空分数的所有行
studf.loc[studf["分数"].notnull(), :]
```

Out[7]:

|      | Unnamed: 0 | 姓名 | 科目 | 分数 |
| ---: | ---------: | ---: | ---: | ---: |
|    0 |        NaN | 小明 | 语文 | 85.0 |
|    1 |        NaN |  NaN | 数学 | 80.0 |
|    2 |        NaN |  NaN | 英语 | 90.0 |
|    4 |        NaN | 小王 | 语文 | 85.0 |
|    6 |        NaN |  NaN | 英语 | 90.0 |
|    8 |        NaN | 小刚 | 语文 | 85.0 |
|    9 |        NaN |  NaN | 数学 | 80.0 |
|   10 |        NaN |  NaN | 英语 | 90.0 |

#### 步骤3：删除掉全是空值的列

In [8]:

```python
studf.dropna(axis="columns", how='all', inplace=True)
```

In [9]:

```python
studf
```

Out[9]:

|      | 姓名 | 科目 | 分数 |
| ---: | ---: | ---: | ---: |
|    0 | 小明 | 语文 | 85.0 |
|    1 |  NaN | 数学 | 80.0 |
|    2 |  NaN | 英语 | 90.0 |
|    3 |  NaN |  NaN |  NaN |
|    4 | 小王 | 语文 | 85.0 |
|    5 |  NaN | 数学 |  NaN |
|    6 |  NaN | 英语 | 90.0 |
|    7 |  NaN |  NaN |  NaN |
|    8 | 小刚 | 语文 | 85.0 |
|    9 |  NaN | 数学 | 80.0 |
|   10 |  NaN | 英语 | 90.0 |

#### 步骤4：删除掉全是空值的行

In [10]:

```python
studf.dropna(axis="index", how='all', inplace=True)
```

In [11]:

```python
studf
```

Out[11]:

|      | 姓名 | 科目 | 分数 |
| ---: | ---: | ---: | ---: |
|    0 | 小明 | 语文 | 85.0 |
|    1 |  NaN | 数学 | 80.0 |
|    2 |  NaN | 英语 | 90.0 |
|    4 | 小王 | 语文 | 85.0 |
|    5 |  NaN | 数学 |  NaN |
|    6 |  NaN | 英语 | 90.0 |
|    8 | 小刚 | 语文 | 85.0 |
|    9 |  NaN | 数学 | 80.0 |
|   10 |  NaN | 英语 | 90.0 |

### 步骤5：将分数列为空的填充为0分

In [12]:

```python
studf.fillna({"分数":0})
```

Out[12]:

|      | 姓名 | 科目 | 分数 |
| ---: | ---: | ---: | ---: |
|    0 | 小明 | 语文 | 85.0 |
|    1 |  NaN | 数学 | 80.0 |
|    2 |  NaN | 英语 | 90.0 |
|    4 | 小王 | 语文 | 85.0 |
|    5 |  NaN | 数学 |  0.0 |
|    6 |  NaN | 英语 | 90.0 |
|    8 | 小刚 | 语文 | 85.0 |
|    9 |  NaN | 数学 | 80.0 |
|   10 |  NaN | 英语 | 90.0 |

In [13]:

```python
# 等同于
studf.loc[:, '分数'] = studf['分数'].fillna(0)
```

In [14]:

```python
studf
```

Out[14]:

|      | 姓名 | 科目 | 分数 |
| ---: | ---: | ---: | ---: |
|    0 | 小明 | 语文 | 85.0 |
|    1 |  NaN | 数学 | 80.0 |
|    2 |  NaN | 英语 | 90.0 |
|    4 | 小王 | 语文 | 85.0 |
|    5 |  NaN | 数学 |  0.0 |
|    6 |  NaN | 英语 | 90.0 |
|    8 | 小刚 | 语文 | 85.0 |
|    9 |  NaN | 数学 | 80.0 |
|   10 |  NaN | 英语 | 90.0 |

### 步骤6：将姓名的缺失值填充

使用前面的有效值填充，用ffill：forward fill

In [15]:

```python
studf.loc[:, '姓名'] = studf['姓名'].fillna(method="ffill")
```

In [16]:

```python
studf
```

Out[16]:

|      | 姓名 | 科目 | 分数 |
| ---: | ---: | ---: | ---: |
|    0 | 小明 | 语文 | 85.0 |
|    1 | 小明 | 数学 | 80.0 |
|    2 | 小明 | 英语 | 90.0 |
|    4 | 小王 | 语文 | 85.0 |
|    5 | 小王 | 数学 |  0.0 |
|    6 | 小王 | 英语 | 90.0 |
|    8 | 小刚 | 语文 | 85.0 |
|    9 | 小刚 | 数学 | 80.0 |
|   10 | 小刚 | 英语 | 90.0 |

### 步骤7：将清洗好的excel保存

In [17]:

```python
studf.to_excel("./datas/student_excel/student_excel_clean.xlsx", index=False)
```

# 08、Pandas的SettingWithCopyWarning报警

### 0、读取数据

In [1]:

```python
import pandas as pd
```

In [2]:

```python
fpath = "./datas/beijing_tianqi/beijing_tianqi_2018.csv"
df = pd.read_csv(fpath)
```

In [3]:

```python
df.head()
```

Out[3]:

|      |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi | aqiInfo | aqiLevel |
| ---: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | ------: | -------: |
|    0 | 2018-01-01 |     3℃ |    -6℃ | 晴~多云 |    东北风 |  1-2级 |   59 |      良 |        2 |
|    1 | 2018-01-02 |     2℃ |    -5℃ | 阴~多云 |    东北风 |  1-2级 |   49 |      优 |        1 |
|    2 | 2018-01-03 |     2℃ |    -5℃ |    多云 |      北风 |  1-2级 |   28 |      优 |        1 |
|    3 | 2018-01-04 |     0℃ |    -8℃ |      阴 |    东北风 |  1-2级 |   28 |      优 |        1 |
|    4 | 2018-01-05 |     3℃ |    -6℃ | 多云~晴 |    西北风 |  1-2级 |   50 |      优 |        1 |

In [4]:

```python
# 替换掉温度的后缀℃
df.loc[:, "bWendu"] = df["bWendu"].str.replace("℃", "").astype('int32')
df.loc[:, "yWendu"] = df["yWendu"].str.replace("℃", "").astype('int32')
```

In [5]:

```python
df.head()
```

Out[5]:

|      |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi | aqiInfo | aqiLevel |
| ---: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | ------: | -------: |
|    0 | 2018-01-01 |      3 |     -6 | 晴~多云 |    东北风 |  1-2级 |   59 |      良 |        2 |
|    1 | 2018-01-02 |      2 |     -5 | 阴~多云 |    东北风 |  1-2级 |   49 |      优 |        1 |
|    2 | 2018-01-03 |      2 |     -5 |    多云 |      北风 |  1-2级 |   28 |      优 |        1 |
|    3 | 2018-01-04 |      0 |     -8 |      阴 |    东北风 |  1-2级 |   28 |      优 |        1 |
|    4 | 2018-01-05 |      3 |     -6 | 多云~晴 |    西北风 |  1-2级 |   50 |      优 |        1 |

### 1、复现

In [6]:

```python
# 只选出3月份的数据用于分析
condition = df["ymd"].str.startswith("2018-03")
```

In [7]:

```python
# 设置温差
df[condition]["wen_cha"] = df["bWendu"]-df["yWendu"]
d:\appdata\python37\lib\site-packages\ipykernel_launcher.py:2: SettingWithCopyWarning: 
A value is trying to be set on a copy of a slice from a DataFrame.
Try using .loc[row_indexer,col_indexer] = value instead

See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
  
```

In [8]:

```python
# 查看是否修改成功
df[condition].head()
```

Out[8]:

|      |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi |  aqiInfo | aqiLevel |
| ---: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | -------: | -------: |
|   59 | 2018-03-01 |      8 |     -3 |    多云 |    西南风 |  1-2级 |   46 |       优 |        1 |
|   60 | 2018-03-02 |      9 |     -1 | 晴~多云 |      北风 |  1-2级 |   95 |       良 |        2 |
|   61 | 2018-03-03 |     13 |      3 | 多云~阴 |      北风 |  1-2级 |  214 | 重度污染 |        5 |
|   62 | 2018-03-04 |      7 |     -2 | 阴~多云 |    东南风 |  1-2级 |  144 | 轻度污染 |        3 |
|   63 | 2018-03-05 |      8 |     -3 |      晴 |      南风 |  1-2级 |   94 |       良 |        2 |

### 2、原因

发出警告的代码 df[condition]["wen_cha"] = df["bWendu"]-df["yWendu"]

相当于：df.get(condition).set(wen_cha)，第一步骤的get发出了报警

***链式操作其实是两个步骤，先get后set，get得到的dataframe可能是view也可能是copy，pandas发出警告\***

官网文档： https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy

核心要诀：pandas的dataframe的修改写操作，只允许在源dataframe上进行，一步到位

### 3、解决方法1

将get+set的两步操作，改成set的一步操作

In [9]:

```python
df.loc[condition, "wen_cha"] = df["bWendu"]-df["yWendu"]
```

In [10]:

```python
df[condition].head()
```

Out[10]:

|      |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi |  aqiInfo | aqiLevel | wen_cha |
| ---: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | -------: | -------: | ------: |
|   59 | 2018-03-01 |      8 |     -3 |    多云 |    西南风 |  1-2级 |   46 |       优 |        1 |    11.0 |
|   60 | 2018-03-02 |      9 |     -1 | 晴~多云 |      北风 |  1-2级 |   95 |       良 |        2 |    10.0 |
|   61 | 2018-03-03 |     13 |      3 | 多云~阴 |      北风 |  1-2级 |  214 | 重度污染 |        5 |    10.0 |
|   62 | 2018-03-04 |      7 |     -2 | 阴~多云 |    东南风 |  1-2级 |  144 | 轻度污染 |        3 |     9.0 |
|   63 | 2018-03-05 |      8 |     -3 |      晴 |      南风 |  1-2级 |   94 |       良 |        2 |    11.0 |

### 4、解决方法2

如果需要预筛选数据做后续的处理分析，使用copy复制dataframe

In [11]:

```python
df_month3 = df[condition].copy()
```

In [12]:

```python
df_month3.head()
```

Out[12]:

|      |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi |  aqiInfo | aqiLevel | wen_cha |
| ---: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | -------: | -------: | ------: |
|   59 | 2018-03-01 |      8 |     -3 |    多云 |    西南风 |  1-2级 |   46 |       优 |        1 |    11.0 |
|   60 | 2018-03-02 |      9 |     -1 | 晴~多云 |      北风 |  1-2级 |   95 |       良 |        2 |    10.0 |
|   61 | 2018-03-03 |     13 |      3 | 多云~阴 |      北风 |  1-2级 |  214 | 重度污染 |        5 |    10.0 |
|   62 | 2018-03-04 |      7 |     -2 | 阴~多云 |    东南风 |  1-2级 |  144 | 轻度污染 |        3 |     9.0 |
|   63 | 2018-03-05 |      8 |     -3 |      晴 |      南风 |  1-2级 |   94 |       良 |        2 |    11.0 |

In [13]:

```python
df_month3["wen_cha"] = df["bWendu"]-df["yWendu"]
```

In [14]:

```python
df_month3.head()
```

Out[14]:

|      |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi |  aqiInfo | aqiLevel | wen_cha |
| ---: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | -------: | -------: | ------: |
|   59 | 2018-03-01 |      8 |     -3 |    多云 |    西南风 |  1-2级 |   46 |       优 |        1 |      11 |
|   60 | 2018-03-02 |      9 |     -1 | 晴~多云 |      北风 |  1-2级 |   95 |       良 |        2 |      10 |
|   61 | 2018-03-03 |     13 |      3 | 多云~阴 |      北风 |  1-2级 |  214 | 重度污染 |        5 |      10 |
|   62 | 2018-03-04 |      7 |     -2 | 阴~多云 |    东南风 |  1-2级 |  144 | 轻度污染 |        3 |       9 |
|   63 | 2018-03-05 |      8 |     -3 |      晴 |      南风 |  1-2级 |   94 |       良 |        2 |      11 |

***总之，pandas不允许先筛选子dataframe，再进行修改写入\***
要么使用.loc实现一个步骤直接修改源dataframe
要么先复制一个子dataframe再一个步骤执行修改

## 09、Pandas数据排序

Series的排序：
***Series.sort_values(ascending=True, inplace=False)\***
参数说明：

- ascending：默认为True升序排序，为False降序排序
- inplace：是否修改原始Series

DataFrame的排序：
***DataFrame.sort_values(by, ascending=True, inplace=False)\***
参数说明：

- by：字符串或者List<字符串>，单列排序或者多列排序
- ascending：bool或者List，升序还是降序，如果是list对应by的多列
- inplace：是否修改原始DataFrame

In [2]:

```python
import pandas as pd
```

### 0、读取数据

In [3]:

```python
fpath = "./datas/beijing_tianqi/beijing_tianqi_2018.csv"
df = pd.read_csv(fpath)

# 替换掉温度的后缀℃
df.loc[:, "bWendu"] = df["bWendu"].str.replace("℃", "").astype('int32')
df.loc[:, "yWendu"] = df["yWendu"].str.replace("℃", "").astype('int32')
```

In [4]:

```python
df.head()
```

Out[4]:

|      |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi | aqiInfo | aqiLevel |
| ---: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | ------: | -------: |
|    0 | 2018-01-01 |      3 |     -6 | 晴~多云 |    东北风 |  1-2级 |   59 |      良 |        2 |
|    1 | 2018-01-02 |      2 |     -5 | 阴~多云 |    东北风 |  1-2级 |   49 |      优 |        1 |
|    2 | 2018-01-03 |      2 |     -5 |    多云 |      北风 |  1-2级 |   28 |      优 |        1 |
|    3 | 2018-01-04 |      0 |     -8 |      阴 |    东北风 |  1-2级 |   28 |      优 |        1 |
|    4 | 2018-01-05 |      3 |     -6 | 多云~晴 |    西北风 |  1-2级 |   50 |      优 |        1 |

### 1、Series的排序

In [5]:

```python
df["aqi"].sort_values()
```

Out[5]:

```python
271     21
281     21
249     22
272     22
301     22
      ... 
317    266
71     287
91     287
72     293
86     387
Name: aqi, Length: 365, dtype: int64
```

In [6]:

```python
df["aqi"].sort_values(ascending=False)
```

Out[6]:

```python
86     387
72     293
91     287
71     287
317    266
      ... 
301     22
272     22
249     22
281     21
271     21
Name: aqi, Length: 365, dtype: int64
```

In [8]:

```python
df["tianqi"].sort_values()
```

Out[8]:

```python
225     中雨~小雨
230     中雨~小雨
197    中雨~雷阵雨
196    中雨~雷阵雨
112        多云
        ...  
191    雷阵雨~大雨
219     雷阵雨~阴
335      雾~多云
353         霾
348         霾
Name: tianqi, Length: 365, dtype: object
```

### 2、DataFrame的排序

#### 2.1 单列排序

In [9]:

```python
df.sort_values(by="aqi")
```

Out[9]:

|      |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi |  aqiInfo | aqiLevel |
| ---: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | -------: | -------: |
|  271 | 2018-09-29 |     22 |     11 |      晴 |      北风 |  3-4级 |   21 |       优 |        1 |
|  281 | 2018-10-09 |     15 |      4 | 多云~晴 |    西北风 |  4-5级 |   21 |       优 |        1 |
|  249 | 2018-09-07 |     27 |     16 |      晴 |    西北风 |  3-4级 |   22 |       优 |        1 |
|  272 | 2018-09-30 |     19 |     13 |    多云 |    西北风 |  4-5级 |   22 |       优 |        1 |
|  301 | 2018-10-29 |     15 |      3 |      晴 |      北风 |  3-4级 |   22 |       优 |        1 |
|  ... |        ... |    ... |    ... |     ... |       ... |    ... |  ... |      ... |      ... |
|  317 | 2018-11-14 |     13 |      5 |    多云 |      南风 |  1-2级 |  266 | 重度污染 |        5 |
|   71 | 2018-03-13 |     17 |      5 | 晴~多云 |      南风 |  1-2级 |  287 | 重度污染 |        5 |
|   91 | 2018-04-02 |     26 |     11 |    多云 |      北风 |  1-2级 |  287 | 重度污染 |        5 |
|   72 | 2018-03-14 |     15 |      6 | 多云~阴 |    东北风 |  1-2级 |  293 | 重度污染 |        5 |
|   86 | 2018-03-28 |     25 |      9 | 多云~晴 |      东风 |  1-2级 |  387 | 严重污染 |        6 |

365 rows × 9 columns

In [10]:

```python
df.sort_values(by="aqi", ascending=False)
```

Out[10]:

|      |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi |  aqiInfo | aqiLevel |
| ---: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | -------: | -------: |
|   86 | 2018-03-28 |     25 |      9 | 多云~晴 |      东风 |  1-2级 |  387 | 严重污染 |        6 |
|   72 | 2018-03-14 |     15 |      6 | 多云~阴 |    东北风 |  1-2级 |  293 | 重度污染 |        5 |
|   71 | 2018-03-13 |     17 |      5 | 晴~多云 |      南风 |  1-2级 |  287 | 重度污染 |        5 |
|   91 | 2018-04-02 |     26 |     11 |    多云 |      北风 |  1-2级 |  287 | 重度污染 |        5 |
|  317 | 2018-11-14 |     13 |      5 |    多云 |      南风 |  1-2级 |  266 | 重度污染 |        5 |
|  ... |        ... |    ... |    ... |     ... |       ... |    ... |  ... |      ... |      ... |
|  249 | 2018-09-07 |     27 |     16 |      晴 |    西北风 |  3-4级 |   22 |       优 |        1 |
|  301 | 2018-10-29 |     15 |      3 |      晴 |      北风 |  3-4级 |   22 |       优 |        1 |
|  272 | 2018-09-30 |     19 |     13 |    多云 |    西北风 |  4-5级 |   22 |       优 |        1 |
|  271 | 2018-09-29 |     22 |     11 |      晴 |      北风 |  3-4级 |   21 |       优 |        1 |
|  281 | 2018-10-09 |     15 |      4 | 多云~晴 |    西北风 |  4-5级 |   21 |       优 |        1 |

365 rows × 9 columns

#### 2.2 多列排序

In [11]:

```python
# 按空气质量等级、最高温度排序，默认升序
df.sort_values(by=["aqiLevel", "bWendu"])
```

Out[11]:

|      |        ymd | bWendu | yWendu |    tianqi | fengxiang | fengli |  aqi |  aqiInfo | aqiLevel |
| ---: | ---------: | -----: | -----: | --------: | --------: | -----: | ---: | -------: | -------: |
|  360 | 2018-12-27 |     -5 |    -12 |   多云~晴 |    西北风 |    3级 |   48 |       优 |        1 |
|   22 | 2018-01-23 |     -4 |    -12 |        晴 |    西北风 |  3-4级 |   31 |       优 |        1 |
|   23 | 2018-01-24 |     -4 |    -11 |        晴 |    西南风 |  1-2级 |   34 |       优 |        1 |
|  340 | 2018-12-07 |     -4 |    -10 |        晴 |    西北风 |    3级 |   33 |       优 |        1 |
|   21 | 2018-01-22 |     -3 |    -10 | 小雪~多云 |      东风 |  1-2级 |   47 |       优 |        1 |
|  ... |        ... |    ... |    ... |       ... |       ... |    ... |  ... |      ... |      ... |
|   71 | 2018-03-13 |     17 |      5 |   晴~多云 |      南风 |  1-2级 |  287 | 重度污染 |        5 |
|   90 | 2018-04-01 |     25 |     11 |   晴~多云 |      南风 |  1-2级 |  218 | 重度污染 |        5 |
|   91 | 2018-04-02 |     26 |     11 |      多云 |      北风 |  1-2级 |  287 | 重度污染 |        5 |
|   85 | 2018-03-27 |     27 |     11 |        晴 |      南风 |  1-2级 |  243 | 重度污染 |        5 |
|   86 | 2018-03-28 |     25 |      9 |   多云~晴 |      东风 |  1-2级 |  387 | 严重污染 |        6 |

365 rows × 9 columns

In [12]:

```python
# 两个字段都是降序
df.sort_values(by=["aqiLevel", "bWendu"], ascending=False)
```

Out[12]:

|      |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi |  aqiInfo | aqiLevel |
| ---: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | -------: | -------: |
|   86 | 2018-03-28 |     25 |      9 | 多云~晴 |      东风 |  1-2级 |  387 | 严重污染 |        6 |
|   85 | 2018-03-27 |     27 |     11 |      晴 |      南风 |  1-2级 |  243 | 重度污染 |        5 |
|   91 | 2018-04-02 |     26 |     11 |    多云 |      北风 |  1-2级 |  287 | 重度污染 |        5 |
|   90 | 2018-04-01 |     25 |     11 | 晴~多云 |      南风 |  1-2级 |  218 | 重度污染 |        5 |
|   71 | 2018-03-13 |     17 |      5 | 晴~多云 |      南风 |  1-2级 |  287 | 重度污染 |        5 |
|  ... |        ... |    ... |    ... |     ... |       ... |    ... |  ... |      ... |      ... |
|  362 | 2018-12-29 |     -3 |    -12 |      晴 |    西北风 |    2级 |   29 |       优 |        1 |
|   22 | 2018-01-23 |     -4 |    -12 |      晴 |    西北风 |  3-4级 |   31 |       优 |        1 |
|   23 | 2018-01-24 |     -4 |    -11 |      晴 |    西南风 |  1-2级 |   34 |       优 |        1 |
|  340 | 2018-12-07 |     -4 |    -10 |      晴 |    西北风 |    3级 |   33 |       优 |        1 |
|  360 | 2018-12-27 |     -5 |    -12 | 多云~晴 |    西北风 |    3级 |   48 |       优 |        1 |

365 rows × 9 columns

In [13]:

```python
# 分别指定升序和降序
df.sort_values(by=["aqiLevel", "bWendu"], ascending=[True, False])
```

Out[13]:

|      |        ymd | bWendu | yWendu |      tianqi | fengxiang | fengli |  aqi |  aqiInfo | aqiLevel |
| ---: | ---------: | -----: | -----: | ----------: | --------: | -----: | ---: | -------: | -------: |
|  178 | 2018-06-28 |     35 |     24 |     多云~晴 |      北风 |  1-2级 |   33 |       优 |        1 |
|  149 | 2018-05-30 |     33 |     18 |          晴 |      西风 |  1-2级 |   46 |       优 |        1 |
|  206 | 2018-07-26 |     33 |     25 | 多云~雷阵雨 |    东北风 |  1-2级 |   40 |       优 |        1 |
|  158 | 2018-06-08 |     32 |     19 | 多云~雷阵雨 |    西南风 |  1-2级 |   43 |       优 |        1 |
|  205 | 2018-07-25 |     32 |     25 |        多云 |      北风 |  1-2级 |   28 |       优 |        1 |
|  ... |        ... |    ... |    ... |         ... |       ... |    ... |  ... |      ... |      ... |
|  317 | 2018-11-14 |     13 |      5 |        多云 |      南风 |  1-2级 |  266 | 重度污染 |        5 |
|  329 | 2018-11-26 |     10 |      0 |        多云 |    东南风 |    1级 |  245 | 重度污染 |        5 |
|  335 | 2018-12-02 |      9 |      2 |     雾~多云 |    东北风 |    1级 |  234 | 重度污染 |        5 |
|   57 | 2018-02-27 |      7 |      0 |          阴 |      东风 |  1-2级 |  220 | 重度污染 |        5 |
|   86 | 2018-03-28 |     25 |      9 |     多云~晴 |      东风 |  1-2级 |  387 | 严重污染 |        6 |

365 rows × 9 columns