---
title: Git忽略文件
date: 2018-03-08 23:16:56
tags: Git
categories: Git
---

IDE在编译过程中经常出现 debug release下的文件，这些文件每份代码生成或重新生成都会有变化，git就会认为是更改项，需要上传，在协作场景中如果大家都上传这些文件导致无数垃圾文件冲突，解决费时费力。

<!-- more -->

##	.gitignore 文件

当我们git init的时候，会产生.gitignore文件
在Linux系统中，你需要通过 ls -a 或者 ll -a才可以看到
在win系统中，可将文件夹改为显示隐藏文件即可

##	正则表达式
```
*.a       # 忽略所有 .a 结尾的文件
!lib.a    # 但 lib.a 除外
/TODO     # 仅仅忽略项目根目录下的 TODO 文件，不包括 subdir/TODO
build/    # 忽略 build/ 目录下的所有文件
doc/*.txt # 会忽略 doc/notes.txt 但不包括 doc/server/arch.txt
```
##	总结
仓库刚建立时就要创建此文件,不然刚开始协作就会出现冲突,如果已经冲突，简单点方式删除仓库重新创建，首先 创建.gitignore 然后在协作. 