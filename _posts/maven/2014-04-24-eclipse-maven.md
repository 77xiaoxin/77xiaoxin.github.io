---
layout: posts
title: Eclipse集成Maven
category: Maven
---

##Eclipse集成Maven

一直以来用的是公司给的eclipse3.4，虽然里面svn、maven等等诸多插件都配置好了，可以直接使用，但是正因为集成了很多插件，运行速度是相当的慢，而且时不时的弹出个框告诉你32位的svn运行在64位的环境上，然后怎么怎么样......于是乎，我准备重新弄个eclipse集成maven，中间遇到许多问题，在此记录一下。

###准备
1. eclipse juno
2. maven3.0
3. svn插件

###集成

先安装m2eclipse插件，打开eclipse，选择help-->install new software，点击work with输入框右边的add按钮，在弹出框中name输入：m2eclipse，Location输入：http://download.eclipse.org/technology/m2e/releases，稍等之后会出来Maven Integration for Eclipse，选中，下一步，根据提示安装即可。

maven安装方法：将下载的maven解压到任意目录并配置好环境变量，在命令行下输入`mvn -v`后，打印出maven版本信息和系统信息，即为安装成功。

将svn插件解压开，复制plugin目录下的jar包到eclipse的plugin目录下即可完成svn插件的安装，还有其他安装方法，但是我觉得这个最简单。

m2eclipse插件安装完成后会提示重启eclipse，重启完成后在eclipse中选择window-->Preferences-->Installations，点击Add按钮，添加并选中安装好的maven。

###导入项目

把本地存在的maven项目导入到eclipse中，选择import--> maven--> Existing Maven Projects，然后根据提示即可导入。

####问题1

我的Maven项目本来是一个Web工程，但是eclipse识别不出来。

解决方法：选中项目，右键-->properties-->Project Facets-->Convert to faceted form...-->选中java 和 Dynamic Web Module，然后点击下方的Further configuration available...链接，Content directory中填写`src/main/webapp`，如果你是按maven标准来生成项目的话。下面的“generate web.xml deployment descriptor”不要选，因为我们导入的项目里已经有这个文件了。

####问题2

编译、打包都没有问题，部署到tomcat却运行不起来。

解决方法：发现部署到tomcat下的工程WEB-INF/lib目录为空，修改工程的.classpath文件：

	<classpathentry kind="con" path="org.eclipse.m2e.MAVEN2_CLASSPATH_CONTAINER">
		<attributes>
			<attribute name="maven.pomderived" value="true"/>
			<attribute name="org.eclipse.jst.component.dependency" value="/WEB-INF/lib"/>
		</attributes>
	</classpathentry>

####问题3

启动成功，打开页面报空指针异常：org.apache.jsp.index_jsp._jspInit(index_jsp.java:22)。

解决办法：这是因为项目中servlet-api.jar和jsp_api.jar与tomcat的jar包冲突，将pom.xml中对应这个两个jar包的代码注释掉，重新打包部署即可。