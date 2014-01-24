---
layout: archive
title: PostgreSQL配置远程连接
category: nosql
---

##PostgreSQL配置远程连接

配置远程连接PostgreSQL，有以下两个文件（两个文件均在数据库目录下）：

1、postgresql.conf文件

	listen_addresses = '*'
	port = 5432

2、pg_hba.conf文件

	# IPv4 local connections:
	host    all    all    192.168.0.2/32    trust

配置完成需要重启

	/usr/local/pgsql/bin/pg_ctl restart -D /data/disk10/data/pg_data_2

注意：一定要加上-D参数。

然后使用pg_hba.conf中配置的主机连接pgsql即可

	psql -h 192.168.0.1 -U postgres -d postgres