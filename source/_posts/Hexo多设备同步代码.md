---
title: Hexo多设备同步代码
date: 2017-12-03 21:21:38
tags: Hexo
categories: hexo
---
很多朋友可能也和我一样，在使用hexo的时候，如果想多设备同步更新博客时，git pull下来的代码和你原来电脑上的代码不一样

<!-- more -->

##	创建分支hexo
```
git branch hexo
git checkout hexo
git add .
git push hexo
```
注：如果你使用了其他的主题，那么你需要进行下面的操作

## 删除主题下面的.git文件
```
删除 A/ 的.git 
文件夹在 ./ 下输入”git rm -r –cached A/“ //谨记：是 A/ ，意为A目录下
在 ./ 下输入”git add A”
git commit -m “”
git push origin hexo
```
原因： 因为你的主题是通过其他地方git clone过来的，所以它也是个git仓库，所以需要删除

##	使用其它电脑

```
git clone 博客的git地址
git g
git s
git d
```

##	如果出现下列错误请按照下列操作
1、错误代码
```
fatal: in unpopulated submodule '.deploy_git'
FATAL Something's wrong. Maybe you can find the solution here: http://hexo.io/docs/troubleshooting.html
Error: fatal: in unpopulated submodule '.deploy_git'
at ChildProcess.<anonymous> (项目路径/node_modules/hexo-util/lib/spawn.js:37:17)
at ChildProcess.emit (events.js:180:13)
at maybeClose (internal/child_process.js:936:16)
at Socket.stream.socket.on (internal/child_process.js:353:11)
at Socket.emit (events.js:180:13)
at Pipe._handle.close [as _onclose] (net.js:541:12)
   ```

   2、解决办法

   ```
   rm -rf .deploy_git
   hexo g
   hexo d
   ```
   备注：别忘了 最后git push origin hexo
