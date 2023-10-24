---
title: Python数据分析之Pandas（三）
date: 2021-post-26 21:54:10.964
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/v2-2103a9f9659baf7be26d40fe5b4e9f1f_1440w.jpg'
theme: 'light'
tags: 
- Python
- 数据分析
---

: | -----: | ------: | -----: | --------: |
|    0 |      1 |    1193 |      5 | 978300760 |
|    1 |      1 |     661 |      3 | 978302109 |
|    2 |      1 |     914 |      3 | 978301968 |
|    3 |      1 |    3408 |      4 | 978300275 |
|    4 |      1 |    2355 |      5 | 978824291 |

In [3]:

```python
# 实现按照用户ID分组，然后对其中一列归一化
def ratings_norm(df):
    """
    @param df：每个用户分组的dataframe
    """
    min_value = df["Rating"].min()
    max_value = df["Rating"].max()
    df["Rating_norm"] = df["Rating"].apply(
        lambda x: (x-min_value)/(max_value-min_value))
    return df

ratings = ratings.groupby("UserID").apply(ratings_norm)
```

In [4]:

```python
ratings[ratings["UserID"]==1].head()
```

Out[4]:

|      | UserID | MovieID | Rating | Timestamp | Rating_norm |
| ---: | -----: | ------: | -----: | --------: | ----------: |
|    0 |      1 |    1193 |      5 | 978300760 |         1.0 |
|    1 |      1 |     661 |      3 | 978302109 |         0.0 |
|    2 |      1 |     914 |      3 | 978301968 |         0.0 |
|    3 |      1 |    3408 |      4 | 978300275 |         0.5 |
|    4 |      1 |    2355 |      5 | 978824291 |         1.0 |

可以看到UserID==1这个用户，Rating==3是他的最低分，是个乐观派，我们归一化到0分；

### 实例2：怎样取每个分组的TOPN数据？

获取2018年每个月温度最高的2天数据

In [5]:

```python
fpath = "./datas/beijing_tianqi/beijing_tianqi_2018.csv"
df = pd.read_csv(fpath)
# 替换掉温度的后缀℃
df.loc[:, "bWendu"] = df["bWendu"].str.replace("℃", "").astype('int32')
df.loc[:, "yWendu"] = df["yWendu"].str.replace("℃", "").astype('int32')
# 新增一列为月份
df['month'] = df['ymd'].str[:7]
df.head()
```

Out[5]:

|      |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi | aqiInfo | aqiLevel |   month |
| ---: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | ------: | -------: | ------: |
|    0 | 2018-01-01 |      3 |     -6 | 晴~多云 |    东北风 |  1-2级 |   59 |      良 |        2 | 2018-01 |
|    1 | 2018-01-02 |      2 |     -5 | 阴~多云 |    东北风 |  1-2级 |   49 |      优 |        1 | 2018-01 |
|    2 | 2018-01-03 |      2 |     -5 |    多云 |      北风 |  1-2级 |   28 |      优 |        1 | 2018-01 |
|    3 | 2018-01-04 |      0 |     -8 |      阴 |    东北风 |  1-2级 |   28 |      优 |        1 | 2018-01 |
|    4 | 2018-01-05 |      3 |     -6 | 多云~晴 |    西北风 |  1-2级 |   50 |      优 |        1 | 2018-01 |

In [6]:

```python
def getWenduTopN(df, topn):
    """
    这里的df，是每个月份分组group的df
    """
    return df.sort_values(by="bWendu")[["ymd", "bWendu"]][-topn:]

df.groupby("month").apply(getWenduTopN, topn=1).head()
```

Out[6]:

|         |      |        ymd | bWendu |
| ------: | ---: | ---------: | -----: |
|   month |      |            |        |
| 2018-01 |   18 | 2018-01-19 |      7 |
| 2018-02 |   56 | 2018-02-26 |     12 |
| 2018-03 |   85 | 2018-03-27 |     27 |
| 2018-04 |  118 | 2018-04-29 |     30 |
| 2018-05 |  150 | 2018-05-31 |     35 |

我们看到，grouby的apply函数返回的dataframe，其实和原来的dataframe其实可以完全不一样

# 20、Pandas的stack和pivot实现数据透视

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_pandas/12.png)

1. 经过统计得到多维度指标数据
2. 使用unstack实现数据二维透视
3. 使用pivot简化透视
4. stack、unstack、pivot的语法

### 1. 经过统计得到多维度指标数据

非常常见的统计场景，指定多个维度，计算聚合后的指标 

实例：统计得到“电影评分数据集”，每个月份的每个分数被评分多少次：（月份、分数1~5、次数）

In [1]:

```python
import pandas as pd
import numpy as np
%matplotlib inline
```

In [2]:

```python
df = pd.read_csv(
    "./datas/movielens-1m/ratings.dat",
    header=None,
    names="UserID::MovieID::Rating::Timestamp".split("::"),
    sep="::",
    engine="python"
)
```

In [3]:

```python
df.head()
```

Out[3]:

|      | UserID | MovieID | Rating | Timestamp |
| ---: | -----: | ------: | -----: | --------: |
|    0 |      1 |    1193 |      5 | 978300760 |
|    1 |      1 |     661 |      3 | 978302109 |
|    2 |      1 |     914 |      3 | 978301968 |
|    3 |      1 |    3408 |      4 | 978300275 |
|    4 |      1 |    2355 |      5 | 978824291 |

In [4]:

```python
df["pdate"] = pd.to_datetime(df["Timestamp"], unit='s')
```

In [5]:

```python
df.head()
```

Out[5]:

|      | UserID | MovieID | Rating | Timestamp |               pdate |
| ---: | -----: | ------: | -----: | --------: | ------------------: |
|    0 |      1 |    1193 |      5 | 978300760 | 2000-12-31 22:12:40 |
|    1 |      1 |     661 |      3 | 978302109 | 2000-12-31 22:35:09 |
|    2 |      1 |     914 |      3 | 978301968 | 2000-12-31 22:32:48 |
|    3 |      1 |    3408 |      4 | 978300275 | 2000-12-31 22:04:35 |
|    4 |      1 |    2355 |      5 | 978824291 | 2001-01-06 23:38:11 |

In [6]:

```python
df.dtypes
```

Out[6]:

```python
UserID                int64
MovieID               int64
Rating                int64
Timestamp             int64
pdate        datetime64[ns]
dtype: object
```

In [19]:

```python
# 实现数据统计
df_group = df.groupby([df["pdate"].dt.month, "Rating"])["UserID"].agg(pv=np.size)
```

In [20]:

```python
df_group.head(20)
```

Out[20]:

|       |        |   pv |
| ----: | -----: | ---: |
| pdate | Rating |      |
|     1 |      1 | 1127 |
|     2 |   2608 |      |
|     3 |   6442 |      |
|     4 |   8400 |      |
|     5 |   4495 |      |
|     2 |      1 |  629 |
|     2 |   1464 |      |
|     3 |   3297 |      |
|     4 |   4403 |      |
|     5 |   2335 |      |
|     3 |      1 |  466 |
|     2 |   1077 |      |
|     3 |   2523 |      |
|     4 |   3032 |      |
|     5 |   1439 |      |
|     4 |      1 | 1048 |
|     2 |   2247 |      |
|     3 |   5501 |      |
|     4 |   6748 |      |
|     5 |   3863 |      |

对这样格式的数据，我想查看按月份，不同评分的次数趋势，是没法实现的

需要将数据变换成每个评分是一列才可以实现

### 2. 使用unstack实现数据二维透视

目的：想要画图对比按照月份的不同评分的数量趋势

In [21]:

```python
df_stack = df_group.unstack()
df_stack
```

Out[21]:

|        |    pv |       |       |        |       |
| -----: | ----: | ----: | ----: | -----: | ----: |
| Rating |     1 |     2 |     3 |      4 |     5 |
|  pdate |       |       |       |        |       |
|      1 |  1127 |  2608 |  6442 |   8400 |  4495 |
|      2 |   629 |  1464 |  3297 |   4403 |  2335 |
|      3 |   466 |  1077 |  2523 |   3032 |  1439 |
|      4 |  1048 |  2247 |  5501 |   6748 |  3863 |
|      5 |  4557 |  7631 | 18481 |  25769 | 17840 |
|      6 |  3196 |  6500 | 15211 |  21838 | 14365 |
|      7 |  4891 |  9566 | 25421 |  34957 | 22169 |
|      8 | 10873 | 20597 | 50509 |  64198 | 42497 |
|      9 |  3107 |  5873 | 14702 |  19927 | 13182 |
|     10 |  2121 |  4785 | 12175 |  16095 | 10324 |
|     11 | 17701 | 32202 | 76069 | 102448 | 67041 |
|     12 |  6458 | 13007 | 30866 |  41156 | 26760 |

In [22]:

```python
df_stack.plot()
```

Out[22]:

```python
<matplotlib.axes._subplots.AxesSubplot at 0x1ba09b0ce48>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_pandas/13.png)

In [23]:

```python
# unstack和stack是互逆操作
df_stack.stack().head(20)
```

Out[23]:

|       |        |   pv |
| ----: | -----: | ---: |
| pdate | Rating |      |
|     1 |      1 | 1127 |
|     2 |   2608 |      |
|     3 |   6442 |      |
|     4 |   8400 |      |
|     5 |   4495 |      |
|     2 |      1 |  629 |
|     2 |   1464 |      |
|     3 |   3297 |      |
|     4 |   4403 |      |
|     5 |   2335 |      |
|     3 |      1 |  466 |
|     2 |   1077 |      |
|     3 |   2523 |      |
|     4 |   3032 |      |
|     5 |   1439 |      |
|     4 |      1 | 1048 |
|     2 |   2247 |      |
|     3 |   5501 |      |
|     4 |   6748 |      |
|     5 |   3863 |      |

### 3. 使用pivot简化透视

In [24]:

```python
df_group.head(20)
```

Out[24]:

|       |        |   pv |
| ----: | -----: | ---: |
| pdate | Rating |      |
|     1 |      1 | 1127 |
|     2 |   2608 |      |
|     3 |   6442 |      |
|     4 |   8400 |      |
|     5 |   4495 |      |
|     2 |      1 |  629 |
|     2 |   1464 |      |
|     3 |   3297 |      |
|     4 |   4403 |      |
|     5 |   2335 |      |
|     3 |      1 |  466 |
|     2 |   1077 |      |
|     3 |   2523 |      |
|     4 |   3032 |      |
|     5 |   1439 |      |
|     4 |      1 | 1048 |
|     2 |   2247 |      |
|     3 |   5501 |      |
|     4 |   6748 |      |
|     5 |   3863 |      |

In [25]:

```python
df_reset = df_group.reset_index()
df_reset.head()
```

Out[25]:

|      | pdate | Rating |   pv |
| ---: | ----: | -----: | ---: |
|    0 |     1 |      1 | 1127 |
|    1 |     1 |      2 | 2608 |
|    2 |     1 |      3 | 6442 |
|    3 |     1 |      4 | 8400 |
|    4 |     1 |      5 | 4495 |

In [26]:

```python
df_pivot = df_reset.pivot("pdate", "Rating", "pv")
```

In [27]:

```python
df_pivot.head()
```

Out[27]:

| Rating |    1 |    2 |     3 |     4 |     5 |
| -----: | ---: | ---: | ----: | ----: | ----: |
|  pdate |      |      |       |       |       |
|      1 | 1127 | 2608 |  6442 |  8400 |  4495 |
|      2 |  629 | 1464 |  3297 |  4403 |  2335 |
|      3 |  466 | 1077 |  2523 |  3032 |  1439 |
|      4 | 1048 | 2247 |  5501 |  6748 |  3863 |
|      5 | 4557 | 7631 | 18481 | 25769 | 17840 |

In [28]:

```python
df_pivot.plot()
```

Out[28]:

```python
<matplotlib.axes._subplots.AxesSubplot at 0x1ba09db6d48>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_pandas/14.png)

***pivot方法相当于对df使用set_index创建分层索引，然后调用unstack\***

### 4. stack、unstack、pivot的语法

#### stack：DataFrame.stack(level=-1, dropna=True)，将column变成index，类似把横放的书籍变成竖放

level=-1代表多层索引的最内层，可以通过==0、1、2指定多层索引的对应层

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_pandas/15.png)

#### unstack：DataFrame.unstack(level=-1, fill_value=None)，将index变成column，类似把竖放的书籍变成横放

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_pandas/16.png)

#### pivot：DataFrame.pivot(index=None, columns=None, values=None)，指定index、columns、values实现二维透视

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_pandas/17.png)

# 21、Pandas怎样快捷方便的处理日期数据

Pandas日期处理的作用：将2018-01-01、1/1/2018等多种日期格式映射成统一的格式对象，在该对象上提供强大的功能支持

几个概念：

1. pd.to_datetime：pandas的一个函数，能将字符串、列表、series变成日期形式
2. Timestamp：pandas表示日期的对象形式
3. DatetimeIndex：pandas表示日期的对象列表形式

其中：

- DatetimeIndex是Timestamp的列表形式
- pd.to_datetime对单个日期字符串处理会得到Timestamp
- pd.to_datetime对日期字符串列表处理会得到DatetimeIndex

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_pandas/18.png)

### 问题：怎样统计每周、每月、每季度的最高温度？

### 1、读取天气数据到dataframe

In [1]:

```python
import pandas as pd
%matplotlib inline
```

In [2]:

```python
fpath = "./datas/beijing_tianqi/beijing_tianqi_2018.csv"
df = pd.read_csv(fpath)
# 替换掉温度的后缀℃
df.loc[:, "bWendu"] = df["bWendu"].str.replace("℃", "").astype('int32')
df.loc[:, "yWendu"] = df["yWendu"].str.replace("℃", "").astype('int32')
df.head()
```

Out[2]:

|      |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi | aqiInfo | aqiLevel |
| ---: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | ------: | -------: |
|    0 | 2018-01-01 |      3 |     -6 | 晴~多云 |    东北风 |  1-2级 |   59 |      良 |        2 |
|    1 | 2018-01-02 |      2 |     -5 | 阴~多云 |    东北风 |  1-2级 |   49 |      优 |        1 |
|    2 | 2018-01-03 |      2 |     -5 |    多云 |      北风 |  1-2级 |   28 |      优 |        1 |
|    3 | 2018-01-04 |      0 |     -8 |      阴 |    东北风 |  1-2级 |   28 |      优 |        1 |
|    4 | 2018-01-05 |      3 |     -6 | 多云~晴 |    西北风 |  1-2级 |   50 |      优 |        1 |

### 2、将日期列转换成pandas的日期

In [3]:

```python
df.set_index(pd.to_datetime(df["ymd"]), inplace=True)
```

In [4]:

```python
df.head()
```

Out[4]:

|            |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi | aqiInfo | aqiLevel |
| ---------: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | ------: | -------: |
|        ymd |            |        |        |         |           |        |      |         |          |
| 2018-01-01 | 2018-01-01 |      3 |     -6 | 晴~多云 |    东北风 |  1-2级 |   59 |      良 |        2 |
| 2018-01-02 | 2018-01-02 |      2 |     -5 | 阴~多云 |    东北风 |  1-2级 |   49 |      优 |        1 |
| 2018-01-03 | 2018-01-03 |      2 |     -5 |    多云 |      北风 |  1-2级 |   28 |      优 |        1 |
| 2018-01-04 | 2018-01-04 |      0 |     -8 |      阴 |    东北风 |  1-2级 |   28 |      优 |        1 |
| 2018-01-05 | 2018-01-05 |      3 |     -6 | 多云~晴 |    西北风 |  1-2级 |   50 |      优 |        1 |

In [5]:

```python
df.index
```

Out[5]:

```python
DatetimeIndex(['2018-01-01', '2018-01-02', '2018-01-03', '2018-01-04',
               '2018-01-05', '2018-01-06', '2018-01-07', '2018-01-08',
               '2018-01-09', '2018-01-10',
               ...
               '2018-12-22', '2018-12-23', '2018-12-24', '2018-12-25',
               '2018-12-26', '2018-12-27', '2018-12-28', '2018-12-29',
               '2018-12-30', '2018-12-31'],
              dtype='datetime64[ns]', name='ymd', length=365, freq=None)
```

In [6]:

```python
# DatetimeIndex是Timestamp的列表形式
df.index[0]
```

Out[6]:

```python
Timestamp('2018-01-01 00:00:00')
```

### 3、 方便的对DatetimeIndex进行查询

In [7]:

```python
# 筛选固定的某一天
df.loc['2018-01-05']
```

Out[7]:

```python
ymd          2018-01-05
bWendu                3
yWendu               -6
tianqi             多云~晴
fengxiang           西北风
fengli             1-2级
aqi                  50
aqiInfo               优
aqiLevel              1
Name: 2018-01-05 00:00:00, dtype: object
```

In [8]:

```python
# 日期区间
df.loc['2018-01-05':'2018-01-10']
```

Out[8]:

|            |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi | aqiInfo | aqiLevel |
| ---------: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | ------: | -------: |
|        ymd |            |        |        |         |           |        |      |         |          |
| 2018-01-05 | 2018-01-05 |      3 |     -6 | 多云~晴 |    西北风 |  1-2级 |   50 |      优 |        1 |
| 2018-01-06 | 2018-01-06 |      2 |     -5 | 多云~阴 |    西南风 |  1-2级 |   32 |      优 |        1 |
| 2018-01-07 | 2018-01-07 |      2 |     -4 | 阴~多云 |    西南风 |  1-2级 |   59 |      良 |        2 |
| 2018-01-08 | 2018-01-08 |      2 |     -6 |      晴 |    西北风 |  4-5级 |   50 |      优 |        1 |
| 2018-01-09 | 2018-01-09 |      1 |     -8 |      晴 |    西北风 |  3-4级 |   34 |      优 |        1 |
| 2018-01-10 | 2018-01-10 |     -2 |    -10 |      晴 |    西北风 |  1-2级 |   26 |      优 |        1 |

In [10]:

```python
# 按月份前缀筛选
df.loc['2018-03']
```

Out[10]:

|            |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi |  aqiInfo | aqiLevel |
| ---------: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | -------: | -------: |
|        ymd |            |        |        |         |           |        |      |          |          |
| 2018-03-01 | 2018-03-01 |      8 |     -3 |    多云 |    西南风 |  1-2级 |   46 |       优 |        1 |
| 2018-03-02 | 2018-03-02 |      9 |     -1 | 晴~多云 |      北风 |  1-2级 |   95 |       良 |        2 |
| 2018-03-03 | 2018-03-03 |     13 |      3 | 多云~阴 |      北风 |  1-2级 |  214 | 重度污染 |        5 |
| 2018-03-04 | 2018-03-04 |      7 |     -2 | 阴~多云 |    东南风 |  1-2级 |  144 | 轻度污染 |        3 |
| 2018-03-05 | 2018-03-05 |      8 |     -3 |      晴 |      南风 |  1-2级 |   94 |       良 |        2 |
| 2018-03-06 | 2018-03-06 |      6 |     -3 | 多云~阴 |    东南风 |  3-4级 |   67 |       良 |        2 |
| 2018-03-07 | 2018-03-07 |      6 |     -2 | 阴~多云 |      北风 |  1-2级 |   65 |       良 |        2 |
| 2018-03-08 | 2018-03-08 |      8 |     -4 |      晴 |    东北风 |  1-2级 |   62 |       良 |        2 |
| 2018-03-09 | 2018-03-09 |     10 |     -2 |    多云 |    西南风 |  1-2级 |  132 | 轻度污染 |        3 |
| 2018-03-10 | 2018-03-10 |     14 |     -2 |      晴 |    东南风 |  1-2级 |  171 | 中度污染 |        4 |
| 2018-03-11 | 2018-03-11 |     11 |      0 |    多云 |      南风 |  1-2级 |   81 |       良 |        2 |
| 2018-03-12 | 2018-03-12 |     15 |      3 | 多云~晴 |      南风 |  1-2级 |  174 | 中度污染 |        4 |
| 2018-03-13 | 2018-03-13 |     17 |      5 | 晴~多云 |      南风 |  1-2级 |  287 | 重度污染 |        5 |
| 2018-03-14 | 2018-03-14 |     15 |      6 | 多云~阴 |    东北风 |  1-2级 |  293 | 重度污染 |        5 |
| 2018-03-15 | 2018-03-15 |     12 |     -1 | 多云~晴 |    东北风 |  3-4级 |   70 |       良 |        2 |
| 2018-03-16 | 2018-03-16 |     10 |     -1 |    多云 |      南风 |  1-2级 |   58 |       良 |        2 |
| 2018-03-17 | 2018-03-17 |      4 |      0 | 小雨~阴 |      南风 |  1-2级 |   81 |       良 |        2 |
| 2018-03-18 | 2018-03-18 |     13 |      1 | 多云~晴 |    西南风 |  1-2级 |  134 | 轻度污染 |        3 |
| 2018-03-19 | 2018-03-19 |     13 |      2 |    多云 |      东风 |  1-2级 |  107 | 轻度污染 |        3 |
| 2018-03-20 | 2018-03-20 |     10 |     -2 |    多云 |      南风 |  1-2级 |   41 |       优 |        1 |
| 2018-03-21 | 2018-03-21 |     11 |      1 |    多云 |    西南风 |  1-2级 |   76 |       良 |        2 |
| 2018-03-22 | 2018-03-22 |     17 |      4 | 晴~多云 |    西南风 |  1-2级 |  112 | 轻度污染 |        3 |
| 2018-03-23 | 2018-03-23 |     18 |      5 |    多云 |      北风 |  1-2级 |  146 | 轻度污染 |        3 |
| 2018-03-24 | 2018-03-24 |     22 |      5 |      晴 |    西南风 |  1-2级 |  119 | 轻度污染 |        3 |
| 2018-03-25 | 2018-03-25 |     24 |      7 |      晴 |      南风 |  1-2级 |   78 |       良 |        2 |
| 2018-03-26 | 2018-03-26 |     25 |      7 |    多云 |    西南风 |  1-2级 |  151 | 中度污染 |        4 |
| 2018-03-27 | 2018-03-27 |     27 |     11 |      晴 |      南风 |  1-2级 |  243 | 重度污染 |        5 |
| 2018-03-28 | 2018-03-28 |     25 |      9 | 多云~晴 |      东风 |  1-2级 |  387 | 严重污染 |        6 |
| 2018-03-29 | 2018-03-29 |     19 |      7 |      晴 |      南风 |  1-2级 |  119 | 轻度污染 |        3 |
| 2018-03-30 | 2018-03-30 |     18 |      8 |    多云 |      南风 |  1-2级 |   68 |       良 |        2 |
| 2018-03-31 | 2018-03-31 |     23 |      9 | 多云~晴 |      南风 |  1-2级 |  125 | 轻度污染 |        3 |

In [11]:

```python
# 按月份前缀筛选
df.loc["2018-07":"2018-09"].index
```

Out[11]:

```python
DatetimeIndex(['2018-07-01', '2018-07-02', '2018-07-03', '2018-07-04',
               '2018-07-05', '2018-07-06', '2018-07-07', '2018-07-08',
               '2018-07-09', '2018-07-10', '2018-07-11', '2018-07-12',
               '2018-07-13', '2018-07-14', '2018-07-15', '2018-07-16',
               '2018-07-17', '2018-07-18', '2018-07-19', '2018-07-20',
               '2018-07-21', '2018-07-22', '2018-07-23', '2018-07-24',
               '2018-07-25', '2018-07-26', '2018-07-27', '2018-07-28',
               '2018-07-29', '2018-07-30', '2018-07-31', '2018-08-01',
               '2018-08-02', '2018-08-03', '2018-08-04', '2018-08-05',
               '2018-08-06', '2018-08-07', '2018-08-08', '2018-08-09',
               '2018-08-10', '2018-08-11', '2018-08-12', '2018-08-13',
               '2018-08-14', '2018-08-15', '2018-08-16', '2018-08-17',
               '2018-08-18', '2018-08-19', '2018-08-20', '2018-08-21',
               '2018-08-22', '2018-08-23', '2018-08-24', '2018-08-25',
               '2018-08-26', '2018-08-27', '2018-08-28', '2018-08-29',
               '2018-08-30', '2018-08-31', '2018-09-01', '2018-09-02',
               '2018-09-03', '2018-09-04', '2018-09-05', '2018-09-06',
               '2018-09-07', '2018-09-08', '2018-09-09', '2018-09-10',
               '2018-09-11', '2018-09-12', '2018-09-13', '2018-09-14',
               '2018-09-15', '2018-09-16', '2018-09-17', '2018-09-18',
               '2018-09-19', '2018-09-20', '2018-09-21', '2018-09-22',
               '2018-09-23', '2018-09-24', '2018-09-25', '2018-09-26',
               '2018-09-27', '2018-09-28', '2018-09-29', '2018-09-30'],
              dtype='datetime64[ns]', name='ymd', freq=None)
```

In [12]:

```python
# 按年份前缀筛选
df.loc["2018"].head()
```

Out[12]:

|            |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi | aqiInfo | aqiLevel |
| ---------: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | ------: | -------: |
|        ymd |            |        |        |         |           |        |      |         |          |
| 2018-01-01 | 2018-01-01 |      3 |     -6 | 晴~多云 |    东北风 |  1-2级 |   59 |      良 |        2 |
| 2018-01-02 | 2018-01-02 |      2 |     -5 | 阴~多云 |    东北风 |  1-2级 |   49 |      优 |        1 |
| 2018-01-03 | 2018-01-03 |      2 |     -5 |    多云 |      北风 |  1-2级 |   28 |      优 |        1 |
| 2018-01-04 | 2018-01-04 |      0 |     -8 |      阴 |    东北风 |  1-2级 |   28 |      优 |        1 |
| 2018-01-05 | 2018-01-05 |      3 |     -6 | 多云~晴 |    西北风 |  1-2级 |   50 |      优 |        1 |

### 4、方便的获取周、月、季度

Timestamp、DatetimeIndex支持大量的属性可以获取日期分量：
https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#time-date-components

In [13]:

```python
# 周数字列表
df.index.week
```

Out[13]:

```python
Int64Index([ 1,  1,  1,  1,  1,  1,  1,  2,  2,  2,
            ...
            51, 51, 52, 52, 52, 52, 52, 52, 52,  1],
           dtype='int64', name='ymd', length=365)
```

In [14]:

```python
# 月数字列表
df.index.month
```

Out[14]:

```python
Int64Index([ 1,  1,  1,  1,  1,  1,  1,  1,  1,  1,
            ...
            12, 12, 12, 12, 12, 12, 12, 12, 12, 12],
           dtype='int64', name='ymd', length=365)
```

In [15]:

```python
# 季度数字列表
df.index.quarter
```

Out[15]:

```
Int64Index([1, 1, 1, 1, 1, 1, 1, 1, 1, 1,
            ...
            4, 4, 4, 4, 4, 4, 4, 4, 4, 4],
           dtype='int64', name='ymd', length=365)
```

### 5、统计每周、每月、每个季度的最高温度

#### 统计每周的数据

In [16]:

```python
df.groupby(df.index.week)["bWendu"].max().head()
```

Out[16]:

```python
ymd
1    3
2    6
3    7
4   -1
5    4
Name: bWendu, dtype: int32
```

In [17]:

```python
df.groupby(df.index.week)["bWendu"].max().plot()
```

Out[17]:

```python
<matplotlib.axes._subplots.AxesSubplot at 0x23300b75b88>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_pandas/19.png)

#### 统计每个月的数据

In [18]:

```python
df.groupby(df.index.month)["bWendu"].max()
```

Out[18]:

```python
ymd
1      7
2     12
3     27
4     30
5     35
6     38
7     37
8     36
9     31
10    25
11    18
12    10
Name: bWendu, dtype: int32
```

In [19]:

```python
df.groupby(df.index.month)["bWendu"].max().plot()
```

Out[19]:

```python
<matplotlib.axes._subplots.AxesSubplot at 0x23302dac4c8>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_pandas/20.png)

#### 统计每个季度的数据

In [20]:

```
df.groupby(df.index.quarter)["bWendu"].max()
```

Out[20]:

```python
ymd
1    27
2    38
3    37
4    25
Name: bWendu, dtype: int32
```

In [21]:

```python
df.groupby(df.index.quarter)["bWendu"].max().plot()
```

Out[21]:

```python
<matplotlib.axes._subplots.AxesSubplot at 0x23302e338c8>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_pandas/21.png)

# 22、Pandas怎么处理日期索引的缺失？

问题：按日期统计的数据，缺失了某天，导致数据不全该怎么补充日期？

可以用两种方法实现：
1、DataFrame.reindex，调整dataframe的索引以适应新的索引
2、DataFrame.resample，可以对时间序列重采样，支持补充缺失值

## 问题：如果缺失了索引该怎么填充？

In [1]:

```python
import pandas as pd
%matplotlib inline
```

In [2]:

```python
df = pd.DataFrame({
    "pdate": ["2019-12-01", "2019-12-02", "2019-12-04", "2019-12-05"],
    "pv": [100, 200, 400, 500],
    "uv": [10, 20, 40, 50],
})

df
```

Out[2]:

|      |      pdate |   pv |   uv |
| ---: | ---------: | ---: | ---: |
|    0 | 2019-12-01 |  100 |   10 |
|    1 | 2019-12-02 |  200 |   20 |
|    2 | 2019-12-04 |  400 |   40 |
|    3 | 2019-12-05 |  500 |   50 |

In [3]:

```python
df.set_index("pdate").plot()
```

Out[3]:

```python
<matplotlib.axes._subplots.AxesSubplot at 0x1a0d908bf48>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_pandas/22.png)

***问题，这里缺失了2019-12-03的数据，导致数据不全该怎么补充？\***

## 方法1：使用pandas.reindex方法

### 1、将df的索引变成日期索引

In [4]:

```python
df_date = df.set_index("pdate")
df_date
```

Out[4]:

|            |   pv |   uv |
| ---------: | ---: | ---: |
|      pdate |      |      |
| 2019-12-01 |  100 |   10 |
| 2019-12-02 |  200 |   20 |
| 2019-12-04 |  400 |   40 |
| 2019-12-05 |  500 |   50 |

In [5]:

```python
df_date.index
```

Out[5]:

```python
Index(['2019-12-01', '2019-12-02', '2019-12-04', '2019-12-05'], dtype='object', name='pdate')
```

In [6]:

```python
# 将df的索引设置为日期索引
df_date = df_date.set_index(pd.to_datetime(df_date.index))
df_date
```

Out[6]:

|            |   pv |   uv |
| ---------: | ---: | ---: |
|      pdate |      |      |
| 2019-12-01 |  100 |   10 |
| 2019-12-02 |  200 |   20 |
| 2019-12-04 |  400 |   40 |
| 2019-12-05 |  500 |   50 |

In [7]:

```python
df_date.index
```

Out[7]:

```python
DatetimeIndex(['2019-12-01', '2019-12-02', '2019-12-04', '2019-12-05'], dtype='datetime64[ns]', name='pdate', freq=None)
```

### 2、使用pandas.reindex填充缺失的索引

In [8]:

```python
# 生成完整的日期序列
pdates = pd.date_range(start="2019-12-01", end="2019-12-05")
pdates
```

Out[8]:

```python
DatetimeIndex(['2019-12-01', '2019-12-02', '2019-12-03', '2019-12-04',
               '2019-12-05'],
              dtype='datetime64[ns]', freq='D')
```

In [9]:

```python
df_date_new = df_date.reindex(pdates, fill_value=0)
df_date_new
```

Out[9]:

|            |   pv |   uv |
| ---------: | ---: | ---: |
| 2019-12-01 |  100 |   10 |
| 2019-12-02 |  200 |   20 |
| 2019-12-03 |    0 |    0 |
| 2019-12-04 |  400 |   40 |
| 2019-12-05 |  500 |   50 |

In [10]:

```python
df_date_new.plot()
```

Out[10]:

```python
<matplotlib.axes._subplots.AxesSubplot at 0x1a0db1ab388>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_pandas/23.png)

## 方法2：使用pandas.resample方法

### 1、先将索引变成日期索引

In [11]:

```python
df
```

Out[11]:

|      |      pdate |   pv |   uv |
| ---: | ---------: | ---: | ---: |
|    0 | 2019-12-01 |  100 |   10 |
|    1 | 2019-12-02 |  200 |   20 |
|    2 | 2019-12-04 |  400 |   40 |
|    3 | 2019-12-05 |  500 |   50 |

In [12]:

```python
df_new2 = df.set_index(pd.to_datetime(df["pdate"])).drop("pdate", axis=1)
df_new2
```

Out[12]:

|            |   pv |   uv |
| ---------: | ---: | ---: |
|      pdate |      |      |
| 2019-12-01 |  100 |   10 |
| 2019-12-02 |  200 |   20 |
| 2019-12-04 |  400 |   40 |
| 2019-12-05 |  500 |   50 |

In [13]:

```python
df_new2.index
```

Out[13]:

```python
DatetimeIndex(['2019-12-01', '2019-12-02', '2019-12-04', '2019-12-05'], dtype='datetime64[ns]', name='pdate', freq=None)
```

### 2、使用dataframe的resample的方法按照天重采样

resample的含义：
改变数据的时间频率，比如把天数据变成月份，或者把小时数据变成分钟级别

resample的语法：
(DataFrame or Series).resample(arguments).(aggregate function)

resample的采样规则参数：
https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#offset-aliases

In [14]:

```python
# 由于采样会让区间变成一个值，所以需要指定mean等采样值的设定方法
df_new2 = df_new2.resample("D").mean().fillna(0)
df_new2
```

Out[14]:

|            |    pv |   uv |
| ---------: | ----: | ---: |
|      pdate |       |      |
| 2019-12-01 | 100.0 | 10.0 |
| 2019-12-02 | 200.0 | 20.0 |
| 2019-12-03 |   0.0 |  0.0 |
| 2019-12-04 | 400.0 | 40.0 |
| 2019-12-05 | 500.0 | 50.0 |

In [15]:

```python
# resample的使用方式
df_new2.resample("2D").mean()
```

Out[15]:

|            |    pv |   uv |
| ---------: | ----: | ---: |
|      pdate |       |      |
| 2019-12-01 | 150.0 | 15.0 |
| 2019-12-03 | 200.0 | 20.0 |
| 2019-12-05 | 500.0 | 50.0 |

# 23、Pandas怎样实现Excel的vlookup并且在指定列后面输出？

背景： 

1. 有两个excel，他们有相同的一个列； 
2. 按照这个列合并成一个大的excel，即vlookup功能，要求：
   - 只需要第二个excel的少量的列，比如从40个列中挑选2个列 
   - 新增的来自第二个excel的列需要放到第一个excel指定的列后面； 
3. 将结果输出到一个新的excel;

### 步骤1：读取两个数据表

In [1]:

```python
import pandas as pd
```

In [2]:

```python
# 学生成绩表
df_grade = pd.read_excel("./course_datas/c23_excel_vlookup/学生成绩表.xlsx") 
df_grade.head()
```

Out[2]:

|      | 班级 | 学号 | 语文成绩 | 数学成绩 | 英语成绩 |
| ---: | ---: | ---: | -------: | -------: | -------: |
|    0 |  C01 | S001 |       99 |       84 |       88 |
|    1 |  C01 | S002 |       66 |       95 |       77 |
|    2 |  C01 | S003 |       68 |       68 |       61 |
|    3 |  C01 | S004 |       63 |       66 |       82 |
|    4 |  C01 | S005 |       72 |       95 |       94 |

In [3]:

```python
# 学生信息表
df_sinfo = pd.read_excel("./course_datas/c23_excel_vlookup/学生信息表.xlsx") 
df_sinfo.head()
```

Out[3]:

|      | 学号 | 姓名 | 性别 | 年龄 | 籍贯 |
| ---: | ---: | ---: | ---: | ---: | ---: |
|    0 | S001 | 怠涵 |   女 |   23 | 山东 |
|    1 | S002 | 婉清 |   女 |   25 | 河南 |
|    2 | S003 | 溪榕 |   女 |   23 | 湖北 |
|    3 | S004 | 漠涓 |   女 |   19 | 陕西 |
|    4 | S005 | 祈博 |   女 |   24 | 山东 |

***目标：怎样将第二个“学生信息表”的姓名、性别两列，添加到第一个表“学生成绩表”，并且放在第一个表的“学号”列后面？\***

### 步骤2：实现两个表的关联

即excel的vloopup功能

In [4]:

```python
# 只筛选第二个表的少量的列
df_sinfo = df_sinfo[["学号", "姓名", "性别"]]
df_sinfo.head()
```

Out[4]:

|      | 学号 | 姓名 | 性别 |
| ---: | ---: | ---: | ---: |
|    0 | S001 | 怠涵 |   女 |
|    1 | S002 | 婉清 |   女 |
|    2 | S003 | 溪榕 |   女 |
|    3 | S004 | 漠涓 |   女 |
|    4 | S005 | 祈博 |   女 |

In [5]:

```python
df_merge = pd.merge(left=df_grade, right=df_sinfo, left_on="学号", right_on="学号")
df_merge.head()
```

Out[5]:

|      | 班级 | 学号 | 语文成绩 | 数学成绩 | 英语成绩 | 姓名 | 性别 |
| ---: | ---: | ---: | -------: | -------: | -------: | ---: | ---: |
|    0 |  C01 | S001 |       99 |       84 |       88 | 怠涵 |   女 |
|    1 |  C01 | S002 |       66 |       95 |       77 | 婉清 |   女 |
|    2 |  C01 | S003 |       68 |       68 |       61 | 溪榕 |   女 |
|    3 |  C01 | S004 |       63 |       66 |       82 | 漠涓 |   女 |
|    4 |  C01 | S005 |       72 |       95 |       94 | 祈博 |   女 |

### 步骤3：调整列的顺序

In [6]:

```python
df_merge.columns
```

Out[6]:

```python
Index(['班级', '学号', '语文成绩', '数学成绩', '英语成绩', '姓名', '性别'], dtype='object')
```

#### 问题：怎样将'姓名', '性别'两列，放到'学号'的后面？

接下来需要用Python的语法实现列表的处理

In [7]:

```python
# 将columns变成python的列表形式
new_columns = df_merge.columns.to_list()
new_columns
```

Out[7]:

```python
['班级', '学号', '语文成绩', '数学成绩', '英语成绩', '姓名', '性别']
```

In [8]:

```
# 按逆序insert，会将"姓名"，"性别"放到"学号"的后面
for name in ["姓名", "性别"][::-1]:
    new_columns.remove(name)
    new_columns.insert(new_columns.index("学号")+1, name)
```

In [9]:

```python
new_columns
```

Out[9]:

```python
['班级', '学号', '姓名', '性别', '语文成绩', '数学成绩', '英语成绩']
```

In [10]:

```python
df_merge = df_merge.reindex(columns=new_columns)
df_merge.head()
```

Out[10]:

|      | 班级 | 学号 | 姓名 | 性别 | 语文成绩 | 数学成绩 | 英语成绩 |
| ---: | ---: | ---: | ---: | ---: | -------: | -------: | -------: |
|    0 |  C01 | S001 | 怠涵 |   女 |       99 |       84 |       88 |
|    1 |  C01 | S002 | 婉清 |   女 |       66 |       95 |       77 |
|    2 |  C01 | S003 | 溪榕 |   女 |       68 |       68 |       61 |
|    3 |  C01 | S004 | 漠涓 |   女 |       63 |       66 |       82 |
|    4 |  C01 | S005 | 祈博 |   女 |       72 |       95 |       94 |

### 步骤4：输出最终的Excel文件

In [11]:

```python
df_merge.to_excel("./course_datas/c23_excel_vlookup/合并后的数据表.xlsx", index=False)
```

# 24、Pandas怎样结合Pyecharts绘制交互性折线图？

背景： 

- Pandas是Python用于数据分析领域的超级牛的库
- Echarts是百度开源的非常好用强大的可视化图表库，Pyecharts是它的Python库版本

## 1、读取数据

In [1]:

```python
import pandas as pd
```

In [2]:

```python
xlsx_path = "./datas/stocks/baidu_stocks.xlsx"
df = pd.read_excel(xlsx_path, index_col="datetime", parse_dates=True)
df.head()
```

Out[2]:

|            | code |       open |      close |       high |        low |     vol | p_change |
| ---------: | ---: | ---------: | ---------: | ---------: | ---------: | ------: | -------: |
|   datetime |      |            |            |            |            |         |          |
| 2019-12-03 | BIDU | 115.199997 | 114.800003 | 116.019997 | 113.300003 | 3493249 |    -2.25 |
| 2019-12-02 | BIDU | 118.389999 | 117.440002 | 119.764999 | 116.400002 | 2203313 |    -0.92 |
| 2019-11-29 | BIDU | 118.300003 | 118.529999 | 118.690002 | 117.599998 | 1917004 |    -0.82 |
| 2019-11-27 | BIDU | 119.180000 | 119.510002 | 119.839996 | 118.440002 | 2341070 |     0.77 |
| 2019-11-26 | BIDU | 120.010002 | 118.599998 | 120.440002 | 118.099998 | 3813176 |    -1.43 |

In [3]:

```python
df.index
```

Out[3]:

```python
DatetimeIndex(['2019-12-03', '2019-12-02', '2019-11-29', '2019-11-27',
               '2019-11-26', '2019-11-25', '2019-11-22', '2019-11-21',
               '2019-11-20', '2019-11-19',
               ...
               '2019-01-15', '2019-01-14', '2019-01-11', '2019-01-10',
               '2019-01-09', '2019-01-08', '2019-01-07', '2019-01-04',
               '2019-01-03', '2019-01-02'],
              dtype='datetime64[ns]', name='datetime', length=227, freq=None)
```

In [4]:

```
df.sort_index(inplace=True)
df.head()
```

Out[4]:

|            | code |       open |      close |       high |        low |     vol | p_change |
| ---------: | ---: | ---------: | ---------: | ---------: | ---------: | ------: | -------: |
|   datetime |      |            |            |            |            |         |          |
| 2019-01-02 | BIDU | 156.179993 | 162.250000 | 164.330002 | 155.490005 | 2996952 |      NaN |
| 2019-01-03 | BIDU | 158.750000 | 154.710007 | 159.880005 | 153.779999 | 3879180 |    -4.65 |
| 2019-01-04 | BIDU | 157.600006 | 160.949997 | 162.429993 | 157.250000 | 3847497 |     4.03 |
| 2019-01-07 | BIDU | 162.600006 | 162.600006 | 164.490005 | 158.509995 | 3266091 |     1.03 |
| 2019-01-08 | BIDU | 162.190002 | 163.399994 | 163.889999 | 158.160004 | 3253361 |     0.49 |

## 2、使用Pyecharts绘制折线图

In [5]:

```python
# 如果没有安装，使用pip install pyecharts安装
from pyecharts.charts import Line
from pyecharts import options as opts
```

In [6]:

```python
# 折线图
line = Line()

# x轴
line.add_xaxis(df.index.to_list())

# 每个y轴
line.add_yaxis("开盘价", df["open"].round(2).to_list())
line.add_yaxis("收盘价", df["close"].round(2).to_list())

# 图表配置
line.set_global_opts(
    title_opts=opts.TitleOpts(title="百度股票2019年"),
    tooltip_opts=opts.TooltipOpts(trigger="axis", axis_pointer_type="cross")
)
```

Out[6]:

```python
<pyecharts.charts.basic_charts.line.Line at 0x201bd51d088>
```

In [7]:

```python
# 渲染数据
line.render_notebook()
```

# 25、Pandas结合Sklearn实现泰坦尼克存活率预测

### 实例目标：实现泰坦尼克存活预测

处理步骤：
1、输入数据：使用Pandas读取训练数据(历史数据，特点是已经知道了这个人最后有没有活下来)
2、训练模型：使用Sklearn训练模型
3、使用模型：对于一个新的不知道存活的人，预估他存活的概率 

### 步骤1：读取训练数据

In [1]:

```python
import pandas as pd
```

In [2]:

```python
df_train = pd.read_csv("./datas/titanic/titanic_train.csv")
df_train.head()
```

Out[2]:

|      | PassengerId | Survived | Pclass |                                              Name |    Sex |  Age | SibSp | Parch |           Ticket |    Fare | Cabin | Embarked |
| ---: | ----------: | -------: | -----: | ------------------------------------------------: | -----: | ---: | ----: | ----: | ---------------: | ------: | ----: | -------: |
|    0 |           1 |        0 |      3 |                           Braund, Mr. Owen Harris |   male | 22.0 |     1 |     0 |        A/5 21171 |  7.2500 |   NaN |        S |
|    1 |           2 |        1 |      1 | Cumings, Mrs. John Bradley (Florence Briggs Th... | female | 38.0 |     1 |     0 |         PC 17599 | 71.2833 |   C85 |        C |
|    2 |           3 |        1 |      3 |                            Heikkinen, Miss. Laina | female | 26.0 |     0 |     0 | STON/O2. 3101282 |  7.9250 |   NaN |        S |
|    3 |           4 |        1 |      1 |      Futrelle, Mrs. Jacques Heath (Lily May Peel) | female | 35.0 |     1 |     0 |           113803 | 53.1000 |  C123 |        S |
|    4 |           5 |        0 |      3 |                          Allen, Mr. William Henry |   male | 35.0 |     0 |     0 |           373450 |  8.0500 |   NaN |        S |

***其中，Survived==1代表这个人活下来了、==0代表没活下来；其他的都是这个人的信息和当时的仓位、票务情况\***

In [3]:

```python
# 我们只挑选两列，作为预测需要的特征
feature_cols = ['Pclass', 'Parch']
X = df_train.loc[:, feature_cols]
X.head()
```

Out[3]:

|      | Pclass | Parch |
| ---: | -----: | ----: |
|    0 |      3 |     0 |
|    1 |      1 |     0 |
|    2 |      3 |     0 |
|    3 |      1 |     0 |
|    4 |      3 |     0 |

In [4]:

```python
# 单独提取是否存活的列，作为预测的目标
y = df_train.Survived
y.head()
```

Out[4]:

```python
0    0
1    1
2    1
3    1
4    0
Name: Survived, dtype: int64
```

### 步骤2：训练模型

In [5]:

```python
from sklearn.linear_model import LogisticRegression
# 创建模型对象
logreg = LogisticRegression()

# 实现模型训练
logreg.fit(X, y)
//anaconda3/lib/python3.7/site-packages/sklearn/linear_model/logistic.py:432: FutureWarning: Default solver will be changed to 'lbfgs' in 0.22. Specify a solver to silence this warning.
  FutureWarning)
```

Out[5]:

```python
LogisticRegression(C=1.0, class_weight=None, dual=False, fit_intercept=True,
                   intercept_scaling=1, l1_ratio=None, max_iter=100,
                   multi_class='warn', n_jobs=None, penalty='l2',
                   random_state=None, solver='warn', tol=0.0001, verbose=0,
                   warm_start=False)
```

In [6]:

```python
logreg.score(X, y)
```

Out[6]:

```python
0.6879910213243546
```

### 步骤3：对于未知数据使用模型

机器学习的核心目标，是使用模型预测未知的事物

比如预测股票明天是涨还是跌、一套新的二手房成交价大概多少钱、用户打开APP最可能看那些视频等问题

In [7]:

```python
# 找一个历史数据中不存在的数据
X.drop_duplicates().sort_values(by=["Pclass", "Parch"])
```

Out[7]:

|      | Pclass | Parch |
| ---: | -----: | ----: |
|    1 |      1 |     0 |
|   54 |      1 |     1 |
|   27 |      1 |     2 |
|  438 |      1 |     4 |
|    9 |      2 |     0 |
|   98 |      2 |     1 |
|   43 |      2 |     2 |
|  437 |      2 |     3 |
|    0 |      3 |     0 |
|    7 |      3 |     1 |
|    8 |      3 |     2 |
|   86 |      3 |     3 |
|  167 |      3 |     4 |
|   13 |      3 |     5 |
|  678 |      3 |     6 |

In [8]:

```python
# 预测这个数据存活的概率
logreg.predict([[2, 4]])
```

Out[8]:

```python
array([1])
```

In [9]:

```python
logreg.predict_proba([[2, 4]])
```

Out[9]:

```python
array([[0.35053893, 0.64946107]])
```

# 26、Pandas处理分析网站原始访问日志

目标：真实项目的实战，探索Pandas的数据处理与分析

实例：
数据来源：某大佬的wordpress博客http://www.crazyant.net/ 的访问日志 

实现步骤：
1、读取数据、清理、格式化
2、统计爬虫spider的访问比例，输出柱状图
3、统计http状态码的访问占比，输出饼图
4、统计按小时、按天的PV/UV流量趋势，输出折线图 

### 1、读取数据并清理格式化

In [1]:

```python
import pandas as pd
import numpy as np

pd.set_option('display.max_colwidth', -1)

from pyecharts import options as opts
from pyecharts.charts import Bar,Pie,Line
```

In [2]:

```python
# 读取整个目录，将所有的文件合并到一个dataframe
data_dir = "./datas/crazyant/blog_access_log"

df_list = []

import os
for fname in os.listdir(f"{data_dir}"):
    df_list.append(pd.read_csv(f"{data_dir}/{fname}", sep=" ", header=None, error_bad_lines=False))

df = pd.concat(df_list)
b'Skipping line 2245: expected 10 fields, saw 16\nSkipping line 2889: expected 10 fields, saw 14\nSkipping line 2890: expected 10 fields, saw 14\nSkipping line 2891: expected 10 fields, saw 13\nSkipping line 2892: expected 10 fields, saw 13\nSkipping line 2900: expected 10 fields, saw 11\nSkipping line 2902: expected 10 fields, saw 11\nSkipping line 3790: expected 10 fields, saw 14\nSkipping line 3791: expected 10 fields, saw 14\nSkipping line 3792: expected 10 fields, saw 13\nSkipping line 3793: expected 10 fields, saw 13\nSkipping line 3833: expected 10 fields, saw 11\nSkipping line 3835: expected 10 fields, saw 11\nSkipping line 9936: expected 10 fields, saw 16\n'
b'Skipping line 11748: expected 10 fields, saw 11\nSkipping line 11750: expected 10 fields, saw 11\n'
```

In [3]:

```python
df.head()
```

Out[3]:

|      |              0 |    1 |    2 |                     3 |      4 |                                                            5 |    6 |     7 |                                                8 |                                                            9 |
| ---: | -------------: | ---: | ---: | --------------------: | -----: | -----------------------------------------------------------: | ---: | ----: | -----------------------------------------------: | -----------------------------------------------------------: |
|    0 | 106.11.153.226 |    - |    - | [02/Dec/2019:22:40:18 | +0800] |                       GET /740.html?replytocom=1194 HTTP/1.0 |  200 | 13446 |                                                - |                                                  YisouSpider |
|    1 |  42.156.254.60 |    - |    - | [02/Dec/2019:22:40:23 | +0800] | POST /wp-json/wordpress-popular-posts/v1/popular-posts HTTP/1.0 |  201 |    55 | http://www.crazyant.net/740.html?replytocom=1194 | Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.81 YisouSpider/5.0 Safari/537.36 |
|    2 | 106.11.159.254 |    - |    - | [02/Dec/2019:22:40:27 | +0800] |                                       GET /576.html HTTP/1.0 |  200 | 13461 |                                                - |                                                  YisouSpider |
|    3 | 106.11.157.254 |    - |    - | [02/Dec/2019:22:40:28 | +0800] | GET /?lwfcdw=t9n2d3&oqzohc=m5e7j1&oubyvq=iab6a3&oudmbg=6osqd3 HTTP/1.0 |  200 | 10485 |                                                - |                                                  YisouSpider |
|    4 | 42.156.137.109 |    - |    - | [02/Dec/2019:22:40:30 | +0800] | POST /wp-json/wordpress-popular-posts/v1/popular-posts HTTP/1.0 |  201 |    55 |                 http://www.crazyant.net/576.html | Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.81 YisouSpider/5.0 Safari/537.36 |

In [4]:

```python
df = df[[0, 3, 6, 9]].copy()
df.head()
```

Out[4]:

|      |              0 |                     3 |    6 |                                                            9 |
| ---: | -------------: | --------------------: | ---: | -----------------------------------------------------------: |
|    0 | 106.11.153.226 | [02/Dec/2019:22:40:18 |  200 |                                                  YisouSpider |
|    1 |  42.156.254.60 | [02/Dec/2019:22:40:23 |  201 | Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.81 YisouSpider/5.0 Safari/537.36 |
|    2 | 106.11.159.254 | [02/Dec/2019:22:40:27 |  200 |                                                  YisouSpider |
|    3 | 106.11.157.254 | [02/Dec/2019:22:40:28 |  200 |                                                  YisouSpider |
|    4 | 42.156.137.109 | [02/Dec/2019:22:40:30 |  201 | Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.81 YisouSpider/5.0 Safari/537.36 |

In [5]:

```python
df.columns = ["ip", "stime", "status", "client"]
df.head()
```

Out[5]:

|      |             ip |                 stime | status |                                                       client |
| ---: | -------------: | --------------------: | -----: | -----------------------------------------------------------: |
|    0 | 106.11.153.226 | [02/Dec/2019:22:40:18 |    200 |                                                  YisouSpider |
|    1 |  42.156.254.60 | [02/Dec/2019:22:40:23 |    201 | Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.81 YisouSpider/5.0 Safari/537.36 |
|    2 | 106.11.159.254 | [02/Dec/2019:22:40:27 |    200 |                                                  YisouSpider |
|    3 | 106.11.157.254 | [02/Dec/2019:22:40:28 |    200 |                                                  YisouSpider |
|    4 | 42.156.137.109 | [02/Dec/2019:22:40:30 |    201 | Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.81 YisouSpider/5.0 Safari/537.36 |

In [6]:

```python
df.dtypes
```

Out[6]:

```python
ip        object
stime     object
status    int64 
client    object
dtype: object
```

### 2、统计spider的比例

In [7]:

```python
df["is_spider"] = df["client"].str.lower().str.contains("spider")
df.head()
```

Out[7]:

|      |             ip |                 stime | status |                                                       client | is_spider |
| ---: | -------------: | --------------------: | -----: | -----------------------------------------------------------: | --------: |
|    0 | 106.11.153.226 | [02/Dec/2019:22:40:18 |    200 |                                                  YisouSpider |      True |
|    1 |  42.156.254.60 | [02/Dec/2019:22:40:23 |    201 | Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.81 YisouSpider/5.0 Safari/537.36 |      True |
|    2 | 106.11.159.254 | [02/Dec/2019:22:40:27 |    200 |                                                  YisouSpider |      True |
|    3 | 106.11.157.254 | [02/Dec/2019:22:40:28 |    200 |                                                  YisouSpider |      True |
|    4 | 42.156.137.109 | [02/Dec/2019:22:40:30 |    201 | Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.81 YisouSpider/5.0 Safari/537.36 |      True |

In [8]:

```python
df_spider = df["is_spider"].value_counts()
df_spider
```

Out[8]:

```python
False    46641
True     3637 
Name: is_spider, dtype: int64
```

In [9]:

```python
bar = (
        Bar()
        .add_xaxis([str(x) for x in df_spider.index])
        .add_yaxis("是否Spider", df_spider.values.tolist())
        .set_global_opts(title_opts=opts.TitleOpts(title="爬虫访问量占比"))
)
bar.render_notebook()
```

# 27、Pandas怎样找出最影响结果的那些特征？

应用场景： 

- 机器学习的特征选择，去除无用的特征，可以提升模型效果、降低训练时间等等 
- 数据分析领域，找出收入波动的最大因素！！

## 实例演示：泰坦尼克沉船事件中，最影响生死的因素有哪些？

### 1、导入相关的包

In [1]:

```python
import pandas as pd
import numpy as np

# 特征最影响结果的K个特征
from sklearn.feature_selection import SelectKBest

# 卡方检验，作为SelectKBest的参数
from sklearn.feature_selection import chi2
```

### 2、导入泰坦尼克号的数据

In [2]:

```python
df = pd.read_csv("./datas/titanic/titanic_train.csv")
df.head()
```

Out[2]:

|      | PassengerId | Survived | Pclass |                                              Name |    Sex |  Age | SibSp | Parch |           Ticket |    Fare | Cabin | Embarked |
| ---: | ----------: | -------: | -----: | ------------------------------------------------: | -----: | ---: | ----: | ----: | ---------------: | ------: | ----: | -------: |
|    0 |           1 |        0 |      3 |                           Braund, Mr. Owen Harris |   male | 22.0 |     1 |     0 |        A/5 21171 |  7.2500 |   NaN |        S |
|    1 |           2 |        1 |      1 | Cumings, Mrs. John Bradley (Florence Briggs Th... | female | 38.0 |     1 |     0 |         PC 17599 | 71.2833 |   C85 |        C |
|    2 |           3 |        1 |      3 |                            Heikkinen, Miss. Laina | female | 26.0 |     0 |     0 | STON/O2. 3101282 |  7.9250 |   NaN |        S |
|    3 |           4 |        1 |      1 |      Futrelle, Mrs. Jacques Heath (Lily May Peel) | female | 35.0 |     1 |     0 |           113803 | 53.1000 |  C123 |        S |
|    4 |           5 |        0 |      3 |                          Allen, Mr. William Henry |   male | 35.0 |     0 |     0 |           373450 |  8.0500 |   NaN |        S |

In [3]:

```python
df = df[["PassengerId", "Survived", "Pclass", "Sex", "Age", "SibSp", "Parch", "Fare", "Embarked"]].copy()
df.head()
```

Out[3]:

|      | PassengerId | Survived | Pclass |    Sex |  Age | SibSp | Parch |    Fare | Embarked |
| ---: | ----------: | -------: | -----: | -----: | ---: | ----: | ----: | ------: | -------: |
|    0 |           1 |        0 |      3 |   male | 22.0 |     1 |     0 |  7.2500 |        S |
|    1 |           2 |        1 |      1 | female | 38.0 |     1 |     0 | 71.2833 |        C |
|    2 |           3 |        1 |      3 | female | 26.0 |     0 |     0 |  7.9250 |        S |
|    3 |           4 |        1 |      1 | female | 35.0 |     1 |     0 | 53.1000 |        S |
|    4 |           5 |        0 |      3 |   male | 35.0 |     0 |     0 |  8.0500 |        S |

### 3、数据清理和转换

#### 3.1 查看是否有空值列

In [4]:

```python
df.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 891 entries, 0 to 890
Data columns (total 9 columns):
PassengerId    891 non-null int64
Survived       891 non-null int64
Pclass         891 non-null int64
Sex            891 non-null object
Age            714 non-null float64
SibSp          891 non-null int64
Parch          891 non-null int64
Fare           891 non-null float64
Embarked       889 non-null object
dtypes: float64(2), int64(5), object(2)
memory usage: 62.8+ KB
```

#### 3.2 给Age列填充平均值

In [5]:

```python
df["Age"] = df["Age"].fillna(df["Age"].median())
```

In [6]:

```python
df.head()
```

Out[6]:

|      | PassengerId | Survived | Pclass |    Sex |  Age | SibSp | Parch |    Fare | Embarked |
| ---: | ----------: | -------: | -----: | -----: | ---: | ----: | ----: | ------: | -------: |
|    0 |           1 |        0 |      3 |   male | 22.0 |     1 |     0 |  7.2500 |        S |
|    1 |           2 |        1 |      1 | female | 38.0 |     1 |     0 | 71.2833 |        C |
|    2 |           3 |        1 |      3 | female | 26.0 |     0 |     0 |  7.9250 |        S |
|    3 |           4 |        1 |      1 | female | 35.0 |     1 |     0 | 53.1000 |        S |
|    4 |           5 |        0 |      3 |   male | 35.0 |     0 |     0 |  8.0500 |        S |

#### 3.2 将性别列变成数字

In [7]:

```python
# 性别
df.Sex.unique()
```

Out[7]:

```python
array(['male', 'female'], dtype=object)
```

In [8]:

```python
df.loc[df["Sex"] == "male", "Sex"] = 0
df.loc[df["Sex"] == "female", "Sex"] = 1
```

In [9]:

```python
df.head()
```

Out[9]:

|      | PassengerId | Survived | Pclass |  Sex |  Age | SibSp | Parch |    Fare | Embarked |
| ---: | ----------: | -------: | -----: | ---: | ---: | ----: | ----: | ------: | -------: |
|    0 |           1 |        0 |      3 |    0 | 22.0 |     1 |     0 |  7.2500 |        S |
|    1 |           2 |        1 |      1 |    1 | 38.0 |     1 |     0 | 71.2833 |        C |
|    2 |           3 |        1 |      3 |    1 | 26.0 |     0 |     0 |  7.9250 |        S |
|    3 |           4 |        1 |      1 |    1 | 35.0 |     1 |     0 | 53.1000 |        S |
|    4 |           5 |        0 |      3 |    0 | 35.0 |     0 |     0 |  8.0500 |        S |

#### 3.3 给Embarked列填充空值，字符串转换成数字

In [10]:

```python
# Embarked
df.Embarked.unique()
```

Out[10]:

```python
array(['S', 'C', 'Q', nan], dtype=object)
```

In [11]:

```python
# 填充空值
df["Embarked"] = df["Embarked"].fillna(0)

# 字符串变成数字
df.loc[df["Embarked"] == "S", "Embarked"] = 1
df.loc[df["Embarked"] == "C", "Embarked"] = 2
df.loc[df["Embarked"] == "Q", "Embarked"] = 3
```

In [12]:

```python
df.head()
```

Out[12]:

|      | PassengerId | Survived | Pclass |  Sex |  Age | SibSp | Parch |    Fare | Embarked |
| ---: | ----------: | -------: | -----: | ---: | ---: | ----: | ----: | ------: | -------: |
|    0 |           1 |        0 |      3 |    0 | 22.0 |     1 |     0 |  7.2500 |        1 |
|    1 |           2 |        1 |      1 |    1 | 38.0 |     1 |     0 | 71.2833 |        2 |
|    2 |           3 |        1 |      3 |    1 | 26.0 |     0 |     0 |  7.9250 |        1 |
|    3 |           4 |        1 |      1 |    1 | 35.0 |     1 |     0 | 53.1000 |        1 |
|    4 |           5 |        0 |      3 |    0 | 35.0 |     0 |     0 |  8.0500 |        1 |

### 4、将特征列和结果列拆分开

In [13]:

```python
y = df.pop("Survived")
X = df
```

In [14]:

```python
X.head()
```

Out[14]:

|      | PassengerId | Pclass |  Sex |  Age | SibSp | Parch |    Fare | Embarked |
| ---: | ----------: | -----: | ---: | ---: | ----: | ----: | ------: | -------: |
|    0 |           1 |      3 |    0 | 22.0 |     1 |     0 |  7.2500 |        1 |
|    1 |           2 |      1 |    1 | 38.0 |     1 |     0 | 71.2833 |        2 |
|    2 |           3 |      3 |    1 | 26.0 |     0 |     0 |  7.9250 |        1 |
|    3 |           4 |      1 |    1 | 35.0 |     1 |     0 | 53.1000 |        1 |
|    4 |           5 |      3 |    0 | 35.0 |     0 |     0 |  8.0500 |        1 |

In [15]:

```python
y.head()
```

Out[15]:

```python
0    0
1    1
2    1
3    1
4    0
Name: Survived, dtype: int64
```

### 5、使用卡方检验选择topK的特征

In [16]:

```python
# 选择所有的特征，目的是看到特征重要性排序
bestfeatures = SelectKBest(score_func=chi2, k=len(X.columns))
fit = bestfeatures.fit(X, y)
```

### 6、按照重要性顺序打印特征列表

In [17]:

```python
df_scores = pd.DataFrame(fit.scores_)
df_scores
```

Out[17]:

|      |           0 |
| ---: | ----------: |
|    0 |    3.312934 |
|    1 |   30.873699 |
|    2 |  170.348127 |
|    3 |   21.649163 |
|    4 |    2.581865 |
|    5 |   10.097499 |
|    6 | 4518.319091 |
|    7 |    2.771019 |

In [18]:

```python
df_columns = pd.DataFrame(X.columns)
df_columns
```

Out[18]:

|      |           0 |
| ---: | ----------: |
|    0 | PassengerId |
|    1 |      Pclass |
|    2 |         Sex |
|    3 |         Age |
|    4 |       SibSp |
|    5 |       Parch |
|    6 |        Fare |
|    7 |    Embarked |

In [19]:

```python
# 合并两个df
df_feature_scores = pd.concat([df_columns,df_scores],axis=1)
# 列名
df_feature_scores.columns = ['feature_name','Score']  #naming the dataframe columns

# 查看
df_feature_scores
```

Out[19]:

|      | feature_name |       Score |
| ---: | -----------: | ----------: |
|    0 |  PassengerId |    3.312934 |
|    1 |       Pclass |   30.873699 |
|    2 |          Sex |  170.348127 |
|    3 |          Age |   21.649163 |
|    4 |        SibSp |    2.581865 |
|    5 |        Parch |   10.097499 |
|    6 |         Fare | 4518.319091 |
|    7 |     Embarked |    2.771019 |

In [20]:

```python
df_feature_scores.sort_values(by="Score", ascending=False)
```

Out[20]:

|      | feature_name |       Score |
| ---: | -----------: | ----------: |
|    6 |         Fare | 4518.319091 |
|    2 |          Sex |  170.348127 |
|    1 |       Pclass |   30.873699 |
|    3 |          Age |   21.649163 |
|    5 |        Parch |   10.097499 |
|    0 |  PassengerId |    3.312934 |
|    7 |     Embarked |    2.771019 |
|    4 |        SibSp |    2.581865 |