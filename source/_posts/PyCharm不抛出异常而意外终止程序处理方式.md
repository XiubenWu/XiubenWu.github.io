---
title: PyCharm不抛出异常而意外终止程序处理方式
math: true
excerpt: 戳阅读全文&dArr;
date: 2021-03-20 14:29:58
categories:
    - [爬坑]
tags:
    - Pycharm
    - Python
---

# 问题
在编译$python$时，有时程序会异常终止而不抛出错误信息，而是返回一串没有太大意义的终止信号。对于较简单的程序还能肉眼观察debug，但是对于复杂的工程项目我们就难以定位错误位置。

# 解决方式
当这种情况出现是可以考虑在终端运行$python$文件，这样在命令行中会抛出具体的异常信息，方便我们定位错误。

## Pychram解决
当使用$Pychram$来调试程序时，可以通过：
```
Run-->Edit Configuration-->Excution-->Emulate terminal in out console
```
来启动终端模拟而快速的定位Bug.