---
layout: archive
title: hadoop学习笔记——分布式安装
category: hadoop
---

##hadoop学习笔记——分布式安装

做hadoop也有一年的时间了，但是基本上总是在照葫芦画瓢，用的都是搭建好的环境，真正底层上的东西并不是很清楚，这段时间比较闲，准备系统的学习一下hadoop。

####准备

1. VirtualBox
2. Ubuntu
3. Xshell 4
4. Xftp 4


VirtualBox可以去Oracle官方网站上下载，Ubuntu的官网最近似乎不太稳定，访问速度比较慢，不过Ubuntu镜像随处可见，我用的是32位的12.04LTS版本。Xshell和Xftp系列软件相信接触过hadoop的人都比较熟悉了，安装的时候选择home/school选项，不需要在费劲去找注册码了，我感觉注册和不注册，使用起来基本没什么区别。


####安装虚拟机

我总共建立了4台虚拟设备，由于我的笔记本配置有限，每台虚拟机分配1颗CPU和1G内存，不知道这种配置的集群以后运行起来会不会非常慢，不过还是以学习为主。

在给虚拟机安装ubuntu的时候网卡记得要选择桥接网卡，这样的话虚拟机的ip地址和本机的ip地址处于同一ip段内，可以很方便的使用Xshell。安装ubuntu时没必要安装系统提示的一堆一堆的更新，太费时间，语言包也不需要更新了，我们以后只是通过Xshell连接上去做一些操作而已。

ubuntu安装完后，还不能通过Xshell进行连接，先从VirtualBox进入到虚拟机中，运行终端，执行命令

	sudo apt-get install ssh

安装好ssh即可通过Xshell进行连接了。

我总共安装了四台虚拟机，如下：

1. 设备名：NameNode  ip地址：192.168.1.108
2. 设备名：DataNode1  ip地址：192.168.1.109
3. 设备名：DataNode2  ip地址：192.168.1.110
4. 设备名：DataNode3  ip地址：192.168.1.111

####配置环境

部署hadoop需要的前提有两个，JDK和SSH免密码登陆。

JDK我从Oracle官网上下载了最新版本jdk1.7.0_51，安装过程就不多说了，注意在安装完记得配置环境变量，如果安装的Ubuntu本身是带有OpenJDK的，需要把系统默认的JDK改成刚安装的Sun的JDK。其实如果嫌麻烦，不安装Sun的JDK也不是不可以，之前我们公司的集群就一直使用的OpenJDK，直到最近才改过来，也没遇到什么问题，不过还是建议使用Sun官方的JDK，本身也麻烦不了多少，几行命令而已。

SSH免密码登陆，在安装完SSH后通过`ls -a /home/king/`查看是否有.ssh目录，如果没有，可以手动创建一个，然后执行如下命令：

	ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa

然后将id_dsa.pub（公钥）追加到授权的key中去。

	cat ~/.ssh/id_dsa.pub >> ~/.ssh/authorzied_keys

然后运行`ssh localhost`命令验证是否配置成功，第一次应该会需要输入yes，如果配置成功是不需要输入密码的。

现在只是可以免密码登陆到本机，还需要配置NameNode免密码登陆到DataNode，需要把NameNode的公钥内容追加到DataNode中的授权文件（~/.ssh/authorzied_keys）中即可。

此外，还需要配置/etc/hosts文件，将各设备的ip和设备名添加进去。

####部署hadoop

去apache官网下载hadoop，我下载的是hadoop-1.2.1版本。


