---
title: NPS内网穿透的搭建与演示
date: 2021-10-25 18:38:23.971
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
  avatar: "https://aabb-2023.oss-cn-beijing.aliyuncs.com/hjscijg3uw.png"
tags: 
- 计算机网络
---

> 概念

内网穿透：内网穿透，即NAT穿透，网络连接时术语，计算机是局域网内时，外网与内网的计算机节点需要连接通信，有时就会出现不支持内网穿透。就是说映射端口，能让外网的电脑找到处于内网的电脑，提高下载速度。不管是内网穿透还是其他类型的网络穿透，都是网络穿透的统一方法来研究和解决。

简而言之：在外网访问局域网/内网的地址。

假设场景：人在外面逛街，但能通过手机浏览器访问家里的路由管理界面，或者远程操作家里的电脑。

常见内网穿透工具：ngrok、frpc、nps

nps是一款轻量级、功能强大的内网穿透代理服务器。支持tcp、udp流量转发，支持内网http代理、内网socks5代理，同时支持snappy压缩、站点保护、加密传输、多路复用、header修改等。支持web图形化管理，集成多用户模式。

> 操作步骤

前期准备：

- 外网能访问到的云服务器：阿里云、腾讯云等。
- 内网自己搭建的服务器：我这里用的是树莓派4B 8G 64位arm架构的Debian系统服务器

`因为我们内网搭建的服务器，在外网是访问不到的，所以我们需要一台云服务器，利用nps进行转发，并实现访问树莓派服务器。`

接着在nps 的releases 页面:https://github.com/cnlh/nps/releases
找到你需要的版本，我这里用的服务器是腾讯云的centos 7，那么也就是Linux的64位版本，下载对应的服务端为`linux_386_server`,对客户端为`linux_arm64_client`。

仓库里有两个这种的系统，注意区分，x86要用下面的client对应的server，否则域名解析和http都穿透不了！！！！

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/operation/1.png)

> 配置云服务器

下载服务端`linux_386_server.tar.gz`并上传到云服务器。

解压：

```shell
tar -zxvf linux_386_server.tar.gz
```

解压后发现有两个文件夹：1、`conf`:包含了nps的配置文件。2、`web`:nps的web界面文件

和一个可执行文件：`nps`

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/operation/3.png)

进入conf

```shell
cd conf/
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/operation/4.png)

编辑`nps.conf`:这是nps的配置文件。

```shell
vim nps.conf 
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/operation/5.png)

http和https端口，修改为和你内网服务相同的端口，这样才可以进行穿透。比如你建立了个博客，http端口为5000，https端口为：5100，那么服务端就要设置为对应的端口。

建议结合nginx使用，这样就可以一台服务器，部署多个服务，非常的丝滑。

修改下面的web配置：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/operation/6.png)

默认web界面的密码和端口，建议修改成自己的。

全部设置完毕后，保存退出。

注：我这里将http端口设置为：5800，https：5200，bridge_port:5300

`别忘了去云服务器防火墙开启对应的端口哦！！！`

接下来回到nps上层目录

```shell
cd ..
```

启动nps

```shell
./nps
```

建议后台启动，还可以打印日志

```shell
nohup ./nps >> /root/test/nps/nps.log &
```

进入nps的web界面

http://云服务器ip:8080

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/operation/7.png)

输入账号密码，进行登录

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/operation/8.png)

这就是nps的web界面，我们可以看到客户端连接端口为：5300，http端口：5800，https端口：5200

加下来点击左边客户端，添加一个客户端（现在显示客户端总数为1，是因为我之前添加过一个）

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/operation/9.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/operation/10.png)

备注可以写你内网服务器的名字什么的（我们这里写内网测试），其他的都可以留空，会自动生成秘钥！

点击新增：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/operation/11.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/operation/12.png)

我们可以看到自动生成了秘钥，并且客户端还没有连接，显示离线。

接下来，配置内网服务器。

> 配置内网服务器

类似于服务端的配置，将下载的nps客户端，上传的内网服务器，解压，进入。

启动nps客户端

```shell
nohup ./npc -server=云服务器ip:5300 -vkey=fjwbqfxwsy5lr9ui >> /root/test/npc/npc.log &
```

5300是我们上面设置的bridge_port,         fjwbqfxwsy5lr9ui是我们客户端生成的秘钥

注：客户端启动文件叫npc，区别于服务端nps

接下来返回到web界面

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/operation/13.png)

可以看到变成了在线，并且显示了客户端地址，我们点击隧道，进去点击新增，创建内网穿透服务。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/operation/14.png)

比如我们创建一条tcp通道，将外网的33端口，作为访问内网的ssh端口即22端口，就可以上面这样写。

点击新增

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/operation/15.png)

这样我们就可以用外网，用ssh连接工具，连接云服务器ip的33端口，进行ssh内网穿透，实现ssh连接内网的服务器。

当然也支持其他常见的协议穿透，具体可以参见nps官方文档。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/operation/16.png)

文档地址：https://ehang-io.github.io/nps/#/?id=nps

再见！