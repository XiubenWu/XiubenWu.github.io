---
title: OSError:[WinError 1455]
excerpt: ImportError:DLL load failed:页面文件太小，无法完成操作
date: 2021-08-28 15:06:45
tags:
   -	Python
categories:
   -	爬坑

---



***

使用pytorch并调用GPU进行训练时，出现错误`ImportError: DLL load failed: 页面文件太小，无法完成操作`，而使用CPU进行训练却没有问题。网上给出的解决办法有如下：

- 重启pycharm(鸡肋的办法)
- 把num_works设置为0，调用了多进程读取数据造成的内存不足，可以考虑🤔
- 修改虚拟内存大小(终极办法)



亲测第三个终极办法比较有效：

将自动管理所有驱动器分页文件大小取消勾选：

![image-20210828151406390](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210828error1.png)



找到python的安装目录(虚拟环境目录)所在的盘符，自定义大小>设置初始值和最大值>点击设置确认，看情况重启一下电脑生效，大功告成。调小batchsize也可减小空间的占用。

![image-20210828151545223](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210828error2.png)

***



# 注意

划分虚拟内存将占用相应的磁盘空间，酌情划分。
