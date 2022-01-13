---
title: django
excerpt: 一款基于python的后端框架
date: 2022-01-01 22:42:58
tags:
	-	server
categories:
	-	笔记

---





# 简介





# 操作



- 安装

```sh
$ pip install django
```

- 创建工程

```sh
$ django-admin startproject mysite
```

这行代码将会在当前目录下创建一个 `mysite` 目录，大致结构如下：

```sh
mysite/
    manage.py
    mysite/
        __init__.py
        settings.py
        urls.py
        wsgi.py
```

这些目录和文件的用处是：

> 最外层的:file: mysite/ 根目录只是你项目的容器， Django 不关心它的名字，你可以将它重命名为任何你喜欢的名字。
`manage.py`: 一个让你用各种方式管理 Django 项目的命令行工具。你可以阅读 [django-admin and manage.py](https://docs.djangoproject.com/zh-hans/2.1/ref/django-admin/) 获取所有 `manage.py` 的细节。
里面一层的 `mysite/` 目录包含你的项目，它是一个纯 Python 包。它的名字就是当你引用它内部任何东西时需要用到的 Python 包名。 (比如 `mysite.urls`).
`mysite/settings.py`：Django 项目的配置文件。如果你想知道这个文件是如何工作的，请查看 [Django settings](https://docs.djangoproject.com/zh-hans/2.1/topics/settings/) 了解细节。
`mysite/urls.py`：Django 项目的 URL 声明，就像你网站的“目录”。阅读 [URL调度器](https://docs.djangoproject.com/zh-hans/2.1/topics/http/urls/) 文档来获取更多关于 URL 的内容。
`mysite/wsgi.py`：作为你的项目的运行在 WSGI 兼容的Web服务器上的入口。阅读 [如何使用 WSGI 进行部署](https://docs.djangoproject.com/zh-hans/2.1/howto/deployment/wsgi/) 了解更多细节。

- 启动服务

```sh
$ python manage.py runserver
```

> 更换端口
> 默认情况下，[`runserver`](https://docs.djangoproject.com/zh-hans/2.1/ref/django-admin/#django-admin-runserver) 命令会将服务器设置为监听本机内部 IP 的 8000 端口。
> 如果你想更换服务器的监听端口，请使用命令行参数。举个例子，下面的命令会使服务器监听 8080 端口：
>
> ```sh
> $ python manage.py runserver 8080
> ```
> 如果你想要修改服务器监听的IP，在端口之前输入新的。比如，为了监听所有服务器的公开IP（这你运行 Vagrant 或想要向网络上的其它电脑展示你的成果时很有用），使用：
> ```sh
> $ python manage.py runserver 0:8000
> ```



- 创建应用

```sh
$ python manage.py startapp appname
```

# path函数

```python
from django.urls import path
```



Django path() 可以接收四个参数，分别是两个必选参数：route、view 和两个可选参数：kwargs、name。

语法格式：

```python
path(route, view, kwargs=None, name=None)
```

- route: 字符串，表示 URL 规则，与之匹配的 URL 会执行对应的第二个参数 view。
- view: 用于执行与正则表达式匹配的 URL 请求。
- kwargs: 视图使用的字典类型的参数。
- name: 用来反向获取 URL。

此外，使用**re_path()**可以实现某些正则表达式的功能。

```python
from django.urls import include, re_path
```



---



# 数据库操作

生成迁移文件：

```sh
$ python manage.py makemigrations
```

迁移到数据库:

```sh
$ python manage.py migrate
```



---



# 跨域问题

ip、端口、协议其中之一不同即存在跨域问题。本地可以访问，前端axios不可访问，报错：

```
Access to XMLHttpRequest at 'http://127.0.0.1:8000/app0/get' from origin 'http://localhost:3000' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.
```

跨域问题可从前端解决也可从后端解决，前端使用代理来处理，后端使用corsheaders中间件。

安装`django-cors-headers`，settings.py允许跨域设置：

```python
# 设置允许主机，'*'代表所有主机
ALLOWED_HOSTS = ['*', ]


# 跨域请求--------------------------------------允许
CORS_ALLOW_CREDENTIALS = True
CORS_ORIGIN_ALLOW_ALL = True

MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    # 跨域中间件
    'corsheaders.middleware.CorsMiddleware',
    # 'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```

让外部机器可以访问：

```sh
$ python manage.py runserver 0.0.0.0:8000
```

