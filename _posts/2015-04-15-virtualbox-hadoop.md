---
layout: post
category: hadoop云计算
title: VirtualBox安装hadoop
tags: [hadoop]
---
{% include JB/setup %}

###安装虚拟机

系统选择CentOS6.5，最小化安装。

共安装4台虚拟机，值得一提的是网络的配置，使用两块网卡，第一块选网络地址转换（NAT），第二块选仅主机（host-only）。

虚拟机安装完成后，配置网络：

	# vi /etc/sysconfig/network-scripts/ifcfg-eth0

将ONBOOT改为yes。

	# vi /etc/sysconfig/network-scripts/ifcfg-eth1

将ONBOOT改为`yes`，将BOOTPROTO改为`static`，增加`IPADDR=192.168.56.xxx`。

修改完成后重启网卡：

	# /etc/init.d/network restart

修改主机名：

	# vi /etc/sysconfig/network

将HOSTNAME改为想要修改的主机名，重启后生效。

配置免密码登陆：

	# ssh-keygen -t rsa

然后连续回车。

	# ssh-copy-id -i /root/.ssh/id_rsa.pub root@hadoop1

然后回车，输入密码即可，其他机器做相同操作。

官方文档说需要`rsync`：

	# yum install rsync

###软件安装

首先，需要jdk，去oracle官网下载。然后解压：

	# tar -zxvf jdk-8u45-linux-x64.tar.gz
	# mv jdk1.8.0_45 /opt

到apache官网下载hadoop，然后解压：

	# tar -zxvf hadoop-2.6.0.tar.gz
	# mv hadoop-2.6.0 /opt

修改文件属主：

	# chown -R root:root jdk1.8.0_45
	# chown -R root:root hadoop-2.6.0

配置环境变量：

	export JAVA_HOME=/opt/jdk1.8.0_45
	export HADOOP_HOME=/opt/hadoop-2.6.0
	export PATH=$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH

使环境变量生效：

	# source /etc/profile

建立hadoop配置文件软链接：

	# mkdir /etc/hadoop
	# ln -s /opt/hadoop-2.6.0/etc/hadoop/ /etc/hadoop/conf

###hadoop配置

