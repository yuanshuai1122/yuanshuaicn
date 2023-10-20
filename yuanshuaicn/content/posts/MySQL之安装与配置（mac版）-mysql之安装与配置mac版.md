---
title: MySQL之安装与配置（mac版）
date: 2021-11-24 08:53:18.717
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/mysql.png'
theme: 'light'
tags: 
- mysql
- 数据库
---

# 一、安装

第一步：打开MySQL官网网址，[https://www.mysql.com](https://www.mysql.com/) ，点击DOWNLOAD。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/mysql_basics/1.png)

第二步： 第一步结束后程序会跳转到https://dev.mysql.com/downloads/ 网址，一直往下翻，找到MySQL Community（GPL）Downloads，我们要下载的是社区版MySQL，也就是说免费版本。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/mysql_basics/2.png)

第三步：页面跳转后点击MySQL Community Server

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/mysql_basics/3.png)

第四步：接着选择我们对应的操作系统，点击Download下载mysql，如图：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/mysql_basics/4.png)

第五步：页面跳转后，我们不注册，点击如图，直接下载。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/mysql_basics/5.png)

第六步：双击打开点击我们下载好的安装包，如图，继续双击进行安装。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/mysql_basics/6.png)

第七步：打开安装器，无脑下一步就可以了，所有配置都默认就可以了。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/mysql_basics/7.png)

第八步：安装成功！

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/mysql_basics/8.png)

# 二、环境变量

第一步 ：在终端切换到根目录，编辑./.bash_profile文件

```shell
$ cd ~
$ vim ./.bash_profile
```

第二步 ：进入vim 编辑环境。 按下i 进入 insert 模式 ，输入

```shell
export PATH=$PATH:/usr/local/mysql/bin
export PATH=$PATH:/usr/local/mysql/support-files
```

第三步 ：按下esc 退出 insert 模式，输入:wq保存配置文件。

```shell
:wq
```

第四步 ：在终端界面下输入以下命令，让配置文件的修改生效，并查看环境变量是否设置成功

```shell
$ source ~/.bash_profile 
$ echo $PATH
```

## MySQL服务的启停和状态的查看

```shell
停止MySQL服务
sudo mysql.server stop

重启MySQL服务
sudo mysql.server restart

查看MySQL服务状态
sudo mysql.server status
```

# 三、启动

第一步 ：终端界面下输入

```shell
sudo mysql.server start
```

第二步 ：启动mysql服务,启动成功后继续输入

```shell
mysql -u root -p
```

第三步 ：直接回车进入数据库，看到下列欢迎页面

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/mysql_basics/9.png)

# 四、初始化设置

设置初始化密码，进入数据库mysql数据库之后执行下面的语句，设置当前root用户的密码为root。

```shell
set password = password('root');
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/mysql_basics/10.png)

## 4.1 退出sql界面

```shell
exit
```

# 五、配置

进入到 /usr/local/mysql/support-files 目录。里面有个文件:my-default.cnf

将其复制到桌面上，改名为my.cnf，将内容替换为。

```
[mysqld]
default-storage-engine=INNODB
character-set-server=utf8
port = 3306

[client]
default-character-set=utf8
```

将修改后的文件my.cnf复制到 /etc 目录下。

重启mysql

## 5.1 检测修改结果

```shell
$mysql>>>show variables like '%char%';
```

# 六、MySQL服务的启动、重启、停止

```shell
# 启动
sudo /usr/local/mysql/support-files/mysql.server start
# 重启
sudo /usr/local/mysql/support-files/mysql.server restart
# 停止
sudo /usr/local/mysql/support-files/mysql.server stop
```

或者是：打开“系统偏好设置”，单击下端的“MySQL”图标，在“MySQL”对话框中，单击“start MySQL server”按钮。

# 七、常见问题

安装mysql后，登录报错：

```shell
mac ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: NO)
```

解决方法：

```shell
# 第一步：如果mysql服务正在进行，将之停止。
# 第二步：在终端中以管理员权限启动mysqld_safe，命令如下：
sudo /usr/local/mysql/bin/mysqld_safe --skip-grant-tables
```

执行结果如下：

```shell
2019-03-31T03:06:54.6NZ mysqld_safe Logging to '/usr/local/mysql/data/macdeMacBook-Pro.local.err'.
2019-03-31T03:06:54.6NZ mysqld_safe Starting mysqld daemon with databases from /usr/local/mysql/data
```

第三步：不要关闭当前的终端窗口，新建一个终端窗口，输入如下命令，回车登录mysql

```shell
/usr/local/mysql/bin/mysql
```

登陆成功后的欢迎信息：

```shell
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.15 MySQL Community Server - GPL
Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.
Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql> 
```

使用mysql的数据库：

```shell
use mysql；
```

更新root的密码：

```shell
update user set authentication_string='root' where Host='localhost' and User='root';
```

注意：
1、有的版本的mysql中，密码可能存储在password字段中，可以使用"describe user;"命令来查看下表结构再操作
2、authentication_string的值一定通过password函数来计算(password(‘root’)) # 加上会报语法错误
执行结果如下：

```shell
Query OK, 1 row affected, 1 warning (0.01 sec)
Rows matched: 1  Changed: 1  Warnings: 1
```

退出mysql：

```shell
exit
```

最后一步：关闭mysqld_safe进程，重启mysql服务。

## 登录mysql

```shell
/usr/local/mysql/bin/mysql -uroot -proot
```

这个时候，如果执行查询之类的操作，比如执行"show databases;"，可能会有如下提示：

```shell
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.
```

根据提示进行操作，输入如下SQL语句，这个语句的作用是修改root用户的口令为root：

```shell
alter user 'root'@'localhost' identified by 'root';
```

结果：

```shell
Query OK, 0 rows affected
```

结束。