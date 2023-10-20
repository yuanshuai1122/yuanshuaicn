---
title: Linux之网络配置
date: 2021-11-24 08:52:34.326
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/1_ABo4dTsrgA7UboVI7c6yIA.jpeg'
theme: 'light'
tags: 
- linux
- 操作系统
---

# 网络配置

## 1 Linux网络配置原理

> 虚拟机NAT网络配置原理

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/linux/3.9%E8%99%9A%E6%8B%9F%E6%9C%BANAT%E7%BD%91%E7%BB%9C%E9%85%8D%E7%BD%AE%E5%8E%9F%E7%90%86.jpg)

## 2 查看网络IP和网关

### 2.1 虚拟机网络编辑器

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/linux/3.9%E8%99%9A%E6%8B%9F%E6%9C%BA%E7%BD%91%E7%BB%9C%E7%BC%96%E8%BE%91%E5%99%A8.jpg)

### 2.2 修改IP地址

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/linux/3.9%E4%BF%AE%E6%94%B9IP%E5%9C%B0%E5%9D%80.jpg)

### 2.3 查看网关

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/linux/3.9%E6%9F%A5%E7%9C%8B%E7%BD%91%E5%85%B3.jpg)

### 2.4 查看windows中的虚拟网卡的ip地址

* windows中使用ipconfig查看ip配置

## 3. ping测试

> 基本语法: ping [主机地址]

* 例如： ping www.baidu.com

## 4. Linux网络环境配置

### 4.1 自动抓取

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/linux/3.9%E8%87%AA%E5%8A%A8%E6%8A%93%E5%8F%96.jpg)

**`缺点：`** 每次自动获取的ip地址可能不一样，不适用于做服务器

### 4.2 指定ip地址

1. 直接修改配置文件来指定IP，并可以连接到外网，编辑：vim /etc/sysconfig/network-scripts/ifcfg-eth0
    ![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/linux/3.9%E6%8C%87%E5%AE%9Aip%E5%9C%B0%E5%9D%80.jpg)
2. 重启网络服务：service network restart
3. 重启系统：reboot
![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/linux/3.9ifcfg-eth0%E8%AF%B4%E6%98%8E.jpg)