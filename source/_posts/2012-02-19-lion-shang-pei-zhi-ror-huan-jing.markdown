---
layout: post
title: "Lion 上配置 ROR 环境"
date: 2012-02-19 11:52
comments: true
categories: rails Lion Ruby
---
<!--more-->

* 安装[total terminal](http://totalterminal.binaryage.com)和[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)
{% codeblock lang:bash %}
mate ~/.zshrc
{% endcodeblock %}
  
  修改theme参数：
{% codeblock lang:bash %}
ZSH_THEME="robbyrussell"
{% endcodeblock %}

  打开totalterminal preference， 设置homebrew 为default theme, 然后修改其配置选项如图：

  <img src='http://pic.yupoo.com/irubi_v/CRLEVIMb/Bask3.png'>

* 安装、配置 [git](http://help.github.com/mac-set-up-git/)

* 安装[homebrew](https://github.com/mxcl/homebrew)

* 安装 [rvm](http://beginrescueend.com/rvm/install/)

然后执行

{% codeblock lang:bash %}
source ~/.zshrc
type rvm | head -1
{% endcodeblock %}

rvm 安装成功

如果在下面的安装过程中出现错误，请确保已经安装了xcode command line tools。

* 安装ruby

  可以先看一下
{% codeblock lang:bash %}
rvm list known
{% endcodeblock %}
安装1.9.3

{% codeblock lang:bash %}
rvm install 1.9.3
{% endcodeblock %}
安装完成之后
{% codeblock lang:bash %}
rvm use 1.9.3 --default
{% endcodeblock %}

看看irb是否支持中文, 如果不支持，在~/.zshrc中加入

{% codeblock lang:bash %}
export LC_ALL=en_US.UTF-8
export LANG=en_US.UTF-8
{% endcodeblock %}



安装完成