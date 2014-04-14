---
layout: posts
title: RabbitMQ-Java-client使用
category: java
---

##RabbitMQ-Java-client使用

rabbitmq是一个消息队列，这几天工作需要，应用它做了一个分布式的爬虫。

rabbitmq的环境搭建就不在这里说了，需要一个Erlang语言环境，搭建起来比较简单，网上教程也很多。在网上看到过Python版的客户端，这篇文章将使用记录Java版的客户端演示RabbitMQ的连接。

一个公共类，用于获取连接：

	import java.io.IOException;
	 
	import com.rabbitmq.client.Connection;
	import com.rabbitmq.client.ConnectionFactory;
	 
	/**
	 *
	 * @ClassName: BeetleConnection
	 * @Description: TODO(这里用一句话描述这个类的作用)
	 * 将ConnectionFactory包装为单例模式，获取Connection
	 * @date 2013-11-11 下午3:24:52
	 *
	 */
	public class BeetleConnection {
	 
	    private static ConnectionFactory connectionFactory;
	     
	    static {
	        connectionFactory = new ConnectionFactory();
	        connectionFactory.setHost("localhost");
	    }
	     
	    private BeetleConnection() {
	        // TODO Auto-generated constructor stub
	    }
	     
	    public static Connection newConnection() throws IOException{
	        return connectionFactory.newConnection();
	    }
	} 

生产者:

	import java.io.File;
	import java.io.FileInputStream;
	import java.io.IOException;
	import java.text.SimpleDateFormat;
	import java.util.Date;
	import java.util.List;
	import java.util.Map;
	 
	import org.yaml.snakeyaml.Yaml;
	 
	import com.rabbitmq.client.Channel;
	import com.rabbitmq.client.Connection;
	import com.rabbitmq.client.MessageProperties;
	 
	/**
	 *
	 * @ClassName: Producer
	 * @Description: TODO(这里用一句话描述这个类的作用) 生产者
	 * @author  xinchao.wang
	 * @date 2013-11-13 上午10:59:37
	 *
	 */
	public class Producer {
	 
	    /**
	     *
	     * @Title: main
	     * @Description: TODO(这里用一句话描述这个方法的作用) 入口
	     * @param args
	     * @throws IOException   
	     * void
	     * @throws
	     */
	    public static void main(String[] args) throws IOException {
	        //验证参数
	        if (args.length == 0) {
	            System.out.println("yaml文件未找到, 例: '/tmp/urls.yaml'");
	            return ;
	        }
	 
	    //连接消息队列
	    Connection conn = BeetleConnection.newConnection();
	    Channel channel = conn.createChannel();
	    //第二个参数声明为队列持久化
	    channel.queueDeclare(Constant.QUEUENAME, true, false, false, null);
	     
	    //解析yaml文件
	    Yaml yaml = new Yaml();
	    FileInputStream fis = new FileInputStream(new File(args[0]));
	    @SuppressWarnings("unchecked")
	    List<Map<String, String>> list = (List<Map<String, String>>) yaml.load(fis);
	    for (Map<String, String> map : list) {
	            //带上必要的参数
	        ......
	        //推送到消息队列,"MessageProperties.PERSISTENT_TEXT_PLAIN"表示消息持久化
	        channel.basicPublish("", Constant.QUEUENAME,
	            MessageProperties.PERSISTENT_TEXT_PLAIN, yaml.dump(map).getBytes());
	    }
	    //关闭资源
	    fis.close();
	    channel.close();
	    conn.close();
	    }
	}

消费者：

	import java.io.IOException;
	import java.net.InetAddress;
	import java.sql.SQLException;
	import java.text.SimpleDateFormat;
	import java.util.Date;
	import java.util.Map;
	import java.util.concurrent.TimeoutException;
	 
	import org.json.JSONException;
	import org.yaml.snakeyaml.Yaml;
	 
	import com.rabbitmq.client.Channel;
	import com.rabbitmq.client.Connection;
	import com.rabbitmq.client.ConsumerCancelledException;
	import com.rabbitmq.client.MessageProperties;
	import com.rabbitmq.client.QueueingConsumer;
	import com.rabbitmq.client.ShutdownSignalException;
	 
	/**
	 *
	 * @ClassName: Consumer
	 * @Description: TODO(这里用一句话描述这个类的作用) 消费者
	 * @date 2013-11-13 上午10:58:37
	 *
	 */
	public class Consumer {
	 
	    /**
	     *
	     * @Title: main
	     * @Description: TODO(这里用一句话描述这个方法的作用) 入口
	     * @param args
	     * @throws IOException
	     * @throws ShutdownSignalException
	     * @throws ConsumerCancelledException
	     * @throws InterruptedException   
	     * void
	     * @throws
	     */
	    public static void main(String[] args) throws IOException,
	        ShutdownSignalException, ConsumerCancelledException,
	        InterruptedException {
	         
	    //连接消息队列
	    Connection connection = BeetleConnection.newConnection();
	    Channel channel = connection.createChannel();
	 
	    //指定消息队列名称
	    channel.queueDeclare(Constant.QUEUENAME, true, false, false, null);
	    channel.basicQos(1); //每次获取一条消息
	    QueueingConsumer consumer = new QueueingConsumer(channel);
	    channel.basicConsume(Constant.QUEUENAME, false, consumer); //不自动消息确认
	 
	    Yaml yaml = new Yaml();
	    while (true) {
	        QueueingConsumer.Delivery delivery = consumer.nextDelivery();
	        String message = new String(delivery.getBody()); //得到消息体
	        @SuppressWarnings("unchecked")
	            Map<String, String> param = (Map<String, String>) yaml.load(message);
	        ......//对消息进行处理
	        //发送消息确认，否则队列将一直等待
	            channel.basicAck(delivery.getEnvelope().getDeliveryTag(), false);
	    }
	    }
	}
