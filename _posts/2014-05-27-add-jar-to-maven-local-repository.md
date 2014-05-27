---
layout: post
category: 开发环境
title: 手动添加jar包到Maven本地仓库
tags: [maven]
---
{% include JB/setup %}


本来想利用Github Pages功能建个Maven远程仓库，后来想了想，有点占便宜没够的嫌疑，还是算了，况且Github是国外的网站，国内访问毕竟不是特别稳定。

先推荐个网站：<a target="_blank" href="http://mvnrepository.com/">http://mvnrepository.com/</a>.

有的时候会碰到依赖的jar包在本地仓库和远程仓库都没有，下载半天又下载不下来的情况，这时候，可以登录上面的mvnrepository网站，在搜索框中键入jar包的关键字，然后从搜索结果中选择正确的jar包和对应的版本，这个时候会出现下载链接和maven构建信息。

接下来，可以利用迅雷等工具，将jar包下载下来，然后根据给出的maven构建信息，将jar包安装到本地仓库去，使用下面的命令：

	mvn install:install-file -Dfile=xxx.jar -DgroupId=groupId -DartifactId=artifactId -Dversion=version -Dpackaging=jar

