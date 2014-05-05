---
layout: post
category: 开发环境
title: 利用Github和jekyll bootstrap搭建博客
tags: [github, jekyll]
---
{% include JB/setup %}

本站最初搭建在SAE上，2014年初迁移到Github上，在Github上搭建博客有很多好处，比如：不限空间流量，完全免费，使用Git作为版本控制工具，可以绑定自己的域名等等，这两天有个朋友问我怎么在Github上搭建博客，我努力回想了一下当初搭建的过程，在此记录一下。

###准备

1. 下载git的windows版本。
2. 下载ruby语言环境和DevKit。

###安装

首先安装git，十分简单，按照提示一步一步来就OK，可以选择一下在右键菜单增加Git Bash Here项，Git Bash也可以代替cmd使用，并且比cmd好用很多，十分方便。

然后安装ruby，安装时勾选环境变量，安装完成会自动配置好环境变量，在cmd中运行`ruby -v`查看是否安装成功。

ruby安装完成后，接下来安装DevKit，双击DevKit，选择一个目录展开，运行cmd，切换到DevKit的展开目录，依次运行如下命令：

	ruby dk.rb init
	ruby dk.rb install

等待命令运行结束，运行如下命令：

	gem install jekyll

这个安装时间比较长，需要等待一段时间，可以先将jekyll bootstrap的代码clone到本地：

	git clone https://github.com/plusjade/jekyll-bootstrap.git

等待jekyll安装完成，运行`jekyll -v`，如果能正常打印出jekyll版本信息，即为安装成功。

切换目录到刚刚clone的jekyll-bootstrap下，运行`jekyll server`命令，不出现异常的话，即可通过浏览器访问http://localhost:4000查看。

至此，本地环境搭建结束，可以根据自己个人喜好，对jekyll bootstrap的界面做一定的修改，需要用到jekyll语法的部分，这里不做描述，请自己google之。

###编写文章

推荐一款软件：<a href="http://www.markdownpad.com/download.html" target="_blank">MarkdownPad 2</a>，可以助你轻松编写markdown语法的文件。这个软件免费提供的功能已经基本够用，无特殊需求，无需付费注册。

有了上面的环境，可以写一篇文章试一下，打开jekyll-bootstrap\_posts文件夹下的示例文章，在其上面略做修改，然后另存为新文件即可，文件命名遵守`yyyy-MM-dd-example.md`格式，例如:2014-05-05-github-jekyllbootstrap.md。markdown语法在此不做介绍，网上教程很多。

将新文件存储在_posts文件夹下，然后切换到jekyll-bootstrap目录，运行`jekyll server`命令，打开http://localhost:4000进行预览。


###部署

把jekyll bootstrap改造成自己看着顺眼的样子以后，就可以把它部署到Github上了。

首先你应该拥有github账户，然后创建一个新分支，命名为username.github.io，username是你的github账户名，然后将分支clone到本地：

	git clone https://github.com/username/username.github.io

将jekyll-bootstrap目录下的所有文件（除.git文件夹）拷贝到username.github.io文件夹下，然后依次运行如下命令：

	git add --all
	git commit -m 'Initial commit'

然后将本地内容push到github：

	git push origin master

完成后，就可以通过访问http://username.github.io查看自己的博客了，第一次部署可能需要几分钟之后才能看到。详细步骤可参看https://pages.github.com/。

如果你觉得username.github.io这个域名不够酷，可以绑定自己的域名。

首先在网站的根目录添加一个名为CNAME的文件，将域名写进去，如：sep10.com。

然后去域名提供商处配置域名解析：

	A		192.30.252.153
    A		192.30.252.154
	CNAME	username.github.io

在github上搭建博客的过程大致如此，如有疑问可以发邮件给我，我将尽力解答。