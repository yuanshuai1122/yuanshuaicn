---
title: MySQL基础之常见约束和标识列
date: 2021-posts-24 08:53:56.513
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/mysql.png'
theme: 'light'
tags: 
- mysql
- 数据库
---

-----: | :----------: | :------------------: | :----------: |
| 主键 |     √      |      ×       |      至多有1个       | √，但不推荐  |
| 唯一 |     √      |      √       |      可以有多个      | √，但不推荐  |

外键：
	1、要求在从表设置外键关系
	2、从表的外键列的类型和主表的关联列的类型要求一致或兼容，名称无要求
	3、主表的关联列必须是一个key（一般是主键或唯一）
	4、插入数据时，先插入主表，再插入从表
	删除数据时，先删除从表，再删除主表

```mysql
CREATE TABLE 表名(
	字段名 字段类型 列级约束,
	字段名 字段类型,
	表级约束

)
CREATE DATABASE students;
```

## 一、创建表时添加约束

### 1.添加列级约束

语法：

直接在字段名和类型后面追加 约束类型即可。

只支持：默认、非空、主键、唯一

```mysql
USE students;
DROP TABLE stuinfo;
CREATE TABLE stuinfo(
	id INT PRIMARY KEY,#主键
	stuName VARCHAR(20) NOT NULL UNIQUE,#非空
	gender CHAR(1) CHECK(gender='男' OR gender ='女'),#检查
	seat INT UNIQUE,#唯一
	age INT DEFAULT  18,#默认约束
	majorId INT REFERENCES major(id)#外键

);


CREATE TABLE major(
	id INT PRIMARY KEY,
	majorName VARCHAR(20)
);

#查看stuinfo中的所有索引，包括主键、外键、唯一
SHOW INDEX FROM stuinfo;
```

### 2.添加表级约束

语法：在各个字段的最下面
 【constraint 约束名】 约束类型(字段名) 

```mysql
DROP TABLE IF EXISTS stuinfo;
CREATE TABLE stuinfo(
	id INT,
	stuname VARCHAR(20),
	gender CHAR(1),
	seat INT,
	age INT,
	majorid INT,
	
  CONSTRAINT pk PRIMARY KEY(id),#主键
  CONSTRAINT uq UNIQUE(seat),#唯一键
  CONSTRAINT ck CHECK(gender ='男' OR gender  = '女'),#检查
  CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id)#外键

);

SHOW INDEX FROM stuinfo;
```

通用的写法：★

```mysql
CREATE TABLE IF NOT EXISTS stuinfo(
	id INT PRIMARY KEY,
	stuname VARCHAR(20),
	sex CHAR(1),
	age INT DEFAULT 18,
	seat INT UNIQUE,
	majorid INT,
	CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id)

);
```

## 二、修改表时添加约束

### 1、添加列级约束

```mysql
alter table 表名 modify column 字段名 字段类型 新约束;
```

### 2、添加表级约束

```mysql
alter table 表名 add 【constraint 约束名】 约束类型(字段名) 【外键的引用】;
```


```mysql
DROP TABLE IF EXISTS stuinfo;
CREATE TABLE stuinfo(
	id INT,
	stuname VARCHAR(20),
	gender CHAR(1),
	seat INT,
	age INT,
	majorid INT
)
DESC stuinfo;
```

#### 1.添加非空约束

```mysql
ALTER TABLE stuinfo MODIFY COLUMN stuname VARCHAR(20)  NOT NULL;
```

#### 2.添加默认约束

```mysql
ALTER TABLE stuinfo MODIFY COLUMN age INT DEFAULT 18;
```

#### 3.添加主键

①列级约束

```mysql
ALTER TABLE stuinfo MODIFY COLUMN id INT PRIMARY KEY;
```

②表级约束

```mysql
ALTER TABLE stuinfo ADD PRIMARY KEY(id);
```

#### 4.添加唯一

①列级约束

```mysql
ALTER TABLE stuinfo MODIFY COLUMN seat INT UNIQUE;
```

②表级约束

```mysql
ALTER TABLE stuinfo ADD UNIQUE(seat);
```

5.添加外键

```mysql
ALTER TABLE stuinfo ADD CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id); 
```

## 三、修改表时删除约束

#### 1.删除非空约束

```mysql
ALTER TABLE stuinfo MODIFY COLUMN stuname VARCHAR(20) NULL;
```

#### 2.删除默认约束

```mysql
ALTER TABLE stuinfo MODIFY COLUMN age INT ;
```

#### 3.删除主键

```mysql
ALTER TABLE stuinfo DROP PRIMARY KEY;
```

#### 4.删除唯一

```mysql
ALTER TABLE stuinfo DROP INDEX seat;
```

#### 5.删除外键

```mysql
ALTER TABLE stuinfo DROP FOREIGN KEY fk_stuinfo_major;

SHOW INDEX FROM stuinfo;
```

# 标识列

又称为自增长列
含义：可以不用手动的插入值，系统提供默认的序列值

特点：
1、标识列必须和主键搭配吗？不一定，但要求是一个key
2、一个表可以有几个标识列？至多一个！
3、标识列的类型只能是数值型
4、标识列可以通过 SET auto_increment_increment=3;设置步长
可以通过 手动插入值，设置起始值

## 创建表时设置标识列

```mysql
DROP TABLE IF EXISTS tab_identity;
CREATE TABLE tab_identity(
	id INT  ,
	NAME FLOAT UNIQUE AUTO_INCREMENT,
	seat INT 


);
TRUNCATE TABLE tab_identity;


INSERT INTO tab_identity(id,NAME) VALUES(NULL,'john');
INSERT INTO tab_identity(NAME) VALUES('lucy');
SELECT * FROM tab_identity;


SHOW VARIABLES LIKE '%auto_increment%';


SET auto_increment_increment=3;
```