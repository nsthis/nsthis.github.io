---
title: Git常用命令
date: 2018-02-10 21:13:44
tags: Git
categories: Git
---

Git(读音为/gɪt/。)是一个开源的分布式版本控制系统，可以有效、高速的处理从很小到非常大的项目版本管理。 [1]  Git 是 Linus Torvalds 为了帮助管理 Linux 内核开发而开发的一个开放源码的版本控制软件。

<!-- more -->

##	Git与SVN的区别

我没有用过SVN，所以这里摘抄点资料
1、Git是分布式的，SVN不是
2、GIT把内容按元数据方式存储，而SVN是按文件
3、GIT分支和SVN的分支不同
4、GIT没有一个全局的版本号，而SVN有
5、GIT的内容完整性要优于SVN
6、Git下载下来后，在本地不必联网就可以看到所有的log，很方便学习，SVN却需要联网
7、SVN在Commit前，我们都建议是先Update一下，跟本地的代码编译没问题，并确保开发的功能正常后再提交，这样其实挺麻烦的，有好几次同事没有先Updata，就Commit了，发生了一些错误，耽误了大家时间，Git可能这种情况会少些
本文摘自：https://www.cnblogs.com/somethingWithiOS/p/5636356.html

##	常用命令


命令 | 说明
------- | -------
Git clone 地址 | 就是将远程的分支给下载下来,初使化 
Git add | 添加文件
Git commit -m ‘内容’ | 添加说明 
Git init | 仓库初使化
Git push |	提交
Git pull | 拉取
Git diff |	检查对比文件的一致性
Git status |	显示相关的当前的 git 信息 
Git status -s  |	哪些文件发生了变化 
Git branch 分支 |	创建分支
Git checkout 分支 |	切换分支
Git branch -d 分支名 |	删除分支 
Git merge 源分支名 |	从源分支名合并内容至当前分支
Git log | 查看 git 历史 
Git rm | 删除 git 中的文件 
Git reset --hard 写上 log 中的编号  | 回滚至某个版本 
Git checkout -b A B 分支  | 建立 A 分支，从 B 分支取内容 
Git branch | 显示所有分支
Git clone -b 分支名	地址 | 拉去某分支源码

## 	Git分支使用

当A、B、C三人开发同一项目，但是A与B的代码完成，需要提交正式服，但是C的代码没有完成，此时如何设计？

首先我们需要明确，A与B的代码是提交的，但是C的代码不能提交，那么我们则需要有5个分支：master(主，也可以理解为正式服)、dev(测试服)、A、B、C。
然后我们用dev合并A，再合并B，合并完之后如果测试没问题，那么再用master合并dev，完成该需求的git设计。


