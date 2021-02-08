---
title: Git使用
# excerpt: git常用命令笔记
date: 2021-02-05 17:43:31
categories:
    - [笔记]
tags:
    - Git
# photos:
#     - http://i2.tiimg.com/733114/5688242ca3126ac8.jpg
---

**Git常用命令笔记**   

<!-- more -->

# git常用命令

## 创建库
* ***makedir name*** 新建目录
* ***cd name*** 进入目录
* ***git init*** 初始化为仓库
* ***pwd*** 当前路径
* ***ls*** 列举当前目录文件
  * ***ls -ah*** 显示隐藏(.git)

## 将文件添加进库
* ***git add filename*** 将文件添加入仓库
* ***git commit -m "commend"*** 将文件提交至仓库   
*git add* 将**工作区**文件放入**暂缓区**，*git commit* 将暂缓区文件提交到**分支**。可以添加多个文件后一次性进行提交。

## 版本管理
* ***git status*** 查看当前的仓库状态，是否有未更新的改动等。
* ***git diff*** 查看文件的具体改动内容
  * ***git diff HEAD -- filename*** 查看工作区与最新版本库文件中的区别。
* ***git reset --hard HEAD^*** 退回上一版本(HEAD:当前版本；HEAD^上一版本:HEAD^^：前二版本；HEAD~n:前n版本)
* ***git log*** 和 ***git reflog*** 查看提交的日志文件， 之后使用 ***git reset --hard commit_id*** 跳转到相应的版本。
* ***git checkout -- filename*** 将工作区的修改撤回至暂缓区（若存在）或者版本库，其中的--不可缺少，否则成为切换分支的命令。当添加到暂缓区后想放弃工作区的修改时：***git reset HEAD filename*** 再使用 ***git checkout -- filename***.
* ***git rm filename*** 之后 ***git commit*** 删除文件并更新到版本库。

## 远程仓库
* ***ssh-keygen -t rsa -C "youremail@example.com"*** 创建ssh key，加密本地仓库和远程GitHub仓库的传输，无需设置密码。之后**将SSH公匙添加至github仓库Account settings中的SSH keys**.
* ***git remote add origin git@github.com:username/repositoryname.git*** 将本地仓库与远程仓库关联起来。(origin为默认远程仓库名，可修改其他名称。)
* ***git remote rm origin*** 删除Git仓库中的origin信息(关联错误需重新关联时)。
* ***git remote -v*** 查看远程仓库信息。
* ***git push -u origin master*** 本地文件推送至远程仓库，首次使用时需要-u将本地和远程关联，后续命令可简化，以后推送特定文件可直接***git push origin master***.
* ***git clone git@github.com:username/repositoryname.git*** 从远程仓库克隆到本地。
* 从本地推送分支，使用 ***git push origin branch-name***，如果推送失败，先用 ***git pull*** 抓取远程的新提交。
* 在本地创建和远程分支对应的分支，使用 ***git checkout -b branch-name origin/branch-name*** ，本地和远程分支的名称最好一致。
* 建立本地分支和远程分支的关联，使用 ***git branch --set-upstream branch-name origin/branch-name*** .

## 分支管理
* ***git checkout -b branchname***    
***git switch -c branchname*** 创建并切换分支，相当于两命令之和：
    * ***git branch branchname*** 创建分支
    * ***git checkout branchname*** 切换分支
    * ***git switch branchname*** 新版本使用的更直观的切换分支命令。
* ***git branch*** 列举所有分支，*前缀标明当前分支。
* ***git merge branchname*** 合并指定分支到当前分支。
* ***git branch -d branchname*** 删除指定分支。
    * ***git branch -D branchname*** 强行删除未合并的分支。
* ***git stash*** 隐藏未提交的工作区。
* ***git stash list*** 查看隐藏的工作现场。
* ***git stash pop*** 恢复同时删除隐藏区内容。
    * ***git stash apply*** 恢复stash.
    * ***git stash drop*** 删除stash.
* ***git rebase*** 整理分支。

## 标签管理
* ***git tag v1.0*** 给当前分支打标签。
* ***git tag v000 commit_id*** 给特点版本的commit打标签。
* ***git show tagname*** 查看标签信息。
* ***git tag -a tagname -m "blablabla..."*** 可以指定标签信息。
* ***git tag -d tagname*** 删除标签。
* ***git push origin tagname*** 推送标签到远程。
    * ***git push origin --tags*** 推送全部标签。
* 删除远程仓库标签步骤：
    * 先在本地删除：***git tag -d tagname***
    * 再从远程删除：***git push origin :refs/tags/tagname***

## 其他
[图形化工具SourceTree官方网站](https://www.sourcetreeapp.com/)   
