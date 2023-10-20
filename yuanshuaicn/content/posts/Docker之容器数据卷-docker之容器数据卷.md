---
title: Docker之容器数据卷
date: 2021-11-23 23:04:14.12
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/homepage-docker-logo.png'
theme: 'light'
tags: 
- docker
---

# **Docker**容器数据卷

## 1、**是什么**

先来看看Docker的理念： 

- 将运用与运行的环境打包形成容器运行 ，运行可以伴随着容器，但是我们对数据的要求希望是持久化的 
- 容器之间希望有可能共享数据 

Docker容器产生的数据，如果不通过docker commit生成新的镜像，使得数据做为镜像的一部分保存下来， 那么当容器删除后，数据自然也就没有了。 

为了能保存数据在docker中我们使用卷。

- 一句话：有点类似我们Redis里面的rdb和aof文件

## 2、**能干嘛**

- 容器的持久化

- 容器间继承+共享数据

## 3、**数据卷**

### 容器内添加：

### 3.1、直接命令添加

- 命令

```shell
docker run -it -v /宿主机绝对路径目录:/容器内目录  镜像名
```

例：

```shell
docker run -it -v /宿主机目录:/容器内目录 centos /bin/bash 
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/32.png)

- 查看数据卷是否挂载成功

```shell
docker inspect 容器ID 
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/33.png)

- 容器和宿主机之间数据共享

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/34.png)

- 容器停止退出后，主机修改后数据是否同步

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/35.png)

- 命令(带权限)

```shell
 docker run -it -v /宿主机绝对路径目录:/容器内目录:ro 镜像名
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/36.png)

### 3.2、DockerFile添加

- 根目录下新建mydocker文件夹并进入

- 可在Dockerfile中使用VOLUME指令来给镜像添加一个或多个数据卷

```shell
VOLUME["/dataVolumeContainer","/dataVolumeContainer2","/dataVolumeContainer3"] 
```

说明：  

出于可移植和分享的考虑， 用-v 主机目录:容器目录这种方法不能够直接在Dockerfile中实现。 

由于宿主机目录是依赖于特定宿主机的，并不能够保证在所有的宿主机上都存在这样的特定目录。

- File构建

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/37.png)

- build后生成镜像

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/38.png)

获得一个新镜像zzyy/centos

- run容器

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/39.png)

通过上述步骤，容器内的卷目录地址已经知道,

但是对应的主机目录地址哪？？

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/40.png)

- **主机对应默认地址**

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/41.png)

### 3.3、备注

Docker挂载主机目录Docker访问出现cannot open directory .: Permission denied 

解决办法：在挂载目录后多加一个--privileged=true参数即可 

## 4、**数据卷容器**

### 4.1、是什么

命名的容器挂载数据卷，其它容器通过挂载这个(父容器)实现数据共享，挂载数据卷的容器，称之为数据卷容器 

### 4.2、总体介绍

- 以上一步新建的镜像zzyy/centos为模板并运行容器dc01/dc02/dc03

- 它们已经具有容器卷
  - /dataVolumeContainer1
  - /dataVolumeContainer2

### 4.3、容器间传递共享(--volumes-from)

- 先启动一个父容器dc01

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/42.png)

在dataVolumeContainer2新增内容

- dc02/dc03继承自dc01
  - --volumes-from
  - 命令：dc02/dc03分别在dataVolumeContainer2各自新增内容

```shell
docker run -it --name dc02 --volumes-from dc01 zzyy/centos 
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/43.png)

- 回到dc01可以看到02/03各自添加的都能共享了

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/44.png)

- 删除dc01，dc02修改后dc03可否访问

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/45.png)

- 删除dc02后dc03可否访问

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/46.png)

- 再进一步

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/47.png)

- 新建dc04继承dc03后再删除dc03

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/48.png)

结论：容器之间配置信息的传递，数据卷的生命周期一直持续到没有容器使用它为止