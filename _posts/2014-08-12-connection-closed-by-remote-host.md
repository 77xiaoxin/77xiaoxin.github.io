---
layout: post
category: linux
title: ssh_exchange_identification: Connection closed by remote host解决办法
tags: [linux]
---
{% include JB/setup %}

如题，故障表述很清楚：链接被远程主机关闭。

在linux中有三个和host相关的文件：`/etc/hosts`、`/etc/hosts.allow`和`/etc/hosts.deny`

该故障出现在`/etc/hosts.deny`上，多数情况下，是因为发起ssh链接的主机ip被添加到了这个文件中，只要修改这个文件，即可解决。

但是，往往修改了这个文件，过不了多久，又出现了这个错误，这是因为`denyhosts`服务在干坏事，可以将其关闭：

	/etc/init.d/denyhosts stop

DenyHosts是Python语言写的一个程序，它会分析sshd的日志文件（/var/log/secure），当发现重复的攻击时就会记录IP到/etc/hosts.deny文件，从而达到自动屏IP的功能。

虽然这个程序有点弱智了，但是从安全角度来讲，终归是起到了一些作用的，所以，请自行权衡利弊。