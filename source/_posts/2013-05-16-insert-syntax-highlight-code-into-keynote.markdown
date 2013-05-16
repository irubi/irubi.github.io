---
layout: post
title: "Insert syntax highlight code into keynote"
date: 2013-05-16 10:43
comments: true
categories: mac
---
为了做一个更加漂亮的技术类`keynote`，经常需要粘贴代码进去，但是一般情况下复制粘贴之后的代码会变成一段`plain text`,看起来特别不专业。
<!--more-->

这个问题解决思路是将一段代码`copy as rtf`，如果你去搜索的话，可以很快得到两种解决方案。
下面是在`sublime IDE`上实现的，首先`cmd +shift +p`输入`install`选择`install package`，在建立完索引之后输入`highlight`,这是一个`sublime`的`package`，安装。这个package是通过Pygments来实现将代码语法进行高亮显示转化的。

{% img center /images/posts/highlight.png %}

安装好之后可以在`Preferences > Package Settings > SublimeHighlight > Settings - User`文件里定义`Pygments`的主题:
{% codeblock lang:yaml %}

{
    "theme": "monokai",
    "linenos": "inline",
    "noclasses": true,
    "fontface": "Menlo"
}

{% endcodeblock %}

这里选取主题的意义在于对不同的Keynote背景，可能代码高亮之后显示效果不是很好，通过主题的定义可以更适应Keynote的背景色。
可选的主题包括：

`autumn`
`borland`
`bw`
`colorful`
`default`
`emacs`
`friendly`
`fruity`
`manni`
`monokai`
`murphy`
`native`
`pastie`
`perldoc`
`rrt`
`tango`
`trac`
`vim`
`vs`

可以通过[GENSHI](http://genshi.edgewall.org/prefs/pygments)这个网站来查看不同theme的样式。


其他的几个设置`key`的设置可以参考[文档](https://github.com/n1k0/SublimeHighlight)

再来定义一下复制到系统剪切板的快捷键, `Preferences > Key bindings - User`
{% codeblock lang:yaml %}
[
    { "keys": ["super+shift+c"], "command": "sublime_highlight", "args": { "target": "clipboard", "output_type": "rtf"}}
]
{% endcodeblock %}

定义之后可以通过`cmd` + `shift` + `c`来将选中的代码以`rtf`的格式复制到剪切板中了。

效果如下：

{% img center /images/posts/keynote_syntax_highlight.png %}

然后可以通过调整字体的大小来做进一步的美化工作。

当然复制到系统剪切板的内容也可以复制到其他的文字处理软件中，譬如`pages`,`word`,`ppt`等..

如果是用`textmate`的话，`textmate`有一个[`copy as rtf`](https://github.com/drnic/copy-as-rtf-tmbundle)的`bundle`，可以按照`github`上面给出的说明来安装和使用。