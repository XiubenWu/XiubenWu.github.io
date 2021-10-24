---
title: linux安装QQ依赖报错解决
excerpt: POP_OS使用deepwine安装QQ遇到的问题
date: 2021-10-24 13:31:30
tags:
  -	linux
categories:
  -	爬坑
---



# 前言

在使用deepwine安装windows下的QQ软件的时候，报如下错误：

```
sudo apt install com.qq.im.deepin
正在读取软件包列表... 完成
正在分析软件包的依赖关系树... 完成
正在读取状态信息... 完成                 
有一些软件包无法被安装。如果您用的是 unstable 发行版，这也许是
因为系统无法达到您要求的状态造成的。该版本中可能会有一些您需要的软件
包尚未被创建或是它们已被从新到(Incoming)目录移出。
下列信息可能会对解决问题有所帮助：

下列软件包有未满足的依赖关系：
 libgirepository-1.0-1 : 破坏: python-gi (< 3.34.0-4~) 但是 3.30.4-1 正要被安装
E: 无法修正错误，因为您要求某些软件包保持现状，就是它们破坏了软件包间的依赖关系
```

![image-20211024133350448](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20211024qq1.png)



# 解决办法

`libgirepository-1.0-1`依赖于`libffi7`先安装`libffi7`再安装`python-gi`，解决所有依赖后再次安装qq:`sudo apt install com.qq.im.deepin`.

## 安装libffi7

软件包地址:[https://packages.ubuntu.com/zh-cn/focal/libffi7](https://packages.ubuntu.com/zh-cn/focal/libffi7)，可以在新立得软件包(synaptic)直接搜索安装：

![image-20211024133908507](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20211024qq2.png)

## 安装python-gi

到软件包地址[https://packages.ubuntu.com/zh-cn/focal/python-gi](https://packages.ubuntu.com/zh-cn/focal/python-gi)下载软件包，然后以`dpkg`命令安装，此时可能会缺少相关依赖，按照`apt install -f`处理依赖关系继续安装，完成。

## 完成安装

```
sudo apt install com.qq.im.deepin
正在读取软件包列表... 完成
正在分析软件包的依赖关系树... 完成
正在读取状态信息... 完成                 
将会同时安装下列软件：
  deepin-wine-plugin-virtual libffi6 python-dbus python-gobject
  python-gobject-2 python-is-python2
建议安装：
  python-dbus-dbg python-dbus-doc python-gobject-2-dbg
下列【新】软件包将被安装：
  com.qq.im.deepin:i386 deepin-wine-plugin-virtual libffi6 python-dbus
  python-gobject python-gobject-2 python-is-python2
升级了 0 个软件包，新安装了 7 个软件包，要卸载 0 个软件包，有 5 个软件包未被升级。
需要下载 141 MB 的归档。
解压缩后会消耗 161 MB 的额外空间。
您希望继续执行吗？ [Y/n] y
获取:1 http://us.archive.ubuntu.com/ubuntu hirsute/universe amd64 python-is-python2 all 2.7.18-9 [2,976 B]
获取:2 https://deepin-wine.i-m.dev  python-dbus 1.2.8-3 [103 kB]               
获取:3 https://deepin-wine.i-m.dev  libffi6 3.2.1-9 [20.8 kB]
获取:4 https://deepin-wine.i-m.dev  python-gobject-2 2.28.6.1-1+rebuild [281 kB]
获取:5 https://deepin-wine.i-m.dev  python-gobject 3.30.4-1 [20.0 kB]
获取:6 https://deepin-wine.i-m.dev  deepin-wine-plugin-virtual 5.1.13-1 [2,144 B]
获取:7 https://deepin-wine.i-m.dev  com.qq.im.deepin 9.3.2deepin20 [141 MB]
已下载 141 MB，耗时 29秒 (4,814 kB/s)                                          
正在选中未选择的软件包 python-is-python2。
(正在读取数据库 ... 系统当前共安装有 223915 个文件和目录。)
准备解压 .../0-python-is-python2_2.7.18-9_all.deb  ...
正在解压 python-is-python2 (2.7.18-9) ...
正在选中未选择的软件包 python-dbus。
准备解压 .../1-python-dbus_1.2.8-3_amd64.deb  ...
正在解压 python-dbus (1.2.8-3) ...
正在选中未选择的软件包 libffi6:amd64。
准备解压 .../2-libffi6_3.2.1-9_amd64.deb  ...
正在解压 libffi6:amd64 (3.2.1-9) ...
正在选中未选择的软件包 python-gobject-2。
准备解压 .../3-python-gobject-2_2.28.6.1-1+rebuild_amd64.deb  ...
正在解压 python-gobject-2 (2.28.6.1-1+rebuild) ...
正在选中未选择的软件包 python-gobject。
准备解压 .../4-python-gobject_3.30.4-1_all.deb  ...
正在解压 python-gobject (3.30.4-1) ...
正在选中未选择的软件包 deepin-wine-plugin-virtual。
准备解压 .../5-deepin-wine-plugin-virtual_5.1.13-1_all.deb  ...
正在解压 deepin-wine-plugin-virtual (5.1.13-1) ...
正在选中未选择的软件包 com.qq.im.deepin:i386。
准备解压 .../6-com.qq.im.deepin_9.3.2deepin20_i386.deb  ...
正在解压 com.qq.im.deepin:i386 (9.3.2deepin20) ...
正在设置 libffi6:amd
```

注销刷新图标。





