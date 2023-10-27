---
title: Git和GitHub之Git简介
date: 2021-02-23 23:10:02.488
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
tags: 
- git
- github
---

# Git简介

## 1、版本控制工具应该具备的功能

- 协同修改
  - 多人并行不悖的修改服务器端的同一个文件。
- 数据备份
  - 不仅保存目录和文件的当前状态，还能够保存每一个提交过的历史状态。
- 版本管理
  - 在保存每一个版本的文件信息的时候要做到不保存重复数据，以节约存储空间，提高运行效率。这方面 SVN 采用的是增量式管理的方式，而 Git 采取了文件系统快照的方式。
- 权限控制
  - 对团队中参与开发的人员进行权限控制。
  - 对团队外开发者贡献的代码进行审核——Git独有。
- 历史记录
  - 查看修改人、修改时间、修改内容、日志信息。
  - 将本地文件恢复到某一个历史状态。
- 分支管理
  - 允许开发团队在工作过程中多条生产线同时推进任务，进一步提高效率。

## 2、版本控制简介

### 2.1、版本控制

工程设计领域中使用版本控制管理工程蓝图的设计过程。在 IT 开发过程中也可以使用版本控制思想管理代码的版本迭代。

### 2.2、版本控制工具 

思想: 版本控制

实现: 版本控制工具

集中式版本控制工具:  CVS、**SVN**、VSS......

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/1.png)

分布式版本控制工具: **Git**、Mercurial、Bazaar、Darcs......

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/2.png)

## **3、Git**简介

### **3.1、Git** 简史

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/3.png)

### **3.2、Git** 官网和 **Logo**

官网地址: https://git-scm.com/

Logo：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/4.png)

### **3.3、Git** 的优势

- 大部分操作在本地完成，不需要联网
- 完整性保证
- 尽可能添加数据而不是删除或修改数据
- 分支操作非常快捷流畅
- 与Linux命令全面兼容

### **3.4、Git** 安装

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/5.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/6.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/7.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/8.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/9.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/10.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/11.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/12.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/13.png)

### **3.5、Git** 结构

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/14.png)

### **3.6、Git** 和代码托管中心

代码托管中心的任务:维护远程库

- 局域网环境下
  - GitLab服务器
- 外网环境下
- GitHub
- 码云

### **3.7**、本地库和远程库

#### **3.7.1**、团队内部协作

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/15.png)

#### **3.7.2**、跨团队协作

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/16.png)