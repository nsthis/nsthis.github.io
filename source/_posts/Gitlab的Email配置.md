---
title: Gitlab的Email配置
date: 2019-03-08 17:49:35
tags: Docker
categories: Docker
---

当我们在使用Gitlab分发任务时，当我们的同事不经常使用Gitlab则可以通过Email的形式发送过去

<!-- more -->

## 前提

阿里后台将你的服务器安全组开启25或者465端口，本文配置使用465

## 配置163邮箱

修改gitlab.rb文件如下

````
 gitlab_rails['smtp_enable'] = true
 gitlab_rails['smtp_address'] = "smtp.163.com"
 gitlab_rails['smtp_port'] = 465
 gitlab_rails['smtp_user_name'] = "xxxx@163.com"
 gitlab_rails['smtp_password'] = "xxx" //这里是你开启stpm时的授权码
 gitlab_rails['smtp_domain'] = "163.com"
 gitlab_rails['smtp_authentication'] = "login"
 gitlab_rails['smtp_enable_starttls_auto'] = true
 gitlab_rails['smtp_tls'] = true
 gitlab_rails['gitlab_email_from'] = 'xxxx@163.com'
 user['git_user_email'] = "xxxx@163.com"
````

## 测试是否配置成功

````
docker exec -it gitlab bin/bash
gitlab-rails console
Notify.test_email('收件人邮箱', '邮件标题', '邮件正文').deliver_now
````
