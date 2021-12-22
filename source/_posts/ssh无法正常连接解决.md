---
title: ssh无法正常连接解决
date: 2021-12-22 16:25:33
tags:
  -	随笔
categories:
  -	随笔
---





在一般情况下在安装SSH后即可进行远程连接。当然会出现例外情况。

<!-- more -->

```
$ sudo apt-get install ssh
```

SSH分客户端openssh-client和openssh-server

如果你只是想登陆别的机器的SSH只需要安装openssh-client，深度操作系统有默认安装，如果没有终端执行：

```
$ sudo apt-get install openssh-client
```

如果要使本机开放SSH服务就需要安装openssh-server，终端执行：

```
$ sudo apt-get install openssh-server
```

若安装完成后未能正常连接，则查看是否启动了ssh:

```
$ ps -e |grep ssh
```

如果看到sshd那说明ssh-server已经启动了。

如果没有则可以这样启动：

```
sudo /etc/init.d/ssh start 
```

或者

```
service ssh start
```

ssh-server配置文件位于/etc/ssh/sshd_config，在这里可以定义SSH的服务端口，默认端口是22，你可以自己定义成其他端口号，如222。

然后重启SSH服务：

```
sudo /etc/init.d/ssh stop
sudo /etc/init.d/ssh start
```

