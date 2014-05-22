---
layout: post
category: hadoop云计算
title: 将pig的计算结果存入MySQL
tags: [pig, hadoop]
---
{% include JB/setup %}

把MapReduce的计算结果存入关系型数据库是一件很常见的事情，很早以前的做法是将MapReduce的结果从HDFS中get到本地，然后若干part-r-xxxxx合并成一个文件，接下来调用：

	mysql -e "load data local infile 'filePath' into table tableName fields terminated by '\t' lines terminated by '\n';"

`load`语句的执行效率倒是还不错的，不过这么做有很多问题，比如：执行上面命令的服务器上没有安装MySQL的客户端，咋办？数据从HDFS中get到本地，Load到MySQL以后，还有很多清理工作。

再后来就用到了Sqoop，省去了从HDFS中get到本地以及后续清理的工作：

	sqoop export --connect jdbc:mysql://localhost:3306/test --table wordcount --export-dir /test/wordcount.out --fields-terminated-by '\t' --username root

可是偏偏有一些任务，计算结果只有1行，或者几行，至多几十行，用上面的方法总觉得有些不值当的，就想让Pig计算的结果直接存到MySQL中。在piggybank包中有一个DBStorage类，正好可以满足要求。

首先，要在MySQL中创建一张表：

	create table wordcount (word varchar(100), count int);

需要用到两个jar包：

1. mysql-connector-java-5.1.21.jar
2. piggybank.jar（在pig安装lib下有）

用法示例：

	register piggybank.jar
	register mysql-connector-java-5.1.21.jar
	
	A = load '/test/wordcount.txt' AS (line: chararray);
	B = foreach A generate FLATTEN(TOKENIZE(line)) as word;
	C = group B by word;
	D = foreach C generate group as word, COUNT(B) as count;
	store D into 'wordcount' using org.apache.pig.piggybank.storage.DBStorage('com.mysql.jdbc.Driver', 'jdbc:mysql://localhost:3306/test?user=root&password=root', 'insert into wordcount(word, count) values (?, ?)');

在网上看到有人说这种方式会有陷阱，但是没说明是什么陷阱，⊙﹏⊙b汗。不过我在使用过程中尚未遇到问题，大家要是发现了问题，请不吝赐教。