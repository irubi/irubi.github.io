---
layout: post
title: "约瑟夫环Ruby算法"
date: 2009-03-19 11:15
comments: true
categories: ruby
---
N个人排成一圈，编号为1～n。
从1号开始报数，当报到M时，该位置的人出局，余下的人依然是一个圈，从下一个人开始报数（从1开始），再到第M个出局。

输入n和m,输出这个出局序列

RUBY代码:
{% codeblock lang:ruby %}
def circle(n,m)
  arr=(1..n).to_a
  i=0
  while arr.length>0
    x=(i+m-1)%arr.length
    puts arr[x]

    arr.delete_at(x)
    i=x
  end
end

puts "input n and m: [eg:12,5]"
n,m=gets.chomp.split(",")
circle(n.to_i,m.to_i)
{% endcodeblock %}