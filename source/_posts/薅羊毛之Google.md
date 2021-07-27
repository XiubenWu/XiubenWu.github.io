---
title: 薅羊毛之Google
math: true
excerpt: 白嫖google的GPU服务器用于训练模型
date: 2021-07-27 21:00:33
tags:
  -	随笔
categories:
  -	随笔 
---

# 简介

[Google Colab](https://colab.research.google.com/)是一个云端**Jupyter** 笔记本环境，它是完全**免费**的，唯一的限制条件是需要挂个梯子，毕竟是谷歌的东西。



***



# 使用方式

## 1.创建Colaboratory 

在谷歌云盘中新建中选择**更多——>Google Colaboratory**建立一个Jupyter文件。

- 创建文件

![image-20210727211250174](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210727Colab1.png)

- 文件概况

![image-20210727211535104](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/202107272Colab2.png)

## 2.基本使用

此文件中的基本命令使用与Jupyter相同，但是它还支持dos命令，只需要加上!前缀即可：

```
!pwd
!ls
!pip install pyqt5
```

![image-20210727212306863](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210727Colab3.png)

此运行环境本质上是一个linux虚拟机。新建的环境默认使用CPU我们需要将其更改为GPU，在**代码执行程序——>更改运行时类型**中将其改为GPU：

![image-20210727212827529](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210727Colab4.png)

`!nvidia-smi`查看一下Google所分配的GPU，是**Tesla T4**,16G的显存足以应付一般的模型。

但是仅仅在上面简单的运行一些基础命令是不够的。我们需要将我们本地编写的工程文件移植到云端来运行。这需要挂载drive云盘。

## 3.挂载Drive云盘

### 连接云盘

首先安装一些必要的系统库，并进行授权。

```
!apt-get install -y -qq software-properties-common python-software-properties module-init-tools
!add-apt-repository -y ppa:alessandro-strada/ppa 2>&1 > /dev/null
!apt-get update -qq 2>&1 > /dev/null
!apt-get -y install -qq google-drive-ocamlfuse fuse
from google.colab import auth
auth.authenticate_user()
from oauth2client.client import GoogleCredentials
creds = GoogleCredentials.get_application_default()
import getpass
!google-drive-ocamlfuse -headless -id={creds.client_id} -secret={creds.client_secret} < /dev/null 2>&1 | grep URL
vcode = getpass.getpass()
!echo {vcode} | google-drive-ocamlfuse -headless -id={creds.client_id} -secret={creds.client_secret}
```

运行之后出现如下结果：

![image-20210727214018841](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210727Colab5.png)

此时需要点击链接登录google账号复制密匙进行授权(上图为授权完毕后的状态)。每一个Notebook需要进行一次授权。

### 挂载云盘

使用如下命令创建并挂载云盘。运行如下命令后，Notebook运行环境中的drive文件与云盘根目录连接。

```
!mkdir -p drive
!google-drive-ocamlfuse drive
```

### 上传自己的文件

在云盘中建立一个文件夹(例如test)，将自己的工程上传进去

### 更改工作目录

使用os更改工作目录：

```python
import os
os.chdir("drive/test")
```

### 运行模型

直接使用dos运行以编写好的代码

```
!python3 test.py
```

# End

但是，Colab的GPU资源并不是无限制使用的，每天有一定的使用限制，而且Notebook有空闲超时自动断开的缺陷。
