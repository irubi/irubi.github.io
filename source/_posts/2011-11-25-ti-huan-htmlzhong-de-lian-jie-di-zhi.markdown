---
layout: post
title: "替换html中的链接地址"
date: 2011-11-25 12:19
comments: true
categories: html
---
应用场景：将文本或者html中链接进行短地址转换，或者替换为可追踪的链接。
<!--more-->
代码：
{% codeblock gsub_links.rb lang:ruby %}
require 'Digest'
def gsub_links(conetnt, domain)
  hash_links = {}
  time = Time.now
  links_arr = content.scan(/<a.*?href=(['"])(.*?)\1.*?>/).map {|m| m[1] }
  links_arr.each do |l|
    hash_links["#{l}"] = Digest::SHA1.hexdigest(l+time.to_s)[8..16]
  end
  content.gsub!(/<a.*?href=(['"])(.*?)\1.*?>/){|s|s.gsub($2, "#{domain}/#{Digest::SHA1.hexdigest($2+time.to_s)[8..16]}")}
end

{% endcodeblock %}


content是html代码的内容，对于正则表达式`/<a.*?href=(['"])(.*?)\1.*?>/`会匹配到类似`<a href="http://test.com">`的字符串，匹配会得到两个结果，第一个是`"`,第二个是`http://test.com`,可以通过这个表达式来达到替换链接的目的。而`#{Digest::SHA1.hexdigest($2+time.to_s)[8..16]}`可以对url地址进行一个转换，得到一个9位的字符串，通过这个算法确保url被转换成短地址的唯一性和随机性。
最后，link_hash就是原来链接和短链接的对应关系，content中的链接已经被短链接所替代。