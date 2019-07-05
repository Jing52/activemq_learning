# 消息中间件概述
## 中间件介绍
###什么是中间件？
非底层操作系统软件，非业务应用软件，不是直接给最终用户使用的，不能直接给用户带来价值的软件统称中间件。
###什么是消息中间件
关注于数据的发送与接收，利用高效可靠的异步消息传递机制集成分布式系统。
###消息中间件图示
![](https://upload-images.jianshu.io/upload_images/14481291-e3dc5c03f7a90f5b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
应用A通过应用程序接口向消息中间件发送消息，应用B通过应用程序接口向消息中间件接收消息。
###什么是JMS
Java消息服务（Java Message Service）即JMS，是一个Java平台中关于面向消息中间件的API，用于在两个应用程序之间，或分布式系统中发送/接收消息，进行异步通信。
###什么是AMQ
AMQP，即Advanced Message Queuing Protocol,一个提供统一消息服务的应用层标准协议,基于此协议的客户端与消息中间件可传递消息，并不受客户端中间件不同产品，不同的开发语言等条件的限制。
### JMS和AMQP对比
![](https://upload-images.jianshu.io/upload_images/14481291-7e5d75444a23e970.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 常见消息中间件对比
#### ActiveMQ
* 多种语言和协议编写客户端，语言：Java、C语言、C++、C#、Ruby、Perl、Python、PHP。应用协议：OpenWire、Stomp REST、WS Notification、XMPP、AMQP。
* 完全支持JMS1.1和J2EE1.4规范（持久化、XA消息、事务）
* 虚拟主题、组合目的、镜像队列
#### RabbitMQ
* 支持多种客户端，如Python、Ruby、.NET、Java、JMS、C、PHP、ActionScript等
* AMQP的完整实现
* 事务支持/发布确认
* 消息持久化
#### Kafka
Kafka是一种高吞吐量的分布式发布订阅消息系统，是一个分布式的、分区的、可靠的分布式日志存储服务。它通过一种独一无二的设计提供了消息系统的功能。
##### 特性
* 消息的持久化
* 高吞吐量
* Partition、Consumer Group


![综合对比](https://upload-images.jianshu.io/upload_images/14481291-38ced69006bf2557.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## JMS规范
### Java消息服务定义
Java消息服务（Java Message Service）即JMS，是一个Java平台中关于面向消息中间件的API，用于在两个应用程序之间，或分布式系统中发送/接收消息，进行异步通信。
### JMS概念
* 提供者：实现JMS规范的消息中间件服务器
* 客户端：发送或接收消息额应用程序
* 生产者/发布者：创建并发送消息的客户端
* 消费者/订阅者：接收并处理消息的客户端
* 消息： 应用程序间传递的数据内容
* 消息模式：在客户端间传递消息的方式，JMS中定义了主题和队列两种模式
### 消息模式
#### 队列模型
* 客户端包括生产者和消费者
* 队列中的消息只能被一个消费者消费
* 消费者可以随时消费队列中的消息
##### 队列模型示意图
![](https://upload-images.jianshu.io/upload_images/14481291-7572bfb7737aba2c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#### 主题模型
* 客户端包括生产者和消费者
* 主题中的消息被所有订阅者消费
* 消费者不能消费订阅之前就发送到主题中的消息
##### 队列模型示意图
![](https://upload-images.jianshu.io/upload_images/14481291-a8ade2f0f803f64d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#### JMS编码规范
* ConnectionFactory：用于创建连接到消息中间件的连接工厂
* Connection：“链接”,代表了应用程序和消息服务器之间的通信链路
* Destination：“目的地”,指消息发布和接收的地点，包括队列或主题
* Session：“会话”,表示一个单线程的上下文，用于发送和接收消息
* MessageConsumer：“消费者”,一种可以向JMS提供获取消息的客户端类型
* MessageProducer：“生产者”,消费者和生产者间传送的对象，消息头，一组消息属性，一个消息体
#### JMS编码接口之间的关系
![](https://upload-images.jianshu.io/upload_images/14481291-f116a19b98206435.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## windows下安装ActiveMQ
## Linux下安装ActiveMQ
## 队列模式的消息演示
`pom.xml`
```
    <dependencies>
		<dependency>
			<groupId>org.apache.activemq</groupId>
			<artifactId>activemq-all</artifactId>
			<version>5.13.0</version>
		</dependency>
	</dependencies>
```
`ActiveMqProducer.java`
```
package com.cxy.jms.queue;

import org.apache.activemq.ActiveMQConnectionFactory;

import javax.jms.*;

/**
 * @Auther: cxy
 * @Date: 2019/7/4
 * @Description: 生产者
 */
public class ActiveMqProducer {
    private static final String url = "tcp://192:168.31.10:61616";
    private static final String queueName = "queue-test";

    public static void main(String[] args) throws JMSException {
        //1.创建ConnectionFactory
        ConnectionFactory connectionFactory = new ActiveMQConnectionFactory(url);

        //2.创建Connection
        Connection connection = connectionFactory.createConnection();

        //3.启动连接
        connection.start();

        //4.创建会话
        Session session = connection.createSession(false,Session.AUTO_ACKNOWLEDGE);

        //5.创建一个目标
        Destination destination = session.createQueue(queueName);

        //6.创建一个生产者
        MessageProducer producer = session.createProducer(destination);

        for(int i=0;i<100;i++){
            //7.创建消息
            TextMessage textMessage = session.createTextMessage("test" + i);

            //8.发布消息
            producer.send(textMessage);

            System.out.println("发送消息："+textMessage.getText());
        }

        //9.关闭连接
        connection.close();
    }
}
```
`ActiveMqConsumer.java`
```
package com.cxy.jms.queue;

import org.apache.activemq.ActiveMQConnectionFactory;

import javax.jms.*;

/**
 * @Auther: cxy
 * @Date: 2019/7/4
 * @Description: 消费者
 */
public class ActiveMqConsumer {
    private static final String url = "tcp://192:168.31.10:61616";
    private static final String queueName = "queue-test";

    public static void main(String[] args) throws JMSException {
        //1.创建ConnectionFactory
        ConnectionFactory connectionFactory = new ActiveMQConnectionFactory(url);

        //2.创建Connection
        Connection connection = connectionFactory.createConnection();

        //3.启动连接
        connection.start();

        //4.创建会话
        Session session = connection.createSession(false,Session.AUTO_ACKNOWLEDGE);

        //5.创建一个目标
        Destination destination = session.createQueue(queueName);

        //6.创建消费者
        MessageConsumer consumer = session.createConsumer(destination);

        //7.创建一个监听器
        consumer.setMessageListener(new MessageListener() {
            @Override
            public void onMessage(Message message) {
                TextMessage textMessage = (TextMessage) message;
                try {
                    System.out.println("接收消息："+textMessage.getText());
                }catch (JMSException e){
                    e.printStackTrace();
                }
            }
        });

    }
}
```
## 主题模式的消息演示
`ActiveMqProducer.java`
```
package com.cxy.jms.topic;

import org.apache.activemq.ActiveMQConnectionFactory;

import javax.jms.*;

/**
 * @Auther: cxy
 * @Date: 2019/7/4
 * @Description: 生产者
 */
public class ActiveMqProducer {
    private static final String url = "tcp://192:168.31.10:61616";
    private static final String topicName = "topic-test";

    public static void main(String[] args) throws JMSException {
        //1.创建ConnectionFactory
        ConnectionFactory connectionFactory = new ActiveMQConnectionFactory(url);

        //2.创建Connection
        Connection connection = connectionFactory.createConnection();

        //3.启动连接
        connection.start();

        //4.创建会话
        Session session = connection.createSession(false,Session.AUTO_ACKNOWLEDGE);

        //5.创建一个目标
        Destination destination = session.createTopic(topicName);

        //6.创建一个生产者
        MessageProducer producer = session.createProducer(destination);

        for(int i=0;i<100;i++){
            //7.创建消息
            TextMessage textMessage = session.createTextMessage("test" + i);

            //8.发布消息
            producer.send(textMessage);

            System.out.println("发送消息："+textMessage.getText());
        }

        //9.关闭连接
        connection.close();
    }
}
```
`ActiveMqConsumer.java`
```
package com.cxy.jms.topic;

import org.apache.activemq.ActiveMQConnectionFactory;

import javax.jms.*;

/**
 * @Auther: cxy
 * @Date: 2019/7/4
 * @Description: 消费者
 */
public class ActiveMqConsumer {
    private static final String url = "tcp://192:168.31.10:61616";
    private static final String topicName = "topic-test";

    public static void main(String[] args) throws JMSException {
        //1.创建ConnectionFactory
        ConnectionFactory connectionFactory = new ActiveMQConnectionFactory(url);

        //2.创建Connection
        Connection connection = connectionFactory.createConnection();

        //3.启动连接
        connection.start();

        //4.创建会话
        Session session = connection.createSession(false,Session.AUTO_ACKNOWLEDGE);

        //5.创建一个目标
        Destination destination = session.createTopic(topicName);

        //6.创建消费者
        MessageConsumer consumer = session.createConsumer(destination);

        //7.创建一个监听器
        consumer.setMessageListener(new MessageListener() {
            @Override
            public void onMessage(Message message) {
                TextMessage textMessage = (TextMessage) message;
                try {
                    System.out.println("接收消息："+textMessage.getText());
                }catch (JMSException e){
                    e.printStackTrace();
                }
            }
        });

    }
}
```
#### activemq模式区分
队列模式：生产者发送消息，所有消费者对消息进行平分，已消费的消息不能重新消费
主题模式：生产者发送消息，所有已订阅主题的消费者都能收到消息。