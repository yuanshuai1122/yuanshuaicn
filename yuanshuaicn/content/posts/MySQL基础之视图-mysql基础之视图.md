---
title: MySQL基础之视图
date: 2021-11-24 08:54:03.184
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/mysql.png'
theme: 'light'
tags: 
- mysql
- 数据库
---

- | ---------------- | -------------------- | ---------------------------- |
| 视图 | create view      | 只是保存了sql逻辑    | 增删改查，只是一般不能增删改 |
| 表   | create table     | 保存了数据           | 增删改查                     |

案例：查询姓张的学生名和专业名

```mysql
#普通写法
SELECT stuname,majorname
FROM stuinfo s
INNER JOIN major m ON s.`majorid`= m.`id`
WHERE s.`stuname` LIKE '张%';

#创建视图的写法
CREATE VIEW v1
AS
SELECT stuname,majorname
FROM stuinfo s
INNER JOIN major m ON s.`majorid`= m.`id`;

SELECT * FROM v1 WHERE stuname LIKE '张%';
```

## 一、创建视图

语法：

```mysql
create view 视图名
as
查询语句;
```


```mysql
USE myemployees;
```

### 1.查询姓名中包含a字符的员工名、部门名和工种信息

①创建

```mysql
CREATE VIEW myv1
AS

SELECT last_name,department_name,job_title
FROM employees e
JOIN departments d ON e.department_id  = d.department_id
JOIN jobs j ON j.job_id  = e.job_id;
```

②使用

```mysql
SELECT * FROM myv1 WHERE last_name LIKE '%a%';
```

### 2.查询各部门的平均工资级别

①创建视图查看每个部门的平均工资

```mysql
CREATE VIEW myv2
AS
SELECT AVG(salary) ag,department_id
FROM employees
GROUP BY department_id;
```

②使用

```mysql
SELECT myv2.`ag`,g.grade_level
FROM myv2
JOIN job_grades g
ON myv2.`ag` BETWEEN g.`lowest_sal` AND g.`highest_sal`;
```

### 3.查询平均工资最低的部门信息

```mysql
SELECT * FROM myv2 ORDER BY ag LIMIT 1;
```

4.查询平均工资最低的部门名和工资

```mysql
CREATE VIEW myv3
AS
SELECT * FROM myv2 ORDER BY ag LIMIT 1;


SELECT d.*,m.ag
FROM myv3 m
JOIN departments d
ON m.`department_id`=d.`department_id`;
```

## 二、视图的修改

方式一：

```mysql
create or replace view  视图名
as
查询语句;
```


```mysql
SELECT * FROM myv3 

CREATE OR REPLACE VIEW myv3
AS
SELECT AVG(salary),job_id
FROM employees
GROUP BY job_id;
```

方式二：

语法：

```mysql
alter view 视图名
as 
查询语句;
```

```mysql
ALTER VIEW myv3
AS
SELECT * FROM employees;
```

## 三、删除视图

语法：

```mysql
drop view 视图名,视图名,...;

DROP VIEW emp_v1,emp_v2,myv3;
```

## 四、查看视图

```mysql
DESC myv3;

SHOW CREATE VIEW myv3;
```

## 五、视图的更新

```mysql
CREATE OR REPLACE VIEW myv1
AS
SELECT last_name,email,salary*12*(1+IFNULL(commission_pct,0)) "annual salary"
FROM employees;

CREATE OR REPLACE VIEW myv1
AS
SELECT last_name,email
FROM employees;


SELECT * FROM myv1;
SELECT * FROM employees;
```

### 1.插入

```mysql
INSERT INTO myv1 VALUES('张飞','zf@qq.com');
```

### 2.修改

```mysql
UPDATE myv1 SET last_name = '张无忌' WHERE last_name='张飞';
```

### 3.删除

```mysql
DELETE FROM myv1 WHERE last_name = '张无忌';
```

### 具备以下特点的视图不允许更新


①包含以下关键字的sql语句：分组函数、distinct、group  by、having、union或者union all

```mysql
CREATE OR REPLACE VIEW myv1
AS
SELECT MAX(salary) m,department_id
FROM employees
GROUP BY department_id;

SELECT * FROM myv1;

#更新
UPDATE myv1 SET m=9000 WHERE department_id=10;
```

②常量视图

```mysql
CREATE OR REPLACE VIEW myv2
AS

SELECT 'john' NAME;

SELECT * FROM myv2;

#更新
UPDATE myv2 SET NAME='lucy';
```

③Select中包含子查询

```mysql
CREATE OR REPLACE VIEW myv3
AS

SELECT department_id,(SELECT MAX(salary) FROM employees) 最高工资
FROM departments;

#更新
SELECT * FROM myv3;
UPDATE myv3 SET 最高工资=100000;
```

④join

```mysql
CREATE OR REPLACE VIEW myv4
AS

SELECT last_name,department_name
FROM employees e
JOIN departments d
ON e.department_id  = d.department_id;

#更新

SELECT * FROM myv4;
UPDATE myv4 SET last_name  = '张飞' WHERE last_name='Whalen';
INSERT INTO myv4 VALUES('陈真','xxxx');
```

⑤from一个不能更新的视图

```mysql
CREATE OR REPLACE VIEW myv5
AS

SELECT * FROM myv3;

#更新

SELECT * FROM myv5;

UPDATE myv5 SET 最高工资=10000 WHERE department_id=60;
```

⑥where子句的子查询引用了from子句中的表

```mysql
CREATE OR REPLACE VIEW myv6
AS

SELECT last_name,email,salary
FROM employees
WHERE employee_id IN(
	SELECT  manager_id
	FROM employees
	WHERE manager_id IS NOT NULL
);

#更新
SELECT * FROM myv6;
UPDATE myv6 SET salary=10000 WHERE last_name = 'k_ing';
```