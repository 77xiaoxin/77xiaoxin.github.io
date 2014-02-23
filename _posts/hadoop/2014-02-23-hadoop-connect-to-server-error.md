---
layout: archive
title: ipc.Client: Retrying connect to server: NameNode/192.168.1.108:9000错误
category: hadoop
---

##ipc.Client: Retrying connect to server: NameNode/192.168.1.108:9000错误

错误现象：

1. 用`jps`命令查看，找不到namenode进程。
2. 手动启动namenode失败。
3. 使用`hadoop fs -ls`命令即报此错误。
4. 需将namenode重新格式化后，才能恢复使用。

原因：

hadoop集群安装完毕后，默认会将一些临时文件放在/tmp目录下，设备重新启动后，/tmp目录下的文件被删除，导致namenode启动不起来，报错。

解决方法：

修改conf/core-site.xml配置：


	<?xml version="1.0"?>
	<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

	<!-- Put site-specific property overrides in this file. -->

	<configuration>
		<property>
			<name>fs.default.name</name>
			<value>hdfs://NameNode:9000</value>
		</property>
		<property>
			<name>hadoop.tmp.dir</name>
			<value>/var/log/hadoop</value> <!--修改此路径-->
		</property>
	</configuration>

