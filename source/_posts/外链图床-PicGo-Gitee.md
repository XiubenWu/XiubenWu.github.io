---
title: 外链图床(PicGo+Gitee)
excerpt: 终究向图低了头
date: 2021-04-06 20:12:03
tags:
	- [图床]
categories:
	- 随笔
---

# 前言

刚开始写建立博客的时候，头疼过图片该如何处理：

* 直接放github的仓库里吧。可是随着图片越来越多，每次pull、clone都需要耗费大量的时间。而且github的仓库加载很慢，影响博客的流畅性。
* 外链图床吧。可是国内基本上没有什么好用的免费图床，七牛云之类的都需要收费的。国外的图床加载速度又是特别的慢。
* 自己搭建。



当初的自己为了偷懒，选择了不在博客中放图片——没错，就是写纯文本的内容。可是终究是禁不起时间这把杀猪刀的折磨。图片是一种极具表达力的东西，仅仅插入一张图片就可以代替使用大量文字描述而且还描述不清晰的地方。

在一篇文章中图形可使文章不限枯燥，可以使表达更具体。现在的我，已受够没有图片的枯燥码字(效率低、煎熬)，于是搭建了自己的一个图床。
***

# 图床搭建

所使用的工具为**PicGo**、**Gitee**、**Typora**. 在这之前，我一般在**VSCode**上面写**Markdown**文档，但是现在开始添加图片到博客中去，转而使用Typora，以便能够更好的契合PicGo使用(插入直接上传)，而VSCode版本的PicGo插件暂时不支持Gitee图床的插件。

## 工具下载

* PicGo: [https://molunerfinn.com/PicGo/](https://molunerfinn.com/PicGo/)
* Tyora: [https://www.typora.io/](https://www.typora.io/)



## Gitee端配置

在[Gitee](https://gitee.com/)上面注册一个账号，新建立一个仓库，在个人中心的设置中开启一个私人密匙(token). 此密匙需记录好，离开页面后就会消失，不会再次出现，只能重新生成。

![image-20210406204053178](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210406token.png)
***


## PicGo端配置

现在打开安装好的PicGo, 默认的图床中是没有gitee图床的，需要安装插件。在插件选项中搜索gitee，安装好gitee图床插件。

![image-20210406204506519](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210406PicGo1.png)
***
现在需要对gitee图床进行配置，使它和gitee的服务端对接起来：

* repo填写仓库名称；
* branch填写master分支，默认即可；
* token填写刚才生成的密匙；
* path自定义填写，既然用作图床，文件夹名就命名为img；
* 其他的可不给予填写，留空即可。

![image-20210406205014650](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210406PicGo2.png)
***

此外，PicGo还有其他的一些设置可按自己喜好配置，推荐将上传前重命名选上，方便为每张图做自己的标识(但是每次上传会弹出重命名窗口，效率会率低，不喜欢可以关掉)，时间戳重命名可以有效避免文件的冲突，也推荐选上。

![image-20210406205215384](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210406PicGo3.png)
***


## Typora配置

在Typora中打开文件 --->偏好设置(Ctrl+逗号)。

![image-20210406205534526](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210406Typora1.png)
***


在偏好设置中将图像中的插入图片时自动上传图片启用，然后上传服务配置为PicGo.

![image-20210406205827909](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210406Typora2.png)
***

# 尾

至此，一个私人图床就搭建完成了，简单而高效。