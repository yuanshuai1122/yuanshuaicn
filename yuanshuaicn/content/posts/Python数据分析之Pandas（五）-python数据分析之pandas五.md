---
title: Python数据分析之Pandas（五）
date: 2021-posts-26 21:54:21.554
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/v2-2103a9f9659baf7be26d40fe5b4e9f1f_1440w.jpg'
theme: 'light'
tags: 
- Python
- 数据分析
---

: | ---: | ---: | ---: | ---: | ---: |
|    0 | S001 | 怠涵 |   女 |   23 | 山东 |
|    1 | S002 | 婉清 |   女 |   25 | 河南 |
|    2 | S003 | 溪榕 |   女 |   23 | 湖北 |
|    3 | S004 | 漠涓 |   女 |   19 | 陕西 |
|    4 | S005 | 祈博 |   女 |   24 | 山东 |

In [3]:

```python
# 展示索引的name
df.index.name
```

In [4]:

```python
df.index.name = "id"
df.head()
```

Out[4]:

|      | 学号 | 姓名 | 性别 | 年龄 | 籍贯 |
| ---: | ---: | ---: | ---: | ---: | ---: |
|   id |      |      |      |      |      |
|    0 | S001 | 怠涵 |   女 |   23 | 山东 |
|    1 | S002 | 婉清 |   女 |   25 | 河南 |
|    2 | S003 | 溪榕 |   女 |   23 | 湖北 |
|    3 | S004 | 漠涓 |   女 |   19 | 陕西 |
|    4 | S005 | 祈博 |   女 |   24 | 山东 |

### 创建sqlalchemy对象连接MySQL

SQLAlchemy是Python中的ORM框架， Object-Relational Mapping，把关系数据库的表结构映射到对象上。

- 官网：https://www.sqlalchemy.org/
- 如果sqlalchemy包不存在，用这个命令安装：pip install sqlalchemy
- 需要安装依赖Python库：pip install mysql-connector-python

可以直接执行SQL语句

In [5]:

```python
from sqlalchemy import create_engine
```

In [6]:

```python
engine = create_engine("mysql+mysqlconnector://root:123456@127.0.0.1:3306/test", echo=False)
```

### 方法1：当数据表不存在时，每次覆盖整个表

每次运行会drop table，新建表

In [7]:

```python
df.to_sql(name='student', con=engine, if_exists="replace")
```

In [11]:

```python
engine.execute("show create table student").first()[1]
```

Out[11]:

```python
'CREATE TABLE `student` (\n  `id` bigint(20) DEFAULT NULL,\n  `学号` text,\n  `姓名` text,\n  `性别` text,\n  `年龄` bigint(20) DEFAULT NULL,\n  `籍贯` text,\n  KEY `ix_student_id` (`id`)\n) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4'
```

In [8]:

```python
print(engine.execute("show create table student").first()[1])
CREATE TABLE `student` (
  `id` bigint(20) DEFAULT NULL,
  `学号` text,
  `姓名` text,
  `性别` text,
  `年龄` bigint(20) DEFAULT NULL,
  `籍贯` text,
  KEY `ix_student_id` (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4
```

In [12]:

```python
engine.execute("select count(1) from student").first()
```

Out[12]:

```python
(24,)
```

In [13]:

```python
engine.execute("select * from student limit 5").fetchall()
```

Out[13]:

```python
[(0, 'S001', '怠涵', '女', 23, '山东'),
 (1, 'S002', '婉清', '女', 25, '河南'),
 (2, 'S003', '溪榕', '女', 23, '湖北'),
 (3, 'S004', '漠涓', '女', 19, '陕西'),
 (4, 'S005', '祈博', '女', 24, '山东')]
```

### 方法2：当数据表存在时，每次新增数据

场景：每天会新增一部分数据，要添加到数据表，怎么处理？

In [14]:

```python
df_new = df.loc[:4, :]
df_new
```

Out[14]:

|      | 学号 | 姓名 | 性别 | 年龄 | 籍贯 |
| ---: | ---: | ---: | ---: | ---: | ---: |
|   id |      |      |      |      |      |
|    0 | S001 | 怠涵 |   女 |   23 | 山东 |
|    1 | S002 | 婉清 |   女 |   25 | 河南 |
|    2 | S003 | 溪榕 |   女 |   23 | 湖北 |
|    3 | S004 | 漠涓 |   女 |   19 | 陕西 |
|    4 | S005 | 祈博 |   女 |   24 | 山东 |

In [15]:

```python
df_new.to_sql(name='student', con=engine, if_exists="append")
```

In [16]:

```python
engine.execute("SELECT * FROM student where id<5 ").fetchall()
```

Out[16]:

```python
[(0, 'S001', '怠涵', '女', 23, '山东'),
 (1, 'S002', '婉清', '女', 25, '河南'),
 (2, 'S003', '溪榕', '女', 23, '湖北'),
 (3, 'S004', '漠涓', '女', 19, '陕西'),
 (4, 'S005', '祈博', '女', 24, '山东'),
 (0, 'S001', '怠涵', '女', 23, '山东'),
 (1, 'S002', '婉清', '女', 25, '河南'),
 (2, 'S003', '溪榕', '女', 23, '湖北'),
 (3, 'S004', '漠涓', '女', 19, '陕西'),
 (4, 'S005', '祈博', '女', 24, '山东')]
```

#### 问题解决：先根据数据KEY删除旧数据

In [17]:

```python
df_new.index
```

Out[17]:

```python
RangeIndex(start=0, stop=5, step=1, name='id')
```

In [18]:

```python
for id in df_new.index:
    ## 先删除要新增的数据
    delete_sql = f"delete from student where id={id}"
    print(delete_sql)
    engine.execute(delete_sql)
delete from student where id=0
delete from student where id=1
delete from student where id=2
delete from student where id=3
delete from student where id=4
```

In [19]:

```python
engine.execute("SELECT * FROM student where id<5 ").fetchall()
```

Out[19]:

```python
[]
```

In [20]:

```python
engine.execute("select count(1) from student").first()
```

Out[20]:

```python
(19,)
```

In [21]:

```python
# 新增数据到表中
df_new.to_sql(name='student', con=engine, if_exists="append")
```

In [22]:

```python
engine.execute("SELECT * FROM student where id<5 ").fetchall()
```

Out[22]:

```python
[(0, 'S001', '怠涵', '女', 23, '山东'),
 (1, 'S002', '婉清', '女', 25, '河南'),
 (2, 'S003', '溪榕', '女', 23, '湖北'),
 (3, 'S004', '漠涓', '女', 19, '陕西'),
 (4, 'S005', '祈博', '女', 24, '山东')]
```

In [23]:

```python
engine.execute("SELECT count(1) FROM student").first()
```

Out[23]:

```python
(24,)
```

# 38、Python批量翻译英语单词

***用途：\***
对批量的英语文本，生成英语-汉语翻译的单词本，提供Excel下载

***本代码实现：\***

1. 提供一个英文文章URL，自动下载网页；

2. 实现网页中所有英语单词的翻译；

3. 下载翻译结果的Excel

   ***涉及技术：\***

4. pandas的读取csv、多数据merge、输出Excel

5. requests库下载HTML网页

6. BeautifulSoup解析HTML网页

7. Python正则表达式实现英文分词

### 1. 读取英语-汉语翻译词典文件

词典文件来自：https://github.com/skywind3000/ECDICT 使用步骤：

1. 下载代码打包：https://github.com/skywind3000/ECDICT/archive/master.zip
2. 解压master.zip，然后解压其中的‪stardict.csv文件

In [1]:

```python
import pandas as pd
```

In [2]:

```python
# 注意：stardict.csv的地址需要替换成你自己的文件地址
df_dict = pd.read_csv("D:/tmp/ECDICT-master/stardict.csv")
d:\Anaconda3\lib\site-packages\IPython\core\interactiveshell.py:3063: DtypeWarning: Columns (11) have mixed types.Specify dtype option on import or set low_memory=False.
  interactivity=interactivity, compiler=compiler, result=result)
```

In [3]:

```python
df_dict.shape
```

Out[3]:

```python
(3402564, 13)
```

In [4]:

```python
df_dict.sample(10).head()
```

Out[4]:

|         |              word |   phonetic | definition |                                             translation |  pos | collins | oxford |  tag |  bnc |  frq | exchange | detail | audio |
| ------: | ----------------: | ---------: | ---------: | ------------------------------------------------------: | ---: | ------: | -----: | ---: | ---: | ---: | -------: | -----: | ----: |
| 3370509 |              WWDH |        NaN |        NaN |                                           [网络] 淇楄壋 |  NaN |     NaN |    NaN |  NaN |  NaN |  NaN |      NaN |    NaN |   NaN |
|  518014 | chauhtan (chotan) |        NaN |        NaN |                                                    卓丹 |  NaN |     NaN |    NaN |  NaN |  NaN |  NaN |      NaN |    NaN |   NaN |
|  389953 |        breviarist |        NaN |        NaN |                                           [网络] 短笛师 |  NaN |     NaN |    NaN |  NaN |  NaN |  NaN |      NaN |    NaN |   NaN |
|  951231 |  electric-vehicle |        NaN |        NaN | abbr. “EV”的变体；“electric car”的变体\n[网络] 电动汽车 |  NaN |     NaN |    NaN |  NaN |  NaN |  NaN |      NaN |    NaN |   NaN |
|   91258 |         Albionian | æl'biәniәn |        NaN |                                        [地质]阿尔比翁期 |  NaN |     NaN |    NaN |  NaN |  0.0 |  0.0 |      NaN |    NaN |   NaN |

In [5]:

```python
# 把word、translation之外的列扔掉
df_dict = df_dict[["word", "translation"]]
df_dict.head()
```

Out[5]:

|      |          word |                                                  translation |
| ---: | ------------: | -----------------------------------------------------------: |
|    0 |            'a | na. 一\nn. 英文字母表的第一字母；【乐】A音\nart. 冠以不定冠词主要表示类别\... |
|    1 |      'A' game |                                  [网络] 游戏；一个游戏；一局 |
|    2 |    'Abbāsīyah |                                       [地名] 阿巴西耶 ( 埃 ) |
|    3 |  'Abd al Kūrī |                               [地名] 阿卜杜勒库里岛 ( 也门 ) |
|    4 | 'Abd al Mājid |                               [地名] 阿卜杜勒马吉德 ( 苏丹 ) |

### 2. 下载网页，得到网页内容

In [6]:

```python
import requests
```

In [7]:

```python
# Pandas官方文档中的一个URL
url = "https://pandas.pydata.org/docs/user_guide/indexing.html"
```

In [8]:

```python
html_cont = requests.get(url).text
```

In [9]:

```python
html_cont[:100]
```

Out[9]:

```
'\n\n<!DOCTYPE html>\n\n<html xmlns="http://www.w3.org/1999/xhtml">\n  <head>\n    <meta charset="utf-8" />'
```

### 3. 提取HTML的正文内容

即：去除HTML标签，获取正文

In [10]:

```python
from bs4 import BeautifulSoup
soup = BeautifulSoup(html_cont)
html_text = soup.get_text()
```

In [11]:

```python
html_text[:500]
```

Out[11]:

```
'\n\n\nIndexing and selecting data — pandas 1.0.1 documentation\n\n\n\n\n\n\n\n\n\n\n\n\nMathJax.Hub.Config({"tex2jax": {"inlineMath": [["$", "$"], ["\\\\(", "\\\\)"]], "processEscapes": true, "ignoreClass": "document", "processClass": "math|output_area"}})\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\nHome\n\n\nWhat\'s New in 1.0.0\n\n\nGetting started\n\n\nUser Guide\n\n\nAPI reference\n\n\nDevelopment\n\n\nRelease Notes\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\nIO tools (text, CSV, HDF5, â\x80¦)\n\n\nIndexing and selecting data\n\n\nMultiIndex / advanced indexing\n\n\nMerge, join, a'
```

### 4. 英文分词和数据清洗

In [12]:

```python
# 分词
import re
word_list = re.split("""[ ,.\(\)/\n|\-:=\$\["']""",html_text)
word_list[:10]
```

Out[12]:

```python
['', '', '', 'Indexing', 'and', 'selecting', 'data', '—', 'pandas', '1']
```

In [13]:

```python
# 读取停用词表，从网上复制的，位于当前目录下
with open("./datas/stop_words/stop_words.txt") as fin:
    stop_words=set(fin.read().split("\n"))
list(stop_words)[:10]
```

Out[13]:

```python
['',
 'itself',
 'showed',
 'throughout',
 'pointed',
 'n',
 'against',
 'name',
 'none',
 'ran']
```

In [14]:

```python
# 数据清洗
word_list_clean = []
for word in word_list:
    word = str(word).lower().strip()
    # 过滤掉空词、数字、单个字符的词、停用词
    if not word or word.isnumeric() or len(word)<=1 or word in stop_words:
        continue
    word_list_clean.append(word)
word_list_clean[:20]
```

Out[14]:

```python
['indexing',
 'selecting',
 'data',
 'pandas',
 'documentation',
 'mathjax',
 'hub',
 'config',
 'tex2jax',
 'inlinemath',
 '\\\\',
 '\\\\',
 ']]',
 'processescapes',
 'true',
 'ignoreclass',
 'document',
 'processclass',
 'math',
 'output_area']
```

### 5. 分词结果构造成一个DataFrame

In [15]:

```python
df_words = pd.DataFrame({
    "word": word_list_clean
})
df_words.head()
```

Out[15]:

|      |          word |
| ---: | ------------: |
|    0 |      indexing |
|    1 |     selecting |
|    2 |          data |
|    3 |        pandas |
|    4 | documentation |

In [16]:

```python
df_words.shape
```

Out[16]:

```python
(4915, 1)
```

In [17]:

```python
# 统计词频
df_words = (
    df_words
    .groupby("word")["word"]
    .agg(count="size")
    .reset_index()
    .sort_values(by="count", ascending=False)
)
df_words.head(10)
```

Out[17]:

|      |      word | count |
| ---: | --------: | ----: |
|  620 |        df |   161 |
|  659 |     dtype |    87 |
| 1274 |      true |    86 |
|  593 | dataframe |    80 |
| 1038 |        pd |    75 |
|  917 |       loc |    72 |
|  970 |       nan |    72 |
|  721 |     false |    58 |
|  914 |      list |    58 |
|  835 |  indexing |    53 |

### 6. 和单词词典实现merge

In [18]:

```python
df_merge = pd.merge(
    left = df_dict,
    right = df_words,
    left_on = "word",
    right_on = "word"
)
```

In [19]:

```python
df_merge.sample(10)
```

Out[19]:

|      |      word |                                                  translation | count |
| ---: | --------: | -----------------------------------------------------------: | ----: |
|  658 |      team | n. 队, 组\nvt. 把马(牛)套在同一辆车上, 把...编成一组\nvi. 驾驶卡车, 协作 |     3 |
|  523 | providing |                                      conj. 以...为条件, 假如 |     1 |
|  394 |     lines |                                                      n. 台词 |     1 |
|  118 |   columns |                                                         塔器 |    49 |
|  136 |  conforms |       v. 遵守( conform的第三人称单数 ); 顺应; 相一致; 相符合 |     1 |
|  529 |    python | n. 大蟒, 巨蟒\n[计] Python 程序设计语言；人生苦短，我用 Python |    26 |
|  185 | determine |                                                v. 决定, 决心 |     1 |
|  285 |   forward | a. 向前的, 早的, 迅速的, 在前的, 进步的\nvt. 促进...的生长, 转寄, 运... |     1 |
|   49 | arguments |                                                      n. 参数 |     3 |
|  564 |  reported |                                          a. 报告的；据报道的 |     1 |

In [20]:

```python
df_merge.shape
```

Out[20]:

```python
(718, 3)
```

### 7. 存入Excel

In [21]:

```python
df_merge.to_excel("./38. batch_chinese_english.xlsx", index=False)
```

### 后续升级：

1. 可以提供txt/excel/word/pdf的批量输入，生成单词本；
2. 可以做成网页、微信小程序的形式，在线访问和使用
3. 用户可以标记或上传“已经认识的词语”，每次过滤掉

# 39、Python自动翻译英语论文PDF

***涉及技术：\***

1. Python读取PDF文本
2. pandas的读取csv、多数据merge、输出Excel
3. Python正则表达式实现英文分词

### 1. 读取PDF文本内容

In [1]:

```python
!pip install -i https://pypi.tuna.tsinghua.edu.cn/simple pdfplumber
Looking in indexes: https://pypi.tuna.tsinghua.edu.cn/simple
Requirement already satisfied: pdfplumber in d:\anaconda3\lib\site-packages (0.5.16)
Requirement already satisfied: chardet in d:\anaconda3\lib\site-packages (from pdfplumber) (3.0.4)
Requirement already satisfied: pdfminer.six==20200104 in d:\anaconda3\lib\site-packages (from pdfplumber) (20200104)
Requirement already satisfied: pillow>=7.0.0 in d:\anaconda3\lib\site-packages (from pdfplumber) (7.0.0)
Requirement already satisfied: unicodecsv>=0.14.1 in d:\anaconda3\lib\site-packages (from pdfplumber) (0.14.1)
Requirement already satisfied: six in d:\anaconda3\lib\site-packages (from pdfplumber) (1.14.0)
Requirement already satisfied: wand in d:\anaconda3\lib\site-packages (from pdfplumber) (0.5.9)
Requirement already satisfied: pycryptodome in d:\anaconda3\lib\site-packages (from pdfplumber) (3.9.7)
Requirement already satisfied: sortedcontainers in d:\anaconda3\lib\site-packages (from pdfminer.six==20200104->pdfplumber) (2.1.0)
```

In [48]:

```python
import pdfplumber
def read_pdf(pdf_fpath):
    pdf = pdfplumber.open(pdf_fpath)
    page_conts = []
    for page in pdf.pages:
        page_conts.append(page.extract_text())
    pdf.close()
    return " ".join(page_conts)
```

In [49]:

```python
pdf_fpath = "D:/tmp/Wide & Deep Learning for Recommender Systems.pdf"
pdf_cont = read_pdf(pdf_fpath)
```

In [50]:

```python
print(pdf_cont[:2000])
Wide & Deep Learning for Recommender Systems
Heng-Tze Cheng, Levent Koc, Jeremiah Harmsen, Tal Shaked, Tushar Chandra,
Hrishi Aradhye, Glen Anderson, Greg Corrado, Wei Chai, Mustafa Ispir, Rohan Anil,
Zakaria Haque, Lichan Hong, Vihan Jain, Xiaobing Liu, Hemal Shah
∗
GoogleInc.
ABSTRACT
have never or rarely occurred in the past. Recommenda-
6
tions based on memorization are usually more topical and
1 Generalized linear models with nonlinear feature transfor-
directly relevant to the items on which users have already
0 mations are widely used for large-scale regression and clas-
performed actions. Compared with memorization, general-
2 siﬁcationproblemswithsparseinputs. Memorizationoffea-
ization tends to improve the diversity of the recommended
  tureinteractionsthroughawide setofcross-productfeature
n items. Inthispaper,wefocusontheappsrecommendation
transformationsareeﬀectiveandinterpretable,whilegener-
u problemfortheGooglePlaystore,buttheapproachshould
alizationrequiresmorefeatureengineeringeﬀort. Withless
J apply to generic recommender systems.
  featureengineering,deepneuralnetworkscangeneralizebet-
4 Formassive-scaleonlinerecommendationandrankingsys-
tertounseenfeaturecombinationsthroughlow-dimensional
2 temsinanindustrialsetting,generalizedlinearmodelssuch
denseembeddingslearnedforthesparsefeatures. However,
  as logistic regression are widely used because they are sim-
  deep neural networks with embeddings can over-generalize
] ple,scalableandinterpretable. Themodelsareoftentrained
G andrecommendlessrelevantitemswhentheuser-iteminter-
onbinarizedsparsefeatureswithone-hotencoding. E.g.,the
actions are sparse and high-rank. In this paper, we present
L binary feature“user_installed_app=netflix”has value 1
Wide & Deep learning—jointly trained wide linear models
. if the user installed Netﬂix. Memorization can be achieved
s anddeepneuralnetworks—tocombinethebeneﬁtsofmem-
c orization and generalization for recommender systems. We eﬀectively using cross-product t
```

### 2. 读取英语-汉语翻译词典文件

词典文件来自：https://github.com/skywind3000/ECDICT 使用步骤：

1. 下载代码打包：https://github.com/skywind3000/ECDICT/archive/master.zip
2. 解压master.zip，然后解压其中的‪stardict.csv文件

In [8]:

```python
import pandas as pd
```

In [9]:

```python
# 注意：stardict.csv的地址需要替换成你自己的文件地址
df_dict = pd.read_csv("D:/tmp/ECDICT-master/stardict.csv")
d:\Anaconda3\lib\site-packages\IPython\core\interactiveshell.py:3063: DtypeWarning: Columns (11) have mixed types.Specify dtype option on import or set low_memory=False.
  interactivity=interactivity, compiler=compiler, result=result)
```

In [10]:

```python
df_dict.shape
```

Out[10]:

```python
(3402564, 13)
```

In [11]:

```python
df_dict.sample(10).head()
```

Out[11]:

|         |                       word | phonetic | definition |           translation |  pos | collins | oxford |  tag |  bnc |  frq |                        exchange | detail | audio |
| ------: | -------------------------: | -------: | ---------: | --------------------: | ---: | ------: | -----: | ---: | ---: | ---: | ------------------------------: | -----: | ----: |
|  801655 |              design height |      NaN |        NaN |              设计高度 |  NaN |     NaN |    NaN |  NaN |  NaN |  NaN |                             NaN |    NaN |   NaN |
| 2739800 |                      shibu |      NaN |        NaN | [网络] 方回春堂；喊吧 |  NaN |     NaN |    NaN |  NaN |  NaN |  NaN |                             NaN |    NaN |   NaN |
| 1232187 |              genus Testudo |      NaN |        NaN |      [网络] Testudo属 |  NaN |     NaN |    NaN |  NaN |  0.0 |  0.0 |               s:genus testudoes |    NaN |   NaN |
| 2403094 | profit-and-loss statements |      NaN |        NaN |         [会计] 损益表 |  NaN |     NaN |    NaN |  NaN |  NaN |  NaN | 0:profit-and-loss statement/1:s |    NaN |   NaN |
| 1197174 |   gain limited sensitivity |      NaN |        NaN |        极限增益灵敏度 |  NaN |     NaN |    NaN |  NaN |  NaN |  NaN |                             NaN |    NaN |   NaN |

In [12]:

```python
# 把word、translation之外的列扔掉
df_dict = df_dict[["word", "translation"]]
df_dict.head()
```

Out[12]:

|      |          word |                                                  translation |
| ---: | ------------: | -----------------------------------------------------------: |
|    0 |            'a | na. 一\nn. 英文字母表的第一字母；【乐】A音\nart. 冠以不定冠词主要表示类别\... |
|    1 |      'A' game |                                  [网络] 游戏；一个游戏；一局 |
|    2 |    'Abbāsīyah |                                       [地名] 阿巴西耶 ( 埃 ) |
|    3 |  'Abd al Kūrī |                               [地名] 阿卜杜勒库里岛 ( 也门 ) |
|    4 | 'Abd al Mājid |                               [地名] 阿卜杜勒马吉德 ( 苏丹 ) |

### 3. 英文分词和数据清洗

In [13]:

```python
# 分词
import re
word_list = re.split("""[ ,.\(\)/\n|\-:=\$\["']""", pdf_cont)
word_list[:10]
```

Out[13]:

```python
['Wide',
 '&',
 'Deep',
 'Learning',
 'for',
 'Recommender',
 'Systems',
 'Heng',
 'Tze',
 'Cheng']
```

In [14]:

```python
# 数据清洗
word_list_clean = []
for word in word_list:
    word = str(word).lower().strip()
    # 过滤掉空词、数字、单个字符的词、停用词
    if not word or word.isnumeric() or len(word)<=1:
        continue
    word_list_clean.append(word)
word_list_clean[:20]
```

Out[14]:

```python
['wide',
 'deep',
 'learning',
 'for',
 'recommender',
 'systems',
 'heng',
 'tze',
 'cheng',
 'levent',
 'koc',
 'jeremiah',
 'harmsen',
 'tal',
 'shaked',
 'tushar',
 'chandra',
 'hrishi',
 'aradhye',
 'glen']
```

### 4. 分词结果构造成一个DataFrame

In [15]:

```python
df_words = pd.DataFrame({
    "word": word_list_clean
})
df_words.head()
```

Out[15]:

|      |        word |
| ---: | ----------: |
|    0 |        wide |
|    1 |        deep |
|    2 |    learning |
|    3 |         for |
|    4 | recommender |

In [16]:

```python
df_words.shape
```

Out[16]:

```python
(2322, 1)
```

In [17]:

```python
# 统计词频
df_words = (
    df_words
    .groupby("word")["word"]
    .agg(count="size")
    .reset_index()
    .sort_values(by="count", ascending=False)
)
df_words.head(10)
```

Out[17]:

|      |  word | count |
| ---: | ----: | ----: |
|  804 |   the |   128 |
|   57 |   and |    67 |
|  546 |    of |    46 |
|  503 | model |    41 |
|  939 |  wide |    36 |
|  374 |    in |    36 |
|  203 |  deep |    35 |
|  405 |    is |    31 |
|  286 |   for |    30 |
|  845 |    to |    29 |

### 5. 和单词词典实现merge

In [21]:

```python
df_merge = pd.merge(
    left = df_dict,
    right = df_words,
    left_on = "word",
    right_on = "word"
)
```

In [32]:

```python
df_merge.sample(10)
```

Out[32]:

|      |           word |                                                  translation | count |
| ---: | -------------: | -----------------------------------------------------------: | ----: |
|    1 |        account | n. 报告, 解释, 估价, 理由, 利润, 算账, 帐目\nvi. 报帐, 解释, 导致,... |     1 |
|  380 |     prediction |                                     n. 预言, 预报\n[化] 预测 |     2 |
|  185 | generalization |                   n. 一般化, 普遍化, 概括\n[化] 推广; 普适化 |     4 |
|   56 |         burget |                                                [人名] 伯吉特 |     1 |
|  372 |       pipeline |                           n. 管道, 传递途径\n[化] 管路; 管线 |     1 |
|  237 |        include | vt. 包括, 把...算入, 包住\n[计] DOS内部命令:在CONFIG.SYS文件的... |     2 |
|  524 |        threads |                        n. 线；相关串连；线程（thread的复数） |     2 |
|  208 |           heng |                                                    n. 恒; 珩 |     1 |
|   62 |       capacity |                         n. 容量, 能力, 才能, 资格\n[计] 容量 |     1 |
|  228 |      important |       a. 重要的, 有地位的, 大量的, 显要的, 自负的\n[计] 要点 |     2 |

In [33]:

```python
df_merge.shape
```

Out[33]:

```python
(607, 3)
```

### 6. 存入Excel

In [34]:

```python
df_merge.to_excel("./39. pdf_chinese_english.xlsx", index=False)
```

# 40、Pandas怎样实现groupby聚合后字符串列的合并

#### 需求：

计算每个月的最高温度、最低温度、出现的风向列表、出现的空气质量列表

#### 数据输入

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_pandas/31.png)

#### 数据输出

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_pandas/32.png)

### 读取数据

In [1]:

```python
import pandas as pd
```

In [2]:

```python
fpath = "./datas/beijing_tianqi/beijing_tianqi_2018.csv"
df = pd.read_csv(fpath)
df.head(3)
```

Out[2]:

|      |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi | aqiInfo | aqiLevel |
| ---: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | ------: | -------: |
|    0 | 2018-01-01 |     3℃ |    -6℃ | 晴~多云 |    东北风 |  1-2级 |   59 |      良 |        2 |
|    1 | 2018-01-02 |     2℃ |    -5℃ | 阴~多云 |    东北风 |  1-2级 |   49 |      优 |        1 |
|    2 | 2018-01-03 |     2℃ |    -5℃ |    多云 |      北风 |  1-2级 |   28 |      优 |        1 |

#### 知识：使用df.info()可以查看每列的类型

In [3]:

```python
df.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 365 entries, 0 to 364
Data columns (total 9 columns):
 #   Column     Non-Null Count  Dtype 
---  ------     --------------  ----- 
 0   ymd        365 non-null    object
 1   bWendu     365 non-null    object
 2   yWendu     365 non-null    object
 3   tianqi     365 non-null    object
 4   fengxiang  365 non-null    object
 5   fengli     365 non-null    object
 6   aqi        365 non-null    int64 
 7   aqiInfo    365 non-null    object
 8   aqiLevel   365 non-null    int64 
dtypes: int64(2), object(7)
memory usage: 25.8+ KB
```

#### 知识：series怎样从str类型变成int

In [4]:

```python
df["bWendu"] = df["bWendu"].str.replace("℃", "").astype('int32')
df["yWendu"] = df["yWendu"].str.replace("℃", "").astype('int32')
df.head(3)
```

Out[4]:

|      |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi | aqiInfo | aqiLevel |
| ---: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | ------: | -------: |
|    0 | 2018-01-01 |      3 |     -6 | 晴~多云 |    东北风 |  1-2级 |   59 |      良 |        2 |
|    1 | 2018-01-02 |      2 |     -5 | 阴~多云 |    东北风 |  1-2级 |   49 |      优 |        1 |
|    2 | 2018-01-03 |      2 |     -5 |    多云 |      北风 |  1-2级 |   28 |      优 |        1 |

#### 知识：进行日期列解析，可以方便提取月份

In [5]:

```python
df["ymd"] = pd.to_datetime(df["ymd"])
```

In [6]:

```python
df["ymd"].dt.month
```

Out[6]:

```python
0       1
1       1
2       1
3       1
4       1
       ..
360    12
361    12
362    12
363    12
364    12
Name: ymd, Length: 365, dtype: int64
```

#### 知识：series可以用Series.unique去重

In [7]:

```python
df["fengxiang"].unique()
```

Out[7]:

```v
array(['东北风', '北风', '西北风', '西南风', '南风', '东南风', '东风', '西风'], dtype=object)
```

#### 知识：可以用",".join(series)实现数组合并成大字符串

In [8]:

```python
",".join(df["fengxiang"].unique())
```

Out[8]:

```python
'东北风,北风,西北风,西南风,南风,东南风,东风,西风'
```

### 方法1

In [9]:

```python
result = (
    df.groupby(df["ymd"].dt.month)
      .agg(
          # 新列名 = (原列名，函数)
          最高温度=("bWendu", "max"),
          最低温度=("yWendu", "min"),
          风向列表=("fengxiang", lambda x : ",".join(x.unique())),
          空气质量列表=("aqiInfo", lambda x : ",".join(x.unique()))
      )
      .reset_index()
      .rename(columns={"ymd":"月份"})
)
```

In [10]:

```python
result
```

Out[10]:

|      | 月份 | 最高温度 | 最低温度 |                                        风向列表 |                              空气质量列表 |
| ---: | ---: | -------: | -------: | ----------------------------------------------: | ----------------------------------------: |
|    0 |    1 |        7 |      -12 |      东北风,北风,西北风,西南风,南风,东南风,东风 |                   良,优,轻度污染,中度污染 |
|    1 |    2 |       12 |      -10 |        北风,西南风,南风,西北风,西风,东北风,东风 |          良,优,轻度污染,中度污染,重度污染 |
|    2 |    3 |       27 |       -4 |             西南风,北风,东南风,南风,东北风,东风 | 优,良,重度污染,轻度污染,中度污染,严重污染 |
|    3 |    4 |       30 |        1 |           南风,北风,东北风,西南风,西北风,东南风 |          重度污染,良,优,轻度污染,中度污染 |
|    4 |    5 |       35 |       10 | 东北风,北风,西南风,南风,东南风,东风,西风,西北风 |                   轻度污染,优,良,中度污染 |
|    5 |    6 |       38 |       17 |             西南风,南风,北风,东风,东南风,东北风 |                   良,轻度污染,优,中度污染 |
|    6 |    7 |       37 |       22 |        东南风,西南风,南风,东北风,东风,西风,北风 |                            良,轻度污染,优 |
|    7 |    8 |       36 |       20 |             东南风,南风,东风,东北风,北风,西南风 |                            良,轻度污染,优 |
|    8 |    9 |       31 |       11 |                         南风,北风,西南风,西北风 |                            优,良,轻度污染 |
|    9 |   10 |       25 |        1 |             北风,西北风,南风,西风,东北风,西南风 |                   优,良,轻度污染,中度污染 |
|   10 |   11 |       18 |       -4 |           南风,北风,西南风,东南风,西北风,东北风 |          良,轻度污染,重度污染,优,中度污染 |
|   11 |   12 |       10 |      -12 |                     东南风,东北风,西北风,西南风 |          中度污染,重度污染,良,优,轻度污染 |

### 方法2

In [11]:

```python
def agg_func(x):
    """注意，这个x是每个分组的dataframe"""
    return pd.Series({
        "最高温度": x["bWendu"].max(),
        "最低温度": x["yWendu"].min(),
        "风向列表": ",".join(x["fengxiang"].unique()),
        "空气质量列表": ",".join(x["aqiInfo"].unique())
    })

result = df \
        .groupby(df["ymd"].dt.month) \
        .apply(agg_func) \
        .reset_index() \
        .rename(columns={"ymd":"月份"})
```

In [12]:

```python
result
```

Out[12]:

|      | 月份 | 最高温度 | 最低温度 |                                        风向列表 |                              空气质量列表 |
| ---: | ---: | -------: | -------: | ----------------------------------------------: | ----------------------------------------: |
|    0 |    1 |        7 |      -12 |      东北风,北风,西北风,西南风,南风,东南风,东风 |                   良,优,轻度污染,中度污染 |
|    1 |    2 |       12 |      -10 |        北风,西南风,南风,西北风,西风,东北风,东风 |          良,优,轻度污染,中度污染,重度污染 |
|    2 |    3 |       27 |       -4 |             西南风,北风,东南风,南风,东北风,东风 | 优,良,重度污染,轻度污染,中度污染,严重污染 |
|    3 |    4 |       30 |        1 |           南风,北风,东北风,西南风,西北风,东南风 |          重度污染,良,优,轻度污染,中度污染 |
|    4 |    5 |       35 |       10 | 东北风,北风,西南风,南风,东南风,东风,西风,西北风 |                   轻度污染,优,良,中度污染 |
|    5 |    6 |       38 |       17 |             西南风,南风,北风,东风,东南风,东北风 |                   良,轻度污染,优,中度污染 |
|    6 |    7 |       37 |       22 |        东南风,西南风,南风,东北风,东风,西风,北风 |                            良,轻度污染,优 |
|    7 |    8 |       36 |       20 |             东南风,南风,东风,东北风,北风,西南风 |                            良,轻度污染,优 |
|    8 |    9 |       31 |       11 |                         南风,北风,西南风,西北风 |                            优,良,轻度污染 |
|    9 |   10 |       25 |        1 |             北风,西北风,南风,西风,东北风,西南风 |                   优,良,轻度污染,中度污染 |
|   10 |   11 |       18 |       -4 |           南风,北风,西南风,东南风,西北风,东北风 |          良,轻度污染,重度污染,优,中度污染 |
|   11 |   12 |       10 |      -12 |                     东南风,东北风,西北风,西南风 |          中度污染,重度污染,良,优,轻度污染 |

# 41、Pandas读取Excel绘制直方图

***直方图(Histogram)：\***
直方图是数值数据分布的精确图形表示，是一个连续变量（定量变量）的概率分布的估计，它是一种条形图。
为了构建直方图，第一步是将值的范围分段，即将整个值的范围分成一系列间隔，然后计算每个间隔中有多少值。 

### 1. 读取数据

波斯顿房价数据集

In [1]:

```python
import pandas as pd
import numpy as np
```

In [2]:

```python
df = pd.read_excel("./datas/boston-house-prices/housing.xlsx")
```

In [3]:

```python
df
```

Out[3]:

|      |    CRIM |   ZN | INDUS | CHAS |   NOX |    RM |  AGE |    DIS |  RAD |  TAX | PTRATIO |      B | LSTAT | MEDV |
| ---: | ------: | ---: | ----: | ---: | ----: | ----: | ---: | -----: | ---: | ---: | ------: | -----: | ----: | ---: |
|    0 | 0.00632 | 18.0 |  2.31 |    0 | 0.538 | 6.575 | 65.2 | 4.0900 |    1 |  296 |    15.3 | 396.90 |  4.98 | 24.0 |
|    1 | 0.02731 |  0.0 |  7.07 |    0 | 0.469 | 6.421 | 78.9 | 4.9671 |    2 |  242 |    17.8 | 396.90 |  9.14 | 21.6 |
|    2 | 0.02729 |  0.0 |  7.07 |    0 | 0.469 | 7.185 | 61.1 | 4.9671 |    2 |  242 |    17.8 | 392.83 |  4.03 | 34.7 |
|    3 | 0.03237 |  0.0 |  2.18 |    0 | 0.458 | 6.998 | 45.8 | 6.0622 |    3 |  222 |    18.7 | 394.63 |  2.94 | 33.4 |
|    4 | 0.06905 |  0.0 |  2.18 |    0 | 0.458 | 7.147 | 54.2 | 6.0622 |    3 |  222 |    18.7 | 396.90 |  5.33 | 36.2 |
|  ... |     ... |  ... |   ... |  ... |   ... |   ... |  ... |    ... |  ... |  ... |     ... |    ... |   ... |  ... |
|  501 | 0.06263 |  0.0 | 11.93 |    0 | 0.573 | 6.593 | 69.1 | 2.4786 |    1 |  273 |    21.0 | 391.99 |  9.67 | 22.4 |
|  502 | 0.04527 |  0.0 | 11.93 |    0 | 0.573 | 6.120 | 76.7 | 2.2875 |    1 |  273 |    21.0 | 396.90 |  9.08 | 20.6 |
|  503 | 0.06076 |  0.0 | 11.93 |    0 | 0.573 | 6.976 | 91.0 | 2.1675 |    1 |  273 |    21.0 | 396.90 |  5.64 | 23.9 |
|  504 | 0.10959 |  0.0 | 11.93 |    0 | 0.573 | 6.794 | 89.3 | 2.3889 |    1 |  273 |    21.0 | 393.45 |  6.48 | 22.0 |
|  505 | 0.04741 |  0.0 | 11.93 |    0 | 0.573 | 6.030 | 80.8 | 2.5050 |    1 |  273 |    21.0 | 396.90 |  7.88 | 11.9 |

506 rows × 14 columns

In [4]:

```python
df.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 506 entries, 0 to 505
Data columns (total 14 columns):
 #   Column   Non-Null Count  Dtype  
---  ------   --------------  -----  
 0   CRIM     506 non-null    float64
 1   ZN       506 non-null    float64
 2   INDUS    506 non-null    float64
 3   CHAS     506 non-null    int64  
 4   NOX      506 non-null    float64
 5   RM       506 non-null    float64
 6   AGE      506 non-null    float64
 7   DIS      506 non-null    float64
 8   RAD      506 non-null    int64  
 9   TAX      506 non-null    int64  
 10  PTRATIO  506 non-null    float64
 11  B        506 non-null    float64
 12  LSTAT    506 non-null    float64
 13  MEDV     506 non-null    float64
dtypes: float64(11), int64(3)
memory usage: 55.5 KB
```

In [5]:

```python
df["MEDV"]
```

Out[5]:

```python
0      24.0
1      21.6
2      34.7
3      33.4
4      36.2
       ... 
501    22.4
502    20.6
503    23.9
504    22.0
505    11.9
Name: MEDV, Length: 506, dtype: float64
```

### 2. 使用matplotlib画直方图

matplotlib直方图文档：https://matplotlib.org/3.2.0/api/_as_gen/matplotlib.pyplot.hist.html

In [6]:

```python
import matplotlib.pyplot as plt
%matplotlib inline
```

In [7]:

```python
plt.figure(figsize=(12, 5))
plt.hist(df["MEDV"], bins=100)
plt.show()
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/python_pandas/33.png)

### 3. 使用pyecharts画直方图

pyecharts直方图文档：http://gallery.pyecharts.org/#/Bar/bar_histogram
numpy直方图文档：https://docs.scipy.org/doc/numpy/reference/generated/numpy.histogram.html

In [8]:

```python
from pyecharts import options as opts
from pyecharts.charts import Bar
```

In [9]:

```python
# 需要自己计算有多少个间隔、以及每个间隔有多少个值
hist,bin_edges =  np.histogram(df["MEDV"], bins=100)
```

In [10]:

```python
# 这是每个间隔的分割点
bin_edges
```

Out[10]:

```python
array([ 5.  ,  5.45,  5.9 ,  6.35,  6.8 ,  7.25,  7.7 ,  8.15,  8.6 ,
        9.05,  9.5 ,  9.95, 10.4 , 10.85, 11.3 , 11.75, 12.2 , 12.65,
       13.1 , 13.55, 14.  , 14.45, 14.9 , 15.35, 15.8 , 16.25, 16.7 ,
       17.15, 17.6 , 18.05, 18.5 , 18.95, 19.4 , 19.85, 20.3 , 20.75,
       21.2 , 21.65, 22.1 , 22.55, 23.  , 23.45, 23.9 , 24.35, 24.8 ,
       25.25, 25.7 , 26.15, 26.6 , 27.05, 27.5 , 27.95, 28.4 , 28.85,
       29.3 , 29.75, 30.2 , 30.65, 31.1 , 31.55, 32.  , 32.45, 32.9 ,
       33.35, 33.8 , 34.25, 34.7 , 35.15, 35.6 , 36.05, 36.5 , 36.95,
       37.4 , 37.85, 38.3 , 38.75, 39.2 , 39.65, 40.1 , 40.55, 41.  ,
       41.45, 41.9 , 42.35, 42.8 , 43.25, 43.7 , 44.15, 44.6 , 45.05,
       45.5 , 45.95, 46.4 , 46.85, 47.3 , 47.75, 48.2 , 48.65, 49.1 ,
       49.55, 50.  ])
```

In [11]:

```python
len(bin_edges)
```

Out[11]:

```python
101
```

In [12]:

```python
# 这是间隔的计数
hist
```

Out[12]:

```python
array([ 2,  1,  1,  0,  5,  2,  1,  6,  3,  0,  3,  3,  5,  3,  4,  6,  3,
        5, 14,  9,  9,  6, 11,  8,  6,  8,  6, 10,  9,  9, 15, 13, 20, 16,
       19, 10, 14, 19, 13, 15, 21, 16,  9, 12, 14,  1,  0,  4,  5,  2,  6,
        5,  5,  4,  3,  6,  2,  3,  4,  3,  4,  3,  6,  2,  1,  1,  5,  3,
        1,  4,  1,  3,  1,  1,  1,  0,  0,  1,  0,  0,  1,  1,  1,  1,  1,
        1,  2,  0,  1,  1,  0,  1,  1,  0,  0,  0,  2,  1,  0, 16],
      dtype=int64)
```

In [13]:

```python
len(hist)
```

Out[13]:

```python
100
```

#### 对bin_edges的解释，为什么是101个？比hist计数多1个？

举例：如果bins是[1, 2, 3, 4]，那么会分成3个区间：[1, 2)、[2, 3)、[3, 4]；
其中bins的第一个值是数组的最小值，bins的最后一个元素是数组的最大值

In [14]:

```python
# 注意观察，min是bins的第一个值，max是bins的最后一个元素
df["MEDV"].describe()
```

Out[14]:

```python
count    506.000000
mean      22.532806
std        9.197104
min        5.000000
25%       17.025000
50%       21.200000
75%       25.000000
max       50.000000
Name: MEDV, dtype: float64
```

In [15]:

```
# 查看bins每一个值和前一个值的差值，可以看到这是等分的数据
np.diff(bin_edges)
```

Out[15]:

```python
array([0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45,
       0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45,
       0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45,
       0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45,
       0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45,
       0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45,
       0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45,
       0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45,
       0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45, 0.45,
       0.45])
```

In [16]:

```python
# 这些间隔的数目，刚好等于计数hist的数目
len(np.diff(bin_edges))
```

Out[16]:

```python
100
```

In [17]:

```python
# pyecharts的直方图使用bar实现
# 取bins[:-1]，意思是用每个区间的左边元素作为x轴的值
bar = (
    Bar()
    .add_xaxis([str(x) for x in bin_edges[:-1]])
    .add_yaxis("价格分布", [float(x) for x in hist], category_gap=0)
    .set_global_opts(
        title_opts=opts.TitleOpts(title="波斯顿房价-价格分布-直方图", pos_left="center"),
        legend_opts=opts.LegendOpts(is_show=False)
    )
)
```

In [18]:

```python
bar.render_notebook()
```

# 42、Python处理Excel一列变多列

### 1. 读取数据

In [1]:

```python
import pandas as pd
```

In [2]:

```python
df = pd.read_excel("./course_datas/c42_split_onecolumn_tomany/学生数据表.xlsx")
```

In [3]:

```python
df.head()
```

Out[3]:

|      | 学号 |            数据 |
| ---: | ---: | --------------: |
|    0 | S001 | 怠涵:女:23:山东 |
|    1 | S002 | 婉清:女:25:河南 |
|    2 | S003 | 溪榕:女:23:湖北 |
|    3 | S004 | 漠涓:女:19:陕西 |
|    4 | S005 | 祈博:女:24:山东 |

### 2. 实现拆分

In [4]:

```python
def split_func(line):
    line["姓名"], line["性别"], line["年龄"], line["城市"] = line["数据"].split(":")
    return line

df = df.apply(split_func, axis=1)
```

In [5]:

```python
df.head()
```

Out[5]:

|      | 学号 |            数据 | 姓名 | 性别 | 年龄 | 城市 |
| ---: | ---: | --------------: | ---: | ---: | ---: | ---: |
|    0 | S001 | 怠涵:女:23:山东 | 怠涵 |   女 |   23 | 山东 |
|    1 | S002 | 婉清:女:25:河南 | 婉清 |   女 |   25 | 河南 |
|    2 | S003 | 溪榕:女:23:湖北 | 溪榕 |   女 |   23 | 湖北 |
|    3 | S004 | 漠涓:女:19:陕西 | 漠涓 |   女 |   19 | 陕西 |
|    4 | S005 | 祈博:女:24:山东 | 祈博 |   女 |   24 | 山东 |

In [6]:

```python
df.drop(["数据"], axis=1, inplace=True)
```

In [7]:

```python
df.head()
```

Out[7]:

|      | 学号 | 姓名 | 性别 | 年龄 | 城市 |
| ---: | ---: | ---: | ---: | ---: | ---: |
|    0 | S001 | 怠涵 |   女 |   23 | 山东 |
|    1 | S002 | 婉清 |   女 |   25 | 河南 |
|    2 | S003 | 溪榕 |   女 |   23 | 湖北 |
|    3 | S004 | 漠涓 |   女 |   19 | 陕西 |
|    4 | S005 | 祈博 |   女 |   24 | 山东 |

### 3. 输出到结果Excel

In [8]:

```python
df.to_excel("./course_datas/c42_split_onecolumn_tomany/学生数据表_拆分后.xlsx", index=False)
```

# 43、Pandas查询数据的简便方法df.query

怎样进行复杂组合条件对数据查询：

- 方式1. 使用df[(df["a"] > 3) & (df["b"]<5)]的方式；
- 方式2. 使用df.query("a>3 & b<5")的方式；

方法2的语法更加简洁

性能对比：

- 当数据量小时，方法1更快；
- 当数据量大时，因为方法2直接用C语言实现，节省方法1临时数组的多次复制，方法2更快；

In [1]:

```python
import pandas as pd
print(pd.__version__)
1.0.1
```

### 0、读取数据

数据为北京2018年全年天气预报 

In [2]:

```python
df = pd.read_csv("./datas/beijing_tianqi/beijing_tianqi_2018.csv")
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

### 1、使用dataframe条件表达式查询

#### 最低温度低于-10度的列表

In [5]:

```python
df[df["yWendu"] < -10].head()
```

Out[5]:

|      |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi | aqiInfo | aqiLevel |
| ---: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | ------: | -------: |
|   22 | 2018-01-23 |     -4 |    -12 |      晴 |    西北风 |  3-4级 |   31 |      优 |        1 |
|   23 | 2018-01-24 |     -4 |    -11 |      晴 |    西南风 |  1-2级 |   34 |      优 |        1 |
|   24 | 2018-01-25 |     -3 |    -11 |    多云 |    东北风 |  1-2级 |   27 |      优 |        1 |
|  359 | 2018-12-26 |     -2 |    -11 | 晴~多云 |    东北风 |    2级 |   26 |      优 |        1 |
|  360 | 2018-12-27 |     -5 |    -12 | 多云~晴 |    西北风 |    3级 |   48 |      优 |        1 |

#### 复杂条件查询

注意，组合条件用&符号合并，每个条件判断都得带括号

In [6]:

```python
## 查询最高温度小于30度，并且最低温度大于15度，并且是晴天，并且天气为优的数据
df[
    (df["bWendu"]<=30) 
    & (df["yWendu"]>=15) 
    & (df["tianqi"]=='晴') 
    & (df["aqiLevel"]==1)]
```

Out[6]:

|      |        ymd | bWendu | yWendu | tianqi | fengxiang | fengli |  aqi | aqiInfo | aqiLevel |
| ---: | ---------: | -----: | -----: | -----: | --------: | -----: | ---: | ------: | -------: |
|  235 | 2018-08-24 |     30 |     20 |     晴 |      北风 |  1-2级 |   40 |      优 |        1 |
|  249 | 2018-09-07 |     27 |     16 |     晴 |    西北风 |  3-4级 |   22 |      优 |        1 |

### 2、使用df.query可以简化查询

形式：DataFrame.query(expr, inplace=False, **kwargs)

其中expr为要返回boolean结果的字符串表达式

形如：

- df.query('a<100')
- df.query('a < b & b < c')，或者df.query('(a<b)&(b<c)')

df.query可支持的表达式语法：

- 逻辑操作符: &, |, ~
- 比较操作符: <, <=, ==, !=, >=, >
- 单变量操作符: -
- 多变量操作符: +, -, *, /, %

df.query中可以使用@var的方式传入外部变量

df.query支持的语法来自NumExpr，地址：
https://numexpr.readthedocs.io/projects/NumExpr3/en/latest/index.html

#### 查询最低温度低于-10度的列表

In [7]:

```python
df.query("yWendu < 3").head(3)
```

Out[7]:

|      |        ymd | bWendu | yWendu |  tianqi | fengxiang | fengli |  aqi | aqiInfo | aqiLevel |
| ---: | ---------: | -----: | -----: | ------: | --------: | -----: | ---: | ------: | -------: |
|    0 | 2018-01-01 |      3 |     -6 | 晴~多云 |    东北风 |  1-2级 |   59 |      良 |        2 |
|    1 | 2018-01-02 |      2 |     -5 | 阴~多云 |    东北风 |  1-2级 |   49 |      优 |        1 |
|    2 | 2018-01-03 |      2 |     -5 |    多云 |      北风 |  1-2级 |   28 |      优 |        1 |

#### 查询最高温度小于30度，并且最低温度大于15度，并且是晴天，并且天气为优的数据

In [8]:

```python
## 查询最高温度小于30度，并且最低温度大于15度，并且是晴天，并且天气为优的数据
df.query("bWendu<=30 & yWendu>=15 & tianqi=='晴' & aqiLevel==1")
```

Out[8]:

|      |        ymd | bWendu | yWendu | tianqi | fengxiang | fengli |  aqi | aqiInfo | aqiLevel |
| ---: | ---------: | -----: | -----: | -----: | --------: | -----: | ---: | ------: | -------: |
|  235 | 2018-08-24 |     30 |     20 |     晴 |      北风 |  1-2级 |   40 |      优 |        1 |
|  249 | 2018-09-07 |     27 |     16 |     晴 |    西北风 |  3-4级 |   22 |      优 |        1 |

#### 查询温差大于15度的日子

In [9]:

```python
df.query("bWendu-yWendu >= 15").head()
```

Out[9]:

|      |        ymd | bWendu | yWendu | tianqi | fengxiang | fengli |  aqi |  aqiInfo | aqiLevel |
| ---: | ---------: | -----: | -----: | -----: | --------: | -----: | ---: | -------: | -------: |
|   68 | 2018-03-10 |     14 |     -2 |     晴 |    东南风 |  1-2级 |  171 | 中度污染 |        4 |
|   82 | 2018-03-24 |     22 |      5 |     晴 |    西南风 |  1-2级 |  119 | 轻度污染 |        3 |
|   83 | 2018-03-25 |     24 |      7 |     晴 |      南风 |  1-2级 |   78 |       良 |        2 |
|   84 | 2018-03-26 |     25 |      7 |   多云 |    西南风 |  1-2级 |  151 | 中度污染 |        4 |
|   85 | 2018-03-27 |     27 |     11 |     晴 |      南风 |  1-2级 |  243 | 重度污染 |        5 |

#### 可以使用外部的变量

In [10]:

```python
# 查询温度在这两个温度之间的数据
high_temperature = 15
low_temperature = 13
```

In [11]:

```python
df.query("yWendu<=@high_temperature & yWendu>=@low_temperature").head()
```

Out[11]:

|      |        ymd | bWendu | yWendu |    tianqi | fengxiang | fengli |  aqi |  aqiInfo | aqiLevel |
| ---: | ---------: | -----: | -----: | --------: | --------: | -----: | ---: | -------: | -------: |
|  107 | 2018-04-18 |     27 |     14 |   多云~晴 |    西南风 |  3-4级 |  147 | 轻度污染 |        3 |
|  108 | 2018-04-19 |     26 |     13 |      多云 |    东南风 |  4-5级 |  170 | 中度污染 |        4 |
|  109 | 2018-04-20 |     28 |     14 | 多云~小雨 |      南风 |  4-5级 |  164 | 中度污染 |        4 |
|  116 | 2018-04-27 |     25 |     13 |        晴 |    西南风 |  3-4级 |  112 | 轻度污染 |        3 |
|  119 | 2018-04-30 |     24 |     14 |      多云 |      南风 |  3-4级 |   62 |       良 |        2 |

# 44、Pandas按行遍历DataFrame的3种方法

In [1]:

```python
import pandas as pd
import numpy as np
import collections
```

In [2]:

```python
df = pd.DataFrame(
    np.random.random(size=(100000, 4)), 
    columns=list('ABCD')
)
df.head(3)
```

Out[2]:

|      |        A |        B |        C |        D |
| ---: | -------: | -------: | -------: | -------: |
|    0 | 0.329292 | 0.975468 | 0.133584 | 0.224582 |
|    1 | 0.535746 | 0.451585 | 0.713250 | 0.409770 |
|    2 | 0.735287 | 0.667472 | 0.950622 | 0.245938 |

In [3]:

```python
df.shape
```

Out[3]:

```python
(100000, 4)
```

### 1. df.iterrows()

#### 使用方式

In [4]:

```python
for idx, row in df.iterrows():
    print(idx, row)
    print(idx, row["A"], row["B"], row["C"], row["D"])
    break
0 A    0.329292
B    0.975468
C    0.133584
D    0.224582
Name: 0, dtype: float64
0 0.3292915092119043 0.9754683984716609 0.1335841433264423 0.22458227907355865
```

#### 时间耗费

In [5]:

```python
%%time
result = collections.defaultdict(int)
for idx, row in df.iterrows():
    result[(row["A"], row["B"])] += row["A"] + row["B"]
CPU times: user 7.82 s, sys: 35.6 ms, total: 7.85 s
Wall time: 7.89 s
```

### 2. df.itertuples()

#### 使用方式

In [6]:

```python
for row in df.itertuples():
    print(row)
    print(row.Index, row.A, row.B, row.C, row.D)
    break
Pandas(Index=0, A=0.3292915092119043, B=0.9754683984716609, C=0.1335841433264423, D=0.22458227907355865)
0 0.3292915092119043 0.9754683984716609 0.1335841433264423 0.22458227907355865
```

#### 时间耗费

In [7]:

```python
%%time
result = collections.defaultdict(int)
for row in df.itertuples():
    result[(row.A, row.B)] += row.A + row.B
CPU times: user 168 ms, sys: 8.35 ms, total: 177 ms
Wall time: 178 ms
```

### 3. for+zip

#### 使用方式

In [8]:

```python
# 既不需要类型检查，也不需要构建namedtuple
# 缺点是需要挨个指定变量
for A, B in zip(df["A"], df["B"]):
    print(A, B)
    break
0.3292915092119043 0.9754683984716609
```

#### 时间耗费

In [9]:

```python
%%time
result = collections.defaultdict(int)
for A, B in zip(df["A"], df["B"]):
    result[(A, B)] += A + B
CPU times: user 82.2 ms, sys: 7.05 ms, total: 89.2 ms
Wall time: 89.9 ms
```

# 45、Pandas实现模糊匹配Merge数据的方法

In [1]:

```python
import pandas as pd
import numpy as np
import re
```

### 1. 两份数据

In [2]:

```python
# 关键词数据
df_keyword = pd.DataFrame({
    "keyid": np.arange(5),
    "keyword": ["numpy", "pandas", "matplotlib", "sklearn", "tensorflow"]
})
df_keyword
```

Out[2]:

|      | keyid |    keyword |
| ---: | ----: | ---------: |
|    0 |     0 |      numpy |
|    1 |     1 |     pandas |
|    2 |     2 | matplotlib |
|    3 |     3 |    sklearn |
|    4 |     4 | tensorflow |

In [3]:

```python
# 句子数据
df_sentence = pd.DataFrame({
    "senid": np.arange(10, 17),
    "sentence": [
        "怎样用Pandas实现数据的Merge？",
        "Python之Numpy详细教程",
        "怎样使用Pandas批量拆分与合并Excel文件？",
        "怎样使用Pandas的map和apply函数？",
        "深度学习及TensorFlow简介",
        "Tensorflow和Numpy的关系",
        "基于sklearn的一些机器学习的代码"
    ]
})
df_sentence
```

Out[3]:

|      | senid |                                sentence |
| ---: | ----: | --------------------------------------: |
|    0 |    10 |           怎样用Pandas实现数据的Merge？ |
|    1 |    11 |                   Python之Numpy详细教程 |
|    2 |    12 | 怎样使用Pandas批量拆分与合并Excel文件？ |
|    3 |    13 |        怎样使用Pandas的map和apply函数？ |
|    4 |    14 |                深度学习及TensorFlow简介 |
|    5 |    15 |                 Tensorflow和Numpy的关系 |
|    6 |    16 |         基于sklearn的一些机器学习的代码 |

### 方法1：暴力笛卡尔积 + 过滤

#### 新增数字完全一样的列

In [4]:

```python
df_keyword["match"] = 1
df_sentence["match"] = 1
```

In [5]:

```python
df_keyword
```

Out[5]:

|      | keyid |    keyword | match |
| ---: | ----: | ---------: | ----: |
|    0 |     0 |      numpy |     1 |
|    1 |     1 |     pandas |     1 |
|    2 |     2 | matplotlib |     1 |
|    3 |     3 |    sklearn |     1 |
|    4 |     4 | tensorflow |     1 |

In [6]:

```python
df_sentence
```

Out[6]:

|      | senid |                                sentence | match |
| ---: | ----: | --------------------------------------: | ----: |
|    0 |    10 |           怎样用Pandas实现数据的Merge？ |     1 |
|    1 |    11 |                   Python之Numpy详细教程 |     1 |
|    2 |    12 | 怎样使用Pandas批量拆分与合并Excel文件？ |     1 |
|    3 |    13 |        怎样使用Pandas的map和apply函数？ |     1 |
|    4 |    14 |                深度学习及TensorFlow简介 |     1 |
|    5 |    15 |                 Tensorflow和Numpy的关系 |     1 |
|    6 |    16 |         基于sklearn的一些机器学习的代码 |     1 |

#### 实现merge

结果行数 = A表行数 * B表行数

In [7]:

```python
df_merge = pd.merge(df_keyword, df_sentence)
df_merge
```

Out[7]:

|      | keyid |    keyword | match | senid |                                sentence |
| ---: | ----: | ---------: | ----: | ----: | --------------------------------------: |
|    0 |     0 |      numpy |     1 |    10 |           怎样用Pandas实现数据的Merge？ |
|    1 |     0 |      numpy |     1 |    11 |                   Python之Numpy详细教程 |
|    2 |     0 |      numpy |     1 |    12 | 怎样使用Pandas批量拆分与合并Excel文件？ |
|    3 |     0 |      numpy |     1 |    13 |        怎样使用Pandas的map和apply函数？ |
|    4 |     0 |      numpy |     1 |    14 |                深度学习及TensorFlow简介 |
|    5 |     0 |      numpy |     1 |    15 |                 Tensorflow和Numpy的关系 |
|    6 |     0 |      numpy |     1 |    16 |         基于sklearn的一些机器学习的代码 |
|    7 |     1 |     pandas |     1 |    10 |           怎样用Pandas实现数据的Merge？ |
|    8 |     1 |     pandas |     1 |    11 |                   Python之Numpy详细教程 |
|    9 |     1 |     pandas |     1 |    12 | 怎样使用Pandas批量拆分与合并Excel文件？ |
|   10 |     1 |     pandas |     1 |    13 |        怎样使用Pandas的map和apply函数？ |
|   11 |     1 |     pandas |     1 |    14 |                深度学习及TensorFlow简介 |
|   12 |     1 |     pandas |     1 |    15 |                 Tensorflow和Numpy的关系 |
|   13 |     1 |     pandas |     1 |    16 |         基于sklearn的一些机器学习的代码 |
|   14 |     2 | matplotlib |     1 |    10 |           怎样用Pandas实现数据的Merge？ |
|   15 |     2 | matplotlib |     1 |    11 |                   Python之Numpy详细教程 |
|   16 |     2 | matplotlib |     1 |    12 | 怎样使用Pandas批量拆分与合并Excel文件？ |
|   17 |     2 | matplotlib |     1 |    13 |        怎样使用Pandas的map和apply函数？ |
|   18 |     2 | matplotlib |     1 |    14 |                深度学习及TensorFlow简介 |
|   19 |     2 | matplotlib |     1 |    15 |                 Tensorflow和Numpy的关系 |
|   20 |     2 | matplotlib |     1 |    16 |         基于sklearn的一些机器学习的代码 |
|   21 |     3 |    sklearn |     1 |    10 |           怎样用Pandas实现数据的Merge？ |
|   22 |     3 |    sklearn |     1 |    11 |                   Python之Numpy详细教程 |
|   23 |     3 |    sklearn |     1 |    12 | 怎样使用Pandas批量拆分与合并Excel文件？ |
|   24 |     3 |    sklearn |     1 |    13 |        怎样使用Pandas的map和apply函数？ |
|   25 |     3 |    sklearn |     1 |    14 |                深度学习及TensorFlow简介 |
|   26 |     3 |    sklearn |     1 |    15 |                 Tensorflow和Numpy的关系 |
|   27 |     3 |    sklearn |     1 |    16 |         基于sklearn的一些机器学习的代码 |
|   28 |     4 | tensorflow |     1 |    10 |           怎样用Pandas实现数据的Merge？ |
|   29 |     4 | tensorflow |     1 |    11 |                   Python之Numpy详细教程 |
|   30 |     4 | tensorflow |     1 |    12 | 怎样使用Pandas批量拆分与合并Excel文件？ |
|   31 |     4 | tensorflow |     1 |    13 |        怎样使用Pandas的map和apply函数？ |
|   32 |     4 | tensorflow |     1 |    14 |                深度学习及TensorFlow简介 |
|   33 |     4 | tensorflow |     1 |    15 |                 Tensorflow和Numpy的关系 |
|   34 |     4 | tensorflow |     1 |    16 |         基于sklearn的一些机器学习的代码 |

#### 过滤出结果

In [8]:

```python
def match_func(row):
    return re.search(row["keyword"], row["sentence"], re.IGNORECASE) is not None

df_merge[df_merge.apply(match_func, axis=1)]
```

Out[8]:

|      | keyid |    keyword | match | senid |                                sentence |
| ---: | ----: | ---------: | ----: | ----: | --------------------------------------: |
|    1 |     0 |      numpy |     1 |    11 |                   Python之Numpy详细教程 |
|    5 |     0 |      numpy |     1 |    15 |                 Tensorflow和Numpy的关系 |
|    7 |     1 |     pandas |     1 |    10 |           怎样用Pandas实现数据的Merge？ |
|    9 |     1 |     pandas |     1 |    12 | 怎样使用Pandas批量拆分与合并Excel文件？ |
|   10 |     1 |     pandas |     1 |    13 |        怎样使用Pandas的map和apply函数？ |
|   27 |     3 |    sklearn |     1 |    16 |         基于sklearn的一些机器学习的代码 |
|   32 |     4 | tensorflow |     1 |    14 |                深度学习及TensorFlow简介 |
|   33 |     4 | tensorflow |     1 |    15 |                 Tensorflow和Numpy的关系 |

### 方法2：小表变字典做merge最后explode

#### 构建要join的key:index的关系

In [9]:

```python
key_word_dict = {
    row.keyword : row.keyid 
    for row in df_keyword.itertuples()
}
key_word_dict
```

Out[9]:

```python
{'numpy': 0, 'pandas': 1, 'matplotlib': 2, 'sklearn': 3, 'tensorflow': 4}
```

#### 大表搜寻小表字典

In [10]:

```python
def merge_func(row):
    # 新增一列，表示能匹配的keyids
    row["keyids"] = [
        keyid
        for key_word, keyid in key_word_dict.items()
        if re.search(key_word, row["sentence"], re.IGNORECASE)
    ]
    return row

df_merge = df_sentence.apply(merge_func, axis=1)
```

In [11]:

```python
df_merge
```

Out[11]:

|      | senid |                                sentence | match | keyids |
| ---: | ----: | --------------------------------------: | ----: | -----: |
|    0 |    10 |           怎样用Pandas实现数据的Merge？ |     1 |    [1] |
|    1 |    11 |                   Python之Numpy详细教程 |     1 |    [0] |
|    2 |    12 | 怎样使用Pandas批量拆分与合并Excel文件？ |     1 |    [1] |
|    3 |    13 |        怎样使用Pandas的map和apply函数？ |     1 |    [1] |
|    4 |    14 |                深度学习及TensorFlow简介 |     1 |    [4] |
|    5 |    15 |                 Tensorflow和Numpy的关系 |     1 | [0, 4] |
|    6 |    16 |         基于sklearn的一些机器学习的代码 |     1 |    [3] |

#### 展开然后做merge

In [12]:

```python
df_merge.explode("keyids")
```

Out[12]:

|      | senid |                                sentence | match | keyids |
| ---: | ----: | --------------------------------------: | ----: | -----: |
|    0 |    10 |           怎样用Pandas实现数据的Merge？ |     1 |      1 |
|    1 |    11 |                   Python之Numpy详细教程 |     1 |      0 |
|    2 |    12 | 怎样使用Pandas批量拆分与合并Excel文件？ |     1 |      1 |
|    3 |    13 |        怎样使用Pandas的map和apply函数？ |     1 |      1 |
|    4 |    14 |                深度学习及TensorFlow简介 |     1 |      4 |
|    5 |    15 |                 Tensorflow和Numpy的关系 |     1 |      0 |
|    5 |    15 |                 Tensorflow和Numpy的关系 |     1 |      4 |
|    6 |    16 |         基于sklearn的一些机器学习的代码 |     1 |      3 |

In [13]:

```python
df_result = pd.merge(
    left = df_merge.explode("keyids"),
    right = df_keyword,
    left_on = "keyids",
    right_on = "keyid"
)
df_result
```

Out[13]:

|      | senid |                                sentence | match_x | keyids | keyid |    keyword | match_y |
| ---: | ----: | --------------------------------------: | ------: | -----: | ----: | ---------: | ------: |
|    0 |    10 |           怎样用Pandas实现数据的Merge？ |       1 |      1 |     1 |     pandas |       1 |
|    1 |    12 | 怎样使用Pandas批量拆分与合并Excel文件？ |       1 |      1 |     1 |     pandas |       1 |
|    2 |    13 |        怎样使用Pandas的map和apply函数？ |       1 |      1 |     1 |     pandas |       1 |
|    3 |    11 |                   Python之Numpy详细教程 |       1 |      0 |     0 |      numpy |       1 |
|    4 |    15 |                 Tensorflow和Numpy的关系 |       1 |      0 |     0 |      numpy |       1 |
|    5 |    14 |                深度学习及TensorFlow简介 |       1 |      4 |     4 | tensorflow |       1 |
|    6 |    15 |                 Tensorflow和Numpy的关系 |       1 |      4 |     4 | tensorflow |       1 |
|    7 |    16 |         基于sklearn的一些机器学习的代码 |       1 |      3 |     3 |    sklearn |       1 |