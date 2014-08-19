---
layout: post
category: java
title: CommunicationsException异常处理
tags: [java, mysql]
---
{% include JB/setup %}

异常信息为如下：

	com.mysql.jdbc.exceptions.jdbc4.CommunicationsException: The last packet successfully received from the server was 84,071,632 milliseconds ago.  The last packet sent successfully to the server was 84,071,632 milliseconds ago. is longer than the server configured value of 'wait_timeout'. You should consider either expiring and/or testing connection validity before use in your application, increasing the server configured values for client timeouts, or using the Connector/J connection property 'autoReconnect=true' to avoid this problem.

还真是很少见到异常提示信息有这么长的。

用我英语负四级的水平，努力看懂了大概意思，意思是说让我设置一下连接超时时间，或者把autoReconnect属性设为true。

然后我把配置从这样：

	<beans:bean class="org.apache.commons.dbcp.BasicDataSource">
		<beans:property name="driverClassName" value="com.mysql.jdbc.Driver" />
		<beans:property name="url" value="jdbc:mysql://10.0.69.74:3306/ftpd" />
		<beans:property name="username" value="root" />
		<beans:property name="password" value="bigdata" />
	</beans:bean>

改成了这样：

	<beans:bean class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
		<beans:property name="driverClassName" value="com.mysql.jdbc.Driver" />
		<beans:property name="url" value="jdbc:mysql://10.0.69.74:3306/ftpd" />
		<beans:property name="username" value="root" />
		<beans:property name="password" value="bigdata" />
		<beans:property name="initialSize" value="5" /><!-- 初始化连接数量 -->
		<beans:property name="maxActive" value="30" /><!--最大连接数量 -->
		<beans:property name="maxIdle" value="10" /><!--最大空闲连接 -->
		<beans:property name="minIdle" value="5" /><!--最小空闲连接 -->
		<beans:property name="removeAbandoned" value="true" /><!--是否自动回收超时连接 -->
		<beans:property name="removeAbandonedTimeout" value="60" /><!--超时时间(以秒数为单位) -->
		<beans:property name="maxWait" value="6000" /><!-- 超时等待时间以毫秒为单位 -->
	</beans:bean>

观察了好久，再没出过这问题了。