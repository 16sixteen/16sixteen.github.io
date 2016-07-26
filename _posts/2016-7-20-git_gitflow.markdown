---
layout:     post
title:      "gitflow"
subtitle:   "github gitflow"
date:       2016-07-20 14:45:00
categories: Git
tags:       Git
author:     "SixTeen"
header-img: "img/unity3d.jpg"
published:   true
---

## gitflow

gitflow就是如何使用github进行多人开发，在软件设计综合实验这门课中，我们小组制作了一个音乐分享的web，从中不仅学习到了很多前端的javascript，nodejs和jade模板的使用，还有很多关于git的使用方法，了解到了一些git的流程。因此再次回顾记录。

### git分支结构

在我们的项目中，master是发行版，是用于发布在阿里云上的版本，dev是我们的开发版，开发版经过本地测试没有出现问题才会和合并到master版本中，我们小组中的6人每人有一个分支，用于开发自己分配到的功能，当自己开发的功能完善，本地测试没有问题后，便会pull request到dev分支中。

在实际的应用中，每个人的分支也许会变成对应的功能分支，多人开发，每人负责一个功能模块，对应的是一个分支。

这样开发的好处有什么能，在自己的分支上开发，方便回滚更新，不会影响到别人的开发，又可以通过pull来及时更新别人提交的新版本，在dev开发版没有问题后才提交到master上，这样大大减少了单分支造成的混乱。

### git常用命令

1.```git pull origin branch-name```从分支拉取代码与本地代码进行merge

2.```git branch branch-name```创建新的功能分支

3.```git checkout branch-name```切换分支

4.```git add <file-name>```或者```git add .```暂存文件，并用```git commit -m "commit_message"```来进行提交文件，最后用```git pull -u origin branch-name```来推到远程分支上，如果将远程分支映射到本地分支，那么使用```git push```即可。

5.```git checkout -b local-branch-name origin/remote-branch-name```,[git ——映射远程分支到本地分支](http://blog.csdn.net/jikmike/article/details/49737887)

6.打开github，可以在自己的分支进行pull request，有权限即可让别人进行code review，没有问题即可合并到dev分支。

7.发生冲突只需要根据冲突提示进入文件解决冲突即可。

> 1.[音乐共享项目github](https://github.com/ValenW/Online-Studio)<br/>2.[gitflow issue](https://github.com/ValenW/Online-Studio/issues/7)

    FIN 2016.7.24/15.10