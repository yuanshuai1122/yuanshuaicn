---
title: Docker之本地镜像推送到阿里云
date: 2021-posts-23 23:04:28.637
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/homepage-docker-logo.png'
theme: 'light'
tags: 
- docker
---

# **本地镜像发布到阿里云**

## **1、本地镜像发布到阿里云流程**

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/84.png)

## 2、**镜像的生成方法**

1. 前面的DockerFile

2. 从容器创建一个新的镜像

   ````shell
   docker commit [OPTIONS] 容器ID [REPOSITORY[:TAG]]
   ````

   OPTIONS说明： 

   -a :提交的镜像作者； 

   -m :提交时的说明文字； 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/85.png)

## 3、**将本地镜像推送到阿里云**

1. 本地镜像素材原型

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/86.png)

2. 阿里云开发者平台

https://dev.aliyun.com/search.html

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/87.png)



3. 创建仓库镜像

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/88.png)

- 命名空间
- 仓库名称

4. 将镜像推送到registry

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/89.png)

5. 公有云可以查询到

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/90.png)

6. 查看详情

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/91.png)

## 4、 **将阿里云上的镜像下载到本地**

下载到本地

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/92.png)