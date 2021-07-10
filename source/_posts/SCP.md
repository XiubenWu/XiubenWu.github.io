---
title: SCP
excerpt: 使用scp往远程服务器传输文件。
date: 2021-07-10 10:27:56
tags:
    - [linux]
    - [server]

categories:
	- 笔记
    - server
---

# 简介

**scp**是 **secure copy**的缩写, scp是[linux系统](https://www.linuxprobe.com/)下基于ssh登陆进行安全的远程文件拷贝命令。linux的scp命令可以在linux服务器之间复制文件和目录。

```
-1  强制scp命令使用协议ssh1

-2  强制scp命令使用协议ssh2

-4  强制scp命令只使用IPv4寻址

-6  强制scp命令只使用IPv6寻址

-B  使用批处理模式（传输过程中不询问传输口令或短语）

-C  允许压缩。（将-C标志传递给ssh，从而打开压缩功能）

-p 保留原文件的修改时间，访问时间和访问权限。

-q  不显示传输进度条。

-r  递归复制整个目录。

-v 详细方式显示输出。scp和ssh(1)会显示出整个过程的调试信息。这些信息用于调试连接，验证和配置问题。

-c cipher  以cipher将数据传输进行加密，这个选项将直接传递给ssh。

-F ssh_config  指定一个替代的ssh配置文件，此参数直接传递给ssh。

-i identity_file  从指定文件中读取传输时使用的密钥文件，此参数直接传递给ssh。

-l limit  限定用户所能使用的带宽，以Kbit/s为单位。

-o ssh_option  如果习惯于使用ssh_config(5)中的参数传递方式，

-P port  注意是大写的P, port是指定数据传输用到的端口号

-S program  指定加密传输时所使用的程序。此程序必须能够理解ssh(1)的选项。
```



***



# 使用示例

## 上传文件到服务器

```
scp /path/local_filename username@servername:/path

scp /host/test.txt root@192.168.0.101:/home/test/
```

示例表示将本机host文件夹下的test.txt文件上传到服务器的/home/test文件夹，并使用root用户登录。



## 从服务器下载文件

```
scp username@server_address:/path/filename /host/local

scp root@192.168.0.101:/home/kimi/test.txt /myfolder
```

示例表示将服务器上的文件下载到本地的myfolder文件夹(若根目录使用`/`表示下载到当前盘符的根目录)。



## 从服务器下载整个目录

```
scp -r username@server_address:/remote_dir /local_dir

scp -r root@192.168.0.101:/home /local_dir
```

示例表示将服务器下的文件整个下载到本地目录(-r参数)。



## 上传目录到服务器

```
scp -r /tmp/local_dir username@servername:remote_dir

scp -r test root@192.168.0.101:/home
```

将test文件夹上传到服务器home目录。



## 在两个远程主机间复制文件

```
scp root@192.168.1.104:/host1/xx.txt root@192.168.1.105:/host2
```

```
scp source target
```

多文件传输时使用空格隔开。



