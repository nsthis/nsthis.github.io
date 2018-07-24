---
title: Hexo搭建
date: 2017-07-23 17:41:51
tags: Hexo
categories: hexo

---
hexo搭建及加载主题

<!-- more -->
## Mac通过域名 + GitHub Pages + hexo 搭建自己的博客

~~~
	说明： 因本人使用电脑为mac，故部分操作仅限mac
	hexo官网： hexo.io
	github官网： github.com
~~~

## GitHub配置

~~~ 
	登陆github官网后，点击界面右上角头像，找到Settings
	打开电脑的"终端"， 输入：ssh-key -t rsa 然后一只回车
	vi ~/.ssh/id_rsa.pub, 将里面的内容放入Settngs的SSH and GPG keys中
~~~

## 新建GitHub项目

~~~
	打开github主页， New repository
	输入项目名称： 格式必须为： username.github.io
	创建之后， 点击 username.github.io项目，找到Settings -> GitHub Pages -> Custom domain
~~~

## 域名解析

~~~
	登陆你域名所在服务商后台， 添加类型为CNAME的记录值, 域名则是你github仓库的地址: username.github.io
~~~

## 安装 Node.js
	通过homebrew安装(推荐)
~~~
	brew link node
	brew uninstall node
	brew install node
~~~

## 安装 Hexo

~~~
	npm install -g hexo-cli
~~~

## Hexo 建站

~~~
	mkdir -p 网站名称
	cd 网站名称
	hexo init 
~~~
	创建完后，目录应该如下
~~~
	.
	├── _config.yml
	├── package.json
	├── scaffolds
	├── source
	|   ├── _drafts
	|   └── _posts
	└── themes
~~~

## 测试网站

~~~
	hexo s
	打开浏览器，输入localhost:4000/
~~~
	如果能够正常打开hexo，那么本机hexo搞定

## 更换主题

~~~
	hexo的主题官网: https://hexo.io/themes 
	可以在这里面挑选你喜欢的主题，然后点击名称会跳转到github地址去
~~~
	下面我拿我的博客主题为例
~~~
	cd 博客文件
	git clone https://github.com/tufu9441/maupassant-hexo.git themes/maupassant
	npm install hexo-renderer-pug --save
	npm install hexo-renderer-sass --save
~~~
注：	若npm install hexo-renderer-sass安装时报错，可能是国内网络问题请尝试使用代理或者切换至淘宝NPM镜像安装，感谢光头强提供的方法。

## 配置 config.yml 文件
	找到文件最下方，添加下面内容
~~~
	deploy:
  	 type: git
  	 repo: git@github.com:username/username.github.io.git
  	 branch: master
~~~
	保存之后, 输入下面命令
~~~
	hexo clean 
	hexo d
~~~
	如果报错 ERROR Deployer not found: git
~~~
	npm install --save hexo-deployer-git
~~~

##	特别注意
	如果你每次 hexo s之后 访问域名都会报404 那么你需要创建一个CNAME文件
~~~
	cd hexo文件
	cd source
	vi CNAME 
	在这个文件里面输入你的域名就可以了
~~~
	文章中我尽可能的没有用vim 因为有的系统 vim 不一定是自带的




