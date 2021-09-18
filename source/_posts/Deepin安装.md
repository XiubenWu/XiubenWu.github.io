---
title: Deepin安装
excerpt: 一款国产的linux系统
date: 2021-09-18 18:42:04
tags:
  -	linux
categories:
  -	随笔
---



# 软件下载

[Deepin](https://www.deepin.org/zh/download/)官网下载ios镜像文件。

![image-20210918205728743](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210918Deepin1.png)

# 启动U盘制作

下载的镜像文件中有制作启动盘的程序，直接制作即可。

![image-20210918210018693](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210918Deepin2.png)



# 设置分区

## 分区类型

需要在磁盘上新建一个分区来安装Deepin，建议使用UEFI+GPT格式安装，MBR的磁盘格式需进行额外设置。

## 分区大小

至少三个分区，建议**保留空白卷到UEFI安装时手动设置**，也可预先分配好。

- efi分区大小至少为300Mb
- /根目录挂载自定义大小
- swap交换分区当内存小时给内存两倍左右，内存大时给等同于内存的大小



# root权限

![a~1](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210918Deepin3.png)

新装系统没有root用户,需要先设置root:

```
sudo passwd root
>>>输入当前用户密码
>>>输入root用户密码
>>>在此输入root用户密码
```

