---
title: Elementary OS
excerpt: 一款基于Ubuntu的Mac风格Linux系统
date: 2021-09-20 14:05:55
tags:
  -	linux
categories:
  -	随笔
---



# 下载

前往[Elementary官网](https://elementary.io/)下载ISO镜像，将价格改为0之后可以直接下载：

![image-20210920140851567](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210920ElementaryOS.png)

启动U盘制作器官方推荐[Etcher](https://www.balena.io/etcher/)，可以自行选择[rufus](https://rufus.ie/zh/)等。

使用Etcher制作U盘镜像：

![image-20210920141845398](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210920Elementary2.png)

# root

安装完成后设置root用户

```
sudo passwd root
>>>输入当前用户密码
>>>输入root用户密码
>>>在此输入root用户密码
```

# 设置

安装Pantheon Tweaks.这是 elementary OS 的必备应用。它提供了一些无法通过系统原生设置程序修改的额外的设置和配置选项，请打开终端并运行以下命令以安装 Pantheon Tweaks。注意：先前版本的 Tweak 工具叫做 elementary Tweaks，从 Odin 版本开始更名为 Pantheon Tweaks。

```
sudo apt update
sudo apt upgrade

sudo apt install software-properties-common
sudo add-apt-repository -y ppa:philip.scott/pantheon-tweaks
sudo apt install -y pantheon-tweaks
```

换阿里的源,编辑/etc/apt/source.list文件，内容换成以下文本：

```
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse 
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main multiverse restricted universe
```

加入一些其他的源：

```
sudo apt install software-properties-common
sudo add-apt-repository ppa:kdenlive/kdenlive-stable
sudo add-apt-repository ppa:philip.scott/elementary-tweaks
cd /etc/xdg/autostart/
sudo mv io.elementary.appcenter-daemon.desktop io.elementary.appcenter-daemon.desktop.bak
sudo apt update
sudo apt install git
```

