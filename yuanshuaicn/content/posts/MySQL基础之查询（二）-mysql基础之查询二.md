---
title: MySQL基础之查询（二）
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

------- | ------------------ | -------------------- |
| 分组前筛选 | 原始表             | group by前	where  |
| 分组后筛选 | group by后的结果集 | group by后	having |

问题1：分组函数做筛选能不能放在where后面
答：不能

问题2：where——group by——having

一般来讲，能用分组前筛选的，尽量使用分组前筛选，提高效率

3、分组可以按单个字段也可以按多个字段
4、可以搭配着排序使用

引入：查询每个部门的员工个数

```mysql
SELECT COUNT(*) FROM employees WHERE department_id=90;
```

### 1.简单的分组

案例1：查询每个工种的员工平均工资

```mysql
SELECT AVG(salary),job_id
FROM employees
GROUP BY job_id;
```

案例2：查询每个位置的部门个数

```mysql
SELECT COUNT(*),location_id
FROM departments
GROUP BY location_id;
```

### 2、可以实现分组前的筛选

案例1：查询邮箱中包含a字符的 每个部门的最高工资

```mysql
SELECT MAX(salary),department_id
FROM employees
WHERE email LIKE '%a%'
GROUP BY department_id;
```


案例2：查询有奖金的每个领导手下员工的平均工资

```mysql
SELECT AVG(salary),manager_id
FROM employees
WHERE commission_pct IS NOT NULL
GROUP BY manager_id;
```

### 3、分组后筛选

案例：查询哪个部门的员工个数>5

```mysql
#①查询每个部门的员工个数
SELECT COUNT(*),department_id
FROM employees
GROUP BY department_id;

#② 筛选刚才①结果

SELECT COUNT(*),department_id
FROM employees

GROUP BY department_id

HAVING COUNT(*)>5;
```


案例2：每个工种有奖金的员工的最高工资>12000的工种编号和最高工资

```mysql
SELECT job_id,MAX(salary)
FROM employees
WHERE commission_pct IS NOT NULL
GROUP BY job_id
HAVING MAX(salary)>12000;
```


案例3：领导编号>102的每个领导手下的最低工资大于5000的领导编号和最低工资

```mysql
manager_id>102

SELECT manager_id,MIN(salary)
FROM employees
GROUP BY manager_id
HAVING MIN(salary)>5000;
```

### 4.添加排序

案例：每个工种有奖金的员工的最高工资>6000的工种编号和最高工资,按最高工资升序

```mysql
SELECT job_id,MAX(salary) m
FROM employees
WHERE commission_pct IS NOT NULL
GROUP BY job_id
HAVING m>6000
ORDER BY m ;
```

### 5.按多个字段分组

案例：查询每个工种每个部门的最低工资,并按最低工资降序

```mysql
SELECT MIN(salary),job_id,department_id
FROM employees
GROUP BY department_id,job_id
ORDER BY MIN(salary) DESC;
```

# 二、连接查询

含义：又称多表查询，当查询的字段来自于多个表时，就会用到连接查询

笛卡尔乘积现象：表1 有m行，表2有n行，结果=m*n行

发生原因：没有有效的连接条件
如何避免：添加有效的连接条件

分类：

>按年代分类：
>sql92标准:仅仅支持内连接
>sql99标准【推荐】：支持内连接+外连接（左外和右外）+交叉连接
>
>按功能分类：
>	内连接：
>		等值连接
>		非等值连接
>		自连接
>	外连接：
>		左外连接
>		右外连接
>		全外连接
>	交叉连接

```mysql
SELECT * FROM beauty;
SELECT * FROM boys;
#等价为
SELECT NAME,boyName FROM boys,beauty
WHERE beauty.boyfriend_id= boys.id;
```

## 一、sql92语法

### 1、等值连接

① 多表等值连接的结果为多表的交集部分
②n表连接，至少需要n-1个连接条件
③ 多表的顺序没有要求
④一般需要为表起别名
⑤可以搭配前面介绍的所有子句使用，比如排序、分组、筛选

案例1：查询女神名和对应的男神名

```mysql
SELECT NAME,boyName 
FROM boys,beauty
WHERE beauty.boyfriend_id= boys.id;
```

案例2：查询员工名和对应的部门名

```mysql
SELECT last_name,department_name
FROM employees,departments
WHERE employees.`department_id`=departments.`department_id`;
```

### 2、为表起别名

①提高语句的简洁度
②区分多个重名的字段

注意：如果为表起了别名，则查询的字段就不能使用原来的表名去限定

案例：查询员工名、工种号、工种名

```mysql
SELECT e.last_name,e.job_id,j.job_title
FROM employees  e,jobs j
WHERE e.`job_id`=j.`job_id`;
```

### 3、两个表的顺序是否可以调换

查询员工名、工种号、工种名

```mysql
SELECT e.last_name,e.job_id,j.job_title
FROM jobs j,employees e
WHERE e.`job_id`=j.`job_id`;
```

### 4、可以加筛选


案例：查询有奖金的员工名、部门名

```mysql
SELECT last_name,department_name,commission_pct

FROM employees e,departments d
WHERE e.`department_id`=d.`department_id`
AND e.`commission_pct` IS NOT NULL;
```

案例2：查询城市名中第二个字符为o的部门名和城市名

```mysql
SELECT department_name,city
FROM departments d,locations l
WHERE d.`location_id` = l.`location_id`
AND city LIKE '_o%';
```

### 5、可以加分组


案例1：查询每个城市的部门个数

```mysql
SELECT COUNT(*) 个数,city
FROM departments d,locations l
WHERE d.`location_id`=l.`location_id`
GROUP BY city;
```

案例2：查询有奖金的每个部门的部门名和部门的领导编号和该部门的最低工资

```mysql
SELECT department_name,d.`manager_id`,MIN(salary)
FROM departments d,employees e
WHERE d.`department_id`=e.`department_id`
AND commission_pct IS NOT NULL
GROUP BY department_name,d.`manager_id`;
```

### 6、可以加排序


案例：查询每个工种的工种名和员工的个数，并且按员工个数降序

```mysql
SELECT job_title,COUNT(*)
FROM employees e,jobs j
WHERE e.`job_id`=j.`job_id`
GROUP BY job_title
ORDER BY COUNT(*) DESC;
```

### 7、可以实现三表连接？

案例：查询员工名、部门名和所在的城市

```mysql
SELECT last_name,department_name,city
FROM employees e,departments d,locations l
WHERE e.`department_id`=d.`department_id`
AND d.`location_id`=l.`location_id`
AND city LIKE 's%'

ORDER BY department_name DESC;
```

#### 7.1、非等值连接

案例1：查询员工的工资和工资级别

```mysql
SELECT salary,grade_level
FROM employees e,job_grades g
WHERE salary BETWEEN g.`lowest_sal` AND g.`highest_sal`
AND g.`grade_level`='A';

#前提我们要插入等级sql
/*
select salary,employee_id from employees;
select * from job_grades;
CREATE TABLE job_grades
(grade_level VARCHAR(3),
 lowest_sal  int,
 highest_sal int);

INSERT INTO job_grades
VALUES ('A', 1000, 2999);

INSERT INTO job_grades
VALUES ('B', 3000, 5999);

INSERT INTO job_grades
VALUES('C', 6000, 9999);

INSERT INTO job_grades
VALUES('D', 10000, 14999);

INSERT INTO job_grades
VALUES('E', 15000, 24999);

INSERT INTO job_grades
VALUES('F', 25000, 40000);

*/
```

#### 7.2、自连接

案例：查询 员工名和上级的名称

```mysql
SELECT e.employee_id,e.last_name,m.employee_id,m.last_name
FROM employees e,employees m
WHERE e.`manager_id`=m.`employee_id`;
```

## 二、sql99语法

语法：

```mysql
select 查询列表
	from 表1 别名 【连接类型】
	join 表2 别名 
	on 连接条件
	【where 筛选条件】
	【group by 分组】
	【having 筛选条件】
	【order by 排序列表】
```

分类：

>内连接（★）：inner
>外连接
>	左外(★):left 【outer】
>	右外(★)：right 【outer】
>	全外：full【outer】
>交叉连接：cross 

### 一）内连接

语法：

```mysql
select 查询列表
from 表1 别名
inner join 表2 别名
on 连接条件;
```

分类：

>等值
>非等值
>自连接

特点：
①添加排序、分组、筛选
②inner可以省略
③ 筛选条件放在where后面，连接条件放在on后面，提高分离性，便于阅读
④inner join连接和sql92语法中的等值连接效果是一样的，都是查询多表的交集



#### 1、等值连接

案例1.查询员工名、部门名

```mysql
SELECT last_name,department_name
FROM departments d
 JOIN  employees e
ON e.`department_id` = d.`department_id`;
```

案例2.查询名字中包含e的员工名和工种名（添加筛选）

```mysql
SELECT last_name,job_title
FROM employees e
INNER JOIN jobs j
ON e.`job_id`=  j.`job_id`
WHERE e.`last_name` LIKE '%e%';
```

案例3. 查询部门个数>3的城市名和部门个数，（添加分组+筛选）

```mysql
#①查询每个城市的部门个数
#②在①结果上筛选满足条件的
SELECT city,COUNT(*) 部门个数
FROM departments d
INNER JOIN locations l
ON d.`location_id`=l.`location_id`
GROUP BY city
HAVING COUNT(*)>3;
```


案例4.查询哪个部门的员工个数>3的部门名和员工个数，并按个数降序（添加排序）

```mysql
①查询每个部门的员工个数
SELECT COUNT(*),department_name
FROM employees e
INNER JOIN departments d
ON e.`department_id`=d.`department_id`
GROUP BY department_name

#② 在①结果上筛选员工个数>3的记录，并排序

SELECT COUNT(*) 个数,department_name
FROM employees e
INNER JOIN departments d
ON e.`department_id`=d.`department_id`
GROUP BY department_name
HAVING COUNT(*)>3
ORDER BY COUNT(*) DESC;
```

案例5.查询员工名、部门名、工种名，并按部门名降序（添加三表连接）

```mysql
SELECT last_name,department_name,job_title
FROM employees e
INNER JOIN departments d ON e.`department_id`=d.`department_id`
INNER JOIN jobs j ON e.`job_id` = j.`job_id`

ORDER BY department_name DESC;
```

### 二）非等值连接

查询员工的工资级别

```mysql
SELECT salary,grade_level
FROM employees e
 JOIN job_grades g
 ON e.`salary` BETWEEN g.`lowest_sal` AND g.`highest_sal`;
```

查询工资级别的个数>20的个数，并且按工资级别降序

 ```mysql
SELECT COUNT(*),grade_level
FROM employees e
 JOIN job_grades g
 ON e.`salary` BETWEEN g.`lowest_sal` AND g.`highest_sal`
 GROUP BY grade_level
 HAVING COUNT(*)>20
 ORDER BY grade_level DESC;
 ```

### 三）自连接

查询员工的名字、上级的名字

 ```mysql
SELECT e.last_name,m.last_name
 FROM employees e
 JOIN employees m
 ON e.`manager_id`= m.`employee_id`;
 ```

查询姓名中包含字符k的员工的名字、上级的名字

```mysql
 SELECT e.last_name,m.last_name
 FROM employees e
 JOIN employees m
 ON e.`manager_id`= m.`employee_id`
 WHERE e.`last_name` LIKE '%k%';
```

### 四）外连接

 应用场景：用于查询一个表中有，另一个表没有的记录

 特点：
 1、外连接的查询结果为主表中的所有记录
	如果从表中有和它匹配的，则显示匹配的值
	如果从表中没有和它匹配的，则显示null
	外连接查询结果=内连接结果+主表中有而从表没有的记录
 2、左外连接，left join左边的是主表
    右外连接，right join右边的是主表
 3、左外和右外交换两个表的顺序，可以实现同样的效果 
 4、全外连接=内连接的结果+表1中有但表2没有的+表2中有但表1没有的

引入：查询男朋友 不在男神表的的女神名

```mysql
 SELECT * FROM beauty;
 SELECT * FROM boys;

 #左外连接
 SELECT b.*,bo.*
 FROM boys bo
 LEFT OUTER JOIN beauty b
 ON b.`boyfriend_id` = bo.`id`
 WHERE b.`id` IS NULL;
```

案例1：查询哪个部门没有员工

```mysql
 #左外
 SELECT d.*,e.employee_id
 FROM departments d
 LEFT OUTER JOIN employees e
 ON d.`department_id` = e.`department_id`
 WHERE e.`employee_id` IS NULL;


 #右外

  SELECT d.*,e.employee_id
 FROM employees e
 RIGHT OUTER JOIN departments d
 ON d.`department_id` = e.`department_id`
 WHERE e.`employee_id` IS NULL;


 #全外


 USE girls;
 SELECT b.*,bo.*
 FROM beauty b
 FULL OUTER JOIN boys bo
 ON b.`boyfriend_id` = bo.id;


 #交叉连接

 SELECT b.*,bo.*
 FROM beauty b
 CROSS JOIN boys bo; 
```

## 三、sql92和 sql99pk

 功能：sql99支持的较多
 可读性：sql99实现连接条件和筛选条件的分离，可读性较高

# 三、子查询

含义：
出现在其他语句中的select语句，称为子查询或内查询
外部的查询语句，称为主查询或外查询

分类：
按子查询出现的位置：

	>select后面：
	>		仅仅支持标量子查询
	>	from后面：
	>		支持表子查询
	>	where或having后面：★
	>		标量子查询（单行） √
	>		列子查询  （多行） √
	>		
	>
	>​		行子查询
	>
	>​	exists后面（相关子查询）
	>​		表子查询

按结果集的行列数不同：

	>标量子查询（结果集只有一行一列）
	>	列子查询（结果集只有一列多行）
	>	行子查询（结果集有一行多列）
	>	表子查询（结果集一般为多行多列）



## 一、where或having后面

1、标量子查询（单行子查询）
2、列子查询（多行子查询）

3、行子查询（多列多行）

特点：
①子查询放在小括号内
②子查询一般放在条件的右侧
③标量子查询，一般搭配着单行操作符使用

> < >= <= = <>

列子查询，一般搭配着多行操作符使用

> in、any/some、all

④子查询的执行优先于主查询执行，主查询的条件用到了子查询的结果

### 1.标量子查询★

案例1：谁的工资比 Abel 高?

①查询Abel的工资

```mysql
SELECT salary
FROM employees
WHERE last_name = 'Abel'
```

②查询员工的信息，满足 salary>①结果

```mysql
SELECT *
FROM employees
WHERE salary>(

SELECT salary
FROM employees
WHERE last_name = 'Abel'

);
```

案例2：返回job_id与141号员工相同，salary比143号员工多的员工 姓名，job_id 和工资

①查询141号员工的job_id

```mysql
SELECT job_id
FROM employees
WHERE employee_id = 141
```

②查询143号员工的salary

```mysql
SELECT salary
FROM employees
WHERE employee_id = 143
```

③查询员工的姓名，job_id 和工资，要求job_id=①并且salary>②

```mysql
SELECT last_name,job_id,salary
FROM employees
WHERE job_id = (
	SELECT job_id
	FROM employees
	WHERE employee_id = 141
) AND salary>(
	SELECT salary
	FROM employees
	WHERE employee_id = 143

);
```


案例3：返回公司工资最少的员工的last_name,job_id和salary

①查询公司的 最低工资

```mysql
SELECT MIN(salary)
FROM employees
```

②查询last_name,job_id和salary，要求salary=①

```mysql
SELECT last_name,job_id,salary
FROM employees
WHERE salary=(
	SELECT MIN(salary)
	FROM employees
);
```


案例4：查询最低工资大于50号部门最低工资的部门id和其最低工资

①查询50号部门的最低工资

```mysql
SELECT  MIN(salary)
FROM employees
WHERE department_id = 50
```

②查询每个部门的最低工资

```mysql
SELECT MIN(salary),department_id
FROM employees
GROUP BY department_id
```

③ 在②基础上筛选，满足min(salary)>①

```mysql
SELECT MIN(salary),department_id
FROM employees
GROUP BY department_id
HAVING MIN(salary)>(
	SELECT  MIN(salary)
	FROM employees
	WHERE department_id = 50

);
```

非法使用标量子查询

```mysql
SELECT MIN(salary),department_id
FROM employees
GROUP BY department_id
HAVING MIN(salary)>(
	SELECT  salary
	FROM employees
	WHERE department_id = 250

);
```



### 2.列子查询（多行子查询）★

案例1：返回location_id是1400或1700的部门中的所有员工姓名

①查询location_id是1400或1700的部门编号

```mysql
SELECT DISTINCT department_id
FROM departments
WHERE location_id IN(1400,1700)
```

②查询员工姓名，要求部门号是①列表中的某一个

```mysql
SELECT last_name
FROM employees
WHERE department_id  <>ALL(
	SELECT DISTINCT department_id
	FROM departments
	WHERE location_id IN(1400,1700)

);
```


案例2：返回其它工种中比job_id为‘IT_PROG’工种任一工资低的员工的员工号、姓名、job_id 以及salary

①查询job_id为‘IT_PROG’部门任一工资

```mysql
SELECT DISTINCT salary
FROM employees
WHERE job_id = 'IT_PROG'
```

②查询员工号、姓名、job_id 以及salary，salary<(①)的任意一个

```mysql
SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<ANY(
	SELECT DISTINCT salary
	FROM employees
	WHERE job_id = 'IT_PROG'

) AND job_id<>'IT_PROG';

#或
SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<(
	SELECT MAX(salary)
	FROM employees
	WHERE job_id = 'IT_PROG'

) AND job_id<>'IT_PROG';
```


案例3：返回其它部门中比job_id为‘IT_PROG’部门所有工资都低的员工   的员工号、姓名、job_id 以及salary

```mysql
SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<ALL(
	SELECT DISTINCT salary
	FROM employees
	WHERE job_id = 'IT_PROG'

) AND job_id<>'IT_PROG';

#或

SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<(
	SELECT MIN( salary)
	FROM employees
	WHERE job_id = 'IT_PROG'

) AND job_id<>'IT_PROG';
```

### 3、行子查询（结果集一行多列或多行多列）

案例：查询员工编号最小并且工资最高的员工信息

```mysql
SELECT * 
FROM employees
WHERE (employee_id,salary)=(
	SELECT MIN(employee_id),MAX(salary)
	FROM employees
);
```

①查询最小的员工编号

```mysql
SELECT MIN(employee_id)
FROM employees
```

②查询最高工资

```mysql
SELECT MAX(salary)
FROM employees
```

③查询员工信息

```mysql
SELECT *
FROM employees
WHERE employee_id=(
	SELECT MIN(employee_id)
	FROM employees


)AND salary=(
	SELECT MAX(salary)
	FROM employees

);
```

## 二、select后面

**仅仅支持标量子查询**

案例：查询每个部门的员工个数

```mysql
SELECT d.*,(

SELECT COUNT(*)
FROM employees e
WHERE e.department_id = d.`department_id`

 ) 个数
 FROM departments d;
```


案例2：查询员工号=102的部门名

```mysql
SELECT (
	SELECT department_name,e.department_id
	FROM departments d
	INNER JOIN employees e
	ON d.department_id=e.department_id
	WHERE e.employee_id=102
	
) 部门名;
```

## 三、from后面

**将子查询结果充当一张表，要求必须起别名**

案例：查询每个部门的平均工资的工资等级
①查询每个部门的平均工资

```mysql
SELECT AVG(salary),department_id
FROM employees
GROUP BY department_id

SELECT * FROM job_grades;
```


②连接①的结果集和job_grades表，筛选条件平均工资 between lowest_sal and highest_sal

```mysql
SELECT  ag_dep.*,g.`grade_level`
FROM (
	SELECT AVG(salary) ag,department_id
	FROM employees
	GROUP BY department_id
) ag_dep
INNER JOIN job_grades g
ON ag_dep.ag BETWEEN lowest_sal AND highest_sal;
```

## 四、exists后面（相关子查询）

语法：
exists(完整的查询语句)
结果：
1或0

```mysql
SELECT EXISTS(SELECT employee_id FROM employees WHERE salary=300000);
```

案例1：查询有员工的部门名

```mysql
#in
SELECT department_name
FROM departments d
WHERE d.`department_id` IN(
	SELECT department_id
	FROM employees

)

#exists

SELECT department_name
FROM departments d
WHERE EXISTS(
	SELECT *
	FROM employees e
	WHERE d.`department_id`=e.`department_id`


);
```


案例2：查询没有女朋友的男神信息

```mysql
#in

SELECT bo.*
FROM boys bo
WHERE bo.id NOT IN(
	SELECT boyfriend_id
	FROM beauty
)

#exists
SELECT bo.*
FROM boys bo
WHERE NOT EXISTS(
	SELECT boyfriend_id
	FROM beauty b
	WHERE bo.`id`=b.`boyfriend_id`

);
```

## 五、子查询经典案例

### 1. 查询工资最低的员工信息: last_name, salary

```mysql
#①查询最低的工资
SELECT MIN(salary)
FROM employees

#②查询last_name,salary，要求salary=①
SELECT last_name,salary
FROM employees
WHERE salary=(
	SELECT MIN(salary)
	FROM employees
);
```

### 2. 查询平均工资最低的部门信息

```mysql
#方式一：
#①各部门的平均工资
SELECT AVG(salary),department_id
FROM employees
GROUP BY department_id
#②查询①结果上的最低平均工资
SELECT MIN(ag)
FROM (
	SELECT AVG(salary) ag,department_id
	FROM employees
	GROUP BY department_id
) ag_dep

#③查询哪个部门的平均工资=②

SELECT AVG(salary),department_id
FROM employees
GROUP BY department_id
HAVING AVG(salary)=(
	SELECT MIN(ag)
	FROM (
		SELECT AVG(salary) ag,department_id
		FROM employees
		GROUP BY department_id
	) ag_dep

);

#④查询部门信息

SELECT d.*
FROM departments d
WHERE d.`department_id`=(
	SELECT department_id
	FROM employees
	GROUP BY department_id
	HAVING AVG(salary)=(
		SELECT MIN(ag)
		FROM (
			SELECT AVG(salary) ag,department_id
			FROM employees
			GROUP BY department_id
		) ag_dep

)

);

#方式二：
#①各部门的平均工资
SELECT AVG(salary),department_id
FROM employees
GROUP BY department_id

#②求出最低平均工资的部门编号
SELECT department_id
FROM employees
GROUP BY department_id
ORDER BY AVG(salary) 
LIMIT 1;

#③查询部门信息
SELECT *
FROM departments
WHERE department_id=(
	SELECT department_id
	FROM employees
	GROUP BY department_id
	ORDER BY AVG(salary) 
	LIMIT 1
);
```




### 3. 查询平均工资最低的部门信息和该部门的平均工资

```mysql
#①各部门的平均工资
SELECT AVG(salary),department_id
FROM employees
GROUP BY department_id
#②求出最低平均工资的部门编号
SELECT AVG(salary),department_id
FROM employees
GROUP BY department_id
ORDER BY AVG(salary) 
LIMIT 1;
#③查询部门信息
SELECT d.*,ag
FROM departments d
JOIN (
	SELECT AVG(salary) ag,department_id
	FROM employees
	GROUP BY department_id
	ORDER BY AVG(salary) 
	LIMIT 1

) ag_dep
ON d.`department_id`=ag_dep.department_id;
```

### 4. 查询平均工资最高的 job 信息

```mysql
#①查询最高的job的平均工资
SELECT AVG(salary),job_id
FROM employees
GROUP BY job_id
ORDER BY AVG(salary) DESC
LIMIT 1

#②查询job信息
SELECT * 
FROM jobs
WHERE job_id=(
	SELECT job_id
	FROM employees
	GROUP BY job_id
	ORDER BY AVG(salary) DESC
	LIMIT 1

);
```

### 5. 查询平均工资高于公司平均工资的部门有哪些?

```mysql
#①查询平均工资
SELECT AVG(salary)
FROM employees

#②查询每个部门的平均工资
SELECT AVG(salary),department_id
FROM employees
GROUP BY department_id

#③筛选②结果集，满足平均工资>①

SELECT AVG(salary),department_id
FROM employees
GROUP BY department_id
HAVING AVG(salary)>(
	SELECT AVG(salary)
	FROM employees

);
```

### 6. 查询出公司中所有 manager 的详细信息.

```mysql
#①查询所有manager的员工编号
SELECT DISTINCT manager_id
FROM employees

#②查询详细信息，满足employee_id=①
SELECT *
FROM employees
WHERE employee_id =ANY(
	SELECT DISTINCT manager_id
	FROM employees

);
```

### 7. 各个部门中 最高工资中最低的那个部门的 最低工资是多少

```mysql
#①查询各部门的最高工资中最低的部门编号
SELECT department_id
FROM employees
GROUP BY department_id
ORDER BY MAX(salary)
LIMIT 1


#②查询①结果的那个部门的最低工资

SELECT MIN(salary) ,department_id
FROM employees
WHERE department_id=(
	SELECT department_id
	FROM employees
	GROUP BY department_id
	ORDER BY MAX(salary)
	LIMIT 1


);
```

### 8. 查询平均工资最高的部门的 manager 的详细信息: last_name, department_id, email, salary

```mysql
#①查询平均工资最高的部门编号
SELECT 
    department_id 
FROM
    employees 
GROUP BY department_id 
ORDER BY AVG(salary) DESC 
LIMIT 1 

#②将employees和departments连接查询，筛选条件是①
    SELECT 
        last_name, d.department_id, email, salary 
    FROM
        employees e 
        INNER JOIN departments d 
            ON d.manager_id = e.employee_id 
    WHERE d.department_id = 
        (SELECT 
            department_id 
        FROM
            employees 
        GROUP BY department_id 
        ORDER BY AVG(salary) DESC 
        LIMIT 1) ;
```

# 四、分页查询 ★

应用场景：当要显示的数据，一页显示不全，需要分页提交sql请求
语法：

```mysql
select 查询列表
	from 表
	【join type join 表2
	on 连接条件
	where 筛选条件
	group by 分组字段
	having 分组后的筛选
	order by 排序的字段】
	limit 【offset,】size;
```

> offset要显示条目的起始索引（起始索引从0开始）
> size 要显示的条目个数

特点：
	①limit语句放在查询语句的最后
	②公式
	要显示的页数 page，每页的条目数size

```mysql
select 查询列表
from 表
limit (page-1)*size,size;

size=10
page  
1	0
2 10
3	20
```

案例1：查询前五条员工信息

```mysql
SELECT * FROM  employees LIMIT 0,5;
SELECT * FROM  employees LIMIT 5;
```

案例2：查询第11条——第25条

```mysql
SELECT * FROM  employees LIMIT 10,15;
```

案例3：有奖金的员工信息，并且工资较高的前10名显示出来

```mysql
SELECT
		*
FROM
    employees 
WHERE commission_pct IS NOT NULL 
ORDER BY salary DESC 
LIMIT 10 ;
```

# 五、联合查询


union 联合 合并：将多条查询语句的结果合并成一个结果

语法：

```mysql
查询语句1
union
查询语句2
union
...
```

应用场景：
要查询的结果来自于多个表，且多个表没有直接的连接关系，但查询的信息一致时

特点：★
1、要求多条查询语句的查询列数是一致的！
2、要求多条查询语句的查询的每一列的类型和顺序最好一致
3、union关键字默认去重，如果使用union all 可以包含重复项




引入的案例：查询部门编号>90或邮箱包含a的员工信息

```mysql
SELECT * FROM employees WHERE email LIKE '%a%' OR department_id>90;;

SELECT * FROM employees  WHERE email LIKE '%a%'
UNION
SELECT * FROM employees  WHERE department_id>90;
```


案例：查询中国用户中男性的信息以及外国用户中年男性的用户信息

```mysql

```