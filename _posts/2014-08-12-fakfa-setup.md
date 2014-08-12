---
layout: post
category: hadoop
title: kafka搭建
tags: [hadoop, kafka]
---
{% include JB/setup %}

Step 1：下载最新执行包，kafka_2.9.2-0.8.1.1.tgz

	tar xzf kafka-<VERSION>.tgz
	cd kafka-<VERSION>

或者下载最新源代码包，kafka-0.8.0-src.tgz

	tar xzf kafka-<VERSION>.tgz
	cd kafka-<VERSION>
	./sbt update
	./sbt package
	./sbt assembly-package-dependency

Step 2：启动服务

zookeeper服务：

	bin/zookeeper-server-start.sh config/zookeeper.properties

如果使用集群已经安装好的zookeeper，需要配置`config/server.properties`，大致如下几项：

	broker.id=1
	port=9092
	host.name=bigdata101
	log.dirs=/tmp/kafka-logs
	zookeeper.connect=bigdata101:2181,bigdata102:2181,bigdata103:2181

Broker服务：

	bin/kafka-server-start.sh config/server.properties

Step 3：创建 topic

	bin/kafka-topics.sh --create --zookeeper localhost:2181 --topic test --partition 2 --replication-factor 3

检查是否创建成功

	bin/kafka-topics.sh --list --zookeeper localhost:2181

Step 4：消息生产者，推送消息

	bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test

Step 5：创建消费者

	bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic test --from-beginning

Step 6：测试

在生产者终端中输入消息，消费者终端能够接收，例如：

	This is a message
	This is another message