---
title: snap
excerpt: -----
photos:
  -	https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210921snap0.png
date: 2021-09-21 11:07:00
tags:
  -	linux
categories:
  -	笔记
---



# 介绍

[snapcraft](https://snapcraft.io/)提供了很多linux应用，需要先安装snap（snapd），再使用snap管理这些软件，这些软件都制作时将所有运行环境包含在内，不像apt安装deb等需要解决依赖问题，下载的软件都相对于稳定，但也正是包装了所需的运行环境，造成整个软件臃肿，大小较大，占更多的磁盘空间，这是snap不足的。

## 安装snap

```
apt-get update
apt-get install snapd
```

## 基本命令

在snapcraft商店找到要安装的软件，使用`snap install `安装：

![image-20210921120039888](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210921snap1.png)

```
snap list # 列出已安装的snap应用
snap run # 运行已安装的应用
snap remove # 卸载已安装的运用
```



# 其他

snap软件一般安装在根目录下`/snap/`，其桌面启动图标放在`/var/lib/snapd/desktop/applications/`



# 启动图标问题

安装的软件有时候图标不显示在启动菜单中。找到启动菜单的文件位置，以deepin系统为例，在如下位置：

```
/usr/share/applications/
```

启动文件以`.desktop`为后缀，如果此文件夹中没有相应程序的`.desktop`文件，将相应程序的启动文件复制过来，或者创建一个链接（需要root权限）。比如snap安装的程序的`.desktop`文件位置位于:

```
/var/lib/snapd/desktop/applications/
```

如果文件已经在`/usr/share/applications/`中但是还不显示在启动菜单中，以文本方式打开此`.desktop`文件，将其中的`OnlyShowIn=**;**;**;`注释掉，比如安装tweak时(中文为"优化")，不显示在启动菜单，将

```
OnlyShowIn=GNOME;Unity;Pahtheon;
```

注释掉：

```
# OnlyShowIn=GNOME;Unity;Pahtheon;
```

这样就会在启动菜单显示了。

