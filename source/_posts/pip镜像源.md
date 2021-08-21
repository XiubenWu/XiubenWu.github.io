---
title: pip镜像源
excerpt: PIP------
date: 2021-08-21 16:10:20
categories:
  -	笔记
  -	杂谈
tags:
  -	Python
---



# 简介

记录pip安装是镜像源配置文件位置，配置方式等。

***

# 配置方式 

## Windows

通常用-i参数可以暂时指定安装源，全局配置为：

```
pip config set global.index-url http://mirrors.aliyun.com/pypi/simple/
>>>Writing to C:\Users\name\AppData\Roaming\pip\pip.ini
```

查看当前的镜像源配置：

```
pip config list
```

在pip.ini中配置而指定多个镜像源：

```
[global]
index-url = http://mirrors.aliyun.com/pypi/simple/
extra-index-url=
	https://pypi.tuna.tsinghua.edu.cn/simple/
	https://pypi.mirrors.ustc.edu.cn/simple/
	http://pypi.mirrors.ustc.edu.cn/simple/
	https://pypi.douban.com/simple/
	https://pypi.python.org/simple
```

安装时给予镜像源信任(不是必要的):

```
[global]
index-url = http://mirrors.aliyun.com/pypi/simple/
extra-index-url=
	https://pypi.tuna.tsinghua.edu.cn/simple/
	https://pypi.mirrors.ustc.edu.cn/simple/
	http://pypi.mirrors.ustc.edu.cn/simple/
	https://pypi.douban.com/simple/
	https://pypi.python.org/simple
[install]
trusted-host=mirrors.aliyun.com
	pypi.tuna.tsinghua.edu.cn
	pypi.mirrors.ustc.edu.cn
	pypi.mirrors.ustc.edu.cn
	pypi.douban.com
```



## Linux

配置文件位于：

```
~/.pip/pip.conf

 vim ~/.pip/pip.conf 可对其修改
```

格式如下：

```
[global]
index-url=https://mirrors.aliyun.com/pypi/simple/
extra-index-url=
	https://pypi.tuna.tsinghua.edu.cn/simple/
	https://pypi.mirrors.ustc.edu.cn/simple/
	https://pypi.douban.com/simple/
	https://pypi.python.org/simple/
[install]
trusted-host=mirrors.aliyun.com
	pypi.tuna.tsinghua.edu.cn
	pypi.mirrors.ustc.edu.cn
	pypi.mirrors.ustc.edu.cn
	pypi.douban.com
```





