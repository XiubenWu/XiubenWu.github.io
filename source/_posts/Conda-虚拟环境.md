---
title: Conda 虚拟环境
excerpt: 使用conda来简单配置虚拟环境
date: 2021-08-25 14:37:37
tags:
  -	Python
categories:
  -	笔记
---


# 简介

如果在一台电脑上, 想开发多个不同的项目, 需要用到同一个包的不同版本, 如果使用上面的命令, 在同一个目录下安装或者更新, 新版本会覆盖以前的版本, 其它的项目就无法运行了。 解决方案 : 虚拟环境的作用 : 虚拟环境可以搭建独立的python运行环境, 使得单个项目的运行环境与其它项目互不影响。



***



# 使用

基本命令:

查看当前存在哪些虚拟环境:

```
conda env list 或
conda info -e 
```

检查更新当前conda:

```
conda update conda 
```

创建虚拟环境:

```
conda create -n your_env_name python=x.x
```

激活虚拟环境:

```
activate env_name (Windows cmd)
conda activate env_name (Anaconda Powershell)
```

关闭虚拟环境：

```
conda deactivate (Windows cmd)
conda deactivate (Anaconda Powershell)
```

删除虚拟环境:

```
conda remove -n env_name --all
```

在虚拟环境中安装某个包

```
conda install -n your_env_name [package]
```

删除虚拟环境中的某个包:

```
conda remove --name env_name  package_name
```

## 指定路径

conda默认的虚拟环境安装路径在cconda根目录下的**evns**文件夹中。要将虚拟环境安装到指定的路径，则使用如下命令:

```
conda create --prefix=D:\folder\**\envName python=x.x
```

这种方式安装的虚拟环境的名称为其全路径**D:\folder\**\envName**,激活环境时:

```
activate D:\folder\**\envName (Windows cmd)
conda activate D:\folder\**\envName (Anaconda Powershell)
```

删除环境:

```
conda remove --prefix=D:\folder\**\envName --all
```



