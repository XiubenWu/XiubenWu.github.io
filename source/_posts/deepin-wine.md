---
title: deepin-wine
excerpt: 国产软件的容器，供linux上安装运用。
date: 2021-09-22 13:09:36
tags:
  -	linux
categories:
  -	笔记
---



# 简介

Deepin-wine([站点1](https://github.com/wszqkzqk/deepin-wine-ubuntu)，[站点2](https://deepin-wine.i-m.dev/)),为linux提供多种国产软件。



***



# 安装

克隆仓库：

```
git clone https://github.com/wszqkzqk/deepin-wine-ubuntu.git # 国外

git clone https://gitee.com/wszqkzqk/deepin-wine-for-ubuntu.git # 国内
```

运行./install.sh`进行安装。



- 访问镜像仓库寻找软件`https://mirrors.aliyun.com/deepin/pool/non-free/d/`，下载deb包安装。(推荐)
- 添加仓库`wget -O- https://deepin-wine.i-m.dev/setup.sh | sh`，采用`sudo apt-get install com.qq.weixin.deepin`安装



***



# 卸载

卸载与清理按照层次从浅到深可以分为如下四个层级。

如果只是想清除APP账户配置啥的那么请按照`1`清理；如果你发现程序奔溃之类的，请按照`1-2`清理；如果需要卸载APP，按照`1-2-3`清理；如果你想把一切回到最初的起点，执行`1-2-3-4`清理。

1. 清理应用运行时目录

   例如QQ/TIM会把帐号配置、聊天文件等保存`~/Documents/Tencent Files`目录下，而微信是`~/Documents/WeChat Files`，删除这些文件夹以移除帐号配置等数据。

2. 清理wine容器

   deepin-wine应用第一次启动后会在`~/.deepinwine/`目录下生成一个文件夹（名字各不相同）用于存储wine容器（可以理解我一个“Windows虚拟机”），如果使用出了问题，可以试试删除这个目录下对应的子文件夹。

3. 卸载软件包

   执行`sudo apt-get purge --autoremove <包名>`命令把你安装过的包给移除。

4. 移除软件仓库

   ```
   sudo rm /etc/apt/preferences.d/deepin-wine.i-m.dev.pref \
           /etc/apt/sources.list.d/deepin-wine.i-m.dev.list \
           /etc/profile.d/deepin-wine.i-m.dev.sh
   sudo apt-get update
   ```

