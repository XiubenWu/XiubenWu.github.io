---
title: Hexo搭建
# excerpt: HEXO搭建记录
date: 2021-02-04 17:20:17
tags: Hexo
categories: 
    - [笔记]
# photos:
#   - http://i1.fuimg.com/733114/99a345d1aab8720c.png
---

# **Hexo, a easy, fast, simple&powerful framework**
HEXO搭建记录
<!-- more -->

# 准备工作
* Git
* Node.js
* Github
* 建立本地仓库，与远程仓库绑定，SSH密匙。

# 搭建步骤
1. 使用npm命令安装Hexo，输入（参数-g全局安装）：   
```
npm install -g hexo-cli 
```
2. 安装完成以后，需要初始化一下项目，执行下列命令：   
```
hexo init
npm install
```
3. 安装hexo themes，npm安装相关依赖。
4. 建立页面、清除缓存、生成页面、本地预览、部署到网页。
```
hexo new
hexo clean
hexo g
hexo d
```
**Note:** 仓库建立两个分支，master存放代码文件，pages用于部署页面。hexo _config文件中注意更改部署页面。
```
deploy:
  type: git
  repo: https://github.com/name/name.github.io.git
  branch: pages
```
部署到时若出现网页与本地不一致，删除生成文件(public, .deploy_git等)再重新生成部署。
