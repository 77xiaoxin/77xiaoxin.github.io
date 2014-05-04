---
layout: post
category: hadoop云计算
title: Flume-NG自定义拦截器
tags: [flume, 拦截器]
---
{% include JB/setup %}

---
layout: posts
title: Flume NG自定义拦截器
category: hadoop
---

##Flume-NG自定义拦截器

Flume NG是一个分布式、可靠的日志收集、聚合系统，最早由cloudera开发，作为之前Flume OG的一个分支，更加简单，体积更加小，更容易部署。

之前一直使用的是flume自带的TimestampInterceptor来按天分割日志，但是最近公司服务器时钟同步出了问题，导致收集上来的日志总是会包含一部分前一天的日志，或者后一天的日志，在对日志进行计算时，如果剔除掉，会造成数据不够准确，如果不剔除，又比较麻烦，会出现各种问题，所以想自定义拦截器，取出日志中的时间当做Header中的时间戳，不再以服务器时间作为时间戳，这样，就可以解决了。

首先，新建一个工程，引入所需jar包，必要的jar包有以下三个：

1. flume-ng-configuration-1.4.0-cdh4.4.0.jar
2. flume-ng-core-1.4.0-cdh4.4.0.jar
3. flume-ng-sdk-1.4.0-cdf4.4.0.jar

这些jar包在flume的安装目录lib文件夹下可以找到。

新建一个类，实现Interceptor接口，会要求实现四个方法：

1. close方法
2. initialize方法
3. intercept方法（单个事件）
4. intercept方法（事件列表，重载）

在该类中新建内部类，实现Interceptor.Builder接口，会要求实现两个方法：

1. configure方法
2. build方法

完整代码如下：

	package cn.bidlink.flume.interceptors;
	
	import java.io.FileInputStream;
	import java.io.FileNotFoundException;
	import java.io.IOException;
	import java.io.InputStream;
	import java.text.DateFormat;
	import java.text.ParseException;
	import java.text.SimpleDateFormat;
	import java.util.ArrayList;
	import java.util.Date;
	import java.util.List;
	import java.util.Map;
	import java.util.Properties;
	import java.util.regex.Matcher;
	import java.util.regex.Pattern;
	
	import org.apache.flume.Context;
	import org.apache.flume.Event;
	import org.apache.flume.event.EventBuilder;
	import org.apache.flume.interceptor.Interceptor;
	
	/**
	 * 自定义的flume拦截器
	 * 用于对日志进行简单筛选和清洗，形成结构化数据。
	 * 使用日志中实际时间戳替代当前系统时间戳，解决时间不同步的问题。
	 * @author k
	 *
	 */
	public class LogFormatInterceptor implements Interceptor {
		
		private String conf;
		private Pattern pattern;
		private String[] keys;
		private DateFormat parse;
		private DateFormat format;
		
		public LogFormatInterceptor(String conf) {
			// TODO Auto-generated constructor stub
			this.conf = conf;
		}
	
		@Override
		public void close() {
			// TODO Auto-generated method stub
			
		}
	
		@Override
		public void initialize() {
			// TODO Auto-generated method stub
			try {
				//加载配置文件
				InputStream in = new FileInputStream(conf);
				Properties prop= new Properties();
				prop.load(in);
				//读取内容
				this.pattern = Pattern.compile(prop.getProperty("regex"));
				this.keys = prop.getProperty("keys").split(",");
				this.parse = new SimpleDateFormat(prop.getProperty("dateFormat"));
				this.format = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
				//关闭资源
				in.close();
			} catch (FileNotFoundException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			} catch (IOException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
	
		@Override
		public Event intercept(Event event) {
			// TODO Auto-generated method stub
			//得到body转换成字符串进行正则匹配
			Matcher m = this.pattern.matcher(new String(event.getBody()));
			if (m.find() && m.groupCount() == this.keys.length) {
				Map<String, String> header = event.getHeaders();
				StringBuilder builder = new StringBuilder();
				for (int i = 0; i < keys.length - 1; i ++) {
					if ("date".equals(keys[i])) {
						//得到日志中的时间戳，设置到header中，并格式化时间输出。
						try {
							Date logDate = this.parse.parse(m.group(i + 1));
							header.put("timestamp", Long.toString(logDate.getTime()));
							builder.append(this.format.format(logDate) + "\t");
						} catch (ParseException e) {
							// TODO Auto-generated catch block
							//如果日志中时间戳解析失败，返回null
							System.out.println(e.getMessage());
							return null;
						}
					} else {
						builder.append(m.group(i + 1) + "\t");//将每一组匹配结果进行拼接
					}
				}
				builder.append(m.group(this.keys.length));//最后一组
				Event e = EventBuilder.withBody(builder.toString().getBytes(), header);
				return e;
			}
			return null;
		}
	
		@Override
		public List<Event> intercept(List<Event> events) {
			// TODO Auto-generated method stub
			List<Event> list = new ArrayList<Event>();
			for (Event event : events) {
				Event e = intercept(event);
				if (e != null) {
					list.add(e);
				}
			}
			return list;
		}
		
		public static class Builder implements Interceptor.Builder {
			
			private String conf;
	
			@Override
			public void configure(Context context) {
				// TODO Auto-generated method stub
				this.conf = context.getString("conf");
			}
	
			@Override
			public Interceptor build() {
				// TODO Auto-generated method stub
				return new LogFormatInterceptor(this.conf);
			}
		}
	}

ok，现在把工程打包为jar文件，放到flume部署目录下的lib目录下，然后编写flume配置文件：

	agent.sources = ebnewSrc
	agent.channels = ebnewChannel
	agent.sinks = ebnewSink
	
	agent.sources.ebnewSrc.type = avro
	agent.sources.ebnewSrc.bind = big-data-2
	agent.sources.ebnewSrc.port = 55001
	agent.sources.ebnewSrc.channels = ebnewChannel
	agent.sources.ebnewSrc.interceptors = logformat
	agent.sources.ebnewSrc.interceptors.logformat.type = 
	cn.bidlink.flume.interceptors.LogFormatInterceptor$Builder
	agent.sources.ebnewSrc.interceptors.logformat.conf = 
	/app/programs/flumeConf/ebnew.properties
	
	agent.channels.ebnewChannel.type = memory
	agent.channels.ebnewChannel.keep-alive = 10
	agent.channels.ebnewChannel.capacity = 100000
	agent.channels.ebnewChannel.transactionCapacity =100
	
	agent.sinks.ebnewSink.type = hdfs
	agent.sinks.ebnewSink.hdfs.path = hdfs://big-data-1:8020/flume/ebnew/%Y%m%d
	agent.sinks.ebnewSink.hdfs.round = true
	agent.sinks.ebnewSink.hdfs.roundValue = 24
	agent.sinks.ebnewSink.hdfs.roundUnit = hour
	agent.sinks.ebnewSink.hdfs.writeFormat = Text
	agent.sinks.ebnewSink.hdfs.fileType = DataStream
	agent.sinks.ebnewSink.hdfs.batchSize = 100
	agent.sinks.ebnewSink.hdfs.idleTimeout = 3600
	agent.sinks.ebnewSink.hdfs.rollInterval = 0
	agent.sinks.ebnewSink.hdfs.rollSize = 0
	agent.sinks.ebnewSink.hdfs.rollCount = 0
	agent.sinks.ebnewSink.hdfs.minBlockReplicas = 0
	agent.sinks.ebnewSink.channel = ebnewChannel

注意自定义拦截器的全称要书写正确。

启动flume-ng，搞定！

参考文档：http://flume.apache.org/documentation.html