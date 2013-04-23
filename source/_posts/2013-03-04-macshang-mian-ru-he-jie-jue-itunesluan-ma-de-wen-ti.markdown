---
layout: post
title: "mac上面如何解决itunes乱码的问题"
date: 2013-03-04 09:25
comments: true
categories: mac 
---

使用mac的ituens导入音乐时会有乱码（尤其是中文歌曲）的问题，是因为mp3文件的id3标签编码的问题。
<!--more-->
搜索了一下有一个比较好的解决途径是下载id3mod，一个界面工具可以进行编码转换，但是免费版有一次转换的数量限制。所以采取一些开源的工具来解决这个问题：

 * [mp3tagiconv](http://code.google.com/p/mp3tagiconv/)是一款可用于解决MP3乱码问题的软件。它可以自动探测MP3标签的编码，并以合理的方式重新写入，经过其处理的编码可被Linux、Windows（包括Windows Media Player）和各种移动设备正确识别。
 * [Mutagen](http://code.google.com/p/mutagen/) is a Python module to handle audio metadata. It supports ASF, FLAC, M4A, Monkey's Audio, MP3, Musepack, Ogg FLAC, Ogg Speex, Ogg Theora, Ogg Vorbis, True Audio, WavPack and OptimFROG audio files. All versions of ID3v2 are supported, and all standard ID3v2.4 frames are parsed. It can read Xing headers to accurately calculate the bitrate and length of MP3s. ID3 and APEv2 tags can be edited regardless of audio format. It can also manipulate Ogg streams on an individual packet/page level.
 
 首先需要安装Mutagen，它是mp3tagiconv命令运行的必要环境
 
#### 安装过程：
 
* 下载到mutagen-1.20.tar.gz（可以在code google上下载最新版）

* 执行：

{% codeblock lang:bash %}
$ cd mutagen-1.20
$ ./setup.py build
$ sudo ./setup.py install
{% endcodeblock %}

* 下载mp3tagiconv_0.1.tar.gz

{% codeblock lang:bash %}
$ cd mp3tagiconv_0.1
$ ./mp3tagiconv test.mp3 #这条命令会要求确认所需要转换的信息是否正确，若无需确认，请参考下面一条命令
$ ./mp3tagiconv --do-update test.mp3
$ sudo cp mp3tagiconv /usr/bin
{% endcodeblock %}

* 下面是一段ruby的一段脚本，可以批量修改音乐文件的编码信息：
{% codeblock lang:bash %}
# encoding: utf-8
require 'pathname' 

dir = '/Volumes/HD/music/1212'

files = Dir.entries(dir)
reg = /mp3/
files.each do |filename|
  if filename.match(reg)
    file = dir + '/' + filename
	matched_str = reg.match(filename)
    new_name = file.gsub(/\s|\'|-/) {|m| '\\' + m}
	File.rename(file, file.strip)
    result = `yes | mp3tagiconv #{new_name}`
	puts result
  end
end
{% endcodeblock %}
