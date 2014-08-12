---
layout: post
category: hadoop
title: haproxy使用小结
tags: [hadoop, haproxy]
---
{% include JB/setup %}

##安装

方法一：

	wget http://haproxy.1wt.eu/download/1.3/src/haproxy-1.3.20.tar.gz
	tar zcvf haproxy-1.3.20.tar.gz
	cd haproxy-1.3.20
	make TARGET=linux26 PREFIX=/usr/local/haproxy
	make install PREFIX=/usr/local/haproxy

方法二：

	yum install haproxy

##配置

我采用方法二安装，配置文件位置：`/etc/haproxy/haproxy.cfg`，另外，可以通过service方式启动服务。

部分配置如下：

	global
		log			127.0.0.1 local2
		chroot		/var/lib/haproxy
		pidfile		/var/run/haproxy.pid
		maxconn		4000
		user		haproxy
		group		haproxy
		daemon
	
	defaults
		mode		tcp		#{ tcp|http|health }，tcp是4层，http是7层，health只会返回OK
		log			global	#继承全局配置
		option		tcplog	#采用tcp日志格式
		retries		3		#三次连接失败即判定服务不可用
	
	frontend main
		bind		172.18.108.101:9090
		default_backend		app
	
	backend app
		balance	roundrobin		#{ roundrobin|source|uri|url_param|leastconn } roundrobin为轮询，source对ip进行hash匹配，uri对URI进行hash匹配，url_param对URL中的参数进行hash匹配，leastconn选择连接较少的
		server	app1	172.18.108.101:9095	check
		server	app2	172.18.108.101:9093	check