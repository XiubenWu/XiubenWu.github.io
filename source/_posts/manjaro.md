---
title: Manjaro
excerpt: -----
photos:
  -	https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20211223manjaro0.png
date: 2021-12-23 15:12:09
tags:
  -	笔记
  -	linux
categories:
  -	笔记
---



# 简介

Manjaro Linux是快速的、用户友好的、面向桌面的、基于Arch Linux的操作系统。它的一些显著特性包括：一份直观的安装程序、自动硬件检测、稳定的滚动式发布模式、对安装多个内核的支持、用于管理图形卡的特别Bash脚本、高度的桌面可配置性。Manjaro Linux提供Xfce桌面作为核心选项，并为高级用户提供一份最小主义的Net版本。用户还可以获得社区支持的GNOME 3/Cinnamon及KDE版本。Manjaro的社区论坛可提供帮助并充满活力，用户受益其中。

- 桌面环境：GNOME, KDE Plasma, Xfce
- 软件包管理：Flatpak, Pacman, snap
- 发布模式：Rolling



---

# Init

Manjaro 装好后，需要运行的第一条命令：

```shell
sudo pacman -Syy ## 强制更新 package 目录
sudo pacman-mirrors --interactive --country China  # 列出所有国内的镜像源，并提供交互式的界面手动选择镜像源
sudo pacman -Syyu  # 强制更新 package 目录，并尝试更新已安装的所有 packages.
sudo pacman -S yay  # 安装 yay
```

```sh
sudo pacman -S base-devel 
# 安装 fakeroot、binutils 等打包基本工具否则安装软件时报错：
# Cannot find the fakeroot binary. ==> 错误： Cannot find the strip binary required for object fil...
# 错误： Cannot find the fakeroot binary. ==> 错误： Cannot find the strip binary required for object file stripping. ==> 错误：Makepkg 无法构建 deepin-wine-wechat.
```

pacman 是 arch/manjaro 的官方包管理器，而刚刚安装的 yay，则是一个能查询 arch linux 的 aur 仓库的第三方包管理器，非常流行。

pacman 的常用命令语法：

```sh
pacman -S package_name        # 安装软件  
pacman -S extra/package_name  # 安装不同仓库中的版本
pacman -Syu                   # 升级整个系统，y是更新数据库，yy是强制更新，u是升级软件
pacman -Ss string             # 在包数据库中查询软件
pacman -Si package_name       # 显示软件的详细信息
pacman -Sc                    # 清除软件缓存，即/var/cache/pacman/pkg目录下的文件
pacman -R package_name        # 删除单个软件
pacman -Rs package_name       # 删除指定软件及其没有被其他已安装软件使用的依赖关系
pacman -Qs string             # 查询已安装的软件包
pacman -Qi package_name       # 查询本地安装包的详细信息
pacman -Ql package_name       # 获取已安装软件所包含的文件的列表
pacman -U package.tar.zx      # 从本地文件安装
pactree package_name          # 显示软件的依赖树
```

yay 的用法和 pacman 完全类似，上述所有 `pacman xxx` 命令，均可替换成 `yay xxx` 执行。

此外，还有一条 `yay` 命令值得记一下：

```sh
yay -c  # 卸载所有无用的依赖。类比 apt-get autoremove
```

## 安装deb包

pacman系的不能直接安装debian系的软件包，需要进行一定转化，安装<kbd>debtap</kbd>:

```sh
yay -S debtap
# or
yaourt -S debtap
# or 
pacaur -S debtap
# 三种方式选一,但必须先安装三种工具:
 sudo pacman -S XXX  # XXX 可为 yaourt、yay、pacaur 
```
更新 debtap

```sh
sudo debtap -u
```

 **deb** 包转为 **tar.xz** 包，在此过程中，会要求输入包装名（packager name）和许可证（License）（可输入比如 GPL 或者不输入）。对生成的 tar.xz 包，使用 pacman 进行安装。

```sh
sudo debtap XXX.deb
```

```sh
sudo pacman -U XXX.tar.xz
```



---

## 常用软件与配置

### 1. 添加 archlinux 中文社区仓库

[Arch Linux 中文社区仓库](https://www.archlinuxcn.org/archlinux-cn-repo-and-mirror/) 是由 Arch Linux 中文社区驱动的非官方用户仓库，包含一些额外的软件包以及已有软件的 git 版本等变种。部分软件包的打包脚本来源于 AUR。

一些国内软件，如果直接从 aur 安装，那就会有一个编译过程，有点慢。而 archlinuxcn 有已经编译好的包，可以直接安装。更新速度也很快，推荐使用。

配置方法见 [Arch Linux Chinese Community Repository](https://github.com/archlinuxcn/repo)。

### 2. 安装常用软件

```sh
sudo pacman -S google-chrome  firefox         # 浏览器
sudo pacman -S netease-cloud-music     # 网易云音乐
sudo pacman -S noto-fonts-cjk wqy-bitmapfont wqy-microhei wqy-zenhei   # 中文字体：思源系列、文泉系列
sudo pacman -S wps-office ttf-wps-fonts

sudo pacman -S vim                     # 命令行编辑器
sudo pacman -S git                     # 版本管理工具
sudo pacman -S clang make cmake gdb    # 编译调试环境
sudo pacman -S visual-studio-code-bin  # 代码编辑器

sudo pacman -S wireshark-qt  mitmproxy         # 抓包工具
sudo pacman -S docker  # docker 容器
```

## 输入法安装

在<kbd>Manjaro Hello</kbd>中的<kbd>Applications</kbd>中安装fcitx，可以避免手动配置.xprofile

![Manjaro Applications](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20211223majaro1.png)

安装搜狗输入法

```sh
yay -S fcitx-sogoupinyin
# or
sudo pacman -S fcitx-sogoupinyin
```

搜狗经常报错，安装google输入法亦可：

```sh
yay -S fcitx-googlepinyin
```

