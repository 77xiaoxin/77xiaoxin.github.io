---
layout: post
category: python编程
title: 把web.py应用部署到Apache下
tags: [python, apache]
---
{% include JB/setup %}

python是一门强大的语言，在web方面也有不错的表现，其中web.py是一个非常轻量级并且非常好用的框架，可以快速的实现一些小页面工具。不过听说web.py的创始人挂掉了，不知道会不会影响到web.py的发展前景，额，算了，还是不八卦这个了。

###准备

1. 下载<a href="http://webpy.org/static/web.py-0.37.tar.gz" target="_blank">web.py</a>。
2. 下载mod_wsgi。

###开发

首先安装web.py：

	tar -zxvf web.py-0.37.tar.gz
	cd web.py-0.37
	python setup.py install

注意可能需要root权限才能进行上述操作。

接下来写一个web应用：

	import web

	urls = ("/.*", "hello")

	class hello:
	    def GET(self):
	        return 'Hello, world!'

	app = web.application(urls, globals()).wsgifunc()

虽然只有这么几行，但是这确实是一个完整的web应用，保存为`index.py`。

web.py框架的使用就不再多说了，可以参考web.py的<a href="http://webpy.org/cookbook/index.zh-cn" target="_blank">Cookbook</a>。

###部署

首先，安装Apache，大多linux发行版都带有Apache，但是往往会存在一些版本不兼容的问题，可以先卸载掉，然后重新安装：

	yum remove httpd
	yum install httpd httpd-devel

然后安装mod_wsgi：

	tar -zxvf mod_wsgi-3.4.tar.gz
	cd mod_wsgi-3.4
	./configure
	make
	make install

注意，如果是使用linux发行版自带的Apache，那么很有可能会安装失败，报错信息提示`apxs: command not found`，这时候需要安装httpd-devel包：

	yum install httpd-devel

我这里遇到一个错误：`error: Python.h: No such file or directory`,需要安装Python-devel包：

	yum install python-devel

一切安装成功后，配置`httpd.conf`文件：

	LoadModule wsgi_module modules/mod_wsgi.so
	WSGIScriptAlias /hello /var/www/hello/index.py/
	Alias /hello/static /var/www/hello/static/
	AddType text/html .py
	
	<Directory /var/www/webpy-app/>
	    Order deny,allow
	    Allow from all
	</Directory>

其中`/hello/static`是静态文件的目录，配置完成后启动Apache：

	/etc/init.d/httpd start

最后，用浏览器访问`http://localhost/hello/`即可看到helloworld页面。

