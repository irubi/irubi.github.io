---
layout: post
title: "sublime IDE安装使用介绍"
date: 2012-03-10 11:45
comments: true
categories: IDE sublime
---

偶尔看到sublime这样一款类textmate IDE还是蛮兴奋的，一直觉得一款好的IDE应该简单，易用。学习成本不高。用的爽，看着爽是非常重要的，作为一个每天面对代码的人来说，为了让眼球保持舒爽一款decent的IDE是必不可少的。

而且sublime text2是一个跨平台的编辑器，光看截图就够诱惑的了。

价格： USD $59, 不过未注册可以正常使用所有功能，只是会不时的在保存文件时提示购买。
<!--more-->

1. [下载](http://www.sublimetext.com/)安装，因为GTW的原因，需要翻墙下载。
2. 在terminal中可以使用sublime text打开file或者folder进行编辑
{% codeblock lang:bash %}
sudo ln -s /Applications/Sublime\ Text\ 2.app/Contents/SharedSupport/bin/subl /usr/bin/st
{% endcodeblock %}
        
3. 以后就可以下面的命令来打开单个文件或者目录进行工作了。
{% codeblock lang:bash %}
folder > st filename.txt
folder > st .
{% endcodeblock %}
        
4. 开始使用

   * 可以试试这个新的[icon](http://www.sublimetext.com/forum/download/file.php?id=200)
     下载解压之后到/Applications/Sublime\ Text\ 2.app/Contents/Resources下替换原来的文件即可
   
   * shortcut:
   
<img src='/images/stshortcut.png'>
上面的几个方式可以组合使用，基本是2合1居多，譬如要跳转到home_contrller的index方法，可以用cmd t输入homectr@index
    
  * 多重选择
    * 按住cmd拖动鼠标选取，可以任意多重选取
    * 按住alt可以纵向选取一个区块
    * cmd d可以将与已经选择的内容相同的内容加入选择，或者ctrl+cmd+G可以一次性将与已选内容相同的内容加入选择。        
    
 * package control
   
   类似于textmate的bundle，sublimetext也有一个叫做package control的东西.
   在sublimetext中打开console，ctrl `输入下面的命令：
	{% codeblock lang:bash %}
import urllib2,os;pf='Package Control.sublime-package';ipp=sublime.installed_packages_path();os.makedirs(ipp) if not os.path.exists(ipp) else None;open(os.path.join(ipp,pf),'wb').write(urllib2.urlopen('http://sublime.wbond.net/'+pf.replace(' ','%20')).read())
	{% endcodeblock %}

  执行，然后重启sublimetext，会在preterences -> package settings中发现package control。
  
  * 代码对齐
  举例来说,有下面这样格式的代码：
{% codeblock lang:c %}
  var joe = 'joe';
var johnny = 'johnny';
var quaid = 'quaid';
{% endcodeblock %}
看起来会很不爽，因为第一行没有对齐，等号也没对齐。

下面需要安装一个自动对齐的package，用shift cmd p调出package control面板，输入install然后按回车，输入alignment进行安装，重启sublimetext然后选择之前的这段代码，按ctrl+cmd+a，第一次按会将第一行对齐，第二次会将等号对齐，第三次就不会有反应了。很棒的功能。

* 执行代码
   
   编写完代码可以执行cmd d来执行代码。

* 无干扰模式

  ctrl shift cmd f或者在view中选择Enter distraction free mode。
  可以在preferences -> settings-more -> distraction free -user中进行防干扰模式的设置：
{% codeblock lang:json %}
{
    "line_numbers": false,      //是否显示行号
    "gutter": false,            //是否显示边列
    "draw_centered": true,      //是否居中显示
    "wrap_width": 80,           //换行宽度(单位：字符)
    "word_wrap": true,          //是否自动换行
    "scroll_past_end": true     //滚动能否超过结尾
}
{% endcodeblock %}
    
* TextMate Bundle

只要将textmate的bundle直接拷贝到packages目录中就能用，包括textmate的色彩主题。

* sublimetext主题和几个好用的插件
看下面两张展示图:

{% img http://buymeasoda.github.com/soda-theme/images/screenshots/soda-2-light-theme.png?v=4 %}

{% img http://buymeasoda.github.com/soda-theme/images/screenshots/soda-2-dark-theme.png?v=2 %}

  * 安装

    打开`sublime text2 -> preferences -> broswer packages`, 在终端进入该目录， 将主题的内容放进去.
{% codeblock lang:bash %}
git clone https://github.com/buymeasoda/soda-theme/ "Theme - Soda"
{% endcodeblock %}
打开`Sublime Text 2 -> Preferences -> Settings - User`,
{% codeblock lang:bash %}
{
  "theme": "Soda Dark.sublime-theme",
  "soda_classic_tabs": true
}
{% endcodeblock %}

如果在保存文件时发生错误，应该是语法问题，逗号没有写对之类的。

Soda还有两个语法高亮颜色的配置方案，将`colour-schemes.zip`解压之后得到的两个`*.tmtheme`放到`'Packages/User'`目录下
选择`Preferences-> Color Scheme -> User -> **soda..`

#####参考文章：#####

[快捷键和几个plugins](https://github.com/neilsarkar/sublime_user_directory)

[top 10 plugins](http://www.henriquebarroso.com/my-top-10sublime-2-plugins/)

    

打开10000行以上的代码文件也不会像textmate一样卡卡卡之后就crash，之前必须得用xcode来打开，编辑，然后保存。现在用sublimetext不会有这种情况了，打开很快不会卡。


标签什么的有点chrome的感觉，很流畅，很舒服，也没有什么很华丽的让人目眩的东西。而且现在试用没有时间限制，只是在保存的时候会弹出一个让你购买的提示。

