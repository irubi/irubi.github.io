---
layout: post
title: "获取GA数据并绘制chart"
date: 2012-02-13 12:00
comments: true
categories: rails Google Analytics
---

使用garb来获取GA上面的网站分析数据，再通过获取的数据用highcharts来绘制曲线
<!--more-->

[garb](https://github.com/vigetlabs/garb)是一个用来调用Google Analytics API的Ruby gem.
首先需要在rails中新建一个script：

{% codeblock script/ga.rb lang:ruby %}

p "----loGin Google----"
Garb::Session.login('google account', 'password')
p "----Get Profile----"
profile = Garb::Profile.first("UA-XXXXXXX-X")
p profile
p "-----Get unique PV----"

start_date = Date.today - 30
end_date = Date.today

p "-----Define a specific page----"
project_id = 123456

p "-----fetch the page (uri: /projects/12345) unique pageview----"

(start_date..end_date).each do |date|
  result_set = Exits.results(profile, :start_date => date, 
                              :end_date => date, 
                              :filters => {:page_path.eql => '/projects/' + project_id.to_s})
  
  result_set.each do |result|
    #puts date.to_s + "-> " + result.unique_pageviews.to_s
    Pv.create(:ga_date => date, 
              :upv => result.unique_pageviews, 
              :pv => result.pageviews,
              :project_id => project_id.to_s)
  end
end
{% endcodeblock %}

这里代码执行的前提是有一个存放pv数据的表和一个Exits的Model

{% codeblock app/models/exits.rb lang:ruby %}
class Exits
  extend Garb::Model
  metrics :pageviews, :unique_pageviews
end
{% endcodeblock %}


{% codeblock app/models/pv.rb lang:ruby %}
# t.date :ga_date
# t.integer :upv, :default => 0
# t.integer :pv, :default => 0
# t.integer :project_id
# t.timestamps
class Pv < ActiveRecord::Base
end
{% endcodeblock %}

这样就可以将这个projects/123456页面的upv存放到数据库中了。不过这里的数据关系没有进行关联，仅作为一个demo来演示如何进行数据获取及chart绘制，所以具体应用可以根据实际情况进行设计。

另外还需要说明的一点就是，这里是获取一个月之内的每日upv数据，可能存在该页面访问量为0的数据，这种数据不会存入数据库中，所以需要在绘图的时候进行判断处理。

运行script/ga.rb的命令：
{% codeblock lang:bash %}
script/rails runner script/ga_pv.rb
script/rails runner -e production script/test.rb
{% endcodeblock %}
可以在部署之后将命令加入cron中，让它每日定时去抓取GA的数据。
--------

绘制chart:

绘图使用[highchart](http://www.highcharts.com/), 下载highchats的js包，并放到app/assets/javascripts/目录下。


{% codeblock app/views/demo/chart.html.erb lang:html %}
<% content_for :head do %>
  <%= javascript_include_tag "highcharts" %>

<% end %>
<script>
    $(function() {
       chart = new Highcharts.Chart({
            chart: {
                renderTo: "project_upv_chart",
                type: 'spline'
            },
            title: {
              text: "每日不重复访客数"
            },
            xAxis: {
                type: 'datetime',
                labels: {
                    formatter: function() {
                        return Highcharts.dateFormat('%b月%e日', this.value);
                   }
               }
            },
            yAxis: {
              min: 0,
              allowDecimals: false,
              title: {
                text: "访问数"
              }
            },
            tooltip: {
              formatter: function() {
                return Highcharts.dateFormat("%Y年%b月%e日 %A", this.x) + "<br/>" + '唯一身份浏览量： ' +
                Highcharts.numberFormat(this.y,0);
              }
            },
            series: [{
              pointInterval: <%= 1.day.to_i * 1000 %>,
              <% start_date = (Time.now - 30.days) %>
              <% end_date = Time.now %>
              pointStart: <%= start_date.to_i * 1000 %>,
              data: <%= (start_date.to_date..end_date.to_date).map {|date| Pv.find_by_ga_date(date).nil? ? 0 : Pv.find_by_ga_date(date).upv}.inspect%>
          }]
        });
$(".highcharts-legend").hide();
    });
</script>
<div id="project_upv_chart" style="width:560px; height:300px;"></div>
{% endcodeblock %}

在highcharts.js中修改几个需要汉化的地方，有月份，星期等。

在上面的代码中，如果是单一的数据曲线，希望隐藏图表下面的series选择按钮，可以加入``$(".highcharts-legend").hide();``