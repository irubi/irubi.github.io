---
layout: post
title: "ubuntu服务器上通过cron定时备份rails应用的数据库"
date: 2011-11-22 12:26
comments: true
categories: ubuntu database
---
##方案描述： 

用cron每天凌晨2：00自动备份MySQL数据库，文件名以数据库名为前缀紧跟当天的年月日命名（例如project_production2011-12-27.sql），备份后自动删除前六天的备份
<!--more-->
####示例

1.ssh登陆远程服务器
{% codeblock lang:bash %}
ssh username@host_address
{% endcodeblock %}
2.建立目录
{% codeblock lang:bash %}
username@hostname:~$ mkdir dbbak
username@hostname:~$ mkdir dbbak/project_db
{% endcodeblock %}
3.编辑命令文本
{% codeblock lang:bash %}
username@hostname:~/dbbak$ nano dbbak.sh
{% endcodeblock %}
内容：
{% codeblock lang:bash %}
DBbinPath=/usr/bin/
DBName=project_production
BackupPath=/home/username/dbbak/project_db

mysqldump --user="db_username" --password="db_password" ${DBName} > ${BackupPath}"/"${DBName}`date "+%Y-%m-%d"`".sql" ;
find ${BackupPath}"/" -mtime +6 -exec rm {} \;
{% endcodeblock %}

4.执行测试一下
{% codeblock lang:bash %}    
sudo sh dbbak.sh
{% endcodeblock %}
5.在root下添加cron
{% codeblock lang:bash %}
ssh root@hostname
{% endcodeblock %}
6.编辑cron任务
{% codeblock lang:bash %}
root@hostname:~$ crontab -e
{% endcodeblock %}
内容：
    
{% codeblock lang:bash %}
0 2 * * * `sh /home/username/dbbak/dbbak.sh`
{% endcodeblock %}
