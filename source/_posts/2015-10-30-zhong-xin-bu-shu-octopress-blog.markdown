---
layout: post
title: "重新部署octopress blog"
date: 2015-10-30 21:25
comments: true
categories: blog
---
> 文档地址：[http://octopress.org/docs](http://octopress.org/docs)

* 从github clone项目文件, 切换到`source`分支
* 安装`.ruby-version`中记录的ruby版本，或修改`.ruby-version`为当前系统的ruby版本
<!--more-->
* 命令及说明

```ruby
bundle
bundle exec rake new_post 
#Enter a title for your post:  title 生成新的文章
bundle exec rake generate #build 
bundle exec rake deploy #部署
#用pow随时查看blog，但是每次写了新文章需要 执行 bundle exec rake generate
cd ~/.pow
ln -s /path/to/blog

```




