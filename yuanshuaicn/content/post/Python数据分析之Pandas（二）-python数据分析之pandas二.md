---
title: Python数据分析之Pandas（二）
date: 2021-post-26 21:53:10.984
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/v2-2103a9f9659baf7be26d40fe5b4e9f1f_1440w.jpg'
theme: 'light'
tags: 
- Python
- 数据分析
---

: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | ------: | -------: |
|    0 | 2018-01-01 |     3℃ |    -6℃ | 晴~多云 |    东北风 |  1-2级 |   59 |      良 |        2 |
|    1 | 2018-01-02 |     2℃ |    -5℃ | 阴~多云 |    东北风 |  1-2级 |   49 |      优 |        1 |
|    2 | 2018-01-03 |     2℃ |    -5℃ |    多云 |      北风 |  1-2级 |   28 |      优 |        1 |
|    3 | 2018-01-04 |     0℃ |    -8℃ |      阴 |    东北风 |  1-2级 |   28 |      优 |        1 |
|    4 | 2018-01-05 |     3℃ |    -6℃ | 多云~晴 |    西北风 |  1-2级 |   50 |      优 |        1 |

In [4]:

```python
df.dtypes
```

Out[4]:

```python
ymd          object
bWendu       object
yWendu       object
tianqi       object
fengxiang    object
fengli       object
aqi           int64
aqiInfo      object
aqiLevel      int64
dtype: object
```

### 1、获取Series的str属性，使用各种字符串处理函数

In [5]:

```python
df["bWendu"].str
```

Out[5]:

```python
<pandas.core.strings.StringMethods at 0x1af21871808>
```

In [6]:

```python
# 字符串替换函数
df["bWendu"].str.replace("℃", "")
```

Out[6]:

```python
0       3
1       2
2       2
3       0
4       3
       ..
360    -5
361    -3
362    -3
363    -2
364    -2
Name: bWendu, Length: 365, dtype: object
```

In [7]:

```python
# 判断是不是数字
df["bWendu"].str.isnumeric()
```

Out[7]:

```python
0      False
1      False
2      False
3      False
4      False
       ...  
360    False
361    False
362    False
363    False
364    False
Name: bWendu, Length: 365, dtype: bool
```

In [8]:

```python
df["aqi"].str.len()
---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
<ipython-input-8-12cdcbdb6f81> in <module>
----> 1 df["aqi"].str.len()

d:\appdata\python37\lib\site-packages\pandas\core\generic.py in __getattr__(self, name)
   5173             or name in self._accessors
   5174         ):
-> 5175             return object.__getattribute__(self, name)
   5176         else:
   5177             if self._info_axis._can_hold_identifiers_and_holds_name(name):

d:\appdata\python37\lib\site-packages\pandas\core\accessor.py in __get__(self, obj, cls)
    173             # we're accessing the attribute of the class, i.e., Dataset.geo
    174             return self._accessor
--> 175         accessor_obj = self._accessor(obj)
    176         # Replace the property with the accessor object. Inspired by:
    177         # http://www.pydanny.com/cached-property.html

d:\appdata\python37\lib\site-packages\pandas\core\strings.py in __init__(self, data)
   1915 
   1916     def __init__(self, data):
-> 1917         self._inferred_dtype = self._validate(data)
   1918         self._is_categorical = is_categorical_dtype(data)
   1919 

d:\appdata\python37\lib\site-packages\pandas\core\strings.py in _validate(data)
   1965 
   1966         if inferred_dtype not in allowed_types:
-> 1967             raise AttributeError("Can only use .str accessor with string " "values!")
   1968         return inferred_dtype
   1969 

AttributeError: Can only use .str accessor with string values!
```

### 2、使用str的startswith、contains等得到bool的Series可以做条件查询

In [9]:

```python
condition = df["ymd"].str.startswith("2018-03")
```

In [10]:

```python
condition
```

Out[10]:

```python
0      False
1      False
2      False
3      False
4      False
       ...  
360    False
361    False
362    False
363    False
364    False
Name: ymd, Length: 365, dtype: bool
```

In [11]:

```python
df[condition].head()
```

Out[11]:

|      |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi |  aqiInfo | aqiLevel |
| ---: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | -------: | -------: |
|   59 | 2018-03-01 |     8℃ |    -3℃ |    多云 |    西南风 |  1-2级 |   46 |       优 |        1 |
|   60 | 2018-03-02 |     9℃ |    -1℃ | 晴~多云 |      北风 |  1-2级 |   95 |       良 |        2 |
|   61 | 2018-03-03 |    13℃ |     3℃ | 多云~阴 |      北风 |  1-2级 |  214 | 重度污染 |        5 |
|   62 | 2018-03-04 |     7℃ |    -2℃ | 阴~多云 |    东南风 |  1-2级 |  144 | 轻度污染 |        3 |
|   63 | 2018-03-05 |     8℃ |    -3℃ |      晴 |      南风 |  1-2级 |   94 |       良 |        2 |

### 3、需要多次str处理的链式操作

怎样提取201803这样的数字月份？
1、先将日期2018-03-31替换成20180331的形式
2、提取月份字符串201803 

In [12]:

```python
df["ymd"].str.replace("-", "")
```

Out[12]:

```python
0      20180101
1      20180102
2      20180103
3      20180104
4      20180105
         ...   
360    20181227
361    20181228
362    20181229
363    20181230
364    20181231
Name: ymd, Length: 365, dtype: object
```

In [13]:

```python
# 每次调用函数，都返回一个新Series
df["ymd"].str.replace("-", "").slice(0, 6)
---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
<ipython-input-13-ae278fb12255> in <module>
      1 # 每次调用函数，都返回一个新Series
----> 2 df["ymd"].str.replace("-", "").slice(0, 6)

d:\appdata\python37\lib\site-packages\pandas\core\generic.py in __getattr__(self, name)
   5177             if self._info_axis._can_hold_identifiers_and_holds_name(name):
   5178                 return self[name]
-> 5179             return object.__getattribute__(self, name)
   5180 
   5181     def __setattr__(self, name, value):

AttributeError: 'Series' object has no attribute 'slice'
```

In [14]:

```python
df["ymd"].str.replace("-", "").str.slice(0, 6)
```

Out[14]:

```python
0      201801
1      201801
2      201801
3      201801
4      201801
        ...  
360    201812
361    201812
362    201812
363    201812
364    201812
Name: ymd, Length: 365, dtype: object
```

In [15]:

```python
# slice就是切片语法，可以直接用
df["ymd"].str.replace("-", "").str[0:6]
```

Out[15]:

```python
0      201801
1      201801
2      201801
3      201801
4      201801
        ...  
360    201812
361    201812
362    201812
363    201812
364    201812
Name: ymd, Length: 365, dtype: object
```

### 4. 使用正则表达式的处理

In [16]:

```python
# 添加新列
def get_nianyueri(x):
    year,month,day = x["ymd"].split("-")
    return f"{year}年{month}月{day}日"
df["中文日期"] = df.apply(get_nianyueri, axis=1)
```

In [17]:

```python
df["中文日期"]
```

Out[17]:

```python
0      2018年01月01日
1      2018年01月02日
2      2018年01月03日
3      2018年01月04日
4      2018年01月05日
          ...     
360    2018年12月27日
361    2018年12月28日
362    2018年12月29日
363    2018年12月30日
364    2018年12月31日
Name: 中文日期, Length: 365, dtype: object
```

问题：怎样将“2018年12月31日”中的年、月、日三个中文字符去除？

In [18]:

```python
# 方法1：链式replace
df["中文日期"].str.replace("年", "").str.replace("月","").str.replace("日", "")
```

Out[18]:

```python
0      20180101
1      20180102
2      20180103
3      20180104
4      20180105
         ...   
360    20181227
361    20181228
362    20181229
363    20181230
364    20181231
Name: 中文日期, Length: 365, dtype: object
```

***Series.str默认就开启了正则表达式模式\***

In [19]:

```python
# 方法2：正则表达式替换
df["中文日期"].str.replace("[年月日]", "")
```

Out[19]:

```python
0      20180101
1      20180102
2      20180103
3      20180104
4      20180105
         ...   
360    20181227
361    20181228
362    20181229
363    20181230
364    20181231
Name: 中文日期, Length: 365, dtype: object
```

## 11、Pandas的axis参数怎么理解？

- axis=0或者"index"： 

  - 如果是单行操作，就指的是某一行
  - 如果是聚合操作，指的是跨行cross rows

- axis=1或者"columns"：

  - 如果是单列操作，就指的是某一列

  - 如果是聚合操作，指的是跨列cross columns

    ***按哪个axis，就是这个axis要动起来(类似被for遍历)，其它的axis保持不动\***

In [1]:

```python
import pandas as pd
import numpy as np
```

In [2]:

```python
df = pd.DataFrame(
    np.arange(12).reshape(3,4),
    columns=['A', 'B', 'C', 'D']
)
```

In [3]:

```
df
```

Out[3]:

|      |    A |    B |    C |    D |
| ---: | ---: | ---: | ---: | ---: |
|    0 |    0 |    1 |    2 |    3 |
|    1 |    4 |    5 |    6 |    7 |
|    2 |    8 |    9 |   10 |   11 |

### 1、单列drop，就是删除某一列

In [4]:

```python
# 代表的就是删除某列
df.drop("A", axis=1)
```

Out[4]:

|      |    B |    C |    D |
| ---: | ---: | ---: | ---: |
|    0 |    1 |    2 |    3 |
|    1 |    5 |    6 |    7 |
|    2 |    9 |   10 |   11 |

### 2、单行drop，就是删除某一行

In [5]:

```python
df
```

Out[5]:

|      |    A |    B |    C |    D |
| ---: | ---: | ---: | ---: | ---: |
|    0 |    0 |    1 |    2 |    3 |
|    1 |    4 |    5 |    6 |    7 |
|    2 |    8 |    9 |   10 |   11 |

In [6]:

```python
# 代表的就是删除某行
df.drop(1, axis=0)
```

Out[6]:

|      |    A |    B |    C |    D |
| ---: | ---: | ---: | ---: | ---: |
|    0 |    0 |    1 |    2 |    3 |
|    2 |    8 |    9 |   10 |   11 |

### 3、按axis=0/index执行mean聚合操作

反直觉：输出的不是每行的结果，而是每列的结果

In [7]:

```python
df
```

Out[7]:

|      |    A |    B |    C |    D |
| ---: | ---: | ---: | ---: | ---: |
|    0 |    0 |    1 |    2 |    3 |
|    1 |    4 |    5 |    6 |    7 |
|    2 |    8 |    9 |   10 |   11 |

In [8]:

```python
# axis=0 or axis=index
df.mean(axis=0)
```

Out[8]:

```python
A    4.0
B    5.0
C    6.0
D    7.0
dtype: float64
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_pandas/1.png)

***指定了按哪个axis，就是这个axis要动起来(类似被for遍历)，其它的axis保持不动\***

### 4、按axis=1/columns执行mean聚合操作

反直觉：输出的不是每行的结果，而是每列的结果

In [9]:

```python
df
```

Out[9]:

|      |    A |    B |    C |    D |
| ---: | ---: | ---: | ---: | ---: |
|    0 |    0 |    1 |    2 |    3 |
|    1 |    4 |    5 |    6 |    7 |
|    2 |    8 |    9 |   10 |   11 |

In [10]:

```python
# axis=1 or axis=columns
df.mean(axis=1)
```

Out[10]:

```python
0    1.5
1    5.5
2    9.5
dtype: float64
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/python_pandas/2.png)

***指定了按哪个axis，就是这个axis要动起来(类似被for遍历)，其它的axis保持不动\***

### 5、再次举例，加深理解

In [11]:

```python
def get_sum_value(x):
    return x["A"] + x["B"] + x["C"] + x["D"]

df["sum_value"] = df.apply(get_sum_value, axis=1)
```

In [12]:

```
df
```

Out[12]:

|      |    A |    B |    C |    D | sum_value |
| ---: | ---: | ---: | ---: | ---: | --------: |
|    0 |    0 |    1 |    2 |    3 |         6 |
|    1 |    4 |    5 |    6 |    7 |        22 |
|    2 |    8 |    9 |   10 |   11 |        38 |

***指定了按哪个axis，就是这个axis要动起来(类似被for遍历)，其它的axis保持不动\***

## 12、Pandas的索引index的用途

把数据存储于普通的column列也能用于数据查询，那使用index有什么好处？

index的用途总结： 

1. 更方便的数据查询；
2. 使用index可以获得性能提升；
3. 自动的数据对齐功能；
4. 更多更强大的数据结构支持；

In [1]:

```python
import pandas as pd
```

In [2]:

```python
df = pd.read_csv("./datas/ml-latest-small/ratings.csv")
```

In [3]:

```python
df.head()
```

Out[3]:

|      | userId | movieId | rating | timestamp |
| ---: | -----: | ------: | -----: | --------: |
|    0 |      1 |       1 |    4.0 | 964982703 |
|    1 |      1 |       3 |    4.0 | 964981247 |
|    2 |      1 |       6 |    4.0 | 964982224 |
|    3 |      1 |      47 |    5.0 | 964983815 |
|    4 |      1 |      50 |    5.0 | 964982931 |

In [4]:

```python
df.count()
```

Out[4]:

```python
userId       100836
movieId      100836
rating       100836
timestamp    100836
dtype: int64
```

## 1、使用index查询数据

In [5]:

```python
# drop==False，让索引列还保持在column
df.set_index("userId", inplace=True, drop=False)
```

In [6]:

```python
df.head()
```

Out[6]:

|        | userId | movieId | rating | timestamp |
| -----: | -----: | ------: | -----: | --------: |
| userId |        |         |        |           |
|      1 |      1 |       1 |    4.0 | 964982703 |
|      1 |      1 |       3 |    4.0 | 964981247 |
|      1 |      1 |       6 |    4.0 | 964982224 |
|      1 |      1 |      47 |    5.0 | 964983815 |
|      1 |      1 |      50 |    5.0 | 964982931 |

In [7]:

```python
df.index
```

Out[7]:

```python
Int64Index([  1,   1,   1,   1,   1,   1,   1,   1,   1,   1,
            ...
            610, 610, 610, 610, 610, 610, 610, 610, 610, 610],
           dtype='int64', name='userId', length=100836)
```

In [9]:

```python
# 使用index的查询方法
df.loc[500].head(5)
```

Out[9]:

|        | userId | movieId | rating |  timestamp |
| -----: | -----: | ------: | -----: | ---------: |
| userId |        |         |        |            |
|    500 |    500 |       1 |    4.0 | 1005527755 |
|    500 |    500 |      11 |    1.0 | 1005528017 |
|    500 |    500 |      39 |    1.0 | 1005527926 |
|    500 |    500 |     101 |    1.0 | 1005527980 |
|    500 |    500 |     104 |    4.0 | 1005528065 |

In [8]:

```python
# 使用column的condition查询方法
df.loc[df["userId"] == 500].head()
```

Out[8]:

|        | userId | movieId | rating |  timestamp |
| -----: | -----: | ------: | -----: | ---------: |
| userId |        |         |        |            |
|    500 |    500 |       1 |    4.0 | 1005527755 |
|    500 |    500 |      11 |    1.0 | 1005528017 |
|    500 |    500 |      39 |    1.0 | 1005527926 |
|    500 |    500 |     101 |    1.0 | 1005527980 |
|    500 |    500 |     104 |    4.0 | 1005528065 |

## 2. 使用index会提升查询性能

- 如果index是唯一的，Pandas会使用哈希表优化，查询性能为O(1);
- 如果index不是唯一的，但是有序，Pandas会使用二分查找算法，查询性能为O(logN);
- 如果index是完全随机的，那么每次查询都要扫描全表，查询性能为O(N);

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/python_pandas/3.png)

### 实验1：完全随机的顺序查询

In [10]:

```python
# 将数据随机打散
from sklearn.utils import shuffle
df_shuffle = shuffle(df)
```

In [11]:

```python
df_shuffle.head()
```

Out[11]:

|        | userId | movieId | rating |  timestamp |
| -----: | -----: | ------: | -----: | ---------: |
| userId |        |         |        |            |
|    160 |    160 |    2340 |    1.0 |  985383314 |
|    129 |    129 |    1136 |    3.5 | 1167375403 |
|    167 |    167 |   44191 |    4.5 | 1154718915 |
|    536 |    536 |     276 |    3.0 |  832839990 |
|     67 |     67 |    5952 |    2.0 | 1501274082 |

In [12]:

```python
# 索引是否是递增的
df_shuffle.index.is_monotonic_increasing
```

Out[12]:

```python
False
```

In [13]:

```python
df_shuffle.index.is_unique
```

Out[13]:

```python
False
```

In [14]:

```python
# 计时，查询id==500数据性能
%timeit df_shuffle.loc[500]
376 µs ± 52.4 µs per loop (mean ± std. dev. of 7 runs, 1000 loops each)
```

### 实验2：将index排序后的查询

In [15]:

```python
df_sorted = df_shuffle.sort_index()
```

In [16]:

```python
df_sorted.head()
```

Out[16]:

|        | userId | movieId | rating | timestamp |
| -----: | -----: | ------: | -----: | --------: |
| userId |        |         |        |           |
|      1 |      1 |    2985 |    4.0 | 964983034 |
|      1 |      1 |    2617 |    2.0 | 964982588 |
|      1 |      1 |    3639 |    4.0 | 964982271 |
|      1 |      1 |       6 |    4.0 | 964982224 |
|      1 |      1 |     733 |    4.0 | 964982400 |

In [17]:

```python
# 索引是否是递增的
df_sorted.index.is_monotonic_increasing
```

Out[17]:

```python
True
```

In [18]:

```python
df_sorted.index.is_unique
```

Out[18]:

```python
False
```

In [19]:

```python
%timeit df_sorted.loc[500]
203 µs ± 20.8 µs per loop (mean ± std. dev. of 7 runs, 1000 loops each)
```

## 3. 使用index能自动对齐数据

包括series和dataframe

In [20]:

```python
s1 = pd.Series([1,2,3], index=list("abc"))
```

In [21]:

```python
s1
```

Out[21]:

```python
a    1
b    2
c    3
dtype: int64
```

In [22]:

```python
s2 = pd.Series([2,3,4], index=list("bcd"))
```

In [23]:

```python
s2
```

Out[23]:

```python
b    2
c    3
d    4
dtype: int64
```

In [24]:

```python
s1+s2
```

Out[24]:

```python
a    NaN
b    4.0
c    6.0
d    NaN
dtype: float64
```

## 4. 使用index更多更强大的数据结构支持

***很多强大的索引数据结构\***

- CategoricalIndex，基于分类数据的Index，提升性能；
- MultiIndex，多维索引，用于groupby多维聚合后结果等；
- DatetimeIndex，时间类型索引，强大的日期和时间的方法支持；

# 13、Pandas怎样实现DataFrame的Merge

Pandas的Merge，相当于Sql的Join，将不同的表按key关联到一个表

### merge的语法：

pd.merge(left, right, how='inner', on=None, left_on=None, right_on=None, left_index=False, right_index=False, sort=True, suffixes=('_x', '_y'), copy=True, indicator=False, validate=None) 

- left，right：要merge的dataframe或者有name的Series
- how：join类型，'left', 'right', 'outer', 'inner'
- on：join的key，left和right都需要有这个key
- left_on：left的df或者series的key
- right_on：right的df或者seires的key
- left_index，right_index：使用index而不是普通的column做join
- suffixes：两个元素的后缀，如果列有重名，自动添加后缀，默认是('_x', '_y')

文档地址：https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.merge.html

本次讲解提纲：

1. 电影数据集的join实例
2. 理解merge时一对一、一对多、多对多的数量对齐关系
3. 理解left join、right join、inner join、outer join的区别
4. 如果出现非Key的字段重名怎么办

### 1、电影数据集的join实例

#### 电影评分数据集

是推荐系统研究的很好的数据集
位于本代码目录：./datas/movielens-1m

包含三个文件： 

1. 用户对电影的评分数据 ratings.dat
2. 用户本身的信息数据 users.dat
3. 电影本身的数据 movies.dat

可以关联三个表，得到一个完整的大表

数据集官方地址：https://grouplens.org/datasets/movielens/

In [1]:

```python
import pandas as pd
```

In [2]:

```python
df_ratings = pd.read_csv(
    "./datas/movielens-1m/ratings.dat", 
    sep="::",
    engine='python', 
    names="UserID::MovieID::Rating::Timestamp".split("::")
)
```

In [3]:

```python
df_ratings.head()
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
df_users = pd.read_csv(
    "./datas/movielens-1m/users.dat", 
    sep="::",
    engine='python', 
    names="UserID::Gender::Age::Occupation::Zip-code".split("::")
)
```

In [5]:

```python
df_users.head()
```

Out[5]:

|      | UserID | Gender |  Age | Occupation | Zip-code |
| ---: | -----: | -----: | ---: | ---------: | -------: |
|    0 |      1 |      F |    1 |         10 |    48067 |
|    1 |      2 |      M |   56 |         16 |    70072 |
|    2 |      3 |      M |   25 |         15 |    55117 |
|    3 |      4 |      M |   45 |          7 |    02460 |
|    4 |      5 |      M |   25 |         20 |    55455 |

In [6]:

```python
df_movies = pd.read_csv(
    "./datas/movielens-1m/movies.dat", 
    sep="::",
    engine='python', 
    names="MovieID::Title::Genres".split("::")
)
```

In [7]:

```python
df_movies.head()
```

Out[7]:

|      | MovieID |                              Title |                         Genres |
| ---: | ------: | ---------------------------------: | -----------------------------: |
|    0 |       1 |                   Toy Story (1995) |  Animation\|Children's\|Comedy |
|    1 |       2 |                     Jumanji (1995) | Adventure\|Children's\|Fantasy |
|    2 |       3 |            Grumpier Old Men (1995) |                Comedy\|Romance |
|    3 |       4 |           Waiting to Exhale (1995) |                  Comedy\|Drama |
|    4 |       5 | Father of the Bride Part II (1995) |                         Comedy |

In [8]:

```python
df_ratings_users = pd.merge(
   df_ratings, df_users, left_on="UserID", right_on="UserID", how="inner"
)
```

In [9]:

```python
df_ratings_users.head()
```

Out[9]:

|      | UserID | MovieID | Rating | Timestamp | Gender |  Age | Occupation | Zip-code |
| ---: | -----: | ------: | -----: | --------: | -----: | ---: | ---------: | -------: |
|    0 |      1 |    1193 |      5 | 978300760 |      F |    1 |         10 |    48067 |
|    1 |      1 |     661 |      3 | 978302109 |      F |    1 |         10 |    48067 |
|    2 |      1 |     914 |      3 | 978301968 |      F |    1 |         10 |    48067 |
|    3 |      1 |    3408 |      4 | 978300275 |      F |    1 |         10 |    48067 |
|    4 |      1 |    2355 |      5 | 978824291 |      F |    1 |         10 |    48067 |

In [10]:

```python
df_ratings_users_movies = pd.merge(
    df_ratings_users, df_movies, left_on="MovieID", right_on="MovieID", how="inner"
)
```

In [11]:

```python
df_ratings_users_movies.head(10)
```

Out[11]:

|      | UserID | MovieID | Rating | Timestamp | Gender |  Age | Occupation | Zip-code |                                  Title | Genres |
| ---: | -----: | ------: | -----: | --------: | -----: | ---: | ---------: | -------: | -------------------------------------: | -----: |
|    0 |      1 |    1193 |      5 | 978300760 |      F |    1 |         10 |    48067 | One Flew Over the Cuckoo's Nest (1975) |  Drama |
|    1 |      2 |    1193 |      5 | 978298413 |      M |   56 |         16 |    70072 | One Flew Over the Cuckoo's Nest (1975) |  Drama |
|    2 |     12 |    1193 |      4 | 978220179 |      M |   25 |         12 |    32793 | One Flew Over the Cuckoo's Nest (1975) |  Drama |
|    3 |     15 |    1193 |      4 | 978199279 |      M |   25 |          7 |    22903 | One Flew Over the Cuckoo's Nest (1975) |  Drama |
|    4 |     17 |    1193 |      5 | 978158471 |      M |   50 |          1 |    95350 | One Flew Over the Cuckoo's Nest (1975) |  Drama |
|    5 |     18 |    1193 |      4 | 978156168 |      F |   18 |          3 |    95825 | One Flew Over the Cuckoo's Nest (1975) |  Drama |
|    6 |     19 |    1193 |      5 | 982730936 |      M |    1 |         10 |    48073 | One Flew Over the Cuckoo's Nest (1975) |  Drama |
|    7 |     24 |    1193 |      5 | 978136709 |      F |   25 |          7 |    10023 | One Flew Over the Cuckoo's Nest (1975) |  Drama |
|    8 |     28 |    1193 |      3 | 978125194 |      F |   25 |          1 |    14607 | One Flew Over the Cuckoo's Nest (1975) |  Drama |
|    9 |     33 |    1193 |      5 | 978557765 |      M |   45 |          3 |    55421 | One Flew Over the Cuckoo's Nest (1975) |  Drama |

### 2、理解merge时数量的对齐关系

以下关系要正确理解：

- one-to-one：一对一关系，关联的key都是唯一的
  - 比如(学号，姓名) merge (学号，年龄)
  - 结果条数为：1*1
- one-to-many：一对多关系，左边唯一key，右边不唯一key
  - 比如(学号，姓名) merge (学号，[语文成绩、数学成绩、英语成绩])
  - 结果条数为：1*N
- many-to-many：多对多关系，左边右边都不是唯一的
  - 比如（学号，[语文成绩、数学成绩、英语成绩]） merge (学号，[篮球、足球、乒乓球])
  - 结果条数为：M*N

#### 2.1 one-to-one 一对一关系的merge

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/python_pandas/4.png)

In[12]:

```python
left = pd.DataFrame({'sno': [11, 12, 13, 14],
                      'name': ['name_a', 'name_b', 'name_c', 'name_d']
                    })
left
```

Out[12]:

|      |  sno |   name |
| ---: | ---: | -----: |
|    0 |   11 | name_a |
|    1 |   12 | name_b |
|    2 |   13 | name_c |
|    3 |   14 | name_d |

In [13]:

```python
right = pd.DataFrame({'sno': [11, 12, 13, 14],
                      'age': ['21', '22', '23', '24']
                    })
right
```

Out[13]:

|      |  sno |  age |
| ---: | ---: | ---: |
|    0 |   11 |   21 |
|    1 |   12 |   22 |
|    2 |   13 |   23 |
|    3 |   14 |   24 |

In [14]:

```python
# 一对一关系，结果中有4条
pd.merge(left, right, on='sno')
```

Out[14]:

|      |  sno |   name |  age |
| ---: | ---: | -----: | ---: |
|    0 |   11 | name_a |   21 |
|    1 |   12 | name_b |   22 |
|    2 |   13 | name_c |   23 |
|    3 |   14 | name_d |   24 |

#### 2.2 one-to-many 一对多关系的merge

注意：数据会被复制

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/python_pandas/5.png)

In[15]:

```python
left = pd.DataFrame({'sno': [11, 12, 13, 14],
                      'name': ['name_a', 'name_b', 'name_c', 'name_d']
                    })
left
```

Out[15]:

|      |  sno |   name |
| ---: | ---: | -----: |
|    0 |   11 | name_a |
|    1 |   12 | name_b |
|    2 |   13 | name_c |
|    3 |   14 | name_d |

In [16]:

```python
right = pd.DataFrame({'sno': [11, 11, 11, 12, 12, 13],
                       'grade': ['语文88', '数学90', '英语75','语文66', '数学55', '英语29']
                     })
right
```

Out[16]:

|      |  sno |  grade |
| ---: | ---: | -----: |
|    0 |   11 | 语文88 |
|    1 |   11 | 数学90 |
|    2 |   11 | 英语75 |
|    3 |   12 | 语文66 |
|    4 |   12 | 数学55 |
|    5 |   13 | 英语29 |

In [17]:

```python
# 数目以多的一边为准
pd.merge(left, right, on='sno')
```

Out[17]:

|      |  sno |   name |  grade |
| ---: | ---: | -----: | -----: |
|    0 |   11 | name_a | 语文88 |
|    1 |   11 | name_a | 数学90 |
|    2 |   11 | name_a | 英语75 |
|    3 |   12 | name_b | 语文66 |
|    4 |   12 | name_b | 数学55 |
|    5 |   13 | name_c | 英语29 |

#### 2.3 many-to-many 多对多关系的merge

注意：结果数量会出现乘法

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/python_pandas/6.png)

In [18]:

```python
left = pd.DataFrame({'sno': [11, 11, 12, 12,12],
                      '爱好': ['篮球', '羽毛球', '乒乓球', '篮球', "足球"]
                    })
left
```

Out[18]:

|      |  sno |   爱好 |
| ---: | ---: | -----: |
|    0 |   11 |   篮球 |
|    1 |   11 | 羽毛球 |
|    2 |   12 | 乒乓球 |
|    3 |   12 |   篮球 |
|    4 |   12 |   足球 |

In [19]:

```python
right = pd.DataFrame({'sno': [11, 11, 11, 12, 12, 13],
                       'grade': ['语文88', '数学90', '英语75','语文66', '数学55', '英语29']
                     })
right
```

Out[19]:

|      |  sno |  grade |
| ---: | ---: | -----: |
|    0 |   11 | 语文88 |
|    1 |   11 | 数学90 |
|    2 |   11 | 英语75 |
|    3 |   12 | 语文66 |
|    4 |   12 | 数学55 |
|    5 |   13 | 英语29 |

In [20]:

```python
pd.merge(left, right, on='sno')
```

Out[20]:

|      |  sno |   爱好 |  grade |
| ---: | ---: | -----: | -----: |
|    0 |   11 |   篮球 | 语文88 |
|    1 |   11 |   篮球 | 数学90 |
|    2 |   11 |   篮球 | 英语75 |
|    3 |   11 | 羽毛球 | 语文88 |
|    4 |   11 | 羽毛球 | 数学90 |
|    5 |   11 | 羽毛球 | 英语75 |
|    6 |   12 | 乒乓球 | 语文66 |
|    7 |   12 | 乒乓球 | 数学55 |
|    8 |   12 |   篮球 | 语文66 |
|    9 |   12 |   篮球 | 数学55 |
|   10 |   12 |   足球 | 语文66 |
|   11 |   12 |   足球 | 数学55 |

### 3、理解left join、right join、inner join、outer join的区别

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/python_pandas/7.png)

In [21]:

```python
left = pd.DataFrame({'key': ['K0', 'K1', 'K2', 'K3'],
                      'A': ['A0', 'A1', 'A2', 'A3'],
                      'B': ['B0', 'B1', 'B2', 'B3']})

right = pd.DataFrame({'key': ['K0', 'K1', 'K4', 'K5'],
                      'C': ['C0', 'C1', 'C4', 'C5'],
                      'D': ['D0', 'D1', 'D4', 'D5']})
```

In [22]:

```
left
```

Out[22]:

|      |  key |    A |    B |
| ---: | ---: | ---: | ---: |
|    0 |   K0 |   A0 |   B0 |
|    1 |   K1 |   A1 |   B1 |
|    2 |   K2 |   A2 |   B2 |
|    3 |   K3 |   A3 |   B3 |

In [23]:

```python
right
```

Out[23]:

|      |  key |    C |    D |
| ---: | ---: | ---: | ---: |
|    0 |   K0 |   C0 |   D0 |
|    1 |   K1 |   C1 |   D1 |
|    2 |   K4 |   C4 |   D4 |
|    3 |   K5 |   C5 |   D5 |

#### 3.1 inner join，默认

左边和右边的key都有，才会出现在结果里

In [24]:

```python
pd.merge(left, right, how='inner')
```

Out[24]:

|      |  key |    A |    B |    C |    D |
| ---: | ---: | ---: | ---: | ---: | ---: |
|    0 |   K0 |   A0 |   B0 |   C0 |   D0 |
|    1 |   K1 |   A1 |   B1 |   C1 |   D1 |

#### 3.2 left join

左边的都会出现在结果里，右边的如果无法匹配则为Null

In [25]:

```python
pd.merge(left, right, how='left')
```

Out[25]:

|      |  key |    A |    B |    C |    D |
| ---: | ---: | ---: | ---: | ---: | ---: |
|    0 |   K0 |   A0 |   B0 |   C0 |   D0 |
|    1 |   K1 |   A1 |   B1 |   C1 |   D1 |
|    2 |   K2 |   A2 |   B2 |  NaN |  NaN |
|    3 |   K3 |   A3 |   B3 |  NaN |  NaN |

#### 3.3 right join

右边的都会出现在结果里，左边的如果无法匹配则为Null

In [26]:

```python
pd.merge(left, right, how='right')
```

Out[26]:

|      |  key |    A |    B |    C |    D |
| ---: | ---: | ---: | ---: | ---: | ---: |
|    0 |   K0 |   A0 |   B0 |   C0 |   D0 |
|    1 |   K1 |   A1 |   B1 |   C1 |   D1 |
|    2 |   K4 |  NaN |  NaN |   C4 |   D4 |
|    3 |   K5 |  NaN |  NaN |   C5 |   D5 |

#### 3.4 outer join

左边、右边的都会出现在结果里，如果无法匹配则为Null

In [27]:

```python
pd.merge(left, right, how='outer')
```

Out[27]:

|      |  key |    A |    B |    C |    D |
| ---: | ---: | ---: | ---: | ---: | ---: |
|    0 |   K0 |   A0 |   B0 |   C0 |   D0 |
|    1 |   K1 |   A1 |   B1 |   C1 |   D1 |
|    2 |   K2 |   A2 |   B2 |  NaN |  NaN |
|    3 |   K3 |   A3 |   B3 |  NaN |  NaN |
|    4 |   K4 |  NaN |  NaN |   C4 |   D4 |
|    5 |   K5 |  NaN |  NaN |   C5 |   D5 |

### 4、如果出现非Key的字段重名怎么办

In [28]:

```python
left = pd.DataFrame({'key': ['K0', 'K1', 'K2', 'K3'],
                      'A': ['A0', 'A1', 'A2', 'A3'],
                      'B': ['B0', 'B1', 'B2', 'B3']})

right = pd.DataFrame({'key': ['K0', 'K1', 'K4', 'K5'],
                      'A': ['A10', 'A11', 'A12', 'A13'],
                      'D': ['D0', 'D1', 'D4', 'D5']})
```

In [29]:

```python
left
```

Out[29]:

|      |  key |    A |    B |
| ---: | ---: | ---: | ---: |
|    0 |   K0 |   A0 |   B0 |
|    1 |   K1 |   A1 |   B1 |
|    2 |   K2 |   A2 |   B2 |
|    3 |   K3 |   A3 |   B3 |

In [30]:

```python
right
```

Out[30]:

|      |  key |    A |    D |
| ---: | ---: | ---: | ---: |
|    0 |   K0 |  A10 |   D0 |
|    1 |   K1 |  A11 |   D1 |
|    2 |   K4 |  A12 |   D4 |
|    3 |   K5 |  A13 |   D5 |

In [31]:

```python
pd.merge(left, right, on='key')
```

Out[31]:

|      |  key |  A_x |    B |  A_y |    D |
| ---: | ---: | ---: | ---: | ---: | ---: |
|    0 |   K0 |   A0 |   B0 |  A10 |   D0 |
|    1 |   K1 |   A1 |   B1 |  A11 |   D1 |

In [32]:

```python
pd.merge(left, right, on='key', suffixes=('_left', '_right'))
```

Out[32]:

|      |  key | A_left |    B | A_right |    D |
| ---: | ---: | -----: | ---: | ------: | ---: |
|    0 |   K0 |     A0 |   B0 |     A10 |   D0 |
|    1 |   K1 |     A1 |   B1 |     A11 |   D1 |

# 14、Pandas实现数据的合并concat

#### 使用场景：

批量合并相同格式的Excel、给DataFrame添加行、给DataFrame添加列

#### 一句话说明concat语法：

- 使用某种合并方式(inner/outer)
- 沿着某个轴向(axis=0/1)
- 把多个Pandas对象(DataFrame/Series)合并成一个。

#### concat语法：pandas.concat(objs, axis=0, join='outer', ignore_index=False)

- objs：一个列表，内容可以是DataFrame或者Series，可以混合
- axis：默认是0代表按行合并，如果等于1代表按列合并
- join：合并的时候索引的对齐方式，默认是outer join，也可以是inner join
- ignore_index：是否忽略掉原来的数据索引

#### append语法：DataFrame.append(other, ignore_index=False)

append只有按行合并，没有按列合并，相当于concat按行的简写形式 

- other：单个dataframe、series、dict，或者列表
- ignore_index：是否忽略掉原来的数据索引

#### 参考文档：

- pandas.concat的api文档：https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.concat.html
- pandas.concat的教程：https://pandas.pydata.org/pandas-docs/stable/user_guide/merging.html
- pandas.append的api文档：https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.append.html

In [1]:

```python
import pandas as pd

import warnings
warnings.filterwarnings('ignore')
```

### 一、使用pandas.concat合并数据

In [2]:

```python
df1 = pd.DataFrame({'A': ['A0', 'A1', 'A2', 'A3'],
                    'B': ['B0', 'B1', 'B2', 'B3'],
                    'C': ['C0', 'C1', 'C2', 'C3'],
                    'D': ['D0', 'D1', 'D2', 'D3'],
                    'E': ['E0', 'E1', 'E2', 'E3']
                   })
df1
```

Out[2]:

|      |    A |    B |    C |    D |    E |
| ---: | ---: | ---: | ---: | ---: | ---: |
|    0 |   A0 |   B0 |   C0 |   D0 |   E0 |
|    1 |   A1 |   B1 |   C1 |   D1 |   E1 |
|    2 |   A2 |   B2 |   C2 |   D2 |   E2 |
|    3 |   A3 |   B3 |   C3 |   D3 |   E3 |

In [3]:

```python
df2 = pd.DataFrame({ 'A': ['A4', 'A5', 'A6', 'A7'],
                     'B': ['B4', 'B5', 'B6', 'B7'],
                     'C': ['C4', 'C5', 'C6', 'C7'],
                     'D': ['D4', 'D5', 'D6', 'D7'],
                     'F': ['F4', 'F5', 'F6', 'F7']
                   })
df2
```

Out[3]:

|      |    A |    B |    C |    D |    F |
| ---: | ---: | ---: | ---: | ---: | ---: |
|    0 |   A4 |   B4 |   C4 |   D4 |   F4 |
|    1 |   A5 |   B5 |   C5 |   D5 |   F5 |
|    2 |   A6 |   B6 |   C6 |   D6 |   F6 |
|    3 |   A7 |   B7 |   C7 |   D7 |   F7 |

***1、默认的concat，参数为axis=0、join=outer、ignore_index=False\***

In [4]:

```python
pd.concat([df1,df2])
```

Out[4]:

|      |    A |    B |    C |    D |    E |    F |
| ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|    0 |   A0 |   B0 |   C0 |   D0 |   E0 |  NaN |
|    1 |   A1 |   B1 |   C1 |   D1 |   E1 |  NaN |
|    2 |   A2 |   B2 |   C2 |   D2 |   E2 |  NaN |
|    3 |   A3 |   B3 |   C3 |   D3 |   E3 |  NaN |
|    0 |   A4 |   B4 |   C4 |   D4 |  NaN |   F4 |
|    1 |   A5 |   B5 |   C5 |   D5 |  NaN |   F5 |
|    2 |   A6 |   B6 |   C6 |   D6 |  NaN |   F6 |
|    3 |   A7 |   B7 |   C7 |   D7 |  NaN |   F7 |

***2、使用ignore_index=True可以忽略原来的索引\***

In [5]:

```python
pd.concat([df1,df2], ignore_index=True)
```

Out[5]:

|      |    A |    B |    C |    D |    E |    F |
| ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|    0 |   A0 |   B0 |   C0 |   D0 |   E0 |  NaN |
|    1 |   A1 |   B1 |   C1 |   D1 |   E1 |  NaN |
|    2 |   A2 |   B2 |   C2 |   D2 |   E2 |  NaN |
|    3 |   A3 |   B3 |   C3 |   D3 |   E3 |  NaN |
|    4 |   A4 |   B4 |   C4 |   D4 |  NaN |   F4 |
|    5 |   A5 |   B5 |   C5 |   D5 |  NaN |   F5 |
|    6 |   A6 |   B6 |   C6 |   D6 |  NaN |   F6 |
|    7 |   A7 |   B7 |   C7 |   D7 |  NaN |   F7 |

***3、使用join=inner过滤掉不匹配的列\***

In [6]:

```python
pd.concat([df1,df2], ignore_index=True, join="inner")
```

Out[6]:

|      |    A |    B |    C |    D |
| ---: | ---: | ---: | ---: | ---: |
|    0 |   A0 |   B0 |   C0 |   D0 |
|    1 |   A1 |   B1 |   C1 |   D1 |
|    2 |   A2 |   B2 |   C2 |   D2 |
|    3 |   A3 |   B3 |   C3 |   D3 |
|    4 |   A4 |   B4 |   C4 |   D4 |
|    5 |   A5 |   B5 |   C5 |   D5 |
|    6 |   A6 |   B6 |   C6 |   D6 |
|    7 |   A7 |   B7 |   C7 |   D7 |

***4、使用axis=1相当于添加新列\***

In [7]:

```python
df1
```

Out[7]:

|      |    A |    B |    C |    D |    E |
| ---: | ---: | ---: | ---: | ---: | ---: |
|    0 |   A0 |   B0 |   C0 |   D0 |   E0 |
|    1 |   A1 |   B1 |   C1 |   D1 |   E1 |
|    2 |   A2 |   B2 |   C2 |   D2 |   E2 |
|    3 |   A3 |   B3 |   C3 |   D3 |   E3 |

***A：添加一列Series\***

In [8]:

```python
s1 = pd.Series(list(range(4)), name="F")
pd.concat([df1,s1], axis=1)
```

Out[8]:

|      |    A |    B |    C |    D |    E |    F |
| ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|    0 |   A0 |   B0 |   C0 |   D0 |   E0 |    0 |
|    1 |   A1 |   B1 |   C1 |   D1 |   E1 |    1 |
|    2 |   A2 |   B2 |   C2 |   D2 |   E2 |    2 |
|    3 |   A3 |   B3 |   C3 |   D3 |   E3 |    3 |

***B：添加多列Series\***

In [9]:

```python
s2 = df1.apply(lambda x:x["A"]+"_GG", axis=1)
```

In [10]:

```python
s2
```

Out[10]:

```python
0    A0_GG
1    A1_GG
2    A2_GG
3    A3_GG
dtype: object
```

In [11]:

```python
s2.name="G"
```

In [12]:

```python
pd.concat([df1,s1,s2], axis=1)
```

Out[12]:

|      |    A |    B |    C |    D |    E |    F |     G |
| ---: | ---: | ---: | ---: | ---: | ---: | ---: | ----: |
|    0 |   A0 |   B0 |   C0 |   D0 |   E0 |    0 | A0_GG |
|    1 |   A1 |   B1 |   C1 |   D1 |   E1 |    1 | A1_GG |
|    2 |   A2 |   B2 |   C2 |   D2 |   E2 |    2 | A2_GG |
|    3 |   A3 |   B3 |   C3 |   D3 |   E3 |    3 | A3_GG |

In [13]:

```python
# 列表可以只有Series
pd.concat([s1,s2], axis=1)
```

Out[13]:

|      |    F |     G |
| ---: | ---: | ----: |
|    0 |    0 | A0_GG |
|    1 |    1 | A1_GG |
|    2 |    2 | A2_GG |
|    3 |    3 | A3_GG |

In [14]:

```python
# 列表是可以混合顺序的
pd.concat([s1,df1,s2], axis=1)
```

Out[14]:

|      |    F |    A |    B |    C |    D |    E |     G |
| ---: | ---: | ---: | ---: | ---: | ---: | ---: | ----: |
|    0 |    0 |   A0 |   B0 |   C0 |   D0 |   E0 | A0_GG |
|    1 |    1 |   A1 |   B1 |   C1 |   D1 |   E1 | A1_GG |
|    2 |    2 |   A2 |   B2 |   C2 |   D2 |   E2 | A2_GG |
|    3 |    3 |   A3 |   B3 |   C3 |   D3 |   E3 | A3_GG |

### 二、使用DataFrame.append按行合并数据

In [15]:

```python
df1 = pd.DataFrame([[1, 2], [3, 4]], columns=list('AB'))
df1
```

Out[15]:

|      |    A |    B |
| ---: | ---: | ---: |
|    0 |    1 |    2 |
|    1 |    3 |    4 |

In [16]:

```python
df2 = pd.DataFrame([[5, 6], [7, 8]], columns=list('AB'))
df2
```

Out[16]:

|      |    A |    B |
| ---: | ---: | ---: |
|    0 |    5 |    6 |
|    1 |    7 |    8 |

***1、给1个dataframe添加另一个dataframe\***

In [17]:

```python
df1.append(df2)
```

Out[17]:

|      |    A |    B |
| ---: | ---: | ---: |
|    0 |    1 |    2 |
|    1 |    3 |    4 |
|    0 |    5 |    6 |
|    1 |    7 |    8 |

***2、忽略原来的索引ignore_index=True\***

In [18]:

```python
df1.append(df2, ignore_index=True)
```

Out[18]:

|      |    A |    B |
| ---: | ---: | ---: |
|    0 |    1 |    2 |
|    1 |    3 |    4 |
|    2 |    5 |    6 |
|    3 |    7 |    8 |

***3、可以一行一行的给DataFrame添加数据\***

In [19]:

```python
# 一个空的df
df = pd.DataFrame(columns=['A'])
df
```

Out[19]:

|      |    A |
| ---: | ---: |
|      |      |

***A：低性能版本\***

In [20]:

```python
for i in range(5):
    # 注意这里每次都在复制
    df = df.append({'A': i}, ignore_index=True)
df
```

Out[20]:

|      |    A |
| ---: | ---: |
|    0 |    0 |
|    1 |    1 |
|    2 |    2 |
|    3 |    3 |
|    4 |    4 |

***B：性能好的版本\***

In [21]:

```python
# 第一个入参是一个列表，避免了多次复制
pd.concat(
    [pd.DataFrame([i], columns=['A']) for i in range(5)],
    ignore_index=True
)
```

Out[21]:

|      |    A |
| ---: | ---: |
|    0 |    0 |
|    1 |    1 |
|    2 |    2 |
|    3 |    3 |
|    4 |    4 |

# 15、Pandas批量拆分Excel与合并Excel

实例演示：

1. 将一个大Excel等份拆成多个Excel
2. 将多个小Excel合并成一个大Excel并标记来源

In [1]:

```python
work_dir="./course_datas/c15_excel_split_merge"
splits_dir=f"{work_dir}/splits"

import os
if not os.path.exists(splits_dir):
    os.mkdir(splits_dir)
```

### 0、读取源Excel到Pandas

In [2]:

```python
import pandas as pd
```

In [3]:

```python
df_source = pd.read_excel(f"{work_dir}/crazyant_blog_articles_source.xlsx")
```

In [4]:

```python
df_source.head()
```

Out[4]:

|      |   id |                          title |                       tags |
| ---: | ---: | -----------------------------: | -------------------------: |
|    0 | 2585 | Tensorflow怎样接收变长列表特征 | python,tensorflow,特征工程 |
|    1 | 2583 |     Pandas实现数据的合并concat |     pandas,python,数据分析 |
|    2 | 2574 |  Pandas的Index索引有什么用途？ |     pandas,python,数据分析 |
|    3 | 2564 |         机器学习常用数据集大全 |            python,机器学习 |
|    4 | 2561 |       一个数据科学家的修炼路径 |                   数据分析 |

In [5]:

```python
df_source.index
```

Out[5]:

```python
RangeIndex(start=0, stop=258, step=1)
```

In [6]:

```python
df_source.shape
```

Out[6]:

```python
(258, 3)
```

In [7]:

```python
total_row_count = df_source.shape[0]
total_row_count
```

Out[7]:

```python
258
```

### 一、将一个大Excel等份拆成多个Excel

1. 使用df.iloc方法，将一个大的dataframe，拆分成多个小dataframe
2. 将使用dataframe.to_excel保存每个小Excel

#### 1、计算拆分后的每个excel的行数

In [9]:

```python
# 这个大excel，会拆分给这几个人
user_names = ["xiao_shuai", "xiao_wang", "xiao_ming", "xiao_lei", "xiao_bo", "xiao_hong"]
```

In [10]:

```python
# 每个人的任务数目
split_size = total_row_count // len(user_names)
if total_row_count % len(user_names) != 0:
    split_size += 1

split_size
```

Out[10]:

```python
43
```

#### 2、拆分成多个dataframe

In [12]:

```python
df_subs = []
for idx, user_name in enumerate(user_names):
    # iloc的开始索引
    begin = idx*split_size
    # iloc的结束索引
    end = begin+split_size
    # 实现df按照iloc拆分
    df_sub = df_source.iloc[begin:end]
    # 将每个子df存入列表
    df_subs.append((idx, user_name, df_sub))
```

#### 3、将每个datafame存入excel

In [13]:

```python
for idx, user_name, df_sub in df_subs:
    file_name = f"{splits_dir}/crazyant_blog_articles_{idx}_{user_name}.xlsx"
    df_sub.to_excel(file_name, index=False)
```

### 二、合并多个小Excel到一个大Excel

1. 遍历文件夹，得到要合并的Excel文件列表
2. 分别读取到dataframe，给每个df添加一列用于标记来源
3. 使用pd.concat进行df批量合并
4. 将合并后的dataframe输出到excel

#### 1. 遍历文件夹，得到要合并的Excel名称列表

In [14]:

```python
import os
excel_names = []
for excel_name in os.listdir(splits_dir):
    excel_names.append(excel_name)
excel_names
```

Out[14]:

```python
['crazyant_blog_articles_0_xiao_shuai.xlsx',
 'crazyant_blog_articles_1_xiao_wang.xlsx',
 'crazyant_blog_articles_2_xiao_ming.xlsx',
 'crazyant_blog_articles_3_xiao_lei.xlsx',
 'crazyant_blog_articles_4_xiao_bo.xlsx',
 'crazyant_blog_articles_5_xiao_hong.xlsx']
```

#### 2. 分别读取到dataframe

In [15]:

```python
df_list = []

for excel_name in excel_names:
    # 读取每个excel到df
    excel_path = f"{splits_dir}/{excel_name}"
    df_split = pd.read_excel(excel_path)
    # 得到username
    username = excel_name.replace("crazyant_blog_articles_", "").replace(".xlsx", "")[2:]
    print(excel_name, username)
    # 给每个df添加1列，即用户名字
    df_split["username"] = username
    
    df_list.append(df_split)
crazyant_blog_articles_0_xiao_shuai.xlsx xiao_shuai
crazyant_blog_articles_1_xiao_wang.xlsx xiao_wang
crazyant_blog_articles_2_xiao_ming.xlsx xiao_ming
crazyant_blog_articles_3_xiao_lei.xlsx xiao_lei
crazyant_blog_articles_4_xiao_bo.xlsx xiao_bo
crazyant_blog_articles_5_xiao_hong.xlsx xiao_hong
```

#### 3. 使用pd.concat进行合并

In [16]:

```python
df_merged = pd.concat(df_list)
```

In [17]:

```python
df_merged.shape
```

Out[17]:

```python
(258, 4)
```

In [18]:

```python
df_merged.head()
```

Out[18]:

|      |   id |                          title |                       tags |   username |
| ---: | ---: | -----------------------------: | -------------------------: | ---------: |
|    0 | 2585 | Tensorflow怎样接收变长列表特征 | python,tensorflow,特征工程 | xiao_shuai |
|    1 | 2583 |     Pandas实现数据的合并concat |     pandas,python,数据分析 | xiao_shuai |
|    2 | 2574 |  Pandas的Index索引有什么用途？ |     pandas,python,数据分析 | xiao_shuai |
|    3 | 2564 |         机器学习常用数据集大全 |            python,机器学习 | xiao_shuai |
|    4 | 2561 |       一个数据科学家的修炼路径 |                   数据分析 | xiao_shuai |

In [19]:

```python
df_merged["username"].value_counts()
```

Out[19]:

```python
xiao_hong     43
xiao_bo       43
xiao_shuai    43
xiao_lei      43
xiao_wang     43
xiao_ming     43
Name: username, dtype: int64
```

#### 4. 将合并后的dataframe输出到excel

In [20]:

```python
df_merged.to_excel(f"{work_dir}/crazyant_blog_articles_merged.xlsx", index=False)
```



# 16、Pandas怎样实现groupby分组统计

类似SQL：
select city,max(temperature) from city_weather group by city;

groupby：先对数据分组，然后在每个分组上应用聚合函数、转换函数

本次演示：
一、分组使用聚合函数做数据统计
二、遍历groupby的结果理解执行流程
三、实例分组探索天气数据 

In [1]:

```python
import pandas as pd
import numpy as np
# 加上这一句，能在jupyter notebook展示matplot图表
%matplotlib inline
```

In [2]:

```python
df = pd.DataFrame({'A': ['foo', 'bar', 'foo', 'bar', 'foo', 'bar', 'foo', 'foo'],
                   'B': ['one', 'one', 'two', 'three', 'two', 'two', 'one', 'three'],
                   'C': np.random.randn(8),
                   'D': np.random.randn(8)})
df
```

Out[2]:

|      |    A |     B |         C |         D |
| ---: | ---: | ----: | --------: | --------: |
|    0 |  foo |   one |  0.542903 |  0.788896 |
|    1 |  bar |   one | -0.375789 | -0.345869 |
|    2 |  foo |   two | -0.903407 |  0.428031 |
|    3 |  bar | three | -1.564748 |  0.081163 |
|    4 |  foo |   two | -1.093602 |  0.837348 |
|    5 |  bar |   two | -0.202403 |  0.701301 |
|    6 |  foo |   one | -0.665189 | -1.505290 |
|    7 |  foo | three | -0.498339 |  0.534438 |

### 一、分组使用聚合函数做数据统计

#### 1、单个列groupby，查询所有数据列的统计

In [3]:

```python
df.groupby('A').sum()
```

Out[3]:

|      |         C |        D |
| ---: | --------: | -------: |
|    A |           |          |
|  bar | -2.142940 | 0.436595 |
|  foo | -2.617633 | 1.083423 |

我们看到：

1. groupby中的'A'变成了数据的索引列
2. 因为要统计sum，但B列不是数字，所以被自动忽略掉

#### 2、多个列groupby，查询所有数据列的统计

In [4]:

```python
df.groupby(['A','B']).mean()
```

Out[4]:

|       |           |         C |         D |
| ----: | --------: | --------: | --------: |
|     A |         B |           |           |
|   bar |       one | -0.375789 | -0.345869 |
| three | -1.564748 |  0.081163 |           |
|   two | -0.202403 |  0.701301 |           |
|   foo |       one | -0.061143 | -0.358197 |
| three | -0.498339 |  0.534438 |           |
|   two | -0.998504 |  0.632690 |           |

我们看到：('A','B')成对变成了二级索引

In [5]:

```python
df.groupby(['A','B'], as_index=False).mean()
```

Out[5]:

|      |    A |     B |         C |         D |
| ---: | ---: | ----: | --------: | --------: |
|    0 |  bar |   one | -0.375789 | -0.345869 |
|    1 |  bar | three | -1.564748 |  0.081163 |
|    2 |  bar |   two | -0.202403 |  0.701301 |
|    3 |  foo |   one | -0.061143 | -0.358197 |
|    4 |  foo | three | -0.498339 |  0.534438 |
|    5 |  foo |   two | -0.998504 |  0.632690 |

#### 3、同时查看多种数据统计

In [6]:

```python
df.groupby('A').agg([np.sum, np.mean, np.std])
```

Out[6]:

|      |         C |         D |          |          |          |          |
| ---: | --------: | --------: | -------: | -------: | -------: | -------: |
|      |       sum |      mean |      std |      sum |     mean |      std |
|    A |           |           |          |          |          |          |
|  bar | -2.142940 | -0.714313 | 0.741583 | 0.436595 | 0.145532 | 0.526544 |
|  foo | -2.617633 | -0.523527 | 0.637822 | 1.083423 | 0.216685 | 0.977686 |

我们看到：列变成了多级索引

#### 4、查看单列的结果数据统计

In [7]:

```python
# 方法1：预过滤，性能更好
df.groupby('A')['C'].agg([np.sum, np.mean, np.std])
```

Out[7]:

|      |       sum |      mean |      std |
| ---: | --------: | --------: | -------: |
|    A |           |           |          |
|  bar | -2.142940 | -0.714313 | 0.741583 |
|  foo | -2.617633 | -0.523527 | 0.637822 |

In [8]:

```python
# 方法2
df.groupby('A').agg([np.sum, np.mean, np.std])['C']
```

Out[8]:

|      |       sum |      mean |      std |
| ---: | --------: | --------: | -------: |
|    A |           |           |          |
|  bar | -2.142940 | -0.714313 | 0.741583 |
|  foo | -2.617633 | -0.523527 | 0.637822 |

#### 5、不同列使用不同的聚合函数

In [9]:

```python
df.groupby('A').agg({"C":np.sum, "D":np.mean})
```

Out[9]:

|      |         C |        D |
| ---: | --------: | -------: |
|    A |           |          |
|  bar | -2.142940 | 0.145532 |
|  foo | -2.617633 | 0.216685 |

### 二、遍历groupby的结果理解执行流程

for循环可以直接遍历每个group

##### 1、遍历单个列聚合的分组

In [10]:

```python
g = df.groupby('A')
g
```

Out[10]:

```python
<pandas.core.groupby.generic.DataFrameGroupBy object at 0x00000123B250E548>
```

In [11]:

```python
for name,group in g:
    print(name)
    print(group)
    print()
bar
     A      B         C         D
1  bar    one -0.375789 -0.345869
3  bar  three -1.564748  0.081163
5  bar    two -0.202403  0.701301

foo
     A      B         C         D
0  foo    one  0.542903  0.788896
2  foo    two -0.903407  0.428031
4  foo    two -1.093602  0.837348
6  foo    one -0.665189 -1.505290
7  foo  three -0.498339  0.534438
```

***可以获取单个分组的数据\***

In [12]:

```python
g.get_group('bar')
```

Out[12]:

|      |    A |     B |         C |         D |
| ---: | ---: | ----: | --------: | --------: |
|    1 |  bar |   one | -0.375789 | -0.345869 |
|    3 |  bar | three | -1.564748 |  0.081163 |
|    5 |  bar |   two | -0.202403 |  0.701301 |

##### 2、遍历多个列聚合的分组

In [13]:

```python
g = df.groupby(['A', 'B'])
```

In [14]:

```
for name,group in g:
    print(name)
    print(group)
    print()
('bar', 'one')
     A    B         C         D
1  bar  one -0.375789 -0.345869

('bar', 'three')
     A      B         C         D
3  bar  three -1.564748  0.081163

('bar', 'two')
     A    B         C         D
5  bar  two -0.202403  0.701301

('foo', 'one')
     A    B         C         D
0  foo  one  0.542903  0.788896
6  foo  one -0.665189 -1.505290

('foo', 'three')
     A      B         C         D
7  foo  three -0.498339  0.534438

('foo', 'two')
     A    B         C         D
2  foo  two -0.903407  0.428031
4  foo  two -1.093602  0.837348
```

可以看到，name是一个2个元素的tuple，代表不同的列

In [15]:

```python
g.get_group(('foo', 'one'))
```

Out[15]:

|      |    A |    B |         C |         D |
| ---: | ---: | ---: | --------: | --------: |
|    0 |  foo |  one |  0.542903 |  0.788896 |
|    6 |  foo |  one | -0.665189 | -1.505290 |

***可以直接查询group后的某几列，生成Series或者子DataFrame\***

In [16]:

```python
g['C']
```

Out[16]:

```python
<pandas.core.groupby.generic.SeriesGroupBy object at 0x00000123C33F64C8>
```

In [17]:

```python
for name, group in g['C']:
    print(name)
    print(group)
    print(type(group))
    print()
('bar', 'one')
1   -0.375789
Name: C, dtype: float64
<class 'pandas.core.series.Series'>

('bar', 'three')
3   -1.564748
Name: C, dtype: float64
<class 'pandas.core.series.Series'>

('bar', 'two')
5   -0.202403
Name: C, dtype: float64
<class 'pandas.core.series.Series'>

('foo', 'one')
0    0.542903
6   -0.665189
Name: C, dtype: float64
<class 'pandas.core.series.Series'>

('foo', 'three')
7   -0.498339
Name: C, dtype: float64
<class 'pandas.core.series.Series'>

('foo', 'two')
2   -0.903407
4   -1.093602
Name: C, dtype: float64
<class 'pandas.core.series.Series'>
```

其实所有的聚合统计，都是在dataframe和series上进行的；

### 三、实例分组探索天气数据

In [18]:

```python
fpath = "./datas/beijing_tianqi/beijing_tianqi_2018.csv"
df = pd.read_csv(fpath)
# 替换掉温度的后缀℃
df.loc[:, "bWendu"] = df["bWendu"].str.replace("℃", "").astype('int32')
df.loc[:, "yWendu"] = df["yWendu"].str.replace("℃", "").astype('int32')
df.head()
```

Out[18]:

|      |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi | aqiInfo | aqiLevel |
| ---: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | ------: | -------: |
|    0 | 2018-01-01 |      3 |     -6 | 晴~多云 |    东北风 |  1-2级 |   59 |      良 |        2 |
|    1 | 2018-01-02 |      2 |     -5 | 阴~多云 |    东北风 |  1-2级 |   49 |      优 |        1 |
|    2 | 2018-01-03 |      2 |     -5 |    多云 |      北风 |  1-2级 |   28 |      优 |        1 |
|    3 | 2018-01-04 |      0 |     -8 |      阴 |    东北风 |  1-2级 |   28 |      优 |        1 |
|    4 | 2018-01-05 |      3 |     -6 | 多云~晴 |    西北风 |  1-2级 |   50 |      优 |        1 |

In [19]:

```python
# 新增一列为月份
df['month'] = df['ymd'].str[:7]
df.head()
```

Out[19]:

|      |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi | aqiInfo | aqiLevel |   month |
| ---: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | ------: | -------: | ------: |
|    0 | 2018-01-01 |      3 |     -6 | 晴~多云 |    东北风 |  1-2级 |   59 |      良 |        2 | 2018-01 |
|    1 | 2018-01-02 |      2 |     -5 | 阴~多云 |    东北风 |  1-2级 |   49 |      优 |        1 | 2018-01 |
|    2 | 2018-01-03 |      2 |     -5 |    多云 |      北风 |  1-2级 |   28 |      优 |        1 | 2018-01 |
|    3 | 2018-01-04 |      0 |     -8 |      阴 |    东北风 |  1-2级 |   28 |      优 |        1 | 2018-01 |
|    4 | 2018-01-05 |      3 |     -6 | 多云~晴 |    西北风 |  1-2级 |   50 |      优 |        1 | 2018-01 |

#### 1、查看每个月的最高温度

In [20]:

```python
data = df.groupby('month')['bWendu'].max()
data
```

Out[20]:

```python
month
2018-01     7
2018-02    12
2018-03    27
2018-04    30
2018-05    35
2018-06    38
2018-07    37
2018-08    36
2018-09    31
2018-10    25
2018-11    18
2018-12    10
Name: bWendu, dtype: int32
```

In [21]:

```python
type(data)
```

Out[21]:

```python
pandas.core.series.Series
```

In [22]:

```python
data.plot()
```

Out[22]:

```python
<matplotlib.axes._subplots.AxesSubplot at 0x123c344b308>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/python_pandas/8.png)

#### 2、查看每个月的最高温度、最低温度、平均空气质量指数

In [23]:

```python
df.head()
```

Out[23]:

|      |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi | aqiInfo | aqiLevel |   month |
| ---: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | ------: | -------: | ------: |
|    0 | 2018-01-01 |      3 |     -6 | 晴~多云 |    东北风 |  1-2级 |   59 |      良 |        2 | 2018-01 |
|    1 | 2018-01-02 |      2 |     -5 | 阴~多云 |    东北风 |  1-2级 |   49 |      优 |        1 | 2018-01 |
|    2 | 2018-01-03 |      2 |     -5 |    多云 |      北风 |  1-2级 |   28 |      优 |        1 | 2018-01 |
|    3 | 2018-01-04 |      0 |     -8 |      阴 |    东北风 |  1-2级 |   28 |      优 |        1 | 2018-01 |
|    4 | 2018-01-05 |      3 |     -6 | 多云~晴 |    西北风 |  1-2级 |   50 |      优 |        1 | 2018-01 |

In [24]:

```python
group_data = df.groupby('month').agg({"bWendu":np.max, "yWendu":np.min, "aqi":np.mean})
group_data
```

Out[24]:

|         | bWendu | yWendu |        aqi |
| ------: | -----: | -----: | ---------: |
|   month |        |        |            |
| 2018-01 |      7 |    -12 |  60.677419 |
| 2018-02 |     12 |    -10 |  78.857143 |
| 2018-03 |     27 |     -4 | 130.322581 |
| 2018-04 |     30 |      1 | 102.866667 |
| 2018-05 |     35 |     10 |  99.064516 |
| 2018-06 |     38 |     17 |  82.300000 |
| 2018-07 |     37 |     22 |  72.677419 |
| 2018-08 |     36 |     20 |  59.516129 |
| 2018-09 |     31 |     11 |  50.433333 |
| 2018-10 |     25 |      1 |  67.096774 |
| 2018-11 |     18 |     -4 | 105.100000 |
| 2018-12 |     10 |    -12 |  77.354839 |

In [25]:

```python
group_data.plot()
```

Out[25]:

```python
<matplotlib.axes._subplots.AxesSubplot at 0x123c5502d48>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_pandas/9.png)

# 17、Pandas的分层索引MultiIndex

为什么要学习分层索引MultiIndex？

- 分层索引：在一个轴向上拥有多个索引层级，可以表达更高维度数据的形式；
- 可以更方便的进行数据筛选，如果有序则性能更好；
- groupby等操作的结果，如果是多KEY，结果是分层索引，需要会使用
- 一般不需要自己创建分层索引(MultiIndex有构造函数但一般不用)

演示数据：百度、阿里巴巴、爱奇艺、京东四家公司的10天股票数据
数据来自：英为财经
https://cn.investing.com/

本次演示提纲：
一、Series的分层索引MultiIndex
二、Series有多层索引怎样筛选数据？
三、DataFrame的多层索引MultiIndex
四、DataFrame有多层索引怎样筛选数据？ 

In [1]:

```python
import pandas as pd
%matplotlib inline
```

In [1]:

```python
stocks = pd.read_excel('./datas/stocks/互联网公司股票.xlsx')
---------------------------------------------------------------------------
NameError                                 Traceback (most recent call last)
<ipython-input-1-574feeeb8bb0> in <module>
----> 1 stocks = pd.read_excel('./datas/stocks/互联网公司股票.xlsx')

NameError: name 'pd' is not defined
```

In [3]:

```python
stocks.shape
```

Out[3]:

```python
(12, 8)
```

In [4]:

```python
stocks.head(3)
```

Out[4]:

|      |       日期 | 公司 |   收盘 |   开盘 |     高 |     低 | 交易量 | 涨跌幅 |
| ---: | ---------: | ---: | -----: | -----: | -----: | -----: | -----: | -----: |
|    0 | 2019-10-03 | BIDU | 104.32 | 102.35 | 104.73 | 101.15 |   2.24 |   0.02 |
|    1 | 2019-10-02 | BIDU | 102.62 | 100.85 | 103.24 |  99.50 |   2.69 |   0.01 |
|    2 | 2019-10-01 | BIDU | 102.00 | 102.80 | 103.26 | 101.00 |   1.78 |  -0.01 |

In [5]:

```python
stocks["公司"].unique()
```

Out[5]:

```python
array(['BIDU', 'BABA', 'IQ', 'JD'], dtype=object)
```

In [6]:

```python
stocks.index
```

Out[6]:

```python
RangeIndex(start=0, stop=12, step=1)
```

In [7]:

```python
stocks.groupby('公司')["收盘"].mean()
```

Out[7]:

```python
公司
BABA    166.80
BIDU    102.98
IQ       15.90
JD       28.35
Name: 收盘, dtype: float64
```

### 一、Series的分层索引MultiIndex

In [8]:

```python
ser = stocks.groupby(['公司', '日期'])['收盘'].mean()
ser
```

Out[8]:

```python
公司    日期        
BABA  2019-10-01    165.15
      2019-10-02    165.77
      2019-10-03    169.48
BIDU  2019-10-01    102.00
      2019-10-02    102.62
      2019-10-03    104.32
IQ    2019-10-01     15.92
      2019-10-02     15.72
      2019-10-03     16.06
JD    2019-10-01     28.19
      2019-10-02     28.06
      2019-10-03     28.80
Name: 收盘, dtype: float64
```

多维索引中，空白的意思是：使用上面的值

In [9]:

```python
ser.index
```

Out[9]:

```python
MultiIndex([('BABA', '2019-10-01'),
            ('BABA', '2019-10-02'),
            ('BABA', '2019-10-03'),
            ('BIDU', '2019-10-01'),
            ('BIDU', '2019-10-02'),
            ('BIDU', '2019-10-03'),
            (  'IQ', '2019-10-01'),
            (  'IQ', '2019-10-02'),
            (  'IQ', '2019-10-03'),
            (  'JD', '2019-10-01'),
            (  'JD', '2019-10-02'),
            (  'JD', '2019-10-03')],
           names=['公司', '日期'])
```

In [10]:

```python
# unstack把二级索引变成列
ser.unstack()
```

Out[10]:

| 日期 | 2019-10-01 | 2019-10-02 | 2019-10-03 |
| ---: | ---------: | ---------: | ---------: |
| 公司 |            |            |            |
| BABA |     165.15 |     165.77 |     169.48 |
| BIDU |     102.00 |     102.62 |     104.32 |
|   IQ |      15.92 |      15.72 |      16.06 |
|   JD |      28.19 |      28.06 |      28.80 |

In [11]:

```python
ser
```

Out[11]:

```python
公司    日期        
BABA  2019-10-01    165.15
      2019-10-02    165.77
      2019-10-03    169.48
BIDU  2019-10-01    102.00
      2019-10-02    102.62
      2019-10-03    104.32
IQ    2019-10-01     15.92
      2019-10-02     15.72
      2019-10-03     16.06
JD    2019-10-01     28.19
      2019-10-02     28.06
      2019-10-03     28.80
Name: 收盘, dtype: float64
```

In [12]:

```python
ser.reset_index()
```

Out[12]:

|      | 公司 |       日期 |   收盘 |
| ---: | ---: | ---------: | -----: |
|    0 | BABA | 2019-10-01 | 165.15 |
|    1 | BABA | 2019-10-02 | 165.77 |
|    2 | BABA | 2019-10-03 | 169.48 |
|    3 | BIDU | 2019-10-01 | 102.00 |
|    4 | BIDU | 2019-10-02 | 102.62 |
|    5 | BIDU | 2019-10-03 | 104.32 |
|    6 |   IQ | 2019-10-01 |  15.92 |
|    7 |   IQ | 2019-10-02 |  15.72 |
|    8 |   IQ | 2019-10-03 |  16.06 |
|    9 |   JD | 2019-10-01 |  28.19 |
|   10 |   JD | 2019-10-02 |  28.06 |
|   11 |   JD | 2019-10-03 |  28.80 |

### 二、Series有多层索引MultiIndex怎样筛选数据？

In [13]:

```python
ser
```

Out[13]:

```python
公司    日期        
BABA  2019-10-01    165.15
      2019-10-02    165.77
      2019-10-03    169.48
BIDU  2019-10-01    102.00
      2019-10-02    102.62
      2019-10-03    104.32
IQ    2019-10-01     15.92
      2019-10-02     15.72
      2019-10-03     16.06
JD    2019-10-01     28.19
      2019-10-02     28.06
      2019-10-03     28.80
Name: 收盘, dtype: float64
```

In [14]:

```python
ser.loc['BIDU']
```

Out[14]:

```python
日期
2019-10-01    102.00
2019-10-02    102.62
2019-10-03    104.32
Name: 收盘, dtype: float64
```

In [15]:

```python
# 多层索引，可以用元组的形式筛选
ser.loc[('BIDU', '2019-10-02')]
```

Out[15]:

```python
102.62
```

In [16]:

```python
ser.loc[:, '2019-10-02']
```

Out[16]:

```python
公司
BABA    165.77
BIDU    102.62
IQ       15.72
JD       28.06
Name: 收盘, dtype: float64
```

### 三、DataFrame的多层索引MultiIndex

In [17]:

```python
stocks.head()
```

Out[17]:

|      |       日期 | 公司 |   收盘 |   开盘 |     高 |     低 | 交易量 | 涨跌幅 |
| ---: | ---------: | ---: | -----: | -----: | -----: | -----: | -----: | -----: |
|    0 | 2019-10-03 | BIDU | 104.32 | 102.35 | 104.73 | 101.15 |   2.24 |   0.02 |
|    1 | 2019-10-02 | BIDU | 102.62 | 100.85 | 103.24 |  99.50 |   2.69 |   0.01 |
|    2 | 2019-10-01 | BIDU | 102.00 | 102.80 | 103.26 | 101.00 |   1.78 |  -0.01 |
|    3 | 2019-10-03 | BABA | 169.48 | 166.65 | 170.18 | 165.00 |  10.39 |   0.02 |
|    4 | 2019-10-02 | BABA | 165.77 | 162.82 | 166.88 | 161.90 |  11.60 |   0.00 |

In [18]:

```python
stocks.set_index(['公司', '日期'], inplace=True)
stocks
```

Out[18]:

|            |            |   收盘 |   开盘 |     高 |     低 | 交易量 | 涨跌幅 |
| ---------: | ---------: | -----: | -----: | -----: | -----: | -----: | -----: |
|       公司 |       日期 |        |        |        |        |        |        |
|       BIDU | 2019-10-03 | 104.32 | 102.35 | 104.73 | 101.15 |   2.24 |   0.02 |
| 2019-10-02 |     102.62 | 100.85 | 103.24 |  99.50 |   2.69 |   0.01 |        |
| 2019-10-01 |     102.00 | 102.80 | 103.26 | 101.00 |   1.78 |  -0.01 |        |
|       BABA | 2019-10-03 | 169.48 | 166.65 | 170.18 | 165.00 |  10.39 |   0.02 |
| 2019-10-02 |     165.77 | 162.82 | 166.88 | 161.90 |  11.60 |   0.00 |        |
| 2019-10-01 |     165.15 | 168.01 | 168.23 | 163.64 |  14.19 |  -0.01 |        |
|         IQ | 2019-10-03 |  16.06 |  15.71 |  16.38 |  15.32 |  10.08 |   0.02 |
| 2019-10-02 |      15.72 |  15.85 |  15.87 |  15.12 |   8.10 |  -0.01 |        |
| 2019-10-01 |      15.92 |  16.14 |  16.22 |  15.50 |  11.65 |  -0.01 |        |
|         JD | 2019-10-03 |  28.80 |  28.11 |  28.97 |  27.82 |   8.77 |   0.03 |
| 2019-10-02 |      28.06 |  28.00 |  28.22 |  27.53 |   9.53 |   0.00 |        |
| 2019-10-01 |      28.19 |  28.22 |  28.57 |  27.97 |  10.64 |   0.00 |        |

In [19]:

```python
stocks.index
```

Out[19]:

```python
MultiIndex([('BIDU', '2019-10-03'),
            ('BIDU', '2019-10-02'),
            ('BIDU', '2019-10-01'),
            ('BABA', '2019-10-03'),
            ('BABA', '2019-10-02'),
            ('BABA', '2019-10-01'),
            (  'IQ', '2019-10-03'),
            (  'IQ', '2019-10-02'),
            (  'IQ', '2019-10-01'),
            (  'JD', '2019-10-03'),
            (  'JD', '2019-10-02'),
            (  'JD', '2019-10-01')],
           names=['公司', '日期'])
```

In [20]:

```python
stocks.sort_index(inplace=True)
stocks
```

Out[20]:

|            |            |   收盘 |   开盘 |     高 |     低 | 交易量 | 涨跌幅 |
| ---------: | ---------: | -----: | -----: | -----: | -----: | -----: | -----: |
|       公司 |       日期 |        |        |        |        |        |        |
|       BABA | 2019-10-01 | 165.15 | 168.01 | 168.23 | 163.64 |  14.19 |  -0.01 |
| 2019-10-02 |     165.77 | 162.82 | 166.88 | 161.90 |  11.60 |   0.00 |        |
| 2019-10-03 |     169.48 | 166.65 | 170.18 | 165.00 |  10.39 |   0.02 |        |
|       BIDU | 2019-10-01 | 102.00 | 102.80 | 103.26 | 101.00 |   1.78 |  -0.01 |
| 2019-10-02 |     102.62 | 100.85 | 103.24 |  99.50 |   2.69 |   0.01 |        |
| 2019-10-03 |     104.32 | 102.35 | 104.73 | 101.15 |   2.24 |   0.02 |        |
|         IQ | 2019-10-01 |  15.92 |  16.14 |  16.22 |  15.50 |  11.65 |  -0.01 |
| 2019-10-02 |      15.72 |  15.85 |  15.87 |  15.12 |   8.10 |  -0.01 |        |
| 2019-10-03 |      16.06 |  15.71 |  16.38 |  15.32 |  10.08 |   0.02 |        |
|         JD | 2019-10-01 |  28.19 |  28.22 |  28.57 |  27.97 |  10.64 |   0.00 |
| 2019-10-02 |      28.06 |  28.00 |  28.22 |  27.53 |   9.53 |   0.00 |        |
| 2019-10-03 |      28.80 |  28.11 |  28.97 |  27.82 |   8.77 |   0.03 |        |

### 四、DataFrame有多层索引MultiIndex怎样筛选数据？

【***重要知识\***】在选择数据时： 

- 元组(key1,key2)代表筛选多层索引，其中key1是索引第一级，key2是第二级，比如key1=JD, key2=2019-10-02
- 列表[key1,key2]代表同一层的多个KEY，其中key1和key2是并列的同级索引，比如key1=JD, key2=BIDU

In [21]:

```python
stocks.loc['BIDU']
```

Out[21]:

|            |   收盘 |   开盘 |     高 |     低 | 交易量 | 涨跌幅 |
| ---------: | -----: | -----: | -----: | -----: | -----: | -----: |
|       日期 |        |        |        |        |        |        |
| 2019-10-01 | 102.00 | 102.80 | 103.26 | 101.00 |   1.78 |  -0.01 |
| 2019-10-02 | 102.62 | 100.85 | 103.24 |  99.50 |   2.69 |   0.01 |
| 2019-10-03 | 104.32 | 102.35 | 104.73 | 101.15 |   2.24 |   0.02 |

In [22]:

```python
stocks.loc[('BIDU', '2019-10-02'), :]
```

Out[22]:

```python
收盘     102.62
开盘     100.85
高      103.24
低       99.50
交易量      2.69
涨跌幅      0.01
Name: (BIDU, 2019-10-02), dtype: float64
```

In [23]:

```python
stocks.loc[('BIDU', '2019-10-02'), '开盘']
```

Out[23]:

```python
100.85
```

In [24]:

```python
stocks.loc[['BIDU', 'JD'], :]
```

Out[24]:

|            |            |   收盘 |   开盘 |     高 |     低 | 交易量 | 涨跌幅 |
| ---------: | ---------: | -----: | -----: | -----: | -----: | -----: | -----: |
|       公司 |       日期 |        |        |        |        |        |        |
|       BIDU | 2019-10-01 | 102.00 | 102.80 | 103.26 | 101.00 |   1.78 |  -0.01 |
| 2019-10-02 |     102.62 | 100.85 | 103.24 |  99.50 |   2.69 |   0.01 |        |
| 2019-10-03 |     104.32 | 102.35 | 104.73 | 101.15 |   2.24 |   0.02 |        |
|         JD | 2019-10-01 |  28.19 |  28.22 |  28.57 |  27.97 |  10.64 |   0.00 |
| 2019-10-02 |      28.06 |  28.00 |  28.22 |  27.53 |   9.53 |   0.00 |        |
| 2019-10-03 |      28.80 |  28.11 |  28.97 |  27.82 |   8.77 |   0.03 |        |

In [25]:

```python
stocks.loc[(['BIDU', 'JD'], '2019-10-03'), :]
```

Out[25]:

|      |            |   收盘 |   开盘 |     高 |     低 | 交易量 | 涨跌幅 |
| ---: | ---------: | -----: | -----: | -----: | -----: | -----: | -----: |
| 公司 |       日期 |        |        |        |        |        |        |
| BIDU | 2019-10-03 | 104.32 | 102.35 | 104.73 | 101.15 |   2.24 |   0.02 |
|   JD | 2019-10-03 |  28.80 |  28.11 |  28.97 |  27.82 |   8.77 |   0.03 |

In [26]:

```python
stocks.loc[(['BIDU', 'JD'], '2019-10-03'), '收盘']
```

Out[26]:

```python
公司    日期        
BIDU  2019-10-03    104.32
JD    2019-10-03     28.80
Name: 收盘, dtype: float64
```

In [27]:

```python
stocks.loc[('BIDU', ['2019-10-02', '2019-10-03']), '收盘']
```

Out[27]:

```
公司    日期        
BIDU  2019-10-02    102.62
      2019-10-03    104.32
Name: 收盘, dtype: float64
```

In [28]:

```python
# slice(None)代表筛选这一索引的所有内容
stocks.loc[(slice(None), ['2019-10-02', '2019-10-03']), :]
```

Out[28]:

|            |            |   收盘 |   开盘 |     高 |     低 | 交易量 | 涨跌幅 |
| ---------: | ---------: | -----: | -----: | -----: | -----: | -----: | -----: |
|       公司 |       日期 |        |        |        |        |        |        |
|       BABA | 2019-10-02 | 165.77 | 162.82 | 166.88 | 161.90 |  11.60 |   0.00 |
| 2019-10-03 |     169.48 | 166.65 | 170.18 | 165.00 |  10.39 |   0.02 |        |
|       BIDU | 2019-10-02 | 102.62 | 100.85 | 103.24 |  99.50 |   2.69 |   0.01 |
| 2019-10-03 |     104.32 | 102.35 | 104.73 | 101.15 |   2.24 |   0.02 |        |
|         IQ | 2019-10-02 |  15.72 |  15.85 |  15.87 |  15.12 |   8.10 |  -0.01 |
| 2019-10-03 |      16.06 |  15.71 |  16.38 |  15.32 |  10.08 |   0.02 |        |
|         JD | 2019-10-02 |  28.06 |  28.00 |  28.22 |  27.53 |   9.53 |   0.00 |
| 2019-10-03 |      28.80 |  28.11 |  28.97 |  27.82 |   8.77 |   0.03 |        |

In [29]:

```
stocks.reset_index()
```

Out[29]:

|      | 公司 |       日期 |   收盘 |   开盘 |     高 |     低 | 交易量 | 涨跌幅 |
| ---: | ---: | ---------: | -----: | -----: | -----: | -----: | -----: | -----: |
|    0 | BABA | 2019-10-01 | 165.15 | 168.01 | 168.23 | 163.64 |  14.19 |  -0.01 |
|    1 | BABA | 2019-10-02 | 165.77 | 162.82 | 166.88 | 161.90 |  11.60 |   0.00 |
|    2 | BABA | 2019-10-03 | 169.48 | 166.65 | 170.18 | 165.00 |  10.39 |   0.02 |
|    3 | BIDU | 2019-10-01 | 102.00 | 102.80 | 103.26 | 101.00 |   1.78 |  -0.01 |
|    4 | BIDU | 2019-10-02 | 102.62 | 100.85 | 103.24 |  99.50 |   2.69 |   0.01 |
|    5 | BIDU | 2019-10-03 | 104.32 | 102.35 | 104.73 | 101.15 |   2.24 |   0.02 |
|    6 |   IQ | 2019-10-01 |  15.92 |  16.14 |  16.22 |  15.50 |  11.65 |  -0.01 |
|    7 |   IQ | 2019-10-02 |  15.72 |  15.85 |  15.87 |  15.12 |   8.10 |  -0.01 |
|    8 |   IQ | 2019-10-03 |  16.06 |  15.71 |  16.38 |  15.32 |  10.08 |   0.02 |
|    9 |   JD | 2019-10-01 |  28.19 |  28.22 |  28.57 |  27.97 |  10.64 |   0.00 |
|   10 |   JD | 2019-10-02 |  28.06 |  28.00 |  28.22 |  27.53 |   9.53 |   0.00 |
|   11 |   JD | 2019-10-03 |  28.80 |  28.11 |  28.97 |  27.82 |   8.77 |   0.03 |

# 18、Pandas的数据转换函数map、apply、applymap

数据转换函数对比：map、apply、applymap：

1. map：只用于Series，实现每个值->值的映射；
2. apply：用于Series实现每个值的处理，用于Dataframe实现某个轴的Series的处理；
3. applymap：只能用于DataFrame，用于处理该DataFrame的每个元素；

### 1. map用于Series值的转换

实例：将股票代码英文转换成中文名字

Series.map(dict) or Series.map(function)均可

In [1]:

```python
import pandas as pd
stocks = pd.read_excel('./datas/stocks/互联网公司股票.xlsx')
stocks.head()
```

Out[1]:

|      |       日期 | 公司 |   收盘 |   开盘 |     高 |     低 | 交易量 | 涨跌幅 |
| ---: | ---------: | ---: | -----: | -----: | -----: | -----: | -----: | -----: |
|    0 | 2019-10-03 | BIDU | 104.32 | 102.35 | 104.73 | 101.15 |   2.24 |   0.02 |
|    1 | 2019-10-02 | BIDU | 102.62 | 100.85 | 103.24 |  99.50 |   2.69 |   0.01 |
|    2 | 2019-10-01 | BIDU | 102.00 | 102.80 | 103.26 | 101.00 |   1.78 |  -0.01 |
|    3 | 2019-10-03 | BABA | 169.48 | 166.65 | 170.18 | 165.00 |  10.39 |   0.02 |
|    4 | 2019-10-02 | BABA | 165.77 | 162.82 | 166.88 | 161.90 |  11.60 |   0.00 |

In [2]:

```python
stocks["公司"].unique()
```

Out[2]:

```python
array(['BIDU', 'BABA', 'IQ', 'JD'], dtype=object)
```

In [3]:

```python
# 公司股票代码到中文的映射，注意这里是小写
dict_company_names = {
    "bidu": "百度",
    "baba": "阿里巴巴",
    "iq": "爱奇艺", 
    "jd": "京东"
}
```

#### 方法1：Series.map(dict)

In [4]:

```python
stocks["公司中文1"] = stocks["公司"].str.lower().map(dict_company_names)
```

In [5]:

```python
stocks.head()
```

Out[5]:

|      |       日期 | 公司 |   收盘 |   开盘 |     高 |     低 | 交易量 | 涨跌幅 | 公司中文1 |
| ---: | ---------: | ---: | -----: | -----: | -----: | -----: | -----: | -----: | --------: |
|    0 | 2019-10-03 | BIDU | 104.32 | 102.35 | 104.73 | 101.15 |   2.24 |   0.02 |      百度 |
|    1 | 2019-10-02 | BIDU | 102.62 | 100.85 | 103.24 |  99.50 |   2.69 |   0.01 |      百度 |
|    2 | 2019-10-01 | BIDU | 102.00 | 102.80 | 103.26 | 101.00 |   1.78 |  -0.01 |      百度 |
|    3 | 2019-10-03 | BABA | 169.48 | 166.65 | 170.18 | 165.00 |  10.39 |   0.02 |  阿里巴巴 |
|    4 | 2019-10-02 | BABA | 165.77 | 162.82 | 166.88 | 161.90 |  11.60 |   0.00 |  阿里巴巴 |

#### 方法2：Series.map(function)

function的参数是Series的每个元素的值

In [6]:

```
stocks["公司中文2"] = stocks["公司"].map(lambda x : dict_company_names[x.lower()])
```

In [7]:

```
stocks.head()
```

Out[7]:

|      |       日期 | 公司 |   收盘 |   开盘 |     高 |     低 | 交易量 | 涨跌幅 | 公司中文1 | 公司中文2 |
| ---: | ---------: | ---: | -----: | -----: | -----: | -----: | -----: | -----: | --------: | --------: |
|    0 | 2019-10-03 | BIDU | 104.32 | 102.35 | 104.73 | 101.15 |   2.24 |   0.02 |      百度 |      百度 |
|    1 | 2019-10-02 | BIDU | 102.62 | 100.85 | 103.24 |  99.50 |   2.69 |   0.01 |      百度 |      百度 |
|    2 | 2019-10-01 | BIDU | 102.00 | 102.80 | 103.26 | 101.00 |   1.78 |  -0.01 |      百度 |      百度 |
|    3 | 2019-10-03 | BABA | 169.48 | 166.65 | 170.18 | 165.00 |  10.39 |   0.02 |  阿里巴巴 |  阿里巴巴 |
|    4 | 2019-10-02 | BABA | 165.77 | 162.82 | 166.88 | 161.90 |  11.60 |   0.00 |  阿里巴巴 |  阿里巴巴 |

### 2. apply用于Series和DataFrame的转换

- Series.apply(function), 函数的参数是每个值
- DataFrame.apply(function), 函数的参数是Series

#### Series.apply(function)

function的参数是Series的每个值

In [8]:

```python
stocks["公司中文3"] = stocks["公司"].apply(
    lambda x : dict_company_names[x.lower()])
```

In [9]:

```python
stocks.head()
```

Out[9]:

|      |       日期 | 公司 |   收盘 |   开盘 |     高 |     低 | 交易量 | 涨跌幅 | 公司中文1 | 公司中文2 | 公司中文3 |
| ---: | ---------: | ---: | -----: | -----: | -----: | -----: | -----: | -----: | --------: | --------: | --------: |
|    0 | 2019-10-03 | BIDU | 104.32 | 102.35 | 104.73 | 101.15 |   2.24 |   0.02 |      百度 |      百度 |      百度 |
|    1 | 2019-10-02 | BIDU | 102.62 | 100.85 | 103.24 |  99.50 |   2.69 |   0.01 |      百度 |      百度 |      百度 |
|    2 | 2019-10-01 | BIDU | 102.00 | 102.80 | 103.26 | 101.00 |   1.78 |  -0.01 |      百度 |      百度 |      百度 |
|    3 | 2019-10-03 | BABA | 169.48 | 166.65 | 170.18 | 165.00 |  10.39 |   0.02 |  阿里巴巴 |  阿里巴巴 |  阿里巴巴 |
|    4 | 2019-10-02 | BABA | 165.77 | 162.82 | 166.88 | 161.90 |  11.60 |   0.00 |  阿里巴巴 |  阿里巴巴 |  阿里巴巴 |

#### DataFrame.apply(function)

function的参数是对应轴的Series

In [10]:

```python
stocks["公司中文4"] = stocks.apply(
    lambda x : dict_company_names[x["公司"].lower()], 
    axis=1)
```

注意这个代码：
1、apply是在stocks这个DataFrame上调用；
2、lambda x的x是一个Series，因为指定了axis=1所以Seires的key是列名，可以用x['公司']获取

In [11]:

```python
stocks.head()
```

Out[11]:

|      |       日期 | 公司 |   收盘 |   开盘 |     高 |     低 | 交易量 | 涨跌幅 | 公司中文1 | 公司中文2 | 公司中文3 | 公司中文4 |
| ---: | ---------: | ---: | -----: | -----: | -----: | -----: | -----: | -----: | --------: | --------: | --------: | --------: |
|    0 | 2019-10-03 | BIDU | 104.32 | 102.35 | 104.73 | 101.15 |   2.24 |   0.02 |      百度 |      百度 |      百度 |      百度 |
|    1 | 2019-10-02 | BIDU | 102.62 | 100.85 | 103.24 |  99.50 |   2.69 |   0.01 |      百度 |      百度 |      百度 |      百度 |
|    2 | 2019-10-01 | BIDU | 102.00 | 102.80 | 103.26 | 101.00 |   1.78 |  -0.01 |      百度 |      百度 |      百度 |      百度 |
|    3 | 2019-10-03 | BABA | 169.48 | 166.65 | 170.18 | 165.00 |  10.39 |   0.02 |  阿里巴巴 |  阿里巴巴 |  阿里巴巴 |  阿里巴巴 |
|    4 | 2019-10-02 | BABA | 165.77 | 162.82 | 166.88 | 161.90 |  11.60 |   0.00 |  阿里巴巴 |  阿里巴巴 |  阿里巴巴 |  阿里巴巴 |

### 3. applymap用于DataFrame所有值的转换

In [12]:

```python
sub_df = stocks[['收盘', '开盘', '高', '低', '交易量']]
```

In [13]:

```python
sub_df.head()
```

Out[13]:

|      |   收盘 |   开盘 |     高 |     低 | 交易量 |
| ---: | -----: | -----: | -----: | -----: | -----: |
|    0 | 104.32 | 102.35 | 104.73 | 101.15 |   2.24 |
|    1 | 102.62 | 100.85 | 103.24 |  99.50 |   2.69 |
|    2 | 102.00 | 102.80 | 103.26 | 101.00 |   1.78 |
|    3 | 169.48 | 166.65 | 170.18 | 165.00 |  10.39 |
|    4 | 165.77 | 162.82 | 166.88 | 161.90 |  11.60 |

In [14]:

```python
# 将这些数字取整数，应用于所有元素
sub_df.applymap(lambda x : int(x))
```

Out[14]:

|      | 收盘 | 开盘 |   高 |   低 | 交易量 |
| ---: | ---: | ---: | ---: | ---: | -----: |
|    0 |  104 |  102 |  104 |  101 |      2 |
|    1 |  102 |  100 |  103 |   99 |      2 |
|    2 |  102 |  102 |  103 |  101 |      1 |
|    3 |  169 |  166 |  170 |  165 |     10 |
|    4 |  165 |  162 |  166 |  161 |     11 |
|    5 |  165 |  168 |  168 |  163 |     14 |
|    6 |   16 |   15 |   16 |   15 |     10 |
|    7 |   15 |   15 |   15 |   15 |      8 |
|    8 |   15 |   16 |   16 |   15 |     11 |
|    9 |   28 |   28 |   28 |   27 |      8 |
|   10 |   28 |   28 |   28 |   27 |      9 |
|   11 |   28 |   28 |   28 |   27 |     10 |

In [15]:

```python
# 直接修改原df的这几列
stocks.loc[:, ['收盘', '开盘', '高', '低', '交易量']] = sub_df.applymap(lambda x : int(x))
```

In [16]:

```python
stocks.head()
```

Out[16]:

|      |       日期 | 公司 | 收盘 | 开盘 |   高 |   低 | 交易量 | 涨跌幅 | 公司中文1 | 公司中文2 | 公司中文3 | 公司中文4 |
| ---: | ---------: | ---: | ---: | ---: | ---: | ---: | -----: | -----: | --------: | --------: | --------: | --------: |
|    0 | 2019-10-03 | BIDU |  104 |  102 |  104 |  101 |      2 |   0.02 |      百度 |      百度 |      百度 |      百度 |
|    1 | 2019-10-02 | BIDU |  102 |  100 |  103 |   99 |      2 |   0.01 |      百度 |      百度 |      百度 |      百度 |
|    2 | 2019-10-01 | BIDU |  102 |  102 |  103 |  101 |      1 |  -0.01 |      百度 |      百度 |      百度 |      百度 |
|    3 | 2019-10-03 | BABA |  169 |  166 |  170 |  165 |     10 |   0.02 |  阿里巴巴 |  阿里巴巴 |  阿里巴巴 |  阿里巴巴 |
|    4 | 2019-10-02 | BABA |  165 |  162 |  166 |  161 |     11 |   0.00 |  阿里巴巴 |  阿里巴巴 |  阿里巴巴 |  阿里巴巴 |