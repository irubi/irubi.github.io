---
layout: post
title: "Rails app如何实现AB测试"
date: 2012-01-10 12:16
comments: true
categories: rails AB测试
---
rails框架实现AB测试


1.什么是A/B测试

  A/B测试又叫split测试，其实叫split测试更准确一些，因为测试并不局限于非A即B，而是适用于多种场景的。对于web应用来说，A/B测试是指一个页面对用户可呈现几种不同的view，以测试其相应功能在哪种view的情况下效果会更好一些。

2.A/B测试的关键

  * 用户数据量大, 平均出现; 
  * 相关度; 测试内容应该和相应业务逻辑相关，根据测试结果可以决策采取哪种view。

<!--more-->

举例来说，对于电商网站的某个产品介绍页面，该页面直接关系到订单提交（也即购买）的核心业务逻辑，现在进行A/B测试，呈现给用户不同的view，然后根据用户提交订单数量及用户打开该页面次数进行记录得到测试数据，进而判断哪个view是最好的。

{% img center /images/ab-testing.png %}


3.A/B测试的手段

Google Website Optimizer，可能是一个最简单的工具，但是它适合静态的页面，不能整合到动态网站中。所以，如果要在在动态网站中进行A/B测试必须将测试工具整合到应用中。

在rails中实现A/B测试的方式：

1.设置视图(2个或更多)

2.视图与业务逻辑的相关性（多对一或多对多，个人认为多对一的测试较准确）

3.设置视图的呈现逻辑（利用用户浏览器的session（登陆或匿名用户），随机数（用户发起请求时间隔呈现），注册用户id划分等等）

4.在测试数据量增大到一定程度，数据相对稳定的情况下判断哪种视图最优。

----

下面是rails中如何实现AB测试。
rails提供了三个工具来做A/B测试。

实现一个简单的demo，整个过程是这样的：一个博客程序，在新建post的时候给用户呈递不同的视图，然后根据用户创建文章的情况来判断哪种情况会更好。

1.新建blog app：
{% codeblock lang:bash %}
$ rails new blog -d mysql
$ cd blog
$ rails g scaffold post title:string content:text
{% endcodeblock %}
2.A/B测试工具

  * [abingo](https://github.com/webandy/abingo) 这个是专门针对A/B测试的一个插件，简单易用，足以应付普通的测试场景的需求了。但可惜的是，它只能用于rails2，在rails3上面有bug，所以这里不详细介绍了。[railscasts](railscasts.com/episodes/214-a-b-testing-with-a-bingo?view=asciicast)中有一集做了详细介绍。

  * [vanity](https://github.com/assaf/vanity)比较专业的一个。他是专门用来做Experiment Driven Development（体验驱动开发）的一个gem, A/B测试只是其中一部分，[参考](http://vanity.labnotes.org/ab_testing.html)。

  * [split](https://github.com/andrew/split)支持rails 3，并且使用 redis来作数据存储，所以需要安装redis环境

安装gem
{% codeblock lang:bash %}
$ gem 'split'
$ bundle install
{% endcodeblock %}

安装并启动redis(mac环境下)
{% codeblock lang:bash %}
$ brew install redis
$ redis-server /usr/local/etc/redis.conf
{% endcodeblock %}
在新建post的页面的同一个位置设置三种不同的视图，分别命名为plan_1, plan_2, plan_3。当用户在刷新这一页的时候会出现这三种不同的视图。

{% codeblock app/views/posts/new.html.erb lang:ruby %}
<h1>New post</h1>
<% ab_test("create_post_intro", "plan_1", "plan_2", "plan_3") do |plan|%>
  <% case plan %>
  <% when "plan_1"%>
     <div id="plan_1">
        11..
     </div>
  <% when "plan_2"%>
     <div id="plan_2">
        22..
     </div>
  <% when "plan_3"%>
     <div id="plan_3">
        33..
     </div>
  <% end %>
<% end %>
<%= render 'form' %>

<%= link_to 'Back', posts_path %>
{% endcodeblock %}

如果现在查看浏览器posts/new的话，可以通过刷新看到三个不同的视图。

create_post_intro可以看作这个A/B测试的名称描述，下面在create的action中加入记录：

{% codeblock app/controllers/posts_controller.rb lang:ruby %}
def create
  @post = Post.new(params[:post])
  respond_to do |format|
    if @post.save
      finished("create_post_intro")
      format.html { redirect_to @post, notice: 'Post was successfully created.' }
      format.json { render json: @post, status: :created, location: @post }
    else
      format.html { render action: "new" }
      format.json { render json: @post.errors, status: :unprocessable_entity }
    end
  end
end
{% endcodeblock %}

可以测试一下提交表单，未提交表单等等。

如何显示测试结果？

{% codeblock app/config/routes.rb lang:ruby %}
require 'split/dashboard'
Blog::Application.routes.draw do
  mount Split::Dashboard => "/split"
  resources :posts
end
{% endcodeblock %}

此时打开/split可以看到结果如下图：
{% img center /images/111.png %}

可以根据split的统计数据进行判断和决策。

现在基本上A/B测试的功能已经搞定了，但是没有定制视图展示策略，没有在显示/split的页面加入auth。具体方式可以参考[split](https://github.com/andrew/split) 在github上的文档.

另外，[这里](http://www.smashingmagazine.com/2010/06/24/the-ultimate-guide-to-a-b-testing/)对于A/B测试做什么，怎么做，以及一些线上的demo，介绍的还不错。