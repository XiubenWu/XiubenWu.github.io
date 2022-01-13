---
title: vue init webpack...
date: 2022-01-07 19:12:25
tags:
	-	server
	-	Web
categories:
	-	笔记
---



vue-cli · Failed to download repo vuejs-templates/webpack: Hostname/IP does not match certificate's altnames: Host: github.com. is not in the cert's altnames: DNS:erp.bcl-bd.com

<!-- more -->

使用`vue init webpack project`初始化工程时报错，原因是从github下载各种依赖时由于网络状况等导致下载错误，解决办法为：

- 科学上网后执行该命令
- 时先下载webpack，以本地文件形式执行命令：

```sh
vue init ./webpack project
```

之后：

```sh
cd project 
## 
cnpm install
cnpm run dev
```



# vite

Vite 是一个 web 开发构建工具，由于其原生 ES 模块导入方式，可以实现闪电般的冷服务器启动。

通过在终端中运行以下命令，可以使用 Vite 快速构建 Vue 项目。

```sh
cnpm init @vitejs/app project-name
```

- 需要注意的是，此方式在windows下使用git bash时可能出现**bug，导致无法使用箭头进行选择**，推荐在cmd中使用此命令

后续:

```sh
cd
cnpm install
cnpm run dev
```



