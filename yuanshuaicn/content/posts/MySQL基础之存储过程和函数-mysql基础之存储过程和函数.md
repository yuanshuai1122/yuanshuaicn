---
title: MySQL基础之存储过程和函数
date: 2021-11-24 08:54:10.186
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/mysql.png'
theme: 'light'
tags: 
- mysql
- 数据库
---

-----------------------------案例演示-----------------------------------**

#### 1.空参列表

案例：插入到admin表中五条记录

```mysql
SELECT * FROM admin;

DELIMITER $
CREATE PROCEDURE myp1()
BEGIN
	INSERT INTO admin(username,`password`) 
	VALUES('john1','0000'),('lily','0000'),('rose','0000'),('jack','0000'),('tom','0000');
END $

#调用
CALL myp1()$
```

#### 2.创建带in模式参数的存储过程

案例1：创建存储过程实现 根据女神名，查询对应的男神信息

```mysql
CREATE PROCEDURE myp2(IN beautyName VARCHAR(20))
BEGIN
	SELECT bo.*
	FROM boys bo
	RIGHT JOIN beauty b ON bo.id = b.boyfriend_id
	WHERE b.name=beautyName;
	

END $

#调用
CALL myp2('柳岩')$
```

案例2 ：创建存储过程实现，用户是否登录成功

```mysql
CREATE PROCEDURE myp4(IN username VARCHAR(20),IN PASSWORD VARCHAR(20))
BEGIN
	DECLARE result INT DEFAULT 0;#声明并初始化
	SELECT COUNT(*) INTO result#赋值
FROM admin
WHERE admin.username = username
AND admin.password = PASSWORD;

SELECT IF(result>0,'成功','失败');#使用

END $

#调用
CALL myp3('张飞','8888')$
```

#### 3.创建out 模式参数的存储过程

案例1：根据输入的女神名，返回对应的男神名

```mysql
CREATE PROCEDURE myp6(IN beautyName VARCHAR(20),OUT boyName VARCHAR(20))
BEGIN
	SELECT bo.boyname INTO boyname
	FROM boys bo
	RIGHT JOIN
	beauty b ON b.boyfriend_id = bo.id
	WHERE b.name=beautyName ;
	
END $
```


案例2：根据输入的女神名，返回对应的男神名和魅力值

```mysql
CREATE PROCEDURE myp7(IN beautyName VARCHAR(20),OUT boyName VARCHAR(20),OUT usercp INT) 
BEGIN
	SELECT boys.boyname ,boys.usercp INTO boyname,usercp
	FROM boys 
	RIGHT JOIN
	beauty b ON b.boyfriend_id = boys.id
	WHERE b.name=beautyName ;
	
END $


#调用
CALL myp7('小昭',@name,@cp)$
SELECT @name,@cp$
```

#### 4.创建带inout模式参数的存储过程

案例1：传入a和b两个值，最终a和b都翻倍并返回

```mysql
CREATE PROCEDURE myp8(INOUT a INT ,INOUT b INT)
BEGIN
	SET a=a*2;
	SET b=b*2;
END $

#调用
SET @m=10$
SET @n=20$
CALL myp8(@m,@n)$
SELECT @m,@n$
```

### 三、删除存储过程

语法：

```mysql
drop procedure 存储过程名

DROP PROCEDURE p1;
DROP PROCEDURE p2,p3;#×
```

### 四、查看存储过程的信息

```mysql
DESC myp2;× 

SHOW CREATE PROCEDURE  myp2;
```

# 函数

含义：一组预先编译好的SQL语句的集合，理解成批处理语句

1、提高代码的重用性
2、简化操作
3、减少了编译次数并且减少了和数据库服务器的连接次数，提高了效率

区别：

存储过程：可以有0个返回，也可以有多个返回，适合做批量插入、批量更新
函数：有且仅有1 个返回，适合做处理数据后返回一个结果

## 一、创建语法

```mysql
CREATE FUNCTION 函数名(参数列表) RETURNS 返回类型
BEGIN
	函数体
END
```

注意：
1.参数列表 包含两部分：
参数名 参数类型

2.函数体：肯定会有return语句，如果没有会报错
如果return语句没有放在函数体的最后也不报错，但不建议

return 值;

3.函数体中仅有一句话，则可以省略begin end

4.使用 delimiter语句设置结束标记

## 二、调用语法

```mysql
SELECT 函数名(参数列表)
```

**------------------------------案例演示----------------------------**

### 1.无参有返回

案例：返回公司的员工个数

```mysql
CREATE FUNCTION myf1() RETURNS INT
BEGIN

DECLARE c INT DEFAULT 0;#定义局部变量
SELECT COUNT(*) INTO c#赋值
FROM employees;
RETURN c;

END $

SELECT myf1()$
```

### 2.有参有返回

案例1：根据员工名，返回它的工资

```mysql
CREATE FUNCTION myf2(empName VARCHAR(20)) RETURNS DOUBLE
BEGIN
	SET @sal=0;#定义用户变量 
	SELECT salary INTO @sal   #赋值
	FROM employees
	WHERE last_name = empName;
	RETURN @sal;

END $

SELECT myf2('k_ing') $
```

案例2：根据部门名，返回该部门的平均工资

```mysql
CREATE FUNCTION myf3(deptName VARCHAR(20)) RETURNS DOUBLE
BEGIN
	DECLARE sal DOUBLE ;
	SELECT AVG(salary) INTO sal
	FROM employees e
	JOIN departments d ON e.department_id = d.department_id
	WHERE d.department_name=deptName;
	RETURN sal;
END $

SELECT myf3('IT')$
```

## 三、查看函数

```mysql
SHOW CREATE FUNCTION myf3;
```

## 四、删除函数

```mysql
DROP FUNCTION myf3;
```

案例
一、创建函数，实现传入两个float，返回二者之和

```mysql
CREATE FUNCTION test_fun1(num1 FLOAT,num2 FLOAT) RETURNS FLOAT
BEGIN
	DECLARE SUM FLOAT DEFAULT 0;
	SET SUM=num1+num2;
	RETURN SUM;
END $

SELECT test_fun1(1,2)$
```