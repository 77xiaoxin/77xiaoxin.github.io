---
layout: post
category: ftp
title: Apache FtpServer使用小结
tags: [ftp]
---
{% include JB/setup %}

最近要收集各省公司的数据，以前的解决方案是找一台设备搭vsftpd，由于各省公司的数据量都很大，计划给每个省公司搭一台ftp，数据量小的，可以两个省公司共用一台ftp。算下来，要搭将近30台ftp服务器，我不是专业搞系统运维的，这一台一台的搭下去，瞬间感觉人生不美好了。

后来调研了Apache FtpServer，官网地址：http://mina.apache.org/ftpserver-project/index.html。

这是一个用java语言开发的ftp服务器。功能齐全，采用事件模型，用户可以自定义事件通知(ftplet)。使用方便，下载解压即用，配置也很简单。

配置文件：

	<?xml version="1.0" encoding="UTF-8"?>
	<server xmlns="http://mina.apache.org/ftpserver/spring/v1"
		xmlns:beans="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xsi:schemaLocation="
		   http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-2.5.xsd 
		   http://mina.apache.org/ftpserver/spring/v1 http://mina.apache.org/ftpserver/ftpserver-1.0.xsd	
		   "
		id="ftpServer" anon-enabled="false" max-logins="100" >
		<listeners>
			<nio-listener name="default" port="2121" implicit-ssl="false">
				<ssl>
					<keystore file="./res/CA/kserver.keystore" password="bigdata" />
				</ssl>
				<data-connection>
                        <active enabled="true" local-address="172.18.108.101" local-port="2323" ip-check="true" />
                        <passive ports="1123-1133" address="172.18.108.101" external-address="124.127.188.106" />
                </data-connection>
			</nio-listener>
			<nio-listener name="default-ssl" port="2222" implicit-ssl="true" local-address="172.18.108.104">
				<ssl>
					<keystore file="./res/CA/kserver.keystore" password="bigdata" />
					<truststore file="./res/CA/tserver.keystore" password="bigdata" />
				</ssl>
				<data-connection implicit-ssl="true">
					<active enabled="true" local-address="172.18.108.104" local-port="2323" ip-check="true" />
					<passive ports="1123-1133" address="172.18.108.101" external-address="124.127.188.106" />
				</data-connection>
				<!--blacklist>1.2.3.0/16, 1.2.4.0/16, 1.2.3.4</blacklist-->
			</nio-listener>
		</listeners>
		<ftplets>
			<ftplet name="ftplet">
				<beans:bean class="cn.ctyun.ftpserver.ftplet.impl.Ftplet"></beans:bean>
			</ftplet>
		</ftplets>
		<!-- The user manager, choose one -->
		<file-user-manager file="./res/conf/users.properties" encrypt-passwords="true" />
		<!--<db-user-manager>
			<data-source>
				<beans:bean class="some.datasoure.class" />
			</data-source>
			<insert-user>INSERT INTO FTP_USER (userid, userpassword,
				homedirectory, enableflag, writepermission, idletime, uploadrate,
				downloadrate) VALUES ('{userid}', '{userpassword}',
				'{homedirectory}',
				{enableflag}, {writepermission}, {idletime},
				{uploadrate},
				{downloadrate})
			</insert-user>
				<update-user>UPDATE FTP_USER SET
					userpassword='{userpassword}',homedirectory='{homedirectory}',enableflag={enableflag},writepermission={writepermission},idletime={idletime},uploadrate={uploadrate},downloadrate={downloadrate}
					WHERE userid='{userid}'
			</update-user>
				<delete-user>DELETE FROM FTP_USER WHERE userid = '{userid}'
			</delete-user>
				<select-user>SELECT userid, userpassword, homedirectory,
					enableflag, writepermission, idletime, uploadrate, downloadrate,
					maxloginnumber, maxloginperip FROM
					FTP_USER WHERE userid = '{userid}'
			</select-user>
				<select-all-users>SELECT userid FROM FTP_USER ORDER BY userid
			</select-all-users>
				<is-admin>SELECT userid FROM FTP_USER WHERE userid='{userid}'
					AND
					userid='admin'
			</is-admin>
				<authenticate>SELECT userpassword from FTP_USER WHERE
					userid='{userid}'</authenticate>
		</db-user-manager> -->
		<!-- The file system -->
		<native-filesystem case-insensitive="false" create-home="true" />
		<!--
			Use this section to define custom commands. Custom commands can also
			override already existing commands
		-->
		<!--commands use-default="false">
			<command name="MYHELP">
				<beans:bean class="org.apache.ftpserver.examples.MYHELP" />
			</command>
		</commands-->
		<!-- Define the available languages -->
		<!--messages languages="se, no ,da" /-->
	</server>

使用Java自带的keytool命令，生成key：

1、生成服务器端私钥kserver.keystore文件

	keytool -genkey -alias serverkey -validity 365 -keystore kserver.keystore

2、根据私钥，导出服务器端安全证书

	keytool -export -alias serverkey -keystore kserver.keystore -file server.crt 

3、将服务器端证书，导入到客户端的Trust KeyStore中

	keytool -import -alias serverkey -file server.crt -keystore tclient.keystore

4、生成客户端私钥kclient.keystore文件

	keytool -genkey -alias clientkey -validity 365  -keystore kclient.keystore

5、根据私钥，导出客户端安全证书

	keytool -export -alias clientkey -keystore kclient.keystore -file client.crt

6、将客户端证书，导入到服务器端的Trust KeyStore中

	keytool -import -alias clientkey -file client.crt -keystore tserver.keystore

生成的文件分成两组，服务器端保存：kserver.keystore和tserver.keystore，客户端保存：kclient.keystore和tclient.kyestore。

自定义事件通知（ftplet）：

具体做法是继承DefaultFtplet类，该类在ftplet-api工程下，或者在%apache-ftpserver%/common/lib目录下可以找到ftplet-api的jar包，添加到工程classpath下即可。示例代码：

	package cn.ctyun.ftpserver.ftplet.impl;
	
	import java.io.IOException;
	
	import org.apache.ftpserver.ftplet.DefaultFtplet;
	import org.apache.ftpserver.ftplet.FtpException;
	import org.apache.ftpserver.ftplet.FtpRequest;
	import org.apache.ftpserver.ftplet.FtpSession;
	import org.apache.ftpserver.ftplet.FtpletResult;
	
	public class Ftplet extends DefaultFtplet {
	
		@Override
		public FtpletResult onConnect(FtpSession session) throws FtpException,
				IOException {
			// TODO Auto-generated method stub
			System.out.println("建立新连接：" + session.getClientAddress());
			return super.onConnect(session);
		}
		
		@Override
		public FtpletResult onDisconnect(FtpSession session) throws FtpException,
				IOException {
			// TODO Auto-generated method stub
			System.out.println("连接关闭：" + session.getClientAddress());
			return super.onDisconnect(session);
		}
	}

完成自定义事件，将工程打包，放到%apache-ftpserver%/common/lib目录下即可。