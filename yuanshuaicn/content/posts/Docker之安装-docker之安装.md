---
title: Docker之安装
date: 2021-03-23 23:04:09.365
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
tags: 
- docker
---

# Docker**安装步骤**

## 1、CentOS6.8安装Docker

1. #### yum install -y epel-release

```shell
yum install -y epel-release
```

Docker使用EPEL发布，RHEL系的OS首先要确保已经持有EPEL仓库，否则先检查OS的版本，然后安装相应的EPEL包

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/5.png)

2. #### yum install -y docker-io

```shell
yum install -y docker-io
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/6.png)

3. #### 安装后的配置文件：/etc/sysconfig/docker

```shell
cat /etc/sysconfig/docker
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/7.png)

4. #### 启动Docker后台服务：service docker start

```shell
service docker start
```

5. #### docker version验证

```shell
docker version
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/8.png)

## 2、CentOS7安装Docker

1. #### 官网中文安装参考手册

https://docs.docker-cn.com/engine/installation/linux/docker-ce/centos/#prerequisites

2. #### 确定你是CentOS7及以上版本

```shell
cat /etc/redhat-release
```

3. #### yum安装gcc相关

- CentOS7能上外网

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/9.png)

```shell
yum -y install gcc
```

```shell
yum -y install gcc-c++
```

4. #### 卸载旧版本

```shell
yum -y remove docker docker-common docker-selinux docker-engine
```

```shell
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

5. #### 安装需要的软件包

```shell
yum install -y yum-utils device-mapper-persistent-data lvm2
```

6. #### 设置stable镜像仓库

- 大坑

```shell
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

报错： 

1  [Errno 14] curl#35 - TCP connection reset by peer 

2  [Errno 12] curl#35 - Timeout 

- 推荐

```shell
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

7. #### 更新yum软件包索引

```shell
yum makecache fast
```

8. #### 安装DOCKER CE

```shell
yum -y install docker-ce
```

9. #### 启动docker

```shell
systemctl start docker
```

10. #### 测试

```shell
docker version
```

```shell
docker run hello-world
```

11. #### 配置镜像加速

```shell
mkdir -p /etc/docker
```

```shell
vim  /etc/docker/daemon.json
```

>  \#网易云 
>
> {"registry-mirrors": ["http://hub-mirror.c.163.com"] } 
>
> 
>
>  \#阿里云 
>
> { 
>
>  "registry-mirrors": ["https://｛自已的编码｝.mirror.aliyuncs.com"] 
>
> } 

```shell
systemctl daemon-reload
```

```shell
systemctl restart docker
```

12. #### 卸载

```shell
systemctl stop docker 
```

```shell
yum -y remove docker-ce
```

```shell
rm -rf /var/lib/docker
```

## 3、永远的HelloWorld

### **3.1、阿里云镜像加速**

1. #### 是什么

https://dev.aliyun.com/search.html

2. #### 注册一个属于自己的阿里云账户(可复用淘宝账号)

3. #### 获得加速器地址连接

- 登陆阿里云开发者平台

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/10.png)

- 获取加速器地址

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/11.png)

4. #### 配置本机Docker运行镜像加速器

鉴于国内网络问题，后续拉取 Docker 镜像十分缓慢，我们可以需要配置加速器来解决， 

我使用的是阿里云的本人自己账号的镜像地址(需要自己注册有一个属于你自己的)：   https://xxxx.mirror.aliyuncs.com 

```shell
 vim /etc/sysconfig/docker 
```

将获得的自己账户下的阿里云加速地址配置进 

```shell
other_args="--registry-mirror=https://你自己的账号加速信息.mirror.aliyuncs.com" 
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/12.png)

5. #### 重新启动Docker后台服务

```shell
service docker restart
```

6. #### Linux 系统下配置完加速器需要检查是否生效

如果从结果中看到了配置的--registry-mirror参数说明配置成功，如下所示: 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/13.png)

### 3.2、**网易云加速**

基本同上述阿里云

只是配置Json串的地方不同了: 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/14.png)

### 3.3、启动Docker后台容器(测试运行 hello-world)

```shell
docker run hello-world
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/15.png)

输出这段提示以后，hello world就会停止运行，容器自动终止。 

- run干了什么

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/16.png)

## 4、**底层原理**

### 4.1、Docker是怎么工作的

Docker是一个Client-Server结构的系统，Docker守护进程运行在主机上， 然后通过Socket连接从客户端访问，守护进程从客户端接受命令并管理运行在主机上的容器 。 容器，是一个运行时环境，就是我们前面说到的集装箱。 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/docker/17.png)

### 4.2、为什么Docker比较比VM快

1. docker有着比虚拟机更少的抽象层。由于docker不需要Hypervisor实现硬件资源虚拟化,运行在docker容器上的程序直接使用的都是实际物理机的硬件资源。因此在CPU、内存利用率上docker将会在效率上有明显优势。 

2. docker利用的是宿主机的内核,而不需要Guest OS。因此,当新建一个容器时,docker不需要和虚拟机一样重新加载一个操作系统内核。仍而避免引寻、加载操作系统内核返个比较费时费资源的过程,当新建一个虚拟机时,虚拟机软件需要加载Guest OS,返个新建过程是分钟级别的。而docker由于直接利用宿主机的操作系统,则省略了返个过程,因此新建一个docker容器只需要几秒钟。