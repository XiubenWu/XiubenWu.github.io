---
title: 内网访问VPN
excerpt: 自建VPN
photos:
	- https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20220124npsvpn0.png
date: 2022-01-24 21:57:12
tags:
	-	随笔
	-	server
categories:
	-	[随笔]
---



--

当我们在不在内网网段，而想访问内网的一些资源，或借用内网的ip去访问一些资源时(如使用校园网去下载知网文献)，可以使用云服务器对内网进行穿透并搭建socks隧道，然后进行流量转发。可以使用NPS来实现此功能。有关NPS的基本配置可查看以前的博客，[this](https://www.xiubenwu.top/2021/06/27/NPS%E6%90%AD%E5%BB%BA/).



---



# 代理服务器配置

在内网的一个主机中安装NPC，相应的客户端设置用户名和认证密码，连接代理时用到。

![](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20220124npsvpn1.png)

在此客户端下添加一个socks代理隧道，设置服务端口。此时一个简单的代理服务器已经搭建完毕了。

![](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20220124npsvpn2.png)



---



# 用户客户端配置

客户端可以使用的代理软件很多，此处使用**proxifier**进行代理。首先配置代理服务器：

![](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20220124npsvpn3.png)

添加一个代理服务器，输入服务器ip及端口，选择socks5协议，填入用户名和密码，最后检查一下是否连上了

![](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20220124npsvpn4.png)

![](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20220124npsvpn5.png)

最后设置一下代理规则，选择上你要代理的服务器便大功告成了，proxifier启动后再运行程序便可成功代理。

![image-20220124223415131](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20220124npsvpn6.png)



