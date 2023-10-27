---
title: docker安装Kibana和Fscrawler
date: 2023-07-19 18:12:20.377
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
tags: 
- docker
- es
---
## 一、安装Kibana

### 1.1 Kibana是什么

引用ES官方说法

`https://www.elastic.co/cn/what-is/kibana`

> Kibana 是一款免费且开放的前端应用程序，其基础是 Elastic Stack，可以为 Elasticsearch 中索引的数据提供搜索和数据可视化功能。尽管人们通常将 Kibana 视作 Elastic Stack（之前称作 ELK Stack，分别表示 Elasticsearch、Logstash 和 Kibana）的制图工具，但也可将 Kibana 作为用户界面来监测和管理 Elastic Stack 集群并确保集群安全性，还可将其作为基于 Elastic Stack 所开发内置解决方案的汇集中心。Elasticsearch 社区于 2013 年开发出了 Kibana，现在 Kibana 已发展成为 Elastic Stack 的窗口，是用户和公司的一个门户。

简单来说，`Kibana`是`ElasticSearch`的可视化工具。

### 1.2 版本适配

我们在上一节安装了`ElasticSearch:1.17.7`版本，查看官方版本兼容适配：

> https://www.elastic.co/cn/support/matrix#matrix_compatibility

这里就不上图了，我们安装`kibana:7.17.7`

### 1.3 拉取镜像并启动

#### 1.3.1 拉取镜像

```shell
docker pull kibana:7.17.7
```

#### 1.3.2 临时启动

```shell
docker run -d --name kibana -p 5601:5601 kibana:7.17.7
```

#### 1.3.3 拷贝配置文件

```shell
# 创建工作空间
mkdir -p /yyss/kibana
# 进入工作空间
cd /yyss/kibana
# 拷贝容器中配置文件到本地
docker cp kibana:/usr/share/kibana/config ./
# 编辑配置文件
cd config/
vim kibana.yml
```

配置示例：

```yaml
server.host: "0.0.0.0"
server.shutdownTimeout: "5s"
elasticsearch.hosts: [ "http://192.168.100.139:9200" ]
monitoring.ui.container.elasticsearch.enabled: true
elasticsearch.username: "elastic"
elasticsearch.password: "123456"
xpack.security.sessionTimeout: 600000
i18n.locale: "zh-CN"
```

主要设置和ES相关的配置，毕竟我们是要连接ES的。

#### 1.3.4 停掉临时容器并重新启动

```shell
# 删除临时容器
docker rm -f kibana
# 重新启动
docker run -d --name kibana -p 5601:5601 -v /yyss/kibana/config:/usr/share/kibana/config kibana:7.17.7
```

参数介绍：

- -p 5601:5601 将宿主机端口映射到容器端口
- -v /yyss/kibana/config:/usr/share/kibana/config 将宿主机config目录挂载到容器config目录

### 1.3.5 测试访问

打开浏览器，访问IP:5601

账号：elastic

密码：123456

云服务器记得开端口

<img src="https://aabb-2023.oss-cn-beijing.aliyuncs.com/5.png" style="zoom:50%;" />

进入页面如图所示。

## 二、安装elastic-head

### 2.1 elastic-head简介

> ealsticsearch是一个分布式、RESTful 风格的搜索和数据分析引擎，所有的数据都是后台服务存储着，类似于Mysql服务器，因此如果我们需要直观的查看数据，就需要使用可视化工具了。elasticsearch-head是Web前端，用于浏览和与Elastic Search集群进行交互，可用于集群管理、数据可视化、增删改查工具Elasticsearch语句可视化等。
>
> 简答来说，`elasticsearch-head`是es的集群可视化管理工具。

### 2.2 elastic-head安装

```shell
docker run -d \
--name=elasticsearch-head \
-p 9100:9100 \
mobz/elasticsearch-head:5-alpine
```

### 2.3 elastic-head访问

浏览器访问地址：http://IP:9100/

<img src="https://aabb-2023.oss-cn-beijing.aliyuncs.com/6.png" style="zoom:50%;" />

在上方输入框连接ES集群:http://IP:9200/

后续带x-pack的认证信息的访问

```
http://{ip}:9100/?auth_user=elastic&auth_password=123456
```

## 三、安装FSCrawler

### 3.1 FSCrawler简介

> Welcome to the FS Crawler for [Elasticsearch](https://elastic.co/)
>
> This crawler helps to index binary documents such as PDF, Open Office, MS Office.
>
> **Main features**:
>
> - Local file system (or a mounted drive) crawling and index new files, update existing ones and removes old ones.
> - Remote file system over SSH/FTP crawling.
> - REST interface to let you "upload" your binary documents to elasticsearch.

简单来说就是帮助ES索引文件的。

### 3.2 版本适配

> https://github.com/dadoonet/fscrawler

<img src="https://aabb-2023.oss-cn-beijing.aliyuncs.com/7.png" style="zoom:50%;" />

我们ES版本是`7.17.7`，所以我们选择`FSCrawler:2.9`

### 3.3 拉取镜像

```shell
docker pull dadoonet/fscrawler:2.9
```

### 3.4 创建目录并启动

#### 3.4.1 创建目录

```shell
# 创建工作目录
mkdir -p /yyss/fscrawler
# 创建文档目录 即要被摄入的文档位置
mkdir -p /yyss/disk/es-docs
```

#### 3.4.2 启动镜像

```shell
docker run -it -d --name fscrawler -v /yyss/fscrawler:/root/.fscrawler -v /yyss/disk/es-docs:/tmp/es:ro dadoonet/fscrawler:2.9 fscrawler test-job
```

参数介绍：

- -v /yyss/fscrawler:/root/.fscrawler 挂载本地工作目录到镜像
- -v /yyss/disk/es-docs:/tmp/es:ro 挂载本地文档位置到镜像
- fscrawler test-job 创建一个名为`test-job`的作业

在第一次运行时，如果 ~/.fscrawler 中尚不存在该作业，FSCrawler 将询问你是否要创建它

> **注意**：配置文件实际上存储在你机器上的 /yyss/fscrawler/job_name/_settings.yaml 中。 请记住更改你的 elasticsearch 实例的 URL，因为容器将无法看到它在默认 127.0.0.1 下运行。 你将需要使用主机的实际 IP 地址。

#### 3.4.3 编辑配置

```shell
# 进入目录
cd /yyss/fscrawler/test-job/
# 编辑配置
vim _settings.yaml
```

配置示例：

```yaml
---
name: "test-job"
fs:
  url: "/tmp/es"
  update_rate: "15m"
  excludes:
  - "*/~*"
  json_support: false
  filename_as_id: false
  add_filesize: true
  remove_deleted: true
  add_as_inner_object: false
  store_source: false
  index_content: true
  attributes_support: false
  raw_metadata: false
  xml_support: false
  index_folders: true
  lang_detect: false
  continue_on_error: false
  ocr:
    language: "eng"
    enabled: true
    pdf_strategy: "ocr_and_text"
  follow_symlinks: false
elasticsearch:
  nodes:
  - url: "http://192.168.100.139:9200"
  username: "elastic"
  password: "123456"
  bulk_size: 100
  flush_interval: "5s"
  byte_size: "10mb"
  ssl_verification: false
```

如上所示，我们需要做上面的配置。为了方便，我们特意设置 ssl_verification 为 false。你需要根据自己的 Elasticsearch 端点及用户账号信息进行修改。修改完后保存，我们再次运行 Docker：

```shell
docker restart fscrawler
```

到这里，我们添加了索引，并监听`/yyss/disk/es-docs`内文件的变化，我们可以进入该目录下添加点文件，fscrawler会帮我们重新创建文件索引。

#### 3.4.4 进入Kibana验证

我们进入到上第一步安装的kibana界面，点左边开发工具，进入控制台，输入

```shell
GET _cat/indices
```

<img src="https://aabb-2023.oss-cn-beijing.aliyuncs.com/8.png" style="zoom:50%;" />

可以看到我们的索引`test-job`已经可以查到。

查看索引下详细文件

```shell
GET test-job_folder/_search?filter_path=**.hits
```

<img src="https://aabb-2023.oss-cn-beijing.aliyuncs.com/9.png" style="zoom:50%;" />

可以看到我们`/yyss/disk/es-docs`文件夹下所有内容。