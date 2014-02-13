---
layout: post
title: "Rails操作Access数据库(mdb)"
date: 2014-02-13 15:25
comments: true
categories: rails access windows
---
应用场景：

* `windows`上跑着一个短信猫的服务，这个程序二次开发是通过修改服务所使用的一个`access`数据库，插入记录即可实现发短信的功能。

* `linux上`是`Rails`程序，开发了一个通讯录和发短信的功能，发短信时需要在`create`短信之后触发修改`access`数据库的操作。

但是`Rails`连接`windows`下的`access`(mdb数据库)比较麻烦，需要调用`mdb-tools`之类的东西，而且对中文支持可能会出现问题。

所以思路是这样的，在`windows`上安装`ssh server`，用`freesshd.exe`(开源免费)。安装好之后建立一个`ssh`用户(如root),并且将`linux`服务器上的`ssh\id_rsa.pub`内容复制到`c:/freesshd/root`文件中，如果还要加入其他ssh（root）用户，也可以将`id_rsa.pub`内容复制进去。

建立`ssh`通道之后，就可以用`ruby`执行`ssh`远程命令来操纵`windows`来执行命令操作`access`数据库。在`windows`上建立脚本如下：



{% codeblock insertsms.rb lang:ruby %}
  #!/usr/bin/env ruby
  # encoding: utf-8
  require 'rubygems'
  require 'sequel'
  require "net/http"
  require "uri"
  require 'json'
  
  uri = URI.parse(ARGV[0])
  response = JSON.parse(Net::HTTP.get_response(uri).body)
  
  puts uri
  DB = Sequel.ado(:conn_string=>'Provider=Microsoft.Jet.OLEDB.4.0;Data Source=D:\website\db\smsdata.mdb')
  items = DB[:OutBox] 
  
  phones = response["phones"].split(',')
  msg = response["msg"].dup.force_encoding("utf-8")
  sent_at = response["sent_at"]
  
  phones.each do|phone|
    items.insert(:Mbno => phone, :Msg => msg, :SendTime => sent_at)
  end
{% endcodeblock %}

linux服务器 Rails程序：

{% codeblock model/sms.rb lang:ruby %}
  #!/usr/bin/env ruby
  require 'rubygems'
  require 'net/ssh'
  
  class Sms < ActiveRecord::Base  
    def as_json(options={})
      { :id => self.id, :phones => self.contact, :msg => self.content, :sent_at => self.sent_at.strftime("%Y/%m/%d %H:%M:%S") }
    end
  
    def send_msg
      host = '192.192.100.11'
      user = 'root'
      # msg = self.content.force_encoding("utf-8")
      # phone = self.contact
      
        
      # msg = self.content.force_encoding('ASCII-8BIT')
      # phone = self.contact
      # sent_at = self.sent_at.strftime("%Y/%m/%d %H:%M:%S")
      sms_json_url = ''
      if Rails.env == 'development'
        sms_json_url = 'http://192.192.102.97:3000' + Rails.application.routes.url_helpers.admin_sms_path(self) + '.json'
      elsif Rails.env == 'production'
        sms_json_url = 'http://192.192.100.22' + Rails.application.routes.url_helpers.admin_sms_path(self) + '.json'
      end
  
      Net::SSH.start( host, user) do|ssh|
        ssh.exec!("cmd /c c:/ruby200/bin/ruby d:/website/insertsms.rb \"#{sms_json_url}\"")
      end
    end
  end
{% endcodeblock %}

因为如果从`Rails`调用`windows`上的`insertsms.rb`这段`script`如果传中文参数时，会有乱码问题出现，所以将插入短信所需数据参数以`json`的方式提供，直接让`insertsms.rb`去读取，将数据处理之后插入`access`数据库即可。

通信过程可能会耗费时间，造成响应时间长，所以需要引入`delay_job`来提升用户体验。