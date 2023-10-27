---
title: MySQL基础之查询（一）
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

-------------------
SELECT
	*
FROM
	employees
WHERE
	employee_id BETWEEN 120 AND 100;
```

3.in
含义：判断某字段的值是否属于in列表中的某一项
特点：
	①使用in提高语句简洁度
	②in列表的值类型必须一致或兼容
	③in列表中不支持通配符

案例：查询员工的工种编号是 IT_PROG、AD_VP、AD_PRES中的一个员工名和工种编号

```mysql
SELECT
	last_name,
	job_id
FROM
	employees
WHERE
	job_id = 'IT_PROT' OR job_id = 'AD_VP' OR JOB_ID ='AD_PRES';


#------------------

SELECT
	last_name,
	job_id
FROM
	employees
WHERE
	job_id IN( 'IT_PROT' ,'AD_VP','AD_PRES');
```

4、is null

=或<>不能用于判断null值
is null或is not null 可以判断null值



案例1：查询没有奖金的员工名和奖金率

```mysql
SELECT
	last_name,
	commission_pct
FROM
	employees
WHERE
	commission_pct IS NULL;
	#----------以下为错误写法
SELECT
	last_name,
	commission_pct
FROM
	employees

WHERE 
	salary IS 12000;
```


安全等于  <=>

案例1：查询没有奖金的员工名和奖金率

```mysql
SELECT
	last_name,
	commission_pct
FROM
	employees
WHERE
	commission_pct <=>NULL;
```


案例2：查询工资为12000的员工信息

```mysql
SELECT
	last_name,
	salary
FROM
	employees

WHERE 
	salary <=> 12000;
```

​	

**is null  pk  <=>**

- IS NULL:仅仅可以判断NULL值，可读性较高，建议使用
- <=>    :既可以判断NULL值，又可以判断普通的数值，可读性较低

# 三、排序查询

语法：

```mysql
select 查询列表
from 表名
【where  筛选条件】
order by 排序的字段或表达式;
```

特点：
1、asc代表的是升序，可以省略 desc代表的是降序

2、order by子句可以支持 单个字段、别名、表达式、函数、多个字段

3、order by子句在查询语句的最后面，除了limit子句

### 3.1、按单个字段排序

```mysql
SELECT * FROM employees ORDER BY salary DESC;
```

### 3.2、添加筛选条件再排序

案例：查询部门编号>=90的员工信息，并按员工编号降序

```mysql
SELECT *
FROM employees
WHERE department_id>=90
ORDER BY employee_id DESC;
```

### 3.3、按表达式排序

案例：查询员工信息 按年薪降序

```mysql
SELECT *,salary*12*(1+IFNULL(commission_pct,0))
FROM employees
ORDER BY salary*12*(1+IFNULL(commission_pct,0)) DESC;
```

### 3.4、按别名排序

案例：查询员工信息 按年薪升序

```mysql
SELECT *,salary*12*(1+IFNULL(commission_pct,0)) 年薪
FROM employees
ORDER BY 年薪 ASC;
```

### 3.5、按函数排序

案例：查询员工名，并且按名字的长度降序

```mysql
SELECT LENGTH(last_name),last_name 
FROM employees
ORDER BY LENGTH(last_name) DESC;
```

### 3.6、按多个字段排序

案例：查询员工信息，要求先按工资降序，再按employee_id升序

```mysql
SELECT *
FROM employees
ORDER BY salary DESC,employee_id ASC;
```