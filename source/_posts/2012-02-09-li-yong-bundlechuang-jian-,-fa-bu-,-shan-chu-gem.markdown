---
layout: post
title: "利用bundle创建、发布、删除gem"
date: 2012-02-09 12:13
comments: true
categories: rails bundle gem
---
使用bundle工具来做自己的gem，并且把gem发布！
<!--more-->

首先需要系统的RubyGems 1.3.6+ and gemcutter 0.5.0+. 

如果版本不对或者没有安装可以执行：
{% codeblock lang:bash %}
gem update --system
gem install gemcutter
{% endcodeblock %}

创建和发布：
参考[railscasts](http://railscasts.com/episodes/245-new-gem-with-bundler)
{% codeblock lang:bash %}
bundle gem [gemname]
gem build [gemname].gemspec
gem push [gemname]-[version_number].gem
{% endcodeblock %}

删除gem
{% codeblock lang:bash %}
gem yank [gemname] -v[version_number]
gem yank [gemname] -v[version_number] --undo
rake gemcutter:index:update
{% endcodeblock %}
只能yank,但是不能阻止下载， 如果要彻底删除的话还得发一个dicussion 上去申请删除，要不然会影响到下载用户的使用。





