---
title: Git和Github之Github
date: 2021-02-23 23:10:02.488
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
tags: 
- git
- github
---

# **GitHub**

## **1**、账号信息

GitHub 首页就是注册页面:https://github.com/

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/40.png)

## 2、创建远程库

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/41.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/42.png)

## 3、创建远程库地址别名

查看当前所有远程地址别名

```shell
git remote -v
```

```shell
git remote add [别名] [远程地址]
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/43.png)

## 4、推送

```shell
git push [别名] [分支名]
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/44.png)

## 5、 克隆

命令：

```shell
git origin [远程地址]
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/45.png)

- 效果
  - 完整的把远程库下载到本地
  - 创建origin远程地址别名
  - 初始化本地库

## 6、团队成员邀请

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/46.png)

“岳不群”其他方式把邀请链接发送给“令狐冲”，“令狐冲”登录自己的 GitHub 账号，访问邀请链接。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/47.png)

## 7、拉取

 pull=fetch+merge

```shell
git fetch [远程库地址别名] [远程分支名]

git merge [远程库地址别名/远程分支名]

git pull [远程库地址别名] [远程分支名]
```

## 8、解决冲突

- 要点
  - 如果不是基于GitHub远程库的最新版所做的修改，不能推送，必须先拉取。
  - 拉取下来后如果进入冲突状态，则按照“分支冲突解决”操作解决即可。
- 类比
  - 债权人:老王
  - 债务人:小刘
  - 老王说:10天后归还。小刘接受，双方达成一致。
  - 老王媳妇说:5天后归还。小刘不能接受。老王媳妇需要找老王确认后再执行。

## 9、跨团队协作

Fork

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/48.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/49.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/50.png)

本地修改，然后推送到远程

Pull Request

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/51.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/52.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/53.png)

对话

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/54.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/55.png)

审核代码

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/56.png)

合并代码

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/57.png)

将远程库修改拉取到本地

## 10、**SSH** 登录

- 进入当前用户的家目录

  ```shell
  cd ~
  ```

- 删除.ssh目录

  ```shell
  rm -rvf .ssh
  ```

- 运行命令生成.ssh密钥目录

  ```shell
  ssh-keygen -t rsa -C atguigu2018ybuq@aliyun.com
  ```

  [注意:这里**-C** 这个参数是大写的 **C**]

- 进入.ssh目录查看文件列表

  ```shell
  cd .ssh
  ls -lF
  ```

- 查看id_rsa.pub文件内容

  ```shell
  cat id_rsa.pub
  ```

- 复制 id_rsa.pub 文件内容，登录 GitHub，点击用户头像→Settings→SSH and GPG keys

- New SSH Key

- 输入复制的密钥信息

- 回到 Git bash 创建远程地址别名

  ```shell
  git remote add origin_ssh git@github.com:atguigu2018ybuq/huashan.git
  ```

- 推送文件进行测试