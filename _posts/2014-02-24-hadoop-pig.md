---
layout: post
category: hadoop云计算
title: pig学习笔记
tags: [pig, hadoop]
---
{% include JB/setup %}


###环境搭建

有了hadoop环境，搭建pig环境就非常容易了，首先去Apache官网上下载Pig，注意查看官方说明，是否和现有hadoop版本兼容，我的hadoop版本是1.2.1，pig版本是0.12.0。

下载后解压到你喜欢的任意目录下，只要你觉得方便，均可。解压完成后将pig的安装路径配置到环境变量/etc/profile文件中：

	export PIG_INSTALL=/usr/local/pig
	export PATH=$PATH:$PIG_INSTALL/bin

运行`pig -help`查看输出，如果类似如下，则证明安装完成：

	Apache Pig version 0.12.0 (r1529718) 
	compiled Oct 07 2013, 12:20:14
	
	USAGE: Pig [options] [-] : Run interactively in grunt shell.
	       Pig [options] -e[xecute] cmd [cmd ...] : Run cmd(s).
	       Pig [options] [-f[ile]] file : Run cmds found in file.
		   ......

###Pig运行模式

pig有两种运行模式：Local模式和MapReduce模式。当pig在Local模式运行时，只访问本地一台主机，若运行在MapReduce模式下，将访问整个集群。

运行`pig -x local`将进入Local模式，如果直接运行`pig`将使用MapReduce模式，此时会唤起Grunt Shell，如果想要运行pig脚本文件，可以使用`pig script.pig`或者`pig -x local script.pig`即可。

###Pig Latin语言

#####运行

1. pig对所有语句的语法和语义进行确认。
2. 遇到dump或者store命令，开始顺序执行所有语句。

#####查看结果

1. 使用dump语句将结果打印到屏幕上。
2. 使用store语句将结果存储到文件

#####注释

1. 多行注释：/* ... ... */
2. 单行注释：--

#####大小写相关性

关系名、域名、函数名是区分大小写的，参数名、关键字不区分大小写。

#####数据模式

数据组织形式包括：关系（relation）、包（bag）、元组（tuple）、域（field）。

#####数据类型

- int   ---有符号32位整数
- long   ---有符号64位整数
- float   ---32位浮点型
- double   ---64位浮点型
- chararray   ---使用UTF-8形式编码的字符数组
- bytearray   ---字节数组
- tuple   ---有序的字段集
- bag   ---元组集合
- map   ---键值对集合

#####常用关键字

load：用作从文件系统中加载数据，语法如下：

	load 'data' [using function] [as schema]

store：用于将结果保存到文件中，语法如下：

	store alisa into 'directory' [using function]

foreach：作用是基于数据的列进行转换，通常与foreach...generate结合使用，语法如下：

	alias = foreach { gen_blk | nested_gen_blk } [as schema]

dump：用于将结果显示到屏幕上，语法如下：

	dump alias

describe：返回一个名称的模式，语法如下：

	describe alias

filter：经常用于对数据进行筛选，语法如下：

	alias = filter alias_0 by expression

###推荐阅读

网络上有大神整理了一些文章，写的非常详细，收藏起来，以供参阅。

<a target="_blank" href="http://www.codelast.com/?p=3621">Apache Pig的一些基础概念及用法总结（1）</a>

<a target="_blank" href="http://www.codelast.com/?p=4611">Apache Pig的一些基础概念及用法总结（2）</a>

<a target="_blank" href="http://www.codelast.com/?p=4249">Apache Pig中文教程（进阶）</a>

<a target="_blank" href="http://www.codelast.com/?p=4577">使用Apache Pig时应该注意/避免的操作或事项</a>


