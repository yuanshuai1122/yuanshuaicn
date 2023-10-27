---
title: docker安装部署ElasticSearch（单机版）
date: 2023-07-19 18:12:20.377
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
tags:
  - docker
  - es
---
## 一、ElasticSearch安装部署

什么是`ElasticSearch`？

> 简单来说，我们的目标是帮助每个人更快地找到所需内容，从需要通过内网获取文档的员工，到在网上购物寻找适合自己鞋子的客户。但从更技术的角度来说，大致描述如下：
>
> Elasticsearch 是一个免费且开放的分布式搜索和分析引擎，适用于包括文本、数字、地理空间、结构化和非结构化数据等在内的所有类型的数据。Elasticsearch 在 Apache Lucene 的基础上开发而成，由 Elasticsearch N.V.（即现在的 Elastic）于 2010 年首次发布。Elasticsearch 以其简单的 REST 风格 API、分布式特性、速度和可扩展性而闻名，是 Elastic Stack 的核心组件；Elastic Stack 是一套适用于数据采集、扩充、存储、分析和可视化的免费开源工具。人们通常将 Elastic Stack 称为 ELK Stack（代指 Elasticsearch、Logstash 和 Kibana），目前 Elastic Stack 包括一系列丰富的轻量型数据采集代理，这些代理统称为 Beats，可用来向 Elasticsearch 发送数据。
>
> --- 来自ES官方 https://www.elastic.co/cn/what-is/elasticsearch

`简单来说，可以当作一个高性能搜索数据库`

### 1.1 ElasticSearch和JDK版本

ElasticSearch和JDK版本适配图（2023-07-25）。

**官网地址：https://www.elastic.co/cn/support/matrix#matrix_jvm**

<img src="https://aabb-2023.oss-cn-beijing.aliyuncs.com/1.png" style="zoom:50%;" />

可以看到从ES8.0开始，就只支持JDK17以上了。

由于我们环境为：

```
[root@server ~]# java --version
openjdk 11.0.18 2023-01-17 LTS
OpenJDK Runtime Environment (Red_Hat-11.0.18.0.10-1.el7_9) (build 11.0.18+10-LTS)
OpenJDK 64-Bit Server VM (Red_Hat-11.0.18.0.10-1.el7_9) (build 11.0.18+10-LTS, mixed mode, sharing)
```

所以我们本着最新原则，选择镜像`elasticsearch:7.17.7`

千万别选最新`7.17.9`,IK中文分词器只支持到`7.17.7`，导致安装不上，除非自己编译，大坑😭

(PS:官方还说明了ES和Linux版本兼容关系，我们环境时`CentOS7.x`，正好也是兼容ES7版本的...)

### 1.2 拉取镜像

```shell
docker pull elasticsearch:7.17.7
```

### 1.3 创建挂载目录

#### 1.3.1 docker工作目录

ps:请根据自己的环境修改自己的路径

```shell
# 创建工作目录
mkdir -p /yyss/elasticsearch/
cd /yyss/elasticsearch/
# logs挂载目录
mkdir logs
```

给权限（这里很重要，否则es会因为没权限起不来）

```shell
# 由于这里时演示 所以给了777权限 生产环境别学我😄
chmod 777 /yyss/elasticsearch/
```



#### 1.3.2 初次启动镜像

```shell
# 因为只是获取其配置文件，启动时设置了较小的JVM
docker run --name es -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" \
-e ES_JAVA_OPTS="-Xms64m -Xmx128m" \
-d elasticsearch:7.17.7
```

#### 1.3.3 拷贝镜像内配置

先确定下自己所在的位置

```
[root@server elasticsearch]# pwd
/yyss/elasticsearch
```

拷贝镜像中的文件

```shell
docker cp es:/usr/share/elasticsearch/config/ ./
docker cp es:/usr/share/elasticsearch/data/ ./
docker cp es:/usr/share/elasticsearch/plugins/ ./
```

查看下现在目录下的文件

```shell
[root@server elasticsearch]# ll
total 4
drwxrwxrwx. 3 root root 270 Aug  5 13:52 config
drwxrwxrwx. 3 root root  19 Aug  5 13:49 data
drwxrwxrwx. 2 root root  37 Aug  5 13:55 logs
drwxrwxrwx. 2 root root   6 Jan 31  2023 plugins
```

可以看到 我们需要的都拷贝出来了

### 1.4 修改配置

> 根据实际情况修改JVM内存大小

```shell
cd config/
vim jvm.options
```

默认配置为：

> -Xms1g -Xmx1g

**编辑elasticsearch.yml：**

追加内容：

```shell
http.cors.enabled: true
http.cors.allow-origin: "*"
node.name: node-1
discovery.seed_hosts: ["127.0.0.1"]

xpack.security.enabled: true # 这条配置表示开启xpack认证机制 spring boot连接使用
xpack.security.transport.ssl.enabled: true
```

> xpack.security配置后，elasticsearch需要账号密码使用，建议安排上。如果使用springboot查询，那一定要设置，否者会报错！

### 1.5 挂载启动

#### 1.5.1 删除之前启动的临时容器

```shell
docker rm -f es
```

#### 1.5.2 挂载启动新容器

```shell
docker run --name  --restart=always \
-p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" \
-v /yyss/elasticsearch/config:/usr/share/elasticsearch/config \
-v /yyss/elasticsearch/data:/usr/share/elasticsearch/data \
-v /yyss/elasticsearch/plugins:/usr/share/elasticsearch/plugins \
-v /yyss/elasticsearch/logs:/usr/share/elasticsearch/logs \
-d elasticsearch:7.17.7
```

`参数解释`

- --name es  ：指定容器名称
- -p 9200:9200  -p 9300:9300 ：指定端口号映射 格式 宿主机端口号:容器端口号

- -e  "discovery.type=single-node"  ：单机模式

- -v /yyss/elasticsearch/config:/usr/share/elasticsearch/config 映射配置文件在宿主机的挂载位置
- -v /yyss/elasticsearch/data:/usr/share/elasticsearch/data 映射日志文件在宿主机的挂载位置
- -v /yyss/elasticsearch/plugins:/usr/share/elasticsearch/plugins 映射插件在宿主机的挂载位置
- -v /yyss/elasticsearch/logs:/usr/share/elasticsearch/logs 映射日志文件在宿主机的挂载位置

- -d elasticsearch:7.17.7  ：指定镜像和版本

## 二、初始化密码

此项仅在上文xpack配置的情况下才需要执行，首先进入容器命令行：

```shell
docker exec -it es /bin/bash
```

然后直行初始化命令:

```
bin/elasticsearch-setup-passwords interactive
```

<img src="https://aabb-2023.oss-cn-beijing.aliyuncs.com/2.png" style="zoom:50%;" />

> 然后老老实实依次输入密码（需要输入很多很多次，别挣扎了，输入吧！😭）

## 三、 验证安装

访问ip:9200，如果上文开启了xpack.security，需要输入账号密码。

`云服务器记得放行端口安全组～`

账号/密码：elastic/上文设置的密码

如果出现以下页面，则代表成功。

<img src="https://aabb-2023.oss-cn-beijing.aliyuncs.com/3.png" style="zoom:50%;" />

## 四、插件安装：IK中文分词器

#### 4.1 版本对应

下载地址：https://github.com/medcl/elasticsearch-analysis-ik/releases

下载的版本需要与es版本一致；（必须严格一致）

地址：`https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.17.7/elasticsearch-analysis-ik-7.17.7.zip`

#### 4.2 安装

##### 4.2.1 进入到插件挂载目录

```shell
cd /yyss/elasticsearch/plugins
```

##### 4.2.2 下载对IK中文分词器

```shell
wget https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.17.7/elasticsearch-analysis-ik-7.17.7.zip
```

##### 4.2.3 解压并清理

```shell
# 如果unzip命令不存在，则安装：yum install unzip
unzip elasticsearch-analysis-ik-7.17.7.zip -d ik-analyzer
# 解压完成后，删除`elasticsearch-analysis-ik-7.17.7.zip`压缩包
rm elasticsearch-analysis-ik-7.17.7.zip
```

##### 3.2.4 重启容器

```shell
docker restart es
```

#### 4.3 验证

- 使用`docker exec -it es /bin/bash `命令 进入容器内部
- 进入容器的 `cd /usr/share/elasticsearch/bin` 目录
- 执行 `elasticsearch-plugin list` 命令(列出es安装的所有插件)
- 如果列出了 `ik-analyzer` 就说明es的ik中文分词器安装成功了

<img src="https://aabb-2023.oss-cn-beijing.aliyuncs.com/4.png" style="zoom:50%;" />

#### 4.4 测试

##### 4.4.1 测试普通分词器

```json
// GET http://IP:9200/_analyze
{
  "analyzer": "standard",
  "text":     "测试分词器"
}
```

response:

```json
{
    "tokens": [
        {
            "token": "测",
            "start_offset": 0,
            "end_offset": 1,
            "type": "<IDEOGRAPHIC>",
            "position": 0
        },
        {
            "token": "试",
            "start_offset": 1,
            "end_offset": 2,
            "type": "<IDEOGRAPHIC>",
            "position": 1
        },
        {
            "token": "分",
            "start_offset": 2,
            "end_offset": 3,
            "type": "<IDEOGRAPHIC>",
            "position": 2
        },
        {
            "token": "词",
            "start_offset": 3,
            "end_offset": 4,
            "type": "<IDEOGRAPHIC>",
            "position": 3
        },
        {
            "token": "器",
            "start_offset": 4,
            "end_offset": 5,
            "type": "<IDEOGRAPHIC>",
            "position": 4
        }
    ]
}
```

> 可以看到，查询出的内容，每个文字都是单独的，不会组成一个`词`；

##### 4.4.2 使用`IK分词器`

请求json如下：

```json
// GET http://IP:9200/_analyze
{
  "analyzer": "ik_max_word",
  "text":     "测试分词器"
}
```

response:

```json
{
    "tokens": [
        {
            "token": "测试",
            "start_offset": 0,
            "end_offset": 2,
            "type": "CN_WORD",
            "position": 0
        },
        {
            "token": "分词器",
            "start_offset": 2,
            "end_offset": 5,
            "type": "CN_WORD",
            "position": 1
        },
        {
            "token": "分词",
            "start_offset": 2,
            "end_offset": 4,
            "type": "CN_WORD",
            "position": 2
        },
        {
            "token": "器",
            "start_offset": 4,
            "end_offset": 5,
            "type": "CN_CHAR",
            "position": 3
        }
    ]
}
```



结束!