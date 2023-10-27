---
title: Git和Github之Git命令行操作
date: 2021-02-23 23:10:02.488
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
tags: 
- git
- github
---

# **Git**命令行操作

## **1**、本地库初始化

命令:

```shell
git add
```

效果:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/17.png)

注意:.git目录中存放的是本地库相关的子目录和文件，不要删除，也不要胡乱修改。

## **2**、设置签名

形式：

> 用户名:tom
>
> Email 地址:goodMorning@atguigu.com

- 作用:区分不同开发人员的身份
- 辨析:这里设置的签名和登录远程库(代码托管中心)的账号、密码没有任何关系。
- 命令
  - 项目级别/仓库级别:仅在当前本地库范围内有效
  - git **config** user.name tom_pro
  - git **config** user.email goodMorning_pro@atguigu.com
  - 信息保存位置:./.git/config 文件

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/18.png)

- 系统用户级别:登录当前操作系统的用户范围
  - git config **--global** user.name tom_glb
  - git config **--global** goodMorning_pro@atguigu.com 
  - 信息保存位置:~/.gitconfig 文件

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/19.png)

- 级别优先级
  - 就近原则:项目级别优先于系统用户级别，二者都有时采用项目级别的签名
  - 如果只有系统用户级别的签名，就以系统用户级别的签名为准
  - 二者都没有不允许

## 3、基本操作

### 3.1、状态查看 

```shell
git status
```

查看工作区、暂存区状态

### 3.2、添加

```shell
git add [file name]
```

将工作区的“新建/修改”添加到暂存区

### 4.3、提交

```shell
git commit -m "commit message" [file name]
```

将暂存区的内容提交到本地库

### 3.4、查看历史记录

```shell
git log
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/20.png)

- 多屏显示控制方式:
  - 空格向下翻页
  - b 向上翻页 
  - q退出

```shell
git log --pretty=oneline
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/21.png)

```shell
git log --oneline
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/22.png)

```shell
git reflog
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/23.png)

HEAD@{移动到当前版本需要多少步}

### 3.5、前进后退

- 本质：指针

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/24.png)

- 基于索引值操作[推荐]

```shell
git reset --hard [局部索引值] 
```

```shell
git reset --hard a6ace91
```

- 使用^符号:只能后退

```shell
git reset --hard HEAD^
```

注:一个^表示后退一步，n个表示后退n步

- 使用~符号:只能后退

```shell
git reset --hard HEAD~n
```

注:表示后退n步

### 3.6、**reset** 命令的三个参数对比

- --soft参数
  - 仅仅在本地库移动HEAD指针

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/25.png)

- --mixed参数
  - 在本地库移动HEAD指针 
  - 重置暂存区

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/26.png)

- --hard参数
  - 在本地库移动HEAD指针
  - 重置暂存区
  - 重置工作区

### 3.7、** 删除文件并找回

- 前提:删除前，文件存在时的状态提交到了本地库。
- 操作:

```shell
git reset --hard [指针位置]
```

- 删除操作已经提交到本地库:指针位置指向历史记录

- 删除操作尚未提交到本地库:指针位置使用HEAD

### 3.8、比较文件差异

```shell
git diff [文件名]
```

- 将工作区中的文件和暂存区进行比较

```shell
git diff [本地库中历史版本] [文件名]
```

- 将工作区中的文件和本地库历史记录比较

-  不带文件名比较多个文件

## 4、分支管理

### 4.1、什么是分支?

在版本控制过程中，使用多条线同时推进多个任务。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/27.png)

### 4.2、分支的好处?

- 同时并行推进多个功能开发，提高开发效率
- 各个分支在开发过程中，如果某一个分支开发失败，不会对其他分支有任何影响。失败的分支删除重新开始即可。

### 4.3、分支操作

- 创建分支

  ```shell
  git branch [分支名]
  ```

-  查看分支

  ```shell
  git branch -v
  ```

- 切换分支

   ```shell
  git checkout [分支名]
  ```

- 合并分支
  第一步:切换到接受修改的分支(被合并，增加新内容)上

  ```shell
  git checkout [被合并分支名]
  ```

  第二步:执行merge命令 

  ```shell
  git merge [有新内容分支名]
  ```

- 解决冲突
  
  - 冲突的表现

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/git_github/28.png)

- 冲突的解决
  - 第一步:编辑文件，删除特殊符号
  - 第二步:把文件修改到满意的程度，保存退出
  - 第三步:git add [文件名]
  - 第四步:git commit -m "日志信息"
    - 注意:此时commit一定不能带具体文件名