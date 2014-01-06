---
layout: post
title: "中文分词检索sphinx"
date: 2012-04-20 11:20
comments: true
categories: 搜索引擎 rails 中文分词
---
讲述如何在Rails服务器上搭建中文搜索服务。
<!--more-->
中文版sphinx -> coreseek

[官方文档](http://www.coreseek.cn/docs/coreseek_4.1-sphinx_2.0.1-beta.html)

coreseek简单说就是支持中文分词的sphinx，所以选定最新beta版进行安装。


1.在centos 6.0上安装sphinx服务

{% codeblock lang:bash %}
sudo yum -y install yum glibc-common build-essential libtool autoconf automake libexpat-dev
{% endcodeblock %}


下载 coreseek最新版， http://www.coreseek.cn/news/14/52/
解压之后

{% codeblock lang:bash %}
cd mmseg-3.x.x
aclocal
libtoolize --force
automake --add-missing
autoconf
autoheader
make clean #此时如有错误可忽略不管
./configure --prefix=/usr/local/mmseg3
make
make install
cp -f src/*/*.h /usr/local/mmseg3/include/mmseg/
{% endcodeblock %}

一定要执行以上的拷贝，否则make编译时会出错。
{% codeblock lang:bash %}
sudo ln -s /usr/local/mmseg/bin/mmseg /usr/bin

[root@localhost csft-4.1]# sh buildconf.sh 

[root@localhost csft-4.1]# export LIBS="-ldl -lutil -Xlinker -export-dynamic"

[root@localhost csft-4.1]# ./configure --prefix=/usr/local/coreseek --without-python --without-unixodbc --with-mysql --with-mmseg --with-mmseg-includes=/usr/local/mmseg/include/mmseg/ --with-mmseg-libs=/usr/local/mmseg/lib/

[root@localhost csft-4.1]# make && make install

$ yum install mysql-devel libxml2-devel expat-devel

$ cd csft-3.2.14

$ make clean

$ ./configure -prefix=/usr/local/coreseek  --without-unixodbc --with-mmseg --with-mmseg-includes=/usr/local/mmseg/include/mmseg/ --with-mmseg-libs=/usr/local/mmseg/lib/ --with-mysql
$ make && make install
{% endcodeblock %}

需要将coreseek的命令加入path中，

{% codeblock lang:bash %}
vi ~/.bash_profile
PATH=$PATH:$HOME/bin:/usr/local/coreseek/bin/
source ~/.bash_profile
{% endcodeblock %}

{% codeblock lang:bash %}
gem install thinking-sphinx
{% endcodeblock %}


Hack Thinking-Sphinx:

首先查看一下gem的安装路径
{% codeblock lang:bash %}
gem environment
{% endcodeblock %}

修改thinking-sphix-gem:

{% codeblock lang:bash %}
vi $thinking-sphinx-gem-path/lib/thinking_sphinx/configuration.rb 
{% endcodeblock %}

{% codeblock lang:bash %}
self.index_options   = {
  :charset_type => "zh_cn.utf-8",
  :charset_dictpath => "/usr/local/mmseg/etc"
}
{% endcodeblock %}


####Ubuntu环境下：

{% codeblock lang:bash %}
apt-get install make gcc g++ automake libtool mysql-client libmysqlclient15-dev   libxml2-dev libexpat1-dev
{% endcodeblock %}

下载最新版
{% codeblock lang:bash %}
cd ~
wget http://www.wapm.cn/uploads/csft/4.0/coreseek-4.1-beta.tar.gz
{% endcodeblock %}
解压
{% codeblock lang:bash %}
tar -zxvf coreseek-4.1-beta.tar.gz
{% endcodeblock %}

安装

