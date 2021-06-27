---
title: NPS搭建
excerpt: 服务器代理，内网穿透
math: true
date: 2021-06-27 21:53:15
tags:
	- [随笔]
categories:
	- 随笔
---

# 简介

**NPS**是一款轻量级、高性能、功能强大的**内网穿透**代理服务器。目前支持**tcp、udp流量转发**，可支持任何**tcp、udp**上层协议（访问内网网站、本地支付接口调试、ssh访问、远程桌面，内网dns解析等等……），此外还**支持内网http代理、内网socks5代理**、**p2p等**，并带有功能强大的web管理端。



***

# 准备工作

* 带有公网IP的服务器(此处以阿里云为例)
* 本地服务器
* NPS服务端和客户端



***

# 步骤

前往[https://github.com/ehang-io/nps](https://github.com/ehang-io/nps)可查看NPS相关信息。在公网服务器和本地服务器**分别**下载安装服务端和客户端([根据所用系统自行选择](https://github.com/ehang-io/nps/releases))。

## 服务端

**在公网IP服务器下载server安装包并配置**

将nps的压缩包解压到自定义安装位置，其中的conf文件夹中nps.conf配置文件可以配置相关参数，主要修改用于配置nps的web端的用户名和密码。

```
#web
web_host=a.o.com
web_username=admin  <-可不修改
web_password=123    <-建议修改，提高安全性
web_port = 8080
web_ip=0.0.0.0
web_base_url=
web_open_ssl=false
web_cert_file=conf/server.pem
web_key_file=conf/server.key
# if web under proxy use sub path. like http://host/nps need this.
#web_base_url=/nps
```

使用管理员运行cmd命令行。路径变换到nps的**安装目录**，执行`nps install`，此时会在`C:\Program Files`文件夹下产生新的nps配置文件，之后的nps配置直接配置此处文件生效。使用`nps start`启用服务端，`nps stop`停止服务，`nps restart`重启服务。

服务端启用后进入`ip:web_port`(默认端口为8080)进行web端部署，输入用户名及密码(同配置文件)登录。

**nps** web端登录界面：

![image-20210627222122777](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210627npslogin.png)

**nps** web配置界面：

![image-20210627222254529](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210627npsWeb.png)

**新增一个客户端**：

![image-20210627222504249](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210627nps-3.png)

![image-20210627222749706](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210627nps-4.png)

留意客户端id及唯一验证密匙，之后会用到：

![image-20210627222956319](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210627nps-key.png)

## 客户端

**在本地服务器下载client安装包并配置**

**npc**的和**nps**的命令使用方式相似。npc直接在其安装目录下的conf文件夹中的npc.conf文件中进行配置，删除掉冗余信息，只保留常规配置中的信息：

```
[common]
server_addr=ip:8024    <-填入公网ip即客户端连接端口(默认8024)
conn_type=tcp
vkey=3raa67c9m18yflr4  <-填入唯一验证密匙
auto_reconnection=true
max_conn=1000
flow_limit=1000
rate_limit=1000
basic_username=11
basic_password=3
web_username=user
web_password=1234
crypt=true
compress=true
```

之后直接运行npc.exe即可连接至服务端。也可以命令行使用start命令运行。之后可以看到服务端的已连接上：

![image-20210627224109081](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210627nps-5.png)

## 添加隧道

此时公网ip服务器已和本地连接，可以添加端口映射：

![image-20210627224408860](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210627nps-6.png)

配置端口后，浏览器访问`公网ip:服务端端口`后可以映射至`内网地址:端口`



## 云服务器配置

有的云服务器没有开放响应端口，此时诸多功能限制，如客户端与服务端无法连接等。以阿里云为开放所有端口。

在云服务器ECS中创建新的安全组：

![image-20210627224917076](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210627nps-7.png)

可选择打开所需的端口，此处设置全部打开，设置完成后创建安全组。

![image-20210627225139193](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210627nps-8.png)

最后将刚才创建的安全组加入实例中：

![image-20210627225332450](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210627nps-9.png)

端口打开完成。
END

***
