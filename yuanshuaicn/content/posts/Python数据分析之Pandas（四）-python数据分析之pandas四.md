---
title: Python数据分析之Pandas（四）
date: 2021-10-24 18:38:23.971
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
  avatar: "https://aabb-2023.oss-cn-beijing.aliyuncs.com/hjscijg3uw.png"
tags: 
- Python
- 数据分析
---

: | -----: | -----: | ---: | ---------: | -------: |
|    0 |      1 |      F |    1 |         10 |    48067 |
|    1 |      2 |      M |   56 |         16 |    70072 |
|    2 |      3 |      M |   25 |         15 |    55117 |
|    3 |      4 |      M |   45 |          7 |    02460 |
|    4 |      5 |      M |   25 |         20 |    55455 |

In [4]:

```python
df.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 6040 entries, 0 to 6039
Data columns (total 5 columns):
UserID        6040 non-null int64
Gender        6040 non-null object
Age           6040 non-null int64
Occupation    6040 non-null int64
Zip-code      6040 non-null object
dtypes: int64(3), object(2)
memory usage: 236.1+ KB
```

In [5]:

```python
df.info(memory_usage="deep")
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 6040 entries, 0 to 6039
Data columns (total 5 columns):
UserID        6040 non-null int64
Gender        6040 non-null object
Age           6040 non-null int64
Occupation    6040 non-null int64
Zip-code      6040 non-null object
dtypes: int64(3), object(2)
memory usage: 873.4 KB
```

In [6]:

```python
df_cat = df.copy()
df_cat.head()
```

Out[6]:

|      | UserID | Gender |  Age | Occupation | Zip-code |
| ---: | -----: | -----: | ---: | ---------: | -------: |
|    0 |      1 |      F |    1 |         10 |    48067 |
|    1 |      2 |      M |   56 |         16 |    70072 |
|    2 |      3 |      M |   25 |         15 |    55117 |
|    3 |      4 |      M |   45 |          7 |    02460 |
|    4 |      5 |      M |   25 |         20 |    55455 |

### 2、使用categorical类型降低存储量

In [8]:

```python
df_cat["Gender"] = df_cat["Gender"].astype("category")
```

In [9]:

```python
df_cat.info(memory_usage="deep")
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 6040 entries, 0 to 6039
Data columns (total 5 columns):
UserID        6040 non-null int64
Gender        6040 non-null category
Age           6040 non-null int64
Occupation    6040 non-null int64
Zip-code      6040 non-null object
dtypes: category(1), int64(3), object(1)
memory usage: 513.8 KB
```

In [10]:

```
df_cat.head()
```

Out[10]:

|      | UserID | Gender |  Age | Occupation | Zip-code |
| ---: | -----: | -----: | ---: | ---------: | -------: |
|    0 |      1 |      F |    1 |         10 |    48067 |
|    1 |      2 |      M |   56 |         16 |    70072 |
|    2 |      3 |      M |   25 |         15 |    55117 |
|    3 |      4 |      M |   45 |          7 |    02460 |
|    4 |      5 |      M |   25 |         20 |    55455 |

In [11]:

```python
df_cat["Gender"].value_counts()
```

Out[11]:

```python
M    4331
F    1709
Name: Gender, dtype: int64
```

### 3、提升运算速度

In [12]:

```python
%timeit df.groupby("Gender").size()
564 µs ± 10.8 µs per loop (mean ± std. dev. of 7 runs, 1000 loops each)
```

In [13]:

```python
%timeit df_cat.groupby("Gender").size()
324 µs ± 5 µs per loop (mean ± std. dev. of 7 runs, 1000 loops each)
```

# 29、Pandas和Flask配合实现快速在网页上展示表格数据

本次演示是使用PyCharm实现的

在当前目录下有一个子目录就是代码：pandas-flask

打开Pycharm，然后打开pandas-flask这个目录，然后运行app.py就可以启动web服务器

# 30、Pandas的get_dummies用于机器学习的特征处理

分类特征有两种：

- 普通分类：性别、颜色
- 顺序分类：评分、级别

对于评分，可以把这个分类直接转换成1、2、3、4、5表示，因为它们之间有顺序、大小关系

但是对于颜色这种分类，直接用1/2/3/4/5/6/7表达，是不合适的，因为机器学习会误以为这些数字之间有大小关系

get_dummies就是用于颜色、性别这种特征的处理，也叫作one-hot-encoding处理

比如：

- 男性：1 0
- 女性：0 1

这就叫做one-hot-encoding，是机器学习对类别的特征处理

### 1、读取泰坦尼克数据集

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

In [3]:

```python
df_train.drop(columns=["Name", "Ticket", "Cabin"], inplace=True)
df_train.head()
```

Out[3]:

|      | PassengerId | Survived | Pclass |    Sex |  Age | SibSp | Parch |    Fare | Embarked |
| ---: | ----------: | -------: | -----: | -----: | ---: | ----: | ----: | ------: | -------: |
|    0 |           1 |        0 |      3 |   male | 22.0 |     1 |     0 |  7.2500 |        S |
|    1 |           2 |        1 |      1 | female | 38.0 |     1 |     0 | 71.2833 |        C |
|    2 |           3 |        1 |      3 | female | 26.0 |     0 |     0 |  7.9250 |        S |
|    3 |           4 |        1 |      1 | female | 35.0 |     1 |     0 | 53.1000 |        S |
|    4 |           5 |        0 |      3 |   male | 35.0 |     0 |     0 |  8.0500 |        S |

In [4]:

```python
df_train.info()
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

#### 特征说明：

- 数值特征：Fare
- 分类-有序特征：Age
- 分类-普通特征：PassengerId、Pclass、Sex、SibSp、Parch、Embarked

Survived为要预测的Label

### 2、分类有序特征可以用数字的方法处理

In [5]:

```python
# 使用年龄的平均值，填充空值
df_train["Age"] = df_train["Age"].fillna(df_train["Age"].mean())
```

In [6]:

```python
df_train.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 891 entries, 0 to 890
Data columns (total 9 columns):
PassengerId    891 non-null int64
Survived       891 non-null int64
Pclass         891 non-null int64
Sex            891 non-null object
Age            891 non-null float64
SibSp          891 non-null int64
Parch          891 non-null int64
Fare           891 non-null float64
Embarked       889 non-null object
dtypes: float64(2), int64(5), object(2)
memory usage: 62.8+ KB
```

### 3、普通无序分类特征可以用get_dummies编码

其实就是one-hot编码

In [7]:

```python
# series
pd.get_dummies(df_train["Sex"]).head()
```

Out[7]:

|      | female | male |
| ---: | -----: | ---: |
|    0 |      0 |    1 |
|    1 |      1 |    0 |
|    2 |      1 |    0 |
|    3 |      1 |    0 |
|    4 |      0 |    1 |

***注意，One-hot-Encoding一般要去掉一列，不然会出现dummy variable trap，因为一个人不是male就是femal，它俩有推导关系\***https://www.geeksforgeeks.org/ml-dummy-variable-trap-in-regression-models/

In [8]:

```python
# 便捷方法，用df全部替换
needcode_cat_columns = ["Pclass","Sex","SibSp","Parch","Embarked"]
df_coded = pd.get_dummies(
    df_train,
    # 要转码的列
    columns=needcode_cat_columns,
    # 生成的列名的前缀
    prefix=needcode_cat_columns,
    # 把空值也做编码
    dummy_na=True,
    # 把1 of k移除（dummy variable trap）
    drop_first=True
)
```

In [9]:

```
df_coded.head()
```

Out[9]:

|      | PassengerId | Survived |  Age |    Fare | Pclass_2.0 | Pclass_3.0 | Pclass_nan | Sex_male | Sex_nan | SibSp_1.0 |  ... | Parch_1.0 | Parch_2.0 | Parch_3.0 | Parch_4.0 | Parch_5.0 | Parch_6.0 | Parch_nan | Embarked_Q | Embarked_S | Embarked_nan |
| ---: | ----------: | -------: | ---: | ------: | ---------: | ---------: | ---------: | -------: | ------: | --------: | ---: | --------: | --------: | --------: | --------: | --------: | --------: | --------: | ---------: | ---------: | -----------: |
|    0 |           1 |        0 | 22.0 |  7.2500 |          0 |          1 |          0 |        1 |       0 |         1 |  ... |         0 |         0 |         0 |         0 |         0 |         0 |         0 |          0 |          1 |            0 |
|    1 |           2 |        1 | 38.0 | 71.2833 |          0 |          0 |          0 |        0 |       0 |         1 |  ... |         0 |         0 |         0 |         0 |         0 |         0 |         0 |          0 |          0 |            0 |
|    2 |           3 |        1 | 26.0 |  7.9250 |          0 |          1 |          0 |        0 |       0 |         0 |  ... |         0 |         0 |         0 |         0 |         0 |         0 |         0 |          0 |          1 |            0 |
|    3 |           4 |        1 | 35.0 | 53.1000 |          0 |          0 |          0 |        0 |       0 |         1 |  ... |         0 |         0 |         0 |         0 |         0 |         0 |         0 |          0 |          1 |            0 |
|    4 |           5 |        0 | 35.0 |  8.0500 |          0 |          1 |          0 |        1 |       0 |         0 |  ... |         0 |         0 |         0 |         0 |         0 |         0 |         0 |          0 |          1 |            0 |

5 rows × 26 columns

### 4、机器学习模型训练

In [10]:

```python
y = df_coded.pop("Survived")
y.head()
```

Out[10]:

```python
0    0
1    1
2    1
3    1
4    0
Name: Survived, dtype: int64
```

In [11]:

```
X = df_coded
X.head()
```

Out[11]:

|      | PassengerId |  Age |    Fare | Pclass_2.0 | Pclass_3.0 | Pclass_nan | Sex_male | Sex_nan | SibSp_1.0 | SibSp_2.0 |  ... | Parch_1.0 | Parch_2.0 | Parch_3.0 | Parch_4.0 | Parch_5.0 | Parch_6.0 | Parch_nan | Embarked_Q | Embarked_S | Embarked_nan |
| ---: | ----------: | ---: | ------: | ---------: | ---------: | ---------: | -------: | ------: | --------: | --------: | ---: | --------: | --------: | --------: | --------: | --------: | --------: | --------: | ---------: | ---------: | -----------: |
|    0 |           1 | 22.0 |  7.2500 |          0 |          1 |          0 |        1 |       0 |         1 |         0 |  ... |         0 |         0 |         0 |         0 |         0 |         0 |         0 |          0 |          1 |            0 |
|    1 |           2 | 38.0 | 71.2833 |          0 |          0 |          0 |        0 |       0 |         1 |         0 |  ... |         0 |         0 |         0 |         0 |         0 |         0 |         0 |          0 |          0 |            0 |
|    2 |           3 | 26.0 |  7.9250 |          0 |          1 |          0 |        0 |       0 |         0 |         0 |  ... |         0 |         0 |         0 |         0 |         0 |         0 |         0 |          0 |          1 |            0 |
|    3 |           4 | 35.0 | 53.1000 |          0 |          0 |          0 |        0 |       0 |         1 |         0 |  ... |         0 |         0 |         0 |         0 |         0 |         0 |         0 |          0 |          1 |            0 |
|    4 |           5 | 35.0 |  8.0500 |          0 |          1 |          0 |        1 |       0 |         0 |         0 |  ... |         0 |         0 |         0 |         0 |         0 |         0 |         0 |          0 |          1 |            0 |

5 rows × 25 columns

In [12]:

```python
from sklearn.linear_model import LogisticRegression
# 创建模型对象
logreg = LogisticRegression(solver='liblinear')

# 实现模型训练
logreg.fit(X, y)
```

Out[12]:

```python
LogisticRegression(C=1.0, class_weight=None, dual=False, fit_intercept=True,
                   intercept_scaling=1, l1_ratio=None, max_iter=100,
                   multi_class='warn', n_jobs=None, penalty='l2',
                   random_state=None, solver='liblinear', tol=0.0001, verbose=0,
                   warm_start=False)
```

In [13]:

```python
logreg.score(X, y)
```

Out[13]:

```python
0.8148148148148148
```

# 31、Pandas使用explode实现一行变多行统计

解决实际问题：一个字段包含多个值，怎样将这个值拆分成多行，然后实现统计

比如：一个电影有多个分类、一个人有多个喜好，需要按分类、喜好做统计

### 1、读取数据

In [1]:

```python
import pandas as pd
```

In [2]:

```python
df = pd.read_csv(
    "./datas/movielens-1m/movies.dat",
    header=None,
    names="MovieID::Title::Genres".split("::"),
    sep="::",
    engine="python"
)
```

In [3]:

```python
df.head()
```

Out[3]:

|      | MovieID |                              Title |                         Genres |
| ---: | ------: | ---------------------------------: | -----------------------------: |
|    0 |       1 |                   Toy Story (1995) |  Animation\|Children's\|Comedy |
|    1 |       2 |                     Jumanji (1995) | Adventure\|Children's\|Fantasy |
|    2 |       3 |            Grumpier Old Men (1995) |                Comedy\|Romance |
|    3 |       4 |           Waiting to Exhale (1995) |                  Comedy\|Drama |
|    4 |       5 | Father of the Bride Part II (1995) |                         Comedy |

***问题：怎样实现这样的统计，每个题材有多少部电影？\***

解决思路：

- 将Genres按照分隔符|拆分
- 按Genres拆分成多行
- 统计每个Genres下的电影数目

### 2、将Genres字段拆分成列表

In [4]:

```python
df.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 3883 entries, 0 to 3882
Data columns (total 3 columns):
MovieID    3883 non-null int64
Title      3883 non-null object
Genres     3883 non-null object
dtypes: int64(1), object(2)
memory usage: 91.1+ KB
```

In [5]:

```python
# 当前的Genres字段是字符串类型
type(df.iloc[0]["Genres"])
```

Out[5]:

```python
str
```

In [6]:

```python
# 新增一列
df["Genre"] = df["Genres"].map(lambda x:x.split("|"))
```

In [7]:

```python
df.head()
```

Out[7]:

|      | MovieID |                              Title |                         Genres |                            Genre |
| ---: | ------: | ---------------------------------: | -----------------------------: | -------------------------------: |
|    0 |       1 |                   Toy Story (1995) |  Animation\|Children's\|Comedy |  [Animation, Children's, Comedy] |
|    1 |       2 |                     Jumanji (1995) | Adventure\|Children's\|Fantasy | [Adventure, Children's, Fantasy] |
|    2 |       3 |            Grumpier Old Men (1995) |                Comedy\|Romance |                [Comedy, Romance] |
|    3 |       4 |           Waiting to Exhale (1995) |                  Comedy\|Drama |                  [Comedy, Drama] |
|    4 |       5 | Father of the Bride Part II (1995) |                         Comedy |                         [Comedy] |

In [8]:

```python
# Genre的类型是列表
print(df["Genre"][0])
print(type(df["Genre"][0]))
['Animation', "Children's", 'Comedy']
<class 'list'>
```

In [9]:

```python
df.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 3883 entries, 0 to 3882
Data columns (total 4 columns):
MovieID    3883 non-null int64
Title      3883 non-null object
Genres     3883 non-null object
Genre      3883 non-null object
dtypes: int64(1), object(3)
memory usage: 121.5+ KB
```

### 3、使用explode将一行拆分成多行

语法：pandas.DataFrame.explode(column)
将dataframe的一个list-like的元素按行复制，index索引随之复制

In [10]:

```python
df_new = df.explode("Genre")
```

In [11]:

```python
df_new.head(10)
```

Out[11]:

|      | MovieID |                    Title |                         Genres |      Genre |
| ---: | ------: | -----------------------: | -----------------------------: | ---------: |
|    0 |       1 |         Toy Story (1995) |  Animation\|Children's\|Comedy |  Animation |
|    0 |       1 |         Toy Story (1995) |  Animation\|Children's\|Comedy | Children's |
|    0 |       1 |         Toy Story (1995) |  Animation\|Children's\|Comedy |     Comedy |
|    1 |       2 |           Jumanji (1995) | Adventure\|Children's\|Fantasy |  Adventure |
|    1 |       2 |           Jumanji (1995) | Adventure\|Children's\|Fantasy | Children's |
|    1 |       2 |           Jumanji (1995) | Adventure\|Children's\|Fantasy |    Fantasy |
|    2 |       3 |  Grumpier Old Men (1995) |                Comedy\|Romance |     Comedy |
|    2 |       3 |  Grumpier Old Men (1995) |                Comedy\|Romance |    Romance |
|    3 |       4 | Waiting to Exhale (1995) |                  Comedy\|Drama |     Comedy |
|    3 |       4 | Waiting to Exhale (1995) |                  Comedy\|Drama |      Drama |

### 4、实现拆分后的题材的统计

In [12]:

```python
%matplotlib inline
df_new["Genre"].value_counts().plot.bar()
```

Out[12]:

```python
<matplotlib.axes._subplots.AxesSubplot at 0x23d73917cc8>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_pandas/25.png)

# 32、Pandas借助Python爬虫读取HTML网页表格存储到Excel文件

实现目标：

- 网易有道词典可以用于英语单词查询，可以将查询的单词加入到单词本;
- 当前没有导出全部单词列表的功能。为了复习方便，可以爬取所有的单词列表，存入Excel方便复习

涉及技术：

- Pandas：Python语言最强大的数据处理和数据分析库
- Python爬虫：可以将网页下载下来然后解析，使用requests库实现，需要绕过登录验证

In [1]:

```python
import requests
import requests.cookies
import json
import time
import pandas as pd
```

### 0. 处理流程

#### 输入网页：有道词典-单词本

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_pandas/26.png)

#### 处理流程

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_pandas/27.png)

#### 数据结果到Excel文件（方便打印复习）：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_pandas/28.png)

### 1. 登录网易有道词典的PC版，微信扫码登录，复制cookies到文件

- PC版地址：http://dict.youdao.com/
- Chrome插件可以复制Cookies为Json格式：http://www.editthiscookie.com/

In [2]:

```python
cookie_jar = requests.cookies.RequestsCookieJar()

with open("./course_datas/c32_read_html/cookie.txt") as fin:
    cookiejson = json.loads(fin.read())
    for cookie in cookiejson:
        cookie_jar.set(
            name=cookie["name"],
            value=cookie["value"],
            domain=cookie["domain"],
            path=cookie["path"]
        )
```

In [3]:

```python
cookie_jar
```

Out[3]:

```
<RequestsCookieJar[Cookie(version=0, name='DICT_LOGIN', value='3||1578922508302', port=None, port_specified=False, domain='.youdao.com', domain_specified=True, domain_initial_dot=True, path='/', path_specified=True, secure=False, expires=None, discard=True, comment=None, comment_url=None, rest={'HttpOnly': None}, rfc2109=False), Cookie(version=0, name='DICT_PERS', value='v2|weixin||DICT||web||2592000000||1578922508299||114.244.161.198||wxoXQUDj_FtHSw23tfJWsboPkq38ok||gFnMeLRLQLRpBOMYMhf6LRUf0Mz5P4TLRqSOM6uhfY5RzW0L6ZhHTB0kGRHeukLg40QZOMOMkMwu0gBkfJF0LTL0', port=None, port_specified=False, domain='.youdao.com', domain_specified=True, domain_initial_dot=True, path='/', path_specified=True, secure=False, expires=None, discard=True, comment=None, comment_url=None, rest={'HttpOnly': None}, rfc2109=False), Cookie(version=0, name='DICT_SESS', value='v2|odmTRIUgTmgz6MlEOMqB0TBnfk5h4pZ0Py0MeBP4Q40qynHeuPMOWRpLPMY5RHJuRQykfJBOLQBRPKO4YYOLquR6zhLwBnMYMR', port=None, port_specified=False, domain='.youdao.com', domain_specified=True, domain_initial_dot=True, path='/', path_specified=True, secure=False, expires=None, discard=True, comment=None, comment_url=None, rest={'HttpOnly': None}, rfc2109=False), Cookie(version=0, name='DICT_UGC', value='be3af0da19b5c5e6aa4e17bd8d90b28a|', port=None, port_specified=False, domain='.youdao.com', domain_specified=True, domain_initial_dot=True, path='/', path_specified=True, secure=False, expires=None, discard=True, comment=None, comment_url=None, rest={'HttpOnly': None}, rfc2109=False), Cookie(version=0, name='JSESSIONID', value='abc46uQPL03Au_P0ghF_w', port=None, port_specified=False, domain='.youdao.com', domain_specified=True, domain_initial_dot=True, path='/', path_specified=True, secure=False, expires=None, discard=True, comment=None, comment_url=None, rest={'HttpOnly': None}, rfc2109=False), Cookie(version=0, name='OUTFOX_SEARCH_USER_ID', value='"1678365514@10.108.160.18"', port=None, port_specified=False, domain='.youdao.com', domain_specified=True, domain_initial_dot=True, path='/', path_specified=True, secure=False, expires=None, discard=True, comment=None, comment_url=None, rest={'HttpOnly': None}, rfc2109=False), Cookie(version=0, name='OUTFOX_SEARCH_USER_ID_NCOO', value='1349541628.6994112', port=None, port_specified=False, domain='.youdao.com', domain_specified=True, domain_initial_dot=True, path='/', path_specified=True, secure=False, expires=None, discard=True, comment=None, comment_url=None, rest={'HttpOnly': None}, rfc2109=False), Cookie(version=0, name='ACCSESSIONID', value='8F00E30693F3BD052C9A4F293394BE0A', port=None, port_specified=False, domain='dict.youdao.com', domain_specified=True, domain_initial_dot=False, path='/', path_specified=True, secure=False, expires=None, discard=True, comment=None, comment_url=None, rest={'HttpOnly': None}, rfc2109=False), Cookie(version=0, name='___rl__test__cookies', value='1578922438675', port=None, port_specified=False, domain='dict.youdao.com', domain_specified=True, domain_initial_dot=False, path='/', path_specified=True, secure=False, expires=None, discard=True, comment=None, comment_url=None, rest={'HttpOnly': None}, rfc2109=False)]>
```

### 2. 将html都下载下来存入列表

In [4]:

```python
htmls = []
url = "http://dict.youdao.com/wordbook/wordlist?p={idx}&tags="
for idx in range(6):
    time.sleep(1)
    print("**爬数据：第%d页" % idx)
    r = requests.get(url.format(idx=idx), cookies=cookie_jar)
    htmls.append(r.text)
**爬数据：第0页
**爬数据：第1页
**爬数据：第2页
**爬数据：第3页
**爬数据：第4页
**爬数据：第5页
```

In [5]:

```python
htmls[0]
```

Out[5]:

```
'<!doctype html>\n<html>\n<head>\n<meta http-equiv="Content-Type" content="text/html; charset=UTF-8"/>\n<title>有道单词本</title>\n\n<link rel="canonical" href="http://dict.youdao.com/wordbook/"/> \n<meta name="Keywords" content="单词本,web单词本,有道,词典,youdao" />\n<meta name="Description" content="有道词典单词本" />\n<link rel="shortcut icon" href="http://shared.ydstatic.com/images/favicon.ico?213" type="image/x-icon"/>\n<link href="http://shared.ydstatic.com/r/1.0/s/g3.css?20110428" rel="stylesheet" type="text/css"/>\n<link type="text/css" href="resources/styles/main.css" rel="stylesheet">\n\n<style type="text/css">\n\n#f{background-image:url(http://shared.ydstatic.com/images/skins/default/skin-x.jpg)}\n#fbl{background:url(http://shared.ydstatic.com/images/skins/default/skin_.jpg) left top}\n#fbr{background:url(http://shared.ydstatic.com/images/skins/default/skin_.jpg) right -200px}\n\n</style>\n<script type="text/javascript">\nvar VARIABLES={ \n                tags:"",\n                page:"0",\n                sort:"",\n                querystring:""\n        };\n</script>\n\n\n</head>\n\n<body>\n\n<div id="t">\n    <div id="u">\n                    <span id="un">\n        <span class="un_n">晚上好，</span>\n        <span id="mun" class="un_box"><b class="un_l"><q></q></b><b class="un_r"><q></q></b>\n                 <span class="un_btn"><b class="un_m">&nbsp;<q></q></b>\n               <span class="un_ml">\n                    wxoXQUDj_FtHSw23tfJWsboPkq38ok\n                                  </span>\n                                    </span>\n            </span>\n       </span>\n                       <span class="sl">|</span>\n                            <a href="http://account.youdao.com/logout?service=dict&back_url=http%3A%2F%2Fdict.youdao.com%2Fwordbook%2Fwordlist">登出</a>\n            </div>\n    <div id="n">\n        <a href="http://www.163.com/" id="mn" class="mn" target="_blank"><u>网易</u><s>▼</s></a>\n        <span class="sl">|</span>\n        <a class="search-js" data-product=\'www\' href="http://www.youdao.com">网页</a>\n        <a class="search-js" data-product=\'image\' href="http://image.youdao.com">图片</a>\n        <a class="search-js" data-product=\'news\' href="http://news.youdao.com">热闻</a>\n        <a class="search-js" data-product=\'gouwu\' href="http://gouwu.youdao.com">购物</a>\n        <a class="search-js" data-product=\'dict\' href="http://dict.youdao.com">词典</a>\n        <a class="search-js" data-product=\'fanyi\' data-trans=\'translate?i=\' href="http://fanyi.youdao.com/">翻译</a>\n        <a class="search-js" data-product=\'note\' href="http://note.youdao.com">笔记</a>\n        <strong>单词本</strong>\n\t<a class="mn" target="_blank" href="http://www.youdao.com/about/productlist.html"><u>更多»</u></a>\n    </div>\n    </div>\n\n\n<div id="ym" class="pm">\n    <ul>\n        <li><a href="http://video.youdao.com" class="search-js" data-product=\'video\'>视频</a></li>\n        <li><a href="http://blog.youdao.com/" class="search-js" data-product=\'blog\'>博客</a></li>\n        <li><a href="http://tie.youdao.com/" class="search-js" data-product=\'tie\'>快贴</a></li>\n        <li><a href="http://ditu.youdao.com/" class="search-js" data-product=\'ditu\'>地图</a></li>\n\n        <li class="sl"></li>\n        <li><a href="http://reader.youdao.com">阅读</a></li>\n        <li><a href="http://m.youdao.com/help">手机</a></li>\n        <li><a href="http://shuqian.youdao.com">书签</a></li>\n        <li><a href="http://cidian.youdao.com" class="search-js" data-product=\'cidian\'>桌面词典</a></li>\n        <li class="sl"></li>\n        <li><a href="http://www.youdao.com/about/productlist.html">全部产品</a></li>\n\n    </ul>\n</div>\n<div id="nm" class="pm">\n    <ul>\n        <li><a href="http://www.163.com/" target="_blank">首页</a></li>\n        <li><a href="http://news.163.com/" target="_blank">新闻</a></li>\n        <li><a href="http://email.163.com/" target="_blank">邮箱</a></li>\n        <li><a href="http://blog.163.com/" target="_blank">博客</a></li>\n\n        <li><a href="http://photo.163.com/" target="_blank">相册</a></li>\n        <li><a href="http://nie.163.com/" target="_blank">游戏</a></li>\n        <li class="sl"></li>\n        <li><a href="http://sitemap.163.com/" target="_blank">全部产品</a></li>\n    </ul>\n</div>\n\n\n<!-- 图标与搜索框 -->\n<form id="f" method="get" action="#" name="sb">\n  <h1 id="yd"><a href="/wordbook/wordlist">有道单词本</a></h1>\n   <!--<div id="ts" class="fc">\n \n    <div class="qc no-suggest" id="qc">\n      <input name="tab" value="chn" type="hidden">\n      <input name="keyfrom" value="shuqian.top" type="hidden">\n      <input type="text" class="q" name="q" id="query" autocomplete="off"  value=""/>\n    </div>\n    <input type="submit" value="搜 索" class="qb" name="btnSearchTag"/>\n    \n  </div>-->\n  <div class="ao"></div>\n  <div id="fbl"> </div>\n  <div id="fbr"> </div>\n</form> \n \n\n<div id="wrapper">\n\n\n    <div id="top" >\n        \n\n                <a href="#" id="addword"></a>\n\n                \n              \n            <div style="width:500px;float:right;text-align:right;">    \n                <label for="select_category">分类</label>\n                <select id="select_category">\n                    <option value="">全部分类</option>\n                                            <option value="无标签" >无标签 </option>\n                                    </select>  \n                        \n        <a href="#" id="toggle_listmode" class="active"></a><a href="#" id="toggle_cardmode" ></a>\n        </div>\n        <div class="clear"></div>\n\n    </div>   \n    \n    <div id="listmode">\n               <div id="wordhead">\n            <table  width="100%" style="table-layout:fixed;background:#fff;">\n                    <tr>\n                        <th width="50px">序号</th>\n                        <th width="80px">单词</th>\n                        <th width="80px">音标</th>\n                        <th width="320px">解释</th>\n                       <!--  <th width="50px">难度</th> -->\n                        <th width="85px">时间</th>\n                        <th>分类</th>\n                        <th width="65px">操作</th>\n                    </tr>\n            </table>\n        </div> \n        \n        <div id="wordlist" >\n            <table  width="100%" style="table-layout:fixed">\n\n                <tbody>\n                                        <tr>\n                        <td width="50px"> 1</td>\n                        <td width="80px"><div class="word"  title="agglomerative"><a href="/search?keyfrom=webwordbook&q=agglomerative"  target="_blank"><strong>agglomerative</strong></a></div></td>\n                        <td width="80px"><div class="phonetic"  title=""></div></td>\n                        <td width="320px">\n                            <div  class="desc"  title="adj. 会凝聚的；[冶] 烧结的，凝结的">adj. 会凝聚的；[冶] 烧结的，凝结的</div>\n                        </td>\n                        <!-- <td width="50px">\n                            <span class="flag" style="display:none;">0</span>\n                            <span class="level">\n                                                        </span>\n                        </td> -->\n\n                        <td width="85px">2020-1-13</td>\n                        <td >\n                            <div  class="tags" title=""></div>\n                        </td>\n                        <td width="65px" style="vertical-align:middle;">\n                            <a href="#" class="editword"  title="编辑agglomerative" ></a>\n                            \n                           \n                            <a href=\n                                                        "wordlist?action=delete&word=agglomerative&p=0" \n                                                        class="deleteword" title="删除agglomerative" onclick=\'if(!confirm("您确定删除单词 agglomerative 吗？")){ return false;}else return true;\'></a>\n                        </td>\n                    </tr>\n                                        <tr>\n                        <td width="50px"> 2</td>\n                        <td width="80px"><div class="word"  title="anatomy"><a href="/search?keyfrom=webwordbook&q=anatomy"  target="_blank"><strong>anatomy</strong></a></div></td>\n                        <td width="80px"><div class="phonetic"  title="[ə&#39;nætəmɪ]">[ə&#39;nætəmɪ]</div></td>\n                        <td width="320px">\n                            <div  class="desc"  title="n. 解剖；解剖学；剖析；骨骼">n. 解剖；解剖学；剖析；骨骼</div>\n                        </td>\n                        <!-- <td width="50px">\n                            <span class="flag" style="display:none;">0</span>\n                            <span class="level">\n                                                        </span>\n                        </td> -->\n\n                        <td width="85px">2017-7-17</td>\n                        <td >\n                            <div  class="tags" title=""></div>\n                        </td>\n                        <td width="65px" style="vertical-align:middle;">\n                            <a href="#" class="editword"  title="编辑anatomy" ></a>\n                            \n                           \n                            <a href=\n                                                        "wordlist?action=delete&word=anatomy&p=0" \n                                                        class="deleteword" title="删除anatomy" onclick=\'if(!confirm("您确定删除单词 anatomy 吗？")){ return false;}else return true;\'></a>\n                        </td>\n                    </tr>\n                                        <tr>\n                        <td width="50px"> 3</td>\n                        <td width="80px"><div class="word"  title="backbone"><a href="/search?keyfrom=webwordbook&q=backbone"  target="_blank"><strong>backbone</strong></a></div></td>\n                        <td width="80px"><div class="phonetic"  title="[&#39;bækbəʊn]">[&#39;bækbəʊn]</div></td>\n                        <td width="320px">\n                            <div  class="desc"  title="n. 支柱;主干网;决心,毅力;脊椎">n. 支柱;主干网;决心,毅力;脊椎</div>\n                        </td>\n                        <!-- <td width="50px">\n                            <span class="flag" style="display:none;">0</span>\n                            <span class="level">\n                                                        </span>\n                        </td> -->\n\n                        <td width="85px">2017-7-13</td>\n                        <td >\n                            <div  class="tags" title=""></div>\n                        </td>\n                        <td width="65px" style="vertical-align:middle;">\n                            <a href="#" class="editword"  title="编辑backbone" ></a>\n                            \n                           \n                            <a href=\n                                                        "wordlist?action=delete&word=backbone&p=0" \n                                                        class="deleteword" title="删除backbone" onclick=\'if(!confirm("您确定删除单词 backbone 吗？")){ return false;}else return true;\'></a>\n                        </td>\n                    </tr>\n                                        <tr>\n                        <td width="50px"> 4</td>\n                        <td width="80px"><div class="word"  title="ballpark"><a href="/search?keyfrom=webwordbook&q=ballpark"  target="_blank"><strong>ballpark</strong></a></div></td>\n                        <td width="80px"><div class="phonetic"  title="[&#39;bɔːlpɑːk]">[&#39;bɔːlpɑːk]</div></td>\n                        <td width="320px">\n                            <div  class="desc"  title="n. (美)棒球场;活动领域;可变通范围\nadj. 大约的">n. (美)棒球场;活动领域;可变通范围\nadj. 大约的</div>\n                        </td>\n                        <!-- <td width="50px">\n                            <span class="flag" style="display:none;">0</span>\n                            <span class="level">\n                                                        </span>\n                        </td> -->\n\n                        <td width="85px">2019-10-16</td>\n                        <td >\n                            <div  class="tags" title=""></div>\n                        </td>\n                        <td width="65px" style="vertical-align:middle;">\n                            <a href="#" class="editword"  title="编辑ballpark" ></a>\n                            \n                           \n                            <a href=\n                                                        "wordlist?action=delete&word=ballpark&p=0" \n                                                        class="deleteword" title="删除ballpark" onclick=\'if(!confirm("您确定删除单词 ballpark 吗？")){ return false;}else return true;\'></a>\n                        </td>\n                    </tr>\n                                        <tr>\n                        <td width="50px"> 5</td>\n                        <td width="80px"><div class="word"  title="bilingual"><a href="/search?keyfrom=webwordbook&q=bilingual"  target="_blank"><strong>bilingual</strong></a></div></td>\n                        <td width="80px"><div class="phonetic"  title="[baɪ&#39;lɪŋgw(ə)l]">[baɪ&#39;lɪŋgw(ə)l]</div></td>\n                        <td width="320px">\n                            <div  class="desc"  title="adj. 双语的\nn. 通两种语言的人">adj. 双语的\nn. 通两种语言的人</div>\n                        </td>\n                        <!-- <td width="50px">\n                            <span class="flag" style="display:none;">0</span>\n                            <span class="level">\n                                                        </span>\n                        </td> -->\n\n                        <td width="85px">2019-10-15</td>\n                        <td >\n                            <div  class="tags" title=""></div>\n                        </td>\n                        <td width="65px" style="vertical-align:middle;">\n                            <a href="#" class="editword"  title="编辑bilingual" ></a>\n                            \n                           \n                            <a href=\n                                                        "wordlist?action=delete&word=bilingual&p=0" \n                                                        class="deleteword" title="删除bilingual" onclick=\'if(!confirm("您确定删除单词 bilingual 吗？")){ return false;}else return true;\'></a>\n                        </td>\n                    </tr>\n                                        <tr>\n                        <td width="50px"> 6</td>\n                        <td width="80px"><div class="word"  title="canonical"><a href="/search?keyfrom=webwordbook&q=canonical"  target="_blank"><strong>canonical</strong></a></div></td>\n                        <td width="80px"><div class="phonetic"  title="[kə&#39;nɒnɪk(ə)l]">[kə&#39;nɒnɪk(ə)l]</div></td>\n                        <td width="320px">\n                            <div  class="desc"  title="adj. 依教规的;权威的;牧师的\nn. 牧师礼服">adj. 依教规的;权威的;牧师的\nn. 牧师礼服</div>\n                        </td>\n                        <!-- <td width="50px">\n                            <span class="flag" style="display:none;">0</span>\n                            <span class="level">\n                                                        </span>\n                        </td> -->\n\n                        <td width="85px">2019-10-14</td>\n                        <td >\n                            <div  class="tags" title=""></div>\n                        </td>\n                        <td width="65px" style="vertical-align:middle;">\n                            <a href="#" class="editword"  title="编辑canonical" ></a>\n                            \n                           \n                            <a href=\n                                                        "wordlist?action=delete&word=canonical&p=0" \n                                                        class="deleteword" title="删除canonical" onclick=\'if(!confirm("您确定删除单词 canonical 吗？")){ return false;}else return true;\'></a>\n                        </td>\n                    </tr>\n                                        <tr>\n                        <td width="50px"> 7</td>\n                        <td width="80px"><div class="word"  title="cater"><a href="/search?keyfrom=webwordbook&q=cater"  target="_blank"><strong>cater</strong></a></div></td>\n                        <td width="80px"><div class="phonetic"  title="[&#39;keɪtə]">[&#39;keɪtə]</div></td>\n                        <td width="320px">\n                            <div  class="desc"  title="vt. 投合，迎合；满足需要；提供饮食及服务\nn. (Cater)人名；(英)凯特">vt. 投合，迎合；满足需要；提供饮食及服务\nn. (Cater)人名；(英)凯特</div>\n                        </td>\n                        <!-- <td width="50px">\n                            <span class="flag" style="display:none;">0</span>\n                            <span class="level">\n                                                        </span>\n                        </td> -->\n\n                        <td width="85px">2017-7-17</td>\n                        <td >\n                            <div  class="tags" title=""></div>\n                        </td>\n                        <td width="65px" style="vertical-align:middle;">\n                            <a href="#" class="editword"  title="编辑cater" ></a>\n                            \n                           \n                            <a href=\n                                                        "wordlist?action=delete&word=cater&p=0" \n                                                        class="deleteword" title="删除cater" onclick=\'if(!confirm("您确定删除单词 cater 吗？")){ return false;}else return true;\'></a>\n                        </td>\n                    </tr>\n                                        <tr>\n                        <td width="50px"> 8</td>\n                        <td width="80px"><div class="word"  title="clarity"><a href="/search?keyfrom=webwordbook&q=clarity"  target="_blank"><strong>clarity</strong></a></div></td>\n                        <td width="80px"><div class="phonetic"  title="[&#39;klærɪtɪ]">[&#39;klærɪtɪ]</div></td>\n                        <td width="320px">\n                            <div  class="desc"  title="n. 清楚,明晰;透明\nn. (Clarity)人名;(英)克拉里蒂">n. 清楚,明晰;透明\nn. (Clarity)人名;(英)克拉里蒂</div>\n                        </td>\n                        <!-- <td width="50px">\n                            <span class="flag" style="display:none;">0</span>\n                            <span class="level">\n                                                        </span>\n                        </td> -->\n\n                        <td width="85px">2019-10-16</td>\n                        <td >\n                            <div  class="tags" title=""></div>\n                        </td>\n                        <td width="65px" style="vertical-align:middle;">\n                            <a href="#" class="editword"  title="编辑clarity" ></a>\n                            \n                           \n                            <a href=\n                                                        "wordlist?action=delete&word=clarity&p=0" \n                                                        class="deleteword" title="删除clarity" onclick=\'if(!confirm("您确定删除单词 clarity 吗？")){ return false;}else return true;\'></a>\n                        </td>\n                    </tr>\n                                        <tr>\n                        <td width="50px"> 9</td>\n                        <td width="80px"><div class="word"  title="compression"><a href="/search?keyfrom=webwordbook&q=compression"  target="_blank"><strong>compression</strong></a></div></td>\n                        <td width="80px"><div class="phonetic"  title="[kəm&#39;preʃ(ə)n]">[kəm&#39;preʃ(ə)n]</div></td>\n                        <td width="320px">\n                            <div  class="desc"  title="n. 压缩,浓缩;压榨,压迫">n. 压缩,浓缩;压榨,压迫</div>\n                        </td>\n                        <!-- <td width="50px">\n                            <span class="flag" style="display:none;">0</span>\n                            <span class="level">\n                                                        </span>\n                        </td> -->\n\n                        <td width="85px">2019-10-15</td>\n                        <td >\n                            <div  class="tags" title=""></div>\n                        </td>\n                        <td width="65px" style="vertical-align:middle;">\n                            <a href="#" class="editword"  title="编辑compression" ></a>\n                            \n                           \n                            <a href=\n                                                        "wordlist?action=delete&word=compression&p=0" \n                                                        class="deleteword" title="删除compression" onclick=\'if(!confirm("您确定删除单词 compression 吗？")){ return false;}else return true;\'></a>\n                        </td>\n                    </tr>\n                                        <tr>\n                        <td width="50px"> 10</td>\n                        <td width="80px"><div class="word"  title="contaminated"><a href="/search?keyfrom=webwordbook&q=contaminated"  target="_blank"><strong>contaminated</strong></a></div></td>\n                        <td width="80px"><div class="phonetic"  title=""></div></td>\n                        <td width="320px">\n                            <div  class="desc"  title="adj. 受污染的，弄脏的 v. 污染；玷污，毒害（contaminate 的过去式和过去分词）">adj. 受污染的，弄脏的 v. 污染；玷污，毒害（contaminate 的过去式和过去分词）</div>\n                        </td>\n                        <!-- <td width="50px">\n                            <span class="flag" style="display:none;">0</span>\n                            <span class="level">\n                                                        </span>\n                        </td> -->\n\n                        <td width="85px">2020-1-13</td>\n                        <td >\n                            <div  class="tags" title=""></div>\n                        </td>\n                        <td width="65px" style="vertical-align:middle;">\n                            <a href="#" class="editword"  title="编辑contaminated" ></a>\n                            \n                           \n                            <a href=\n                                                        "wordlist?action=delete&word=contaminated&p=0" \n                                                        class="deleteword" title="删除contaminated" onclick=\'if(!confirm("您确定删除单词 contaminated 吗？")){ return false;}else return true;\'></a>\n                        </td>\n                    </tr>\n                                        <tr>\n                        <td width="50px"> 11</td>\n                        <td width="80px"><div class="word"  title="counterparts"><a href="/search?keyfrom=webwordbook&q=counterparts"  target="_blank"><strong>counterparts</strong></a></div></td>\n                        <td width="80px"><div class="phonetic"  title="[]">[]</div></td>\n                        <td width="320px">\n                            <div  class="desc"  title="n. （契约）副本（counterpart的复数）；相对物；相对应的人">n. （契约）副本（counterpart的复数）；相对物；相对应的人</div>\n                        </td>\n                        <!-- <td width="50px">\n                            <span class="flag" style="display:none;">0</span>\n                            <span class="level">\n                                                        </span>\n                        </td> -->\n\n                        <td width="85px">2017-7-16</td>\n                        <td >\n                            <div  class="tags" title=""></div>\n                        </td>\n                        <td width="65px" style="vertical-align:middle;">\n                            <a href="#" class="editword"  title="编辑counterparts" ></a>\n                            \n                           \n                            <a href=\n                                                        "wordlist?action=delete&word=counterparts&p=0" \n                                                        class="deleteword" title="删除counterparts" onclick=\'if(!confirm("您确定删除单词 counterparts 吗？")){ return false;}else return true;\'></a>\n                        </td>\n                    </tr>\n                                        <tr>\n                        <td width="50px"> 12</td>\n                        <td width="80px"><div class="word"  title="criteria"><a href="/search?keyfrom=webwordbook&q=criteria"  target="_blank"><strong>criteria</strong></a></div></td>\n                        <td width="80px"><div class="phonetic"  title="[kraɪ&#39;tɪərɪə]">[kraɪ&#39;tɪərɪə]</div></td>\n                        <td width="320px">\n                            <div  class="desc"  title="n. 标准，条件（criterion的复数）">n. 标准，条件（criterion的复数）</div>\n                        </td>\n                        <!-- <td width="50px">\n                            <span class="flag" style="display:none;">0</span>\n                            <span class="level">\n                                                        </span>\n                        </td> -->\n\n                        <td width="85px">2017-7-6</td>\n                        <td >\n                            <div  class="tags" title=""></div>\n                        </td>\n                        <td width="65px" style="vertical-align:middle;">\n                            <a href="#" class="editword"  title="编辑criteria" ></a>\n                            \n                           \n                            <a href=\n                                                        "wordlist?action=delete&word=criteria&p=0" \n                                                        class="deleteword" title="删除criteria" onclick=\'if(!confirm("您确定删除单词 criteria 吗？")){ return false;}else return true;\'></a>\n                        </td>\n                    </tr>\n                                        <tr>\n                        <td width="50px"> 13</td>\n                        <td width="80px"><div class="word"  title="crunch"><a href="/search?keyfrom=webwordbook&q=crunch"  target="_blank"><strong>crunch</strong></a></div></td>\n                        <td width="80px"><div class="phonetic"  title="[krʌntʃ]">[krʌntʃ]</div></td>\n                        <td width="320px">\n                            <div  class="desc"  title="n.咬碎，咬碎声；扎扎地踏\nvt.压碎；嘎扎嘎扎的咬嚼；扎扎地踏过\nvi.嘎吱作响地咀嚼；嘎吱嘎吱地踏过">n.咬碎，咬碎声；扎扎地踏\nvt.压碎；嘎扎嘎扎的咬嚼；扎扎地踏过\nvi.嘎吱作响地咀嚼；嘎吱嘎吱地踏过</div>\n                        </td>\n                        <!-- <td width="50px">\n                            <span class="flag" style="display:none;">0</span>\n                            <span class="level">\n                                                        </span>\n                        </td> -->\n\n                        <td width="85px">2019-10-8</td>\n                        <td >\n                            <div  class="tags" title=""></div>\n                        </td>\n                        <td width="65px" style="vertical-align:middle;">\n                            <a href="#" class="editword"  title="编辑crunch" ></a>\n                            \n                           \n                            <a href=\n                                                        "wordlist?action=delete&word=crunch&p=0" \n                                                        class="deleteword" title="删除crunch" onclick=\'if(!confirm("您确定删除单词 crunch 吗？")){ return false;}else return true;\'></a>\n                        </td>\n                    </tr>\n                                        <tr>\n                        <td width="50px"> 14</td>\n                        <td width="80px"><div class="word"  title="delighted"><a href="/search?keyfrom=webwordbook&q=delighted"  target="_blank"><strong>delighted</strong></a></div></td>\n                        <td width="80px"><div class="phonetic"  title="[dɪ&#39;laɪtɪd]">[dɪ&#39;laɪtɪd]</div></td>\n                        <td width="320px">\n                            <div  class="desc"  title="adj. 高兴的;欣喜的\nv. 使…兴高采烈;感到快乐(delight的过去分词)">adj. 高兴的;欣喜的\nv. 使…兴高采烈;感到快乐(delight的过去分词)</div>\n                        </td>\n                        <!-- <td width="50px">\n                            <span class="flag" style="display:none;">0</span>\n                            <span class="level">\n                                                        </span>\n                        </td> -->\n\n                        <td width="85px">2019-10-16</td>\n                        <td >\n                            <div  class="tags" title=""></div>\n                        </td>\n                        <td width="65px" style="vertical-align:middle;">\n                            <a href="#" class="editword"  title="编辑delighted" ></a>\n                            \n                           \n                            <a href=\n                                                        "wordlist?action=delete&word=delighted&p=0" \n                                                        class="deleteword" title="删除delighted" onclick=\'if(!confirm("您确定删除单词 delighted 吗？")){ return false;}else return true;\'></a>\n                        </td>\n                    </tr>\n                                        <tr>\n                        <td width="50px"> 15</td>\n                        <td width="80px"><div class="word"  title="denominator"><a href="/search?keyfrom=webwordbook&q=denominator"  target="_blank"><strong>denominator</strong></a></div></td>\n                        <td width="80px"><div class="phonetic"  title=""></div></td>\n                        <td width="320px">\n                            <div  class="desc"  title="n. [数] 分母；命名者；共同特征或共同性质；平均水平或标准">n. [数] 分母；命名者；共同特征或共同性质；平均水平或标准</div>\n                        </td>\n                        <!-- <td width="50px">\n                            <span class="flag" style="display:none;">0</span>\n                            <span class="level">\n                                                        </span>\n                        </td> -->\n\n                        <td width="85px">2020-1-13</td>\n                        <td >\n                            <div  class="tags" title=""></div>\n                        </td>\n                        <td width="65px" style="vertical-align:middle;">\n                            <a href="#" class="editword"  title="编辑denominator" ></a>\n                            \n                           \n                            <a href=\n                                                        "wordlist?action=delete&word=denominator&p=0" \n                                                        class="deleteword" title="删除denominator" onclick=\'if(!confirm("您确定删除单词 denominator 吗？")){ return false;}else return true;\'></a>\n                        </td>\n                    </tr>\n                                    </tbody>\n            </table>\n        </div>\n      \n  \n        <div id="wordfoot" >\n            \n                                <div id="pagination">\n                                                             <span class="current-page">1 </span>\n                    \n                    \n                                                                                                                                                                                                                                                    <a href="wordlist?p=1&tags=">2</a> \n                                                                                                                                                                                                                                                                                                                                                                                            <a href="wordlist?p=2&tags=">3</a> \n                                                                                                                                                                                                                                                                                                        <span style="border:none;">...</span>\n                                \n                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    \n                                        <a href="wordlist?p=1&tags=" class="next-page">下一页</a>\n                                        <a href="wordlist?p=7&tags=" class="next-page">最后一页</a>\n                </div>\n               <form id="pagejumpform" action="#">\n               跳至第<input type="text" value=""/>页<button type="submit">确定</button>\n               </form>                \n                              \n\n               \n             \n             \n             <div class="right" >当前分类：<strong> 全部分类 </strong> &nbsp;&nbsp;共计 <strong>86</strong> 个单词 </div>\n             <div class="clear"></div>\n        </div>\n            </div>\n    \n\n    \n    <div id="cardmode">\n          <div id="cardmode-wrap">\n        <div id="card">\n                                            <h1 ><span id="card_word">agglomerative</span><a href="#" id="phonetic-voice"></a></h1> \n                <div id="card_pronounce">\n                    \n                </div>\n\n                <div id="description" style="display:none;">\n                    adj. 会凝聚的；[冶] 烧结的，凝结的\n                </div>\n\n                <div id="mask" >\n                    <span id="toggle-description" ><img src="http://shared.ydstatic.com/dict/wordbook-v1/images/mask.png"></span>\n                </div>\n            \n                <div id="action">\n                    <a id="pre" href="#"></a>\n                    <a id="next" href="#"></a>\n                    <div style="clear:both;"></div>\n                </div>\n                \n                                                                                                                                                                                                                                                                                                                                                                                                                                            </div>\n      </div>\n        <div style="line-height:28px;text-align:right;">\n            当前分类：<strong> 全部分类 </strong> &nbsp;&nbsp;共计 \n            <strong id="card_max_id">86</strong> 个单词 现在是第<span id="card_id"> 1</span>个\n        </div>\n              \n    </div>\n    \n\n\n\n\n\n<div id="footarea" >\n    <div style=" line-height:2; margin:10px 0 20px;">更好的进行生词的整理/记忆，请使用桌面版和手机版有道词典中的单词本</div>\n    <div id="foot-ad">\n    \n        <a href="http://cidian.youdao.com/?keyform=webwordbook" class="go-to-desktop" target="_blank"></a>\n        <a href="http://cidian.youdao.com/android.html?keyform=webwordbook" class="go-to-mobile" target="_blank"></a>\n\n    </div>\n</div>   \n\n</div>\n\n<div id="bottom">\n  <p><a href="http://youdao.com/">有道首页</a> - <a href="http://www.youdao.com/help/dict/description/001/">帮助</a> - <a href="http://www.youdao.com/about/">关于有道</a> - <a href="http://i.youdao.com/">官方博客</a>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&copy; 2020 网易公司 京ICP证080268号</p>\n  \n</div>\n\n\n\n    <div id="editwordform">\n        <h1>danci</h1>\n        <a href="#" id="close-editwordform"></a>\n        <form method="post" action="wordlist?action=modify">\n        \n        <label for="word">单词<span id="waittext"></span></label>\n        <input id="word" type="text" value="" name="word" autocomplete="off" />\n        <label for="phonetic">音标</label>\n        <input id="phonetic" type="text" value="" name="phonetic" />\n        <label for="desc">解释</label>\n        <textarea id="desc" name="desc" ></textarea>\n        \n        <label style="color:blue;">更多（可不填）</label>\n\n        <label for="tags">分类</label><input id="tags" type="text" value="" name="tags" autocomplete="off" />\n        <ul id="tag-select-list">\n                                            <li>无标签</li>\n                                    </ul>\n            \n        <div class="center-content"><button type="submit"></button></div>\n        </form>\n    </div>        \n\n<div id="leftbar">\n<a href="/?keyfrom=webwordbook">返回词典首页</a>\n<br/><br/>\n<a href="http://xue.youdao.com/">返回有道学堂</a>\n</div>    \n    <object width="1" height="1" type="application/x-shockwave-flash" id="dictVoice" data="/dictVoice.swf">\n        <param name="movie" value="/dictVoice.swf"/>\n        <param name="menu" value="false"/>\n        <param name="allowScriptAccess" value="always"/>\n        <param name="wmode" value="transparent"/>\n    </object>\n    \n<script type="text/javascript" src="http://shared.ydstatic.com/dict/wordbook-v1/scripts/jquery-1.5.2.min.js"></script>\n<script type="text/javascript" src="http://shared.ydstatic.com/dict/wordbook-v1/scripts/jquery.extention.dict4.js"></script>\n<script type="text/javascript" src="http://shared.ydstatic.com/dict/wordbook-v1/scripts/navigatorBar.js"></script>\n<script type="text/javascript" src="resources/scripts/main.js"></script>\n</body>\n</html>\n'
```

### 3. 使用Pandas解析网页中的表格

In [7]:

```python
df = pd.read_html(htmls[0])
```

In [8]:

```python
print(len(df))
print(type(df))
2
<class 'list'>
```

In [9]:

```python
df[0].head(3)
```

Out[9]:

|      | 序号 | 单词 | 音标 | 解释 | 时间 | 分类 | 操作 |
| ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|      |      |      |      |      |      |      |      |

In [10]:

```python
df[1].head(3)
```

Out[10]:

|      |    0 |             1 |          2 |                                  3 |         4 |    5 |    6 |
| ---: | ---: | ------------: | ---------: | ---------------------------------: | --------: | ---: | ---: |
|    0 |    1 | agglomerative |        NaN | adj. 会凝聚的；[冶] 烧结的，凝结的 | 2020-1-13 |  NaN |  NaN |
|    1 |    2 |       anatomy | [ə'nætəmɪ] |        n. 解剖；解剖学；剖析；骨骼 | 2017-7-17 |  NaN |  NaN |
|    2 |    3 |      backbone | ['bækbəʊn] |      n. 支柱;主干网;决心,毅力;脊椎 | 2017-7-13 |  NaN |  NaN |

In [11]:

```python
df_cont = df[1]
```

In [12]:

```python
df_cont.columns = df[0].columns
```

In [13]:

```python
df_cont.head(3)
```

Out[13]:

|      | 序号 |          单词 |       音标 |                               解释 |      时间 | 分类 | 操作 |
| ---: | ---: | ------------: | ---------: | ---------------------------------: | --------: | ---: | ---: |
|    0 |    1 | agglomerative |        NaN | adj. 会凝聚的；[冶] 烧结的，凝结的 | 2020-1-13 |  NaN |  NaN |
|    1 |    2 |       anatomy | [ə'nætəmɪ] |        n. 解剖；解剖学；剖析；骨骼 | 2017-7-17 |  NaN |  NaN |
|    2 |    3 |      backbone | ['bækbəʊn] |      n. 支柱;主干网;决心,毅力;脊椎 | 2017-7-13 |  NaN |  NaN |

In [14]:

```python
# 收集6个网页的表格
df_list = []
for html in htmls:
    df = pd.read_html(html)
    df_cont = df[1]
    df_cont.columns = df[0].columns
    df_list.append(df_cont)
```

In [15]:

```python
# 合并多个表格
df_all = pd.concat(df_list)
```

In [16]:

```python
df_all.head(3)
```

Out[16]:

|      | 序号 |          单词 |       音标 |                               解释 |      时间 | 分类 | 操作 |
| ---: | ---: | ------------: | ---------: | ---------------------------------: | --------: | ---: | ---: |
|    0 |    1 | agglomerative |        NaN | adj. 会凝聚的；[冶] 烧结的，凝结的 | 2020-1-13 |  NaN |  NaN |
|    1 |    2 |       anatomy | [ə'nætəmɪ] |        n. 解剖；解剖学；剖析；骨骼 | 2017-7-17 |  NaN |  NaN |
|    2 |    3 |      backbone | ['bækbəʊn] |      n. 支柱;主干网;决心,毅力;脊椎 | 2017-7-13 |  NaN |  NaN |

In [17]:

```python
df_all.shape
```

Out[17]:

```
(86, 7)
```

### 4. 将结果数据输出到Excel文件

In [18]:

```python
df_all[["单词", "音标", "解释"]].to_excel("./course_datas/c32_read_html/网易有道单词本列表.xlsx", index=False)
```

# 33、Pandas计算同比环比指标的3种方法

#### 同比和环比：环比和同比用于描述统计数据的变化情况

- 环比：表示本次统计段与相连的上次统计段之间的比较。
  - 比如2010年中国第一季度GDP为G2010Q1亿元，第二季度GDP为G2010Q2亿元，则第二季度GDP环比增长（G2010Q2-G2010Q1)/G2010Q1；
- 同比：即同期相比，表示某个特定统计段今年与去年之间的比较。
  - 比如2009年中国第一季度GDP为G2009Q1亿元，则2010年第一季度的GDP同比增长为（G2010Q1-G2009Q1)/G2009Q1。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_pandas/29.jpg)

演示步骤：

1. 读取连续3年的天气数据
2. 方法1：pandas.Series.pct_change
3. 方法2：pandas.Series.shift
4. 方法3：pandas.Series.diff

pct_change、shift、diff，都实现了跨越多行的数据计算

### 0. 读取连续3年的天气数据

In [1]:

```python
import pandas as pd
%matplotlib inline
```

In [2]:

```python
fpath = "./datas/beijing_tianqi/beijing_tianqi_2017-2019.csv"
df = pd.read_csv(fpath, index_col="ymd", parse_dates=True)
```

In [3]:

```python
df.head(3)
```

Out[3]:

|            | bWendu | yWendu | tianqi | fengxiang | fengli |  aqi |  aqiInfo | aqiLevel |
| ---------: | -----: | -----: | -----: | --------: | -----: | ---: | -------: | -------: |
|        ymd |        |        |        |           |        |      |          |          |
| 2017-01-01 |     5℃ |    -3℃ |  霾~晴 |      南风 |  1-2级 |  450 | 严重污染 |        6 |
| 2017-01-02 |     7℃ |    -6℃ |  晴~霾 |      南风 |  1-2级 |  246 | 重度污染 |        5 |
| 2017-01-03 |     5℃ |    -5℃ |     霾 |      南风 |  1-2级 |  320 | 严重污染 |        6 |

In [4]:

```python
# 替换掉温度的后缀℃
df["bWendu"] = df["bWendu"].str.replace("℃", "").astype('int32')
```

In [5]:

```python
df.head(3)
```

Out[5]:

|            | bWendu | yWendu | tianqi | fengxiang | fengli |  aqi |  aqiInfo | aqiLevel |
| ---------: | -----: | -----: | -----: | --------: | -----: | ---: | -------: | -------: |
|        ymd |        |        |        |           |        |      |          |          |
| 2017-01-01 |      5 |    -3℃ |  霾~晴 |      南风 |  1-2级 |  450 | 严重污染 |        6 |
| 2017-01-02 |      7 |    -6℃ |  晴~霾 |      南风 |  1-2级 |  246 | 重度污染 |        5 |
| 2017-01-03 |      5 |    -5℃ |     霾 |      南风 |  1-2级 |  320 | 严重污染 |        6 |

In [6]:

```python
# 新的df，为每个月的平均最高温
df = df[["bWendu"]].resample("M").mean()
```

In [7]:

```python
# 将索引按照日期升序排列
df.sort_index(ascending=True, inplace=True)
```

In [8]:

```python
df.head()
```

Out[8]:

|            |    bWendu |
| ---------: | --------: |
|        ymd |           |
| 2017-01-31 |  3.322581 |
| 2017-02-28 |  7.642857 |
| 2017-03-31 | 14.129032 |
| 2017-04-30 | 23.700000 |
| 2017-05-31 | 29.774194 |

In [9]:

```python
df.index
```

Out[9]:

```python
DatetimeIndex(['2017-01-31', '2017-02-28', '2017-03-31', '2017-04-30',
               '2017-05-31', '2017-06-30', '2017-07-31', '2017-08-31',
               '2017-09-30', '2017-10-31', '2017-11-30', '2017-12-31',
               '2018-01-31', '2018-02-28', '2018-03-31', '2018-04-30',
               '2018-05-31', '2018-06-30', '2018-07-31', '2018-08-31',
               '2018-09-30', '2018-10-31', '2018-11-30', '2018-12-31',
               '2019-01-31', '2019-02-28', '2019-03-31', '2019-04-30',
               '2019-05-31', '2019-06-30', '2019-07-31', '2019-08-31',
               '2019-09-30', '2019-10-31', '2019-11-30', '2019-12-31'],
              dtype='datetime64[ns]', name='ymd', freq='M')
```

In [10]:

```python
df.plot()
```

Out[10]:

```python
<matplotlib.axes._subplots.AxesSubplot at 0x13d8d77dc48>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_pandas/30.png)

### 方法1：pandas.Series.pct_change

pct_change方法直接算好了"(新-旧)/旧"的百分比

官方文档地址：https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.pct_change.html

In [11]:

```python
df["bWendu_way1_huanbi"] = df["bWendu"].pct_change(periods=1)
df["bWendu_way1_tongbi"] = df["bWendu"].pct_change(periods=12)
```

In [12]:

```python
df.head(15)
```

Out[12]:

|            |    bWendu | bWendu_way1_huanbi | bWendu_way1_tongbi |
| ---------: | --------: | -----------------: | -----------------: |
|        ymd |           |                    |                    |
| 2017-01-31 |  3.322581 |                NaN |                NaN |
| 2017-02-28 |  7.642857 |           1.300277 |                NaN |
| 2017-03-31 | 14.129032 |           0.848658 |                NaN |
| 2017-04-30 | 23.700000 |           0.677397 |                NaN |
| 2017-05-31 | 29.774194 |           0.256295 |                NaN |
| 2017-06-30 | 30.966667 |           0.040051 |                NaN |
| 2017-07-31 | 31.612903 |           0.020869 |                NaN |
| 2017-08-31 | 30.129032 |          -0.046939 |                NaN |
| 2017-09-30 | 27.866667 |          -0.075089 |                NaN |
| 2017-10-31 | 17.225806 |          -0.381849 |                NaN |
| 2017-11-30 |  9.566667 |          -0.444632 |                NaN |
| 2017-12-31 |  4.483871 |          -0.531303 |                NaN |
| 2018-01-31 |  1.322581 |          -0.705036 |          -0.601942 |
| 2018-02-28 |  4.892857 |           2.699477 |          -0.359813 |
| 2018-03-31 | 14.129032 |           1.887685 |           0.000000 |

### 方法2：pandas.Series.shift

shift用于移动数据，但是保持索引不变

官方文档地址：https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.shift.html

In [13]:

```python
# 见识一下shift做了什么事情
# 使用pd.concat合并Series列表变成一个大的df
pd.concat(
    [df["bWendu"], 
     df["bWendu"].shift(periods=1), 
     df["bWendu"].shift(periods=12)],
    axis=1
).head(15)
```

Out[13]:

|            |    bWendu |    bWendu |    bWendu |
| ---------: | --------: | --------: | --------: |
|        ymd |           |           |           |
| 2017-01-31 |  3.322581 |       NaN |       NaN |
| 2017-02-28 |  7.642857 |  3.322581 |       NaN |
| 2017-03-31 | 14.129032 |  7.642857 |       NaN |
| 2017-04-30 | 23.700000 | 14.129032 |       NaN |
| 2017-05-31 | 29.774194 | 23.700000 |       NaN |
| 2017-06-30 | 30.966667 | 29.774194 |       NaN |
| 2017-07-31 | 31.612903 | 30.966667 |       NaN |
| 2017-08-31 | 30.129032 | 31.612903 |       NaN |
| 2017-09-30 | 27.866667 | 30.129032 |       NaN |
| 2017-10-31 | 17.225806 | 27.866667 |       NaN |
| 2017-11-30 |  9.566667 | 17.225806 |       NaN |
| 2017-12-31 |  4.483871 |  9.566667 |       NaN |
| 2018-01-31 |  1.322581 |  4.483871 |  3.322581 |
| 2018-02-28 |  4.892857 |  1.322581 |  7.642857 |
| 2018-03-31 | 14.129032 |  4.892857 | 14.129032 |

In [14]:

```python
# 环比
series_shift1 = df["bWendu"].shift(periods=1)
df["bWendu_way2_huanbi"] = (df["bWendu"]-series_shift1)/series_shift1

# 同比
series_shift2 = df["bWendu"].shift(periods=12)
df["bWendu_way2_tongbi"] = (df["bWendu"]-series_shift2)/series_shift2
```

In [15]:

```python
df.head(15)
```

Out[15]:

|            |    bWendu | bWendu_way1_huanbi | bWendu_way1_tongbi | bWendu_way2_huanbi | bWendu_way2_tongbi |
| ---------: | --------: | -----------------: | -----------------: | -----------------: | -----------------: |
|        ymd |           |                    |                    |                    |                    |
| 2017-01-31 |  3.322581 |                NaN |                NaN |                NaN |                NaN |
| 2017-02-28 |  7.642857 |           1.300277 |                NaN |           1.300277 |                NaN |
| 2017-03-31 | 14.129032 |           0.848658 |                NaN |           0.848658 |                NaN |
| 2017-04-30 | 23.700000 |           0.677397 |                NaN |           0.677397 |                NaN |
| 2017-05-31 | 29.774194 |           0.256295 |                NaN |           0.256295 |                NaN |
| 2017-06-30 | 30.966667 |           0.040051 |                NaN |           0.040051 |                NaN |
| 2017-07-31 | 31.612903 |           0.020869 |                NaN |           0.020869 |                NaN |
| 2017-08-31 | 30.129032 |          -0.046939 |                NaN |          -0.046939 |                NaN |
| 2017-09-30 | 27.866667 |          -0.075089 |                NaN |          -0.075089 |                NaN |
| 2017-10-31 | 17.225806 |          -0.381849 |                NaN |          -0.381849 |                NaN |
| 2017-11-30 |  9.566667 |          -0.444632 |                NaN |          -0.444632 |                NaN |
| 2017-12-31 |  4.483871 |          -0.531303 |                NaN |          -0.531303 |                NaN |
| 2018-01-31 |  1.322581 |          -0.705036 |          -0.601942 |          -0.705036 |          -0.601942 |
| 2018-02-28 |  4.892857 |           2.699477 |          -0.359813 |           2.699477 |          -0.359813 |
| 2018-03-31 | 14.129032 |           1.887685 |           0.000000 |           1.887685 |           0.000000 |

### 方法3. pandas.Series.diff

pandas.Series.diff用于新值减去旧值

官方文档：https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.diff.html

In [16]:

```
pd.concat(
    [df["bWendu"], 
     df["bWendu"].diff(periods=1), 
     df["bWendu"].diff(periods=12)],
    axis=1
).head(15)
```

Out[16]:

|            |    bWendu |     bWendu | bWendu |
| ---------: | --------: | ---------: | -----: |
|        ymd |           |            |        |
| 2017-01-31 |  3.322581 |        NaN |    NaN |
| 2017-02-28 |  7.642857 |   4.320276 |    NaN |
| 2017-03-31 | 14.129032 |   6.486175 |    NaN |
| 2017-04-30 | 23.700000 |   9.570968 |    NaN |
| 2017-05-31 | 29.774194 |   6.074194 |    NaN |
| 2017-06-30 | 30.966667 |   1.192473 |    NaN |
| 2017-07-31 | 31.612903 |   0.646237 |    NaN |
| 2017-08-31 | 30.129032 |  -1.483871 |    NaN |
| 2017-09-30 | 27.866667 |  -2.262366 |    NaN |
| 2017-10-31 | 17.225806 | -10.640860 |    NaN |
| 2017-11-30 |  9.566667 |  -7.659140 |    NaN |
| 2017-12-31 |  4.483871 |  -5.082796 |    NaN |
| 2018-01-31 |  1.322581 |  -3.161290 |  -2.00 |
| 2018-02-28 |  4.892857 |   3.570276 |  -2.75 |
| 2018-03-31 | 14.129032 |   9.236175 |   0.00 |

In [17]:

```python
# 环比
series_diff1 = df["bWendu"].diff(periods=1)
df["bWendu_way3_huanbi"] = series_diff1/(df["bWendu"]-series_diff1)

# 同比
series_diff2 = df["bWendu"].diff(periods=12)
df["bWendu_way3_tongbi"] = series_diff2/(df["bWendu"]-series_diff2)
```

In [18]:

```python
df.head(15)
```

Out[18]:

|            |    bWendu | bWendu_way1_huanbi | bWendu_way1_tongbi | bWendu_way2_huanbi | bWendu_way2_tongbi | bWendu_way3_huanbi | bWendu_way3_tongbi |
| ---------: | --------: | -----------------: | -----------------: | -----------------: | -----------------: | -----------------: | -----------------: |
|        ymd |           |                    |                    |                    |                    |                    |                    |
| 2017-01-31 |  3.322581 |                NaN |                NaN |                NaN |                NaN |                NaN |                NaN |
| 2017-02-28 |  7.642857 |           1.300277 |                NaN |           1.300277 |                NaN |           1.300277 |                NaN |
| 2017-03-31 | 14.129032 |           0.848658 |                NaN |           0.848658 |                NaN |           0.848658 |                NaN |
| 2017-04-30 | 23.700000 |           0.677397 |                NaN |           0.677397 |                NaN |           0.677397 |                NaN |
| 2017-05-31 | 29.774194 |           0.256295 |                NaN |           0.256295 |                NaN |           0.256295 |                NaN |
| 2017-06-30 | 30.966667 |           0.040051 |                NaN |           0.040051 |                NaN |           0.040051 |                NaN |
| 2017-07-31 | 31.612903 |           0.020869 |                NaN |           0.020869 |                NaN |           0.020869 |                NaN |
| 2017-08-31 | 30.129032 |          -0.046939 |                NaN |          -0.046939 |                NaN |          -0.046939 |                NaN |
| 2017-09-30 | 27.866667 |          -0.075089 |                NaN |          -0.075089 |                NaN |          -0.075089 |                NaN |
| 2017-10-31 | 17.225806 |          -0.381849 |                NaN |          -0.381849 |                NaN |          -0.381849 |                NaN |
| 2017-11-30 |  9.566667 |          -0.444632 |                NaN |          -0.444632 |                NaN |          -0.444632 |                NaN |
| 2017-12-31 |  4.483871 |          -0.531303 |                NaN |          -0.531303 |                NaN |          -0.531303 |                NaN |
| 2018-01-31 |  1.322581 |          -0.705036 |          -0.601942 |          -0.705036 |          -0.601942 |          -0.705036 |          -0.601942 |
| 2018-02-28 |  4.892857 |           2.699477 |          -0.359813 |           2.699477 |          -0.359813 |           2.699477 |          -0.359813 |
| 2018-03-31 | 14.129032 |           1.887685 |           0.000000 |           1.887685 |           0.000000 |           1.887685 |           0.000000 |

# 34、Pandas和数据库查询语言SQL的对比

- Pandas：Python最流行的数据处理与数据分析的类库
- SQL：结构化查询语言，用于对MySQL、Oracle等关系型数据库的增删改查

两者都是对“表格型”数据的操作和查询，所以很多语法都能对应起来

对比列表：

1. SELECT数据查询
2. WHERE按条件查询
3. in和not in的条件查询
4. groupby分组统计
5. JOIN数据关联
6. UNION数据合并
7. Order Limit先排序后分页
8. 取每个分组group的top n
9. UPDATE数据更新
10. DELETE删除数据

### 0. 读取泰坦尼克数据集

In [1]:

```python
import pandas as pd
import numpy as np
```

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

### 1. SELECT数据查询

In [3]:

```python
# SQL：
sql = """
    SELECT PassengerId, Sex, Age, Survived
    FROM titanic
    LIMIT 5;
"""
```

In [4]:

```python
# Pandas
df[["PassengerId", "Sex", "Age", "Survived"]].head(5)
```

Out[4]:

|      | PassengerId |    Sex |  Age | Survived |
| ---: | ----------: | -----: | ---: | -------: |
|    0 |           1 |   male | 22.0 |        0 |
|    1 |           2 | female | 38.0 |        1 |
|    2 |           3 | female | 26.0 |        1 |
|    3 |           4 | female | 35.0 |        1 |
|    4 |           5 |   male | 35.0 |        0 |

df.head(5)类似select * from table limit 5，查询所有的字段

### 2. WHERE按条件查询

In [5]:

```python
# SQL：
sql = """
    SELECT *
    FROM titanic
    where Sex='male' and Age>=20.0 and Age<=40.0
    LIMIT 5;
"""
```

In [6]:

```python
# 使用括号的方式，级联多个条件|
condition = (df["Sex"]=="male") & (df["Age"]>=20.0) & (df["Age"]<=40.0)
condition.value_counts()
```

Out[6]:

```python
False    629
True     262
dtype: int64
```

In [7]:

```python
df[condition].head(5)
```

Out[7]:

|      | PassengerId | Survived | Pclass |                           Name |  Sex |  Age | SibSp | Parch |    Ticket |   Fare | Cabin | Embarked |
| ---: | ----------: | -------: | -----: | -----------------------------: | ---: | ---: | ----: | ----: | --------: | -----: | ----: | -------: |
|    0 |           1 |        0 |      3 |        Braund, Mr. Owen Harris | male | 22.0 |     1 |     0 | A/5 21171 |  7.250 |   NaN |        S |
|    4 |           5 |        0 |      3 |       Allen, Mr. William Henry | male | 35.0 |     0 |     0 |    373450 |  8.050 |   NaN |        S |
|   12 |          13 |        0 |      3 | Saundercock, Mr. William Henry | male | 20.0 |     0 |     0 | A/5. 2151 |  8.050 |   NaN |        S |
|   13 |          14 |        0 |      3 |    Andersson, Mr. Anders Johan | male | 39.0 |     1 |     5 |    347082 | 31.275 |   NaN |        S |
|   20 |          21 |        0 |      2 |           Fynney, Mr. Joseph J | male | 35.0 |     0 |     0 |    239865 | 26.000 |   NaN |        S |

### 3. in和not in的条件查询

In [8]:

```python
df["Pclass"].unique()
```

Out[8]:

```python
array([3, 1, 2], dtype=int64)
```

In [9]:

```python
# SQL：
sql = """
    SELECT *
    FROM titanic
    where Pclass in (1,2)
    LIMIT 5;
"""
```

In [10]:

```python
# in 
df[df["Pclass"].isin((1,2))].head()
```

Out[10]:

|      | PassengerId | Survived | Pclass |                                              Name |    Sex |  Age | SibSp | Parch |   Ticket |    Fare | Cabin | Embarked |
| ---: | ----------: | -------: | -----: | ------------------------------------------------: | -----: | ---: | ----: | ----: | -------: | ------: | ----: | -------: |
|    1 |           2 |        1 |      1 | Cumings, Mrs. John Bradley (Florence Briggs Th... | female | 38.0 |     1 |     0 | PC 17599 | 71.2833 |   C85 |        C |
|    3 |           4 |        1 |      1 |      Futrelle, Mrs. Jacques Heath (Lily May Peel) | female | 35.0 |     1 |     0 |   113803 | 53.1000 |  C123 |        S |
|    6 |           7 |        0 |      1 |                           McCarthy, Mr. Timothy J |   male | 54.0 |     0 |     0 |    17463 | 51.8625 |   E46 |        S |
|    9 |          10 |        1 |      2 |               Nasser, Mrs. Nicholas (Adele Achem) | female | 14.0 |     1 |     0 |   237736 | 30.0708 |   NaN |        C |
|   11 |          12 |        1 |      1 |                          Bonnell, Miss. Elizabeth | female | 58.0 |     0 |     0 |   113783 | 26.5500 |  C103 |        S |

In [11]:

```python
# not in 
df[~df["Pclass"].isin((1,2))].head()
```

Out[11]:

|      | PassengerId | Survived | Pclass |                           Name |    Sex |  Age | SibSp | Parch |           Ticket |    Fare | Cabin | Embarked |
| ---: | ----------: | -------: | -----: | -----------------------------: | -----: | ---: | ----: | ----: | ---------------: | ------: | ----: | -------: |
|    0 |           1 |        0 |      3 |        Braund, Mr. Owen Harris |   male | 22.0 |     1 |     0 |        A/5 21171 |  7.2500 |   NaN |        S |
|    2 |           3 |        1 |      3 |         Heikkinen, Miss. Laina | female | 26.0 |     0 |     0 | STON/O2. 3101282 |  7.9250 |   NaN |        S |
|    4 |           5 |        0 |      3 |       Allen, Mr. William Henry |   male | 35.0 |     0 |     0 |           373450 |  8.0500 |   NaN |        S |
|    5 |           6 |        0 |      3 |               Moran, Mr. James |   male |  NaN |     0 |     0 |           330877 |  8.4583 |   NaN |        Q |
|    7 |           8 |        0 |      3 | Palsson, Master. Gosta Leonard |   male |  2.0 |     3 |     1 |           349909 | 21.0750 |   NaN |        S |

### 4. groupby分组统计

#### 4.1 单个列的聚合

In [12]:

```python
# SQL：
sql = """
    SELECT 
        -- 分性别的存活人数
        sum(Survived),
        -- 分性别的平均年龄
        mean(Age)
        -- 分性别的平均票价
        mean(Fare)
    FROM titanic
    group by Sex
"""
```

In [13]:

```python
df.groupby("Sex").agg({"Survived":np.sum, "Age":np.mean, "Fare":np.mean})
```

Out[13]:

|        | Survived |       Age |      Fare |
| -----: | -------: | --------: | --------: |
|    Sex |          |           |           |
| female |      233 | 27.915709 | 44.479818 |
|   male |      109 | 30.726645 | 25.523893 |

#### 4.2 多个列的聚合

In [14]:

```python
# SQL：
sql = """
    SELECT 
        -- 不同存活和性别分组的，平均年龄
        mean(Age)
        -- 不同存活和性别分组的，平均票价
        mean(Fare)
    FROM titanic
    group by Survived, Sex
"""
```

In [15]:

```python
df.groupby(["Survived", "Sex"]).agg({"Age":np.mean, "Fare":np.mean})
```

Out[15]:

|          |           |       Age |      Fare |
| -------: | --------: | --------: | --------: |
| Survived |       Sex |           |           |
|        0 |    female | 25.046875 | 23.024385 |
|     male | 31.618056 | 21.960993 |           |
|        1 |    female | 28.847716 | 51.938573 |
|     male | 27.276022 | 40.821484 |           |

### 5. JOIN数据关联

In [16]:

```python
# 电影评分数据集，评分表
df_rating = pd.read_csv("./datas/ml-latest-small/ratings.csv")
df_rating.head(5)
```

Out[16]:

|      | userId | movieId | rating | timestamp |
| ---: | -----: | ------: | -----: | --------: |
|    0 |      1 |       1 |    4.0 | 964982703 |
|    1 |      1 |       3 |    4.0 | 964981247 |
|    2 |      1 |       6 |    4.0 | 964982224 |
|    3 |      1 |      47 |    5.0 | 964983815 |
|    4 |      1 |      50 |    5.0 | 964982931 |

In [17]:

```python
# 电影评分数据集，电影信息表
df_movies = pd.read_csv("./datas/ml-latest-small/movies.csv")
df_movies.head(5)
```

Out[17]:

|      | movieId |                              title |                                          genres |
| ---: | ------: | ---------------------------------: | ----------------------------------------------: |
|    0 |       1 |                   Toy Story (1995) | Adventure\|Animation\|Children\|Comedy\|Fantasy |
|    1 |       2 |                     Jumanji (1995) |                    Adventure\|Children\|Fantasy |
|    2 |       3 |            Grumpier Old Men (1995) |                                 Comedy\|Romance |
|    3 |       4 |           Waiting to Exhale (1995) |                          Comedy\|Drama\|Romance |
|    4 |       5 | Father of the Bride Part II (1995) |                                          Comedy |

In [18]:

```python
# SQL：
sql = """
    SELECT *
    FROM 
        rating join movies 
        on(rating.movieId=movies.movieId)
    limit 5
"""
```

In [19]:

```python
df_merged = pd.merge(left=df_rating, right=df_movies, on="movieId")
df_merged.head(5)
```

Out[19]:

|      | userId | movieId | rating |  timestamp |            title |                                          genres |
| ---: | -----: | ------: | -----: | ---------: | ---------------: | ----------------------------------------------: |
|    0 |      1 |       1 |    4.0 |  964982703 | Toy Story (1995) | Adventure\|Animation\|Children\|Comedy\|Fantasy |
|    1 |      5 |       1 |    4.0 |  847434962 | Toy Story (1995) | Adventure\|Animation\|Children\|Comedy\|Fantasy |
|    2 |      7 |       1 |    4.5 | 1106635946 | Toy Story (1995) | Adventure\|Animation\|Children\|Comedy\|Fantasy |
|    3 |     15 |       1 |    2.5 | 1510577970 | Toy Story (1995) | Adventure\|Animation\|Children\|Comedy\|Fantasy |
|    4 |     17 |       1 |    4.5 | 1305696483 | Toy Story (1995) | Adventure\|Animation\|Children\|Comedy\|Fantasy |

### 6. UNION数据合并

In [20]:

```python
df1 = pd.DataFrame({'city': ['Chicago', 'San Francisco', 'New York City'],
                    'rank': range(1, 4)}) 
df1
```

Out[20]:

|      |          city | rank |
| ---: | ------------: | ---: |
|    0 |       Chicago |    1 |
|    1 | San Francisco |    2 |
|    2 | New York City |    3 |

In [21]:

```python
df2 = pd.DataFrame({'city': ['Chicago', 'Boston', 'Los Angeles'],
                    'rank': [1, 4, 5]})
df2
```

Out[21]:

|      |        city | rank |
| ---: | ----------: | ---: |
|    0 |     Chicago |    1 |
|    1 |      Boston |    4 |
|    2 | Los Angeles |    5 |

In [22]:

```python
# SQL：
sql = """
    SELECT city, rank
    FROM df1
    
    UNION ALL
    
    SELECT city, rank
    FROM df2;
"""
```

In [23]:

```python
# pandas
pd.concat([df1, df2])
```

Out[23]:

|      |          city | rank |
| ---: | ------------: | ---: |
|    0 |       Chicago |    1 |
|    1 | San Francisco |    2 |
|    2 | New York City |    3 |
|    0 |       Chicago |    1 |
|    1 |        Boston |    4 |
|    2 |   Los Angeles |    5 |

### 7. Order Limit先排序后分页

In [24]:

```python
# SQL：
sql = """
    SELECT *
    from titanic
    order by Fare
    limit 5
"""
```

In [25]:

```python
df.sort_values("Fare", ascending=False).head(5)
```

Out[25]:

|      | PassengerId | Survived | Pclass |                               Name |    Sex |  Age | SibSp | Parch |   Ticket |     Fare |       Cabin | Embarked |
| ---: | ----------: | -------: | -----: | ---------------------------------: | -----: | ---: | ----: | ----: | -------: | -------: | ----------: | -------: |
|  258 |         259 |        1 |      1 |                   Ward, Miss. Anna | female | 35.0 |     0 |     0 | PC 17755 | 512.3292 |         NaN |        C |
|  737 |         738 |        1 |      1 |             Lesurer, Mr. Gustave J |   male | 35.0 |     0 |     0 | PC 17755 | 512.3292 |        B101 |        C |
|  679 |         680 |        1 |      1 | Cardeza, Mr. Thomas Drake Martinez |   male | 36.0 |     0 |     1 | PC 17755 | 512.3292 | B51 B53 B55 |        C |
|   88 |          89 |        1 |      1 |         Fortune, Miss. Mabel Helen | female | 23.0 |     3 |     2 |    19950 | 263.0000 | C23 C25 C27 |        S |
|   27 |          28 |        0 |      1 |     Fortune, Mr. Charles Alexander |   male | 19.0 |     3 |     2 |    19950 | 263.0000 | C23 C25 C27 |        S |

### 8. 取每个分组group的top n

In [26]:

```python
# MYSQL不支持
# Oracle有ROW_NUMBER语法
```

In [27]:

```python
# 按（Survived，Sex）分组，取Age的TOP 2
df.groupby(["Survived", "Sex"]).apply(
    lambda df:df.sort_values("Age", ascending=False).head(2))
```

Out[27]:

|          |        |      | PassengerId |                          Survived |                               Pclass |                   Name |    Sex |  Age |       SibSp |   Parch |      Ticket |    Fare | Cabin | Embarked |
| -------: | -----: | ---: | ----------: | --------------------------------: | -----------------------------------: | ---------------------: | -----: | ---: | ----------: | ------: | ----------: | ------: | ----: | -------: |
| Survived |    Sex |      |             |                                   |                                      |                        |        |      |             |         |             |         |       |          |
|        0 | female |  772 |         773 |                                 0 |                                    2 |      Mack, Mrs. (Mary) | female | 57.0 |           0 |       0 | S.O./P.P. 3 | 10.5000 |   E77 |        S |
|      177 |    178 |    0 |           1 |        Isham, Miss. Ann Elizabeth |                               female |                   50.0 |      0 |    0 |    PC 17595 | 28.7125 |         C49 |       C |       |          |
|     male |    851 |  852 |           0 |                                 3 |                  Svensson, Mr. Johan |                   male |   74.0 |    0 |           0 |  347060 |      7.7750 |     NaN |     S |          |
|      493 |    494 |    0 |           1 |           Artagaveytia, Mr. Ramon |                                 male |                   71.0 |      0 |    0 |    PC 17609 | 49.5042 |         NaN |       C |       |          |
|        1 | female |  483 |         484 |                                 1 |                                    3 | Turkula, Mrs. (Hedwig) | female | 63.0 |           0 |       0 |        4134 |  9.5875 |   NaN |        S |
|      275 |    276 |    1 |           1 | Andrews, Miss. Kornelia Theodosia |                               female |                   63.0 |      1 |    0 |       13502 | 77.9583 |          D7 |       S |       |          |
|     male |    630 |  631 |           1 |                                 1 | Barkworth, Mr. Algernon Henry Wilson |                   male |   80.0 |    0 |           0 |   27042 |     30.0000 |     A23 |     S |          |
|      570 |    571 |    1 |           2 |                Harris, Mr. George |                                 male |                   62.0 |      0 |    0 | S.W./PP 752 | 10.5000 |         NaN |       S |       |          |

### 9. UPDATE数据更新

In [28]:

```python
df.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 891 entries, 0 to 890
Data columns (total 12 columns):
PassengerId    891 non-null int64
Survived       891 non-null int64
Pclass         891 non-null int64
Name           891 non-null object
Sex            891 non-null object
Age            714 non-null float64
SibSp          891 non-null int64
Parch          891 non-null int64
Ticket         891 non-null object
Fare           891 non-null float64
Cabin          204 non-null object
Embarked       889 non-null object
dtypes: float64(2), int64(5), object(5)
memory usage: 83.7+ KB
```

In [29]:

```python
# SQL：
sql = """
    UPDATE titanic
    set Age=0
    where Age is null
"""
```

In [30]:

```python
condition = df["Age"].isna()
condition.value_counts()
```

Out[30]:

```python
False    714
True     177
Name: Age, dtype: int64
```

In [31]:

```python
df[condition] = 0
```

In [32]:

```python
df["Age"].isna().value_counts()
```

Out[32]:

```python
False    891
Name: Age, dtype: int64
```

### 10. DELETE删除数据

In [33]:

```python
# SQL：
sql = """
    DELETE FROM titanic
    where Age=0
"""
```

In [34]:

```python
df_new = df[df["Age"]!=0]
```

In [35]:

```python
df_new[df_new["Age"]==0]
```

Out[35]:

|      | PassengerId | Survived | Pclass | Name |  Sex |  Age | SibSp | Parch | Ticket | Fare | Cabin | Embarked |
| ---: | ----------: | -------: | -----: | ---: | ---: | ---: | ----: | ----: | -----: | ---: | ----: | -------: |
|      |             |          |        |      |      |      |       |       |        |      |       |          |

# 35、Pandas实现groupby聚合后不同列数据统计

电影评分数据集（UserID，MovieID，Rating，Timestamp） 

**聚合后单列-单指标统计：每个MovieID的平均评分**
df.groupby("MovieID")["Rating"].mean()

**聚合后单列-多指标统计：每个MoiveID的最高评分、最低评分、平均评分**
df.groupby("MovieID")["Rating"].agg(mean="mean", max="max", min=np.min)
df.groupby("MovieID").agg({"Rating":['mean', 'max', np.min]})

**聚合后多列-多指标统计：每个MoiveID的评分人数，最高评分、最低评分、平均评分**
df.groupby("MovieID").agg( rating_mean=("Rating", "mean"), user_count=("UserID", lambda x : x.nunique())
df.groupby("MovieID").agg( {"Rating": ['mean', 'min', 'max'], "UserID": lambda x :x.nunique()})
df.groupby("MovieID").apply( lambda x: pd.Series( {"min": x["Rating"].min(), "mean": x["Rating"].mean()})) 

**记忆：**agg(新列名=函数)、agg(新列名=(原列名，函数))、agg({"原列名"：函数/列表})
agg函数的两种形式，等号代表“把结果赋值给新列”，字典/元组代表“对这个列运用这些函数”

官网文档：https://pandas.pydata.org/pandas-docs/version/0.23.4/generated/pandas.core.groupby.DataFrameGroupBy.agg.html

### 读取数据

In [1]:

```python
import pandas as pd
import numpy as np
```

In [2]:

```python
df = pd.read_csv(
    "./datas/movielens-1m/ratings.dat", 
    sep="::",
    engine='python', 
    names="UserID::MovieID::Rating::Timestamp".split("::")
)
```

In [3]:

```python
df.head(3)
```

Out[3]:

|      | UserID | MovieID | Rating | Timestamp |
| ---: | -----: | ------: | -----: | --------: |
|    0 |      1 |    1193 |      5 | 978300760 |
|    1 |      1 |     661 |      3 | 978302109 |
|    2 |      1 |     914 |      3 | 978301968 |

### 聚合后单列-单指标统计

In [4]:

```python
# 每个MovieID的平均评分
result = df.groupby("MovieID")["Rating"].mean()
result.head()
```

Out[4]:

```python
MovieID
1    4.146846
2    3.201141
3    3.016736
4    2.729412
5    3.006757
Name: Rating, dtype: float64
```

In [5]:

```python
type(result)
```

Out[5]:

```python
pandas.core.series.Series
```

### 聚合后单列-多指标统计

每个MoiveID的最高评分、最低评分、平均评分

#### 方法1：agg函数传入多个结果列名=函数名形式

In [6]:

```python
result = df.groupby("MovieID")["Rating"].agg(
    mean="mean", max="max", min=np.min
)
result.head()
```

Out[6]:

|         |     mean |  max |  min |
| ------: | -------: | ---: | ---: |
| MovieID |          |      |      |
|       1 | 4.146846 |    5 |    1 |
|       2 | 3.201141 |    5 |    1 |
|       3 | 3.016736 |    5 |    1 |
|       4 | 2.729412 |    5 |    1 |
|       5 | 3.006757 |    5 |    1 |

#### 方法2：agg函数传入字典，key是column名，value是函数列表

In [7]:

```python
# 每个MoiveID的最高评分、最低评分、平均评分
result = df.groupby("MovieID").agg(
    {"Rating":['mean', 'max', np.min]}
)
result.head()
```

Out[7]:

|         |   Rating |      |      |
| ------: | -------: | ---: | ---: |
|         |     mean |  max | amin |
| MovieID |          |      |      |
|       1 | 4.146846 |    5 |    1 |
|       2 | 3.201141 |    5 |    1 |
|       3 | 3.016736 |    5 |    1 |
|       4 | 2.729412 |    5 |    1 |
|       5 | 3.006757 |    5 |    1 |

In [8]:

```python
result.columns = ['age_mean', 'age_min', 'age_max']
result.head()
```

Out[8]:

|         | age_mean | age_min | age_max |
| ------: | -------: | ------: | ------: |
| MovieID |          |         |         |
|       1 | 4.146846 |       5 |       1 |
|       2 | 3.201141 |       5 |       1 |
|       3 | 3.016736 |       5 |       1 |
|       4 | 2.729412 |       5 |       1 |
|       5 | 3.006757 |       5 |       1 |

### 聚合后多列-多指标统计

每个MoiveID的评分人数，最高评分、最低评分、平均评分

#### 方法1：agg函数传入字典，key是原列名，value是原列名和函数元组

In [9]:

```python
# 回忆：agg函数的两种形式，等号代表“把结果赋值给新列”，字典/元组代表“对这个列运用这些函数”
result = df.groupby("MovieID").agg(
        rating_mean=("Rating", "mean"),
        rating_min=("Rating", "min"),
        rating_max=("Rating", "max"),
        user_count=("UserID", lambda x : x.nunique())
)
result.head()
```

Out[9]:

|         | rating_mean | rating_min | rating_max | user_count |
| ------: | ----------: | ---------: | ---------: | ---------: |
| MovieID |             |            |            |            |
|       1 |    4.146846 |          1 |          5 |       2077 |
|       2 |    3.201141 |          1 |          5 |        701 |
|       3 |    3.016736 |          1 |          5 |        478 |
|       4 |    2.729412 |          1 |          5 |        170 |
|       5 |    3.006757 |          1 |          5 |        296 |

#### 方法2：agg函数传入字典，key是原列名，value是函数列表

统计后是二级索引，需要做索引处理

In [10]:

```python
result = df.groupby("MovieID").agg(
    {
        "Rating": ['mean', 'min', 'max'],
        "UserID": lambda x :x.nunique()
    }
)
result.head()
```

Out[10]:

|         |   Rating | UserID |      |          |
| ------: | -------: | -----: | ---: | -------: |
|         |     mean |    min |  max | <lambda> |
| MovieID |          |        |      |          |
|       1 | 4.146846 |      1 |    5 |     2077 |
|       2 | 3.201141 |      1 |    5 |      701 |
|       3 | 3.016736 |      1 |    5 |      478 |
|       4 | 2.729412 |      1 |    5 |      170 |
|       5 | 3.006757 |      1 |    5 |      296 |

In [11]:

```python
result["Rating"].head(3)
```

Out[11]:

|         |     mean |  min |  max |
| ------: | -------: | ---: | ---: |
| MovieID |          |      |      |
|       1 | 4.146846 |    1 |    5 |
|       2 | 3.201141 |    1 |    5 |
|       3 | 3.016736 |    1 |    5 |

In [12]:

```python
result.columns = ["rating_mean", "rating_min","rating_max","user_count"]
result.head()
```

Out[12]:

|         | rating_mean | rating_min | rating_max | user_count |
| ------: | ----------: | ---------: | ---------: | ---------: |
| MovieID |             |            |            |            |
|       1 |    4.146846 |          1 |          5 |       2077 |
|       2 |    3.201141 |          1 |          5 |        701 |
|       3 |    3.016736 |          1 |          5 |        478 |
|       4 |    2.729412 |          1 |          5 |        170 |
|       5 |    3.006757 |          1 |          5 |        296 |

#### 方法3：使用groupby之后apply对每个子df单独统计

In [13]:

```python
def agg_func(x):
    """注意，这个x是子DF"""
    
    # 这个Series会变成一行，字典KEY是列名
    return pd.Series({
        "rating_mean": x["Rating"].mean(),
        "rating_min": x["Rating"].min(),
        "rating_max": x["Rating"].max(),
        "user_count": x["UserID"].nunique()
    })

result = df.groupby("MovieID").apply(agg_func)
result.head()
```

Out[13]:

|         | rating_mean | rating_min | rating_max | user_count |
| ------: | ----------: | ---------: | ---------: | ---------: |
| MovieID |             |            |            |            |
|       1 |    4.146846 |        1.0 |        5.0 |     2077.0 |
|       2 |    3.201141 |        1.0 |        5.0 |      701.0 |
|       3 |    3.016736 |        1.0 |        5.0 |      478.0 |
|       4 |    2.729412 |        1.0 |        5.0 |      170.0 |
|       5 |    3.006757 |        1.0 |        5.0 |      296.0 |

# 36、Pandas读取Excel将数据展示在网页上

注意：

本节课程演示的代码，在当前目录下找。

子目录名字："Pandas读取Excel将数据展示在网页上"