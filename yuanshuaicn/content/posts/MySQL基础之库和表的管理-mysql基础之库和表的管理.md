---
title: MySQL基础之库和表的管理
date: 2021-11-24 18:38:23.971
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
  avatar: "https://aabb-2023.oss-cn-beijing.aliyuncs.com/hjscijg3uw.png"
tags: 
- mysql
- 数据库
---

# DDL（数据定义语言）

库和表的管理

一、库的管理
创建、修改、删除
二、表的管理
创建、修改、删除

创建： create
修改： alter
删除： drop

## 一、库的管理

### 1、库的创建

语法：

```mysql
create database  [if not exists]库名;
```


案例：创建库Books

```mysql
CREATE DATABASE IF NOT EXISTS books ;
```

### 2、库的修改

```mysql
RENAME DATABASE books TO 新库名;
```

更改库的字符集

```mysql
ALTER DATABASE books CHARACTER SET gbk;
```

### 3、库的删除

```mysql
DROP DATABASE IF EXISTS books;
```

## 二、表的管理

### 1.表的创建 ★

语法：

```mysql
create table 表名(
	列名 列的类型【(长度) 约束】,
	列名 列的类型【(长度) 约束】,
	列名 列的类型【(长度) 约束】,
	...
	列名 列的类型【(长度) 约束】


)
```


案例：创建表Book

```mysql
CREATE TABLE book(
	id INT,#编号
	bName VARCHAR(20),#图书名
	price DOUBLE,#价格
	authorId  INT,#作者编号
	publishDate DATETIME#出版日期

);


DESC book;
```

案例：创建表author

```mysql
CREATE TABLE IF NOT EXISTS author(
	id INT,
	au_name VARCHAR(20),
	nation VARCHAR(10)

)
DESC author;
```

### 2.表的修改

语法

```mysql
alter table 表名 add|drop|modify|change column 列名 【列类型 约束】;
```

①修改列名

```mysql
ALTER TABLE book CHANGE COLUMN publishdate pubDate DATETIME;
```

②修改列的类型或约束

```mysql
ALTER TABLE book MODIFY COLUMN pubdate TIMESTAMP;
```

③添加新列

```mysql
ALTER TABLE author ADD COLUMN annual DOUBLE; 
```

④删除列

```mysql
ALTER TABLE book_author DROP COLUMN  annual;
```

⑤修改表名

```mysql
ALTER TABLE author RENAME TO book_author;

DESC book;
```

### 3.表的删除

```mysql
DROP TABLE IF EXISTS book_author;

SHOW TABLES;
```


通用的写法：

```mysql
DROP DATABASE IF EXISTS 旧库名;
CREATE DATABASE 新库名;


DROP TABLE IF EXISTS 旧表名;
CREATE TABLE  表名();
```

### 4.表的复制

```mysql
INSERT INTO author VALUES
(1,'村上春树','日本'),
(2,'莫言','中国'),
(3,'冯唐','中国'),
(4,'金庸','中国');

SELECT * FROM Author;
SELECT * FROM copy2;
```

1.仅仅复制表的结构

```mysql
CREATE TABLE copy LIKE author;
```

2.复制表的结构+数据

```mysql
CREATE TABLE copy2 
SELECT * FROM author;
```

只复制部分数据

```mysql
CREATE TABLE copy3
SELECT id,au_name
FROM author 
WHERE nation='中国';
```


仅仅复制某些字段

```mysql
CREATE TABLE copy4 
SELECT id,au_name
FROM author
WHERE 0;
```