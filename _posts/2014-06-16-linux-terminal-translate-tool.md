---
layout: post
category: linux
title: linux命令行下的翻译工具
tags: [linux]
---
{% include JB/setup %}

本人英语不好，经常能碰到不认识的单词，所以翻译工具属于我居家必备之良品。写这个小工具纯属心血来潮，没有太大作用，也就是有些时候，在服务器上查程序日志，发现日志里有那么一两个词儿不认识，⊙﹏⊙b汗，这个时候，可以用这个小工具简单快捷的查一下。后续准备加上个leveldb神马的，记录下来自己曾经都查过哪些单词，然后可以针对性的记忆它们。

原理其实很简单，用的是百度翻译API，至于为什么用百度，因为在墙内，百度是老大。API具体使用说明可以移步<a href="http://developer.baidu.com/wiki/index.php?title=%E5%B8%AE%E5%8A%A9%E6%96%87%E6%A1%A3%E9%A6%96%E9%A1%B5/%E7%99%BE%E5%BA%A6%E7%BF%BB%E8%AF%91API" target="_blank"> 这里 </a>查看。

python程序`translate.py`：

	#!/usr/bin/python
	import sys
	import urllib
	import urllib2
	import json
	
	url = 'http://openapi.baidu.com/public/2.0/bmt/translate'
	params = {'client_id': 'yourApikey', 'from': 'auto', 'to': 'auto', 'q': sys.argv[1]}
	request = urllib2.Request(url, data=urllib.urlencode(params))
	response = urllib2.urlopen(request).read()
	result = json.loads(response).get('trans_result')
	for e in result:
	    print e.get('dst')

想封装成命令，很简单，写个shell脚本`translate`：

	#!/bin/bash
	python /PATH/translate.py $1

然后把`translate`脚本添加到环境变量，或者在环境变量目录下建立`translate`的软连接即可。

使用方法：

	[root@big-data-2 ~]# translate today
	今天