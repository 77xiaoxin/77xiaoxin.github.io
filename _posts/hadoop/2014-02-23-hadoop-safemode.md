---
layout: posts
title: hadoop学习笔记——安全模式
category: hadoop
---

##hadoop学习笔记——安全模式

在分布式文件系统启动的时候，开始的时候会有安全模式，当分布式文件系统处于安全模式的情况下，文件系统中的内容不允许修改也不允许删除，直到安全模式结束。

安全模式主要是为了系统启动的时候检查各个DataNode上数据块的有效性，同时根据策略必要的复制或者删除部分数据块。如果有必要，可以强制离开安全模式，也可以进入安全模式。使用如下命令即可：

	hadoop dfsadmin-safemode 命令
 
	格式：Usage: java DFSAdmin [-safemode enter | leave | get |wait]
	用户可以通过dfsadmin -safemode value 来操作安全模式，参数value的说明如下：
	enter - 进入安全模式
	leave - 强制NameNode离开安全模式
	get   - 返回安全模式是否开启的信息
	wait  - 等待，一直到安全模式结束。
