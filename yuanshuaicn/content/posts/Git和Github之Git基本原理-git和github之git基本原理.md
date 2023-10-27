---
title: Git和Github之Git基本原理
date: 2021-02-23 23:10:02.488
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
tags: 
- git
- github
---

# Git基本原理

## 1、哈希

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/29.png)

哈希是一个系列的加密算法，各个不同的哈希算法虽然加密强度不同，但是有以下 几个共同点:

- 不管输入数据的数据量有多大，输入同一个哈希算法，得到的加密结果长度固定。 
- 哈希算法确定，输入数据确定，输出数据能够保证不变 
- 哈希算法确定，输入数据有变化，输出数据一定有变化，而且通常变化很大 
- 哈希算法不可逆

Git 底层采用的是 SHA-1 算法。 

哈希算法可以被用来验证文件。原理如下图所示:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/30.png)

## **2、Git** 保存版本的机制

### 2.1、集中式版本控制工具的文件管理机制

以文件变更列表的方式存储信息。

这类系统将它们保存的信息看作是一组基本文件和每个文件随时间逐步累积的差异

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/31.png)

### 2.2、Git的文件管理机制

Git 把数据看作是小型文件系统的一组快照。每次提交更新时 Git 都会对当前的全部文件制作一个快照并保存这个快照的索引。为了高效，如果文件没有修改， Git 不再重新存储该文件，而是只保留一个链接指向之前存储的文件。所以 Git 的 工作方式可以称之为快照流。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/32.png)

### 2.3、**Git** 文件管理机制细节

Git的“提交对象”

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/33.png)

提交对象及其父对象形成的链条

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/34.png)

## 3、**Git** 分支管理机制

### 3.1、分支的创建

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/35.png)

### 3.2、分支的切换

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/36.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/37.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/38.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/39.png)