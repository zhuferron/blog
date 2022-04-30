---
title: AWS ec2服务器创建多用户登录(使用密码登录)
tags: 云计算
categories: 云计算
description: 介绍AWS的ec2云服务器如何使用密码进行多用户登录
---

## 1、使用ubuntu登录

- 好像ec2服务器不支持使用root直接登录，但是却支持使用root用户组的用户名登录，不同服务器的登录名不同，具体对应名称参考https://blog.csdn.net/Linrena/article/details/106171588。本篇博客使用的是Ubuntu，用户名为ubuntu

- 使用xshell创建新连接

  ![](http://fl.ljuuu.com/img/20210405124402.png)

- 输入用户名

  ![](http://fl.ljuuu.com/img/20210405124809.png)

- 使用密钥文件登录

  ![](http://fl.ljuuu.com/img/20210405124844.png)

- 登录成功

  ![](http://fl.ljuuu.com/img/20210405124648.png)

## 2、创建新用户

- 登录之后，当前用户为`ubuntu`，使用命令`sudo su `切换为`root`用户

- 使用`adduser user`命令创建一个名字为`user`新用户

  ![](http://fl.ljuuu.com/img/20210405125309.png)

  极少数情况创建用户时不会让你输入密码，这时候需要使用命令`passwd user`为该用户创建密码

## 3、修改配置文件

因为ec2服务器默认是不允许使用密码登录的，因此需要修改ssh的配置文件让其允许使用密码登录

- 使用`root`账户修改`sshd_config`文件，使用命令`vim /etc/ssh/sshd_config`。

- 切换成编辑模式将第56行的注释去掉，打开允许密码登录功能。

  ![](http://fl.ljuuu.com/img/20210405125701.png)

- 切换成命令模式，输入`wq`保存退出。

- 使用命令`sudo service ssh restart`，重启ssh服务令配置文件生效。

- 重新用新创建的用户登录

## 4、新用户登录

- ![](http://fl.ljuuu.com/img/20210405130316.png)
- ![](http://fl.ljuuu.com/img/20210405130337.png)
- ![](http://fl.ljuuu.com/img/20210405130422.png)

登录成功！

## 5、允许root用户登录

- 重新使用用户`ubuntu`登录，**切换成root用户进行以下操作**

- 新的ec2服务器的root账户默认是没有密码的，需要使用命令`sudo passwd root`给`root`用户设置密码

  ![](http://fl.ljuuu.com/img/20210405130634.png)

- 使用命令`vim /etc/ssh/sshd_config`修改`sshd_config`文件的第32行的`PermitRootLogin`，并保存退出。

  ![](http://fl.ljuuu.com/img/20210405131026.png)

- 使用命令`sudo service ssh restart`，重启ssh服务令配置文件生效。

- 重新登录

  ![](http://fl.ljuuu.com/img/20210405131321.png)

![](http://fl.ljuuu.com/img/20210405131213.png)

![](http://fl.ljuuu.com/img/20210405131239.png)

登录成功，完结撒花！