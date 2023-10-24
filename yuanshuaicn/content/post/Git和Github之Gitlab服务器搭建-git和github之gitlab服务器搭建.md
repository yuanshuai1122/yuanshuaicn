---
title: Git和Github之Gitlab服务器搭建
date: 2021-post-23 23:10:07.263
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/1_dDNpLKu_oTLzStsDTnkJ-g.png'
theme: 'light'
tags: 
- git
- github
---

# **Gitlab**服务器搭建过程

## 1、官网地址

首页:https://about.gitlab.com/ 

安装说明:https://about.gitlab.com/installation/

## 2、安装命令摘录

```shell
sudo yum install -y curl policycoreutils-python openssh-server cronie sudo lokkit -s http -s ssh
sudo yum install postfix
sudo service postfix start
sudo chkconfig postfix on
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.rpm.sh | sudo bash sudo EXTERNAL_URL="http://gitlab.example.com" yum -y install gitlab-ee
```

实际问题:yum 安装 gitlab-ee(或 ce)时，需要联网下载几百 M 的安装文件，非常耗 时，所以应提前把所需 RPM 包下载并安装好。

下载地址为:

> https://packages.gitlab.com/gitlab/gitlab-ce/packages/el/7/gitlab-ce-10.8.2-ce.0.el7.x86_64.rpm

## 3、调整后的安装过程

```shell
sudo rpm -ivh /opt/gitlab-ce-10.8.2-ce.0.el7.x86_64.rpm
sudo yum install -y curl policycoreutils-python openssh-server cronie sudo lokkit -s http -s ssh
sudo yum install postfix
sudo service postfix start
sudo chkconfig postfix on
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash sudo EXTERNAL_URL="http://gitlab.example.com" yum -y install gitlab-ce
```

当前步骤完成后重启。

## 4、**gitlab** 服务操作

初始化配置gitlab

```shell
gitlab-ctl reconfigure
```

启动gitlab服务

```shell
gitlab-ctl start
```

停止gitlab服务

```shell
gitlab-ctl stop
```

## 5、浏览器访问

访问 Linux 服务器 IP 地址即可，如果想访问 EXTERNAL_URL 指定的域名还需要配置域名服务器或本地 hosts 文件。
初次登录时需要为 gitlab 的 root 用户设置密码。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/72.png)

进不去应该会需要停止防火墙服务:

```shell
service firewalld stop
```