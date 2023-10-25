---
title: MySQL基础之变量
date: 2021-posts-24 08:54:06.608
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/mysql.png'
theme: 'light'
tags: 
- mysql
- 数据库
---

---: | :-----------------: | :-----------------: | :----------------------: |
| 用户变量 |      当前会话       |   会话的任何地方    |  加@符号，不用指定类型   |
| 局部变量 | 定义它的BEGIN END中 | BEGIN END的第一句话 | 一般不用加@,需要指定类型 |