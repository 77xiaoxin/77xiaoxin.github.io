---
layout: post
category: ftp
title: ftp主动模式和被动模式
tags: [ftp]
---
{% include JB/setup %}

ftp在传输过程中其实建立了两种连接：控制连接和数据连接。控制连接用于传输命令，数据连接用于文件传输。

数据连接又分为两种传输模式：主动模式和被动模式。

PORT（主动）模式：客户端向服务器的FTP端口（默认是21）发送连接请求，服务器接受连接，建立一条命令链路。当需要传送数据时，客户端在命令链路上用PORT命令告诉服务器：“我打开了XXXX端口，你过来连接我”。于是服务器从20端口向客户端的XXXX端口发送连接请求，建立一条数据链路来传送数据。

PASV（被动）模式：客户端向服务器的FTP端口（默认是21）发送连接请求，服务器接受连接，建立一条命令链路。当需要传送数据时，服务器在命令链路上用PASV命令告诉客户端：“我打开了XXXX端口，你过来连接我”。于是客户端向服务器的XXXX端口发送连接请求，建立一条数据链路来传送数据。