---
title: MySQL基础之变量
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

---: | :-----------------: | :-----------------: | :----------------------: |
| 用户变量 |      当前会话       |   会话的任何地方    |  加@符号，不用指定类型   |
| 局部变量 | 定义它的BEGIN END中 | BEGIN END的第一句话 | 一般不用加@,需要指定类型 |