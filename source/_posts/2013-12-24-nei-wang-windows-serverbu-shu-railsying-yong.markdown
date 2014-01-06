---
layout: post
title: "内网windows server部署rails应用"
date: 2013-12-24 09:14
comments: true
categories: 
---

最近在做医院的项目，是一个部署到内网的OA系统
内网服务器用的是`windows server 2003`, 没有外网连接

开发上没有什么大问题，用`rails 4.0`开发完成之后，困难的是部署。
部署时候因为考虑到`windows`对`ROR`的不友好，所以利用`vmware server 2.0.2`制作虚拟机来实现。

找一台可以上网的`windows`电脑，安装`vmware server 2.0.2`, `Linux`我比较顺手的是`centos`和`ubuntu`，因为这个版本的`vmware server`支持`ubuntu`的最高版本是8.04, 再往后的版本在安装时会卡住，安装无法继续。所以我选择了`centos`桌面版，因为还考虑到桌面版在设置（网络配置等）方面比较便捷。

装好之后配置`vmware`网络连接方式为桥接（并设置好固定IP），这样的设置可以将虚拟机作为内网中的一台可通过固定IP访问的服务器。

接下来就是安装`ssh`, 这样就可以在`mac`上访问虚拟服务器进行各种操作了。
`git`, `rvm`, `mysql`, `passenger`, `nginx`...

这里有一个好的建议是在虚拟服务器上安装`gitolite`服务（配置什么的比`gitosis`更简单），到时候可以直接把代码`push`到服务器上，从服务器上部署代码。

因为现在都是在有网络的宿主`windows`电脑上操作，所以可以将需要的`gem`都安装完全，直至`rails`应用可以在虚拟服务器上跑起来。

跑起来之后将`vmware`文件夹拷贝到服务器上，在服务器上安装好`vmware server`后直接将虚拟服务器导入，修改网络配置，就ok了！

接下来在运行过程中可能出现的问题：

1. 配置vmware server自动启动，centos随vmware server 自启动。

    在Configure Options里有Edit Virtual Machine Startup/Shutdown，点击一下，出来自启动的控制面板，将Allow virtual machines to start and stop automatically with the system勾选中，然后最下面有Startup Order, 将需要启动的虚拟机Move up到Specified Order下。
    重启一下测试启动是否正常。

2. mysql可能在重启centos虚拟机（非正常reboot）时不删除/var/lib/mysql/mysql.sock导致开机后mysql启动失败，解决方案：

vmware server非正常关机时不会删除/var/lib/mysql/mysql.sock文件，导致开机后mysql启动失败，解决方案：


    [root@master ~]#  vi /etc/init.d/mysqld


    start(){
      ...
      # Check and delete mysql.sock
      if [ -x /var/lib/mysql/mysql.sock ]; then
        rm -f /var/lib/mysql/mysql.sock
      fi