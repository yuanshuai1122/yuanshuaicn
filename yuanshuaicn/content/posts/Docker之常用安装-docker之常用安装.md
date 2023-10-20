---
title: Docker之常用安装
date: 2021-11-23 23:04:24.129
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/homepage-docker-logo.png'
theme: 'light'
tags: 
- docker
---

# **Docker**常用安装

## 1、 **总体步骤**

- 搜索镜像
- 拉取镜像
- 查看镜像
- 启动镜像
- 停止容器
- 移除容器

## 2、**安装**tomcat

1. docker hub上面查找tomcat镜像

```shell
docker search tomcat
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/71.png)

2. 从docker hub上拉取tomcat镜像到本地

```shell
docker pull tomcat
```

 官网命令

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/72.png)

拉取完成

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/73.png)

3. docker images查看是否有拉取到的tomcat

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/74.png)

4. 使用tomcat镜像创建容器(也叫运行镜像)

```shell
docker run -it -p 8080:8080 tomcat
```

-p 主机端口:docker容器端口

-P 随机分配端口

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/75.png)

i:交互

t:终端

## 3、**安装**mysql

1. docker hub上面查找mysql镜像

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/76.png)

2. 从docker hub上(阿里云加速器)拉取mysql镜像到本地标签为5.6

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/77.png)

3. 使用mysql5.6镜像创建容器(也叫运行镜像)

- 使用mysql镜像

```shell
docker run -p 12345:3306 --name mysql -v /zzyyuse/mysql/conf:/etc/mysql/conf.d -v /zzyyuse/mysql/logs:/logs -v /zzyyuse/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.6
```

命令说明： 

-p 12345:3306：将主机的12345端口映射到docker容器的3306端口。 

--name mysql：运行服务名字 

-v /zzyyuse/mysql/conf:/etc/mysql/conf.d ：将主机/zzyyuse/mysql录下的conf/my.cnf 挂载到容器的 /etc/mysql/conf.d 

-v /zzyyuse/mysql/logs:/logs：将主机/zzyyuse/mysql目录下的 logs 目录挂载到容器的 /logs。 

-v /zzyyuse/mysql/data:/var/lib/mysql ：将主机/zzyyuse/mysql目录下的data目录挂载到容器的 /var/lib/mysql 

-e MYSQL_ROOT_PASSWORD=123456：初始化 root 用户的密码。 

-d mysql:5.6 : 后台程序运行mysql5.6 

> docker exec -it MySQL运行成功后的容器ID   /bin/bash 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/78.png)

- 外部Win10也来连接运行在dokcer上的mysql服务

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/79.png)

- 数据备份小测试(可以不做)

```shell
docker exec myql服务容器ID sh -c ' exec mysqldump --all-databases -uroot -p"123456" ' > /zzyyuse/all-databases.sql 
```

## 4、**安装**redis

1. 从docker hub上(阿里云加速器)拉取redis镜像到本地标签为3.2

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/80.png)

2. 使用redis3.2镜像创建容器(也叫运行镜像)

- 使用镜像

```shell
docker run -p 6379:6379 -v /zzyyuse/myredis/data:/data -v /zzyyuse/myredis/conf/redis.conf:/usr/local/etc/redis/redis.conf  -d redis:3.2 redis-server /usr/local/etc/redis/redis.conf --appendonly yes 
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/81.png)

- 在主机/zzyyuse/myredis/conf/redis.conf目录下新建redis.conf文件

```shell
vim /zzyyuse/myredis/conf/redis.conf/redis.conf
```

配置conf文件

- 测试redis-cli连接上来

```shell
docker exec -it 运行着Rediis服务的容器ID redis-cli 
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/82.png)

- 测试持久化文件生成

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/83.png)