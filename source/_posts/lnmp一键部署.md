---
title: lnmp一键部署
excerpt: ---
photos:
  - https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210817lnmp0.png
date: 2021-08-17 12:30:57
tags:
  -	linux
categories:
  -	笔记
  -	server
---

<!-- more -->

# 简介

**LNMP**一键安装包是一个用Linux Shell编写的可以为CentOS/RHEL/Fedora/Aliyun/Amazon、Debian/Ubuntu/Raspbian/Deepin/Mint Linux VPS或独立主机安装**LNMP(Nginx/MySQL/PHP)、LNMPA(Nginx/MySQL/PHP/Apache)、LAMP(Apache/MySQL/PHP)**生产环境的Shell程序。

[相关网站](https://lnmp.org/)

# 安装

使用`screen -S lnmp`建立新的窗口，若`screen`命令不存在则安装:

```
yum|apt-egt install screen
```

下载并安装LNMP一键安装包：

```
wget http://soft.vpser.net/lnmp/lnmp1.8.tar.gz -cO lnmp1.8.tar.gz && tar zxf lnmp1.8.tar.gz && cd lnmp1.8 && ./install.sh lnmp
```

如需要安装**LNMPA**或LAMP，将./install.sh 后面的参数lnmp替换为lnmpa或lamp即可。同时也支持单独安装Nginx或数据库，命令为 ./install.sh nginx 或 ./install.sh db。如需更改网站和数据库目录、自定义Nginx参数、PHP参数模块、开启lua等需在运行./install.sh 命令前修改安装包目录下的 lnmp.conf 文件

安装开始时需要选择各类版本，注意查看自己系统所支持的版本。



***



# 端口冲突

安装完成后会显示运行状态。如果系统中有其它软件占用端口将导致相应程序启动失败。如NPS默认占用了80和443端口，这将导致nginx不能正常启动，除非关闭NPS.因此可以调整NPS的默认端口：

找到NPS的配置文件：

```
[root@xxxxxxxxxxxxx ~]# find / -name nps.conf
/etc/nps/conf/nps.conf
/home/Programes/nps/conf/nps.conf
```

打开`/etc/nps/conf/nps.conf`找到其中的端口配置，将80和443修改为81、444(或其他)：

```
#HTTP(S) proxy port, no startup if empty
http_proxy_ip=0.0.0.0
http_proxy_port=81  # 80
https_proxy_port=444 # 443
https_just_proxy=true
```

重启nps完成修改：

```
nps restart
```

这样访问ip地址调用80端口就可以直接出现lnmp的界面了。

![image-20210817130530024](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210817lnmp1.png)



