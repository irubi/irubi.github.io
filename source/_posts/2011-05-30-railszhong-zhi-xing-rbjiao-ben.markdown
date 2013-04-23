---
layout: post
title: "rails中执行rb脚本"
date: 2011-05-30 12:27
comments: true
categories: rails
---
有时候需要在rails中写一段能在console里执行的小脚本来实现某些功能，具体做法是，在script目录下新建脚本文件test.rb,
在test.rb中写脚本内容：
<!--more-->
{% codeblock /script/test.rb lang:ruby %}
require File.dirname(FILE) + '/../config/environment'
# SOME RUBY CODE...
{% endcodeblock %}
执行命令：
{% codeblock lang:bash %}
script/rails runner -e production script/test.rb
{% endcodeblock %}

`-e production`参数是执行时的环境设定，如果是`development`环境可省略
