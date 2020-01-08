# RabbitMQ

遵循AMQP协议

有两种传递消息的模式

- 点对点。基于队列
- 发布/订阅模式。基于主题

适用于分布式环境，可以暂存消息。

## 安装

[rabbitmq centos 安装](https://blog.csdn.net/yexiaomodemo/article/details/80473411)

```sh
# 启动
rabbitmq-server -detached
# 查看状态
rabbitmqctl status
# 关闭服务
rabbitmqctl stop【 /usr/local/rabbitmq/sbin/rabbitmqctl stop 】
# 列出角色
rabbitmqctl list_users
```

安装网页

## 入门程序

maven 依赖

```xml
<dependency>
    <groupId>com.rabbitmq</groupId>
    <artifactId>amqp-client</artifactId>
    <version>5.4.3</version>
</dependency>
```

生产者客户端

```java
ConnectionFactory factory = new ConnectionFactory();
factory.setHost("192.168.56.4");
factory.setPort(5672);
factory.setUsername("root");
factory.setPassword("root");
// 创建连接
Connection conn = factory.newConnection();
// 创建信道
Channel channel = conn.createChannel();
// 创建一个type="direct"、持久化的、非自动删除的交换器
channel.exchangeDeclare("exchangeName", "direct", true, false, null);
// 创建一个持久化、非排他的、非自动删除的队列
channel.queueDeclare("queueName", true, false, false, null);
// 将交换器与队列通过路由键绑定
channel.queueBind("queueName", "exchangeName", "routingKey");
// 发送一条持久化的消息：hello world!
String message = "hello world!";
channel.basicPublish("exchangeName", "routingKey", MessageProperties.PERSISTENT_TEXT_PLAIN, message.getBytes());
// 关闭资源
channel.close();
conn.close();
```

消费者客户端

```java
Address[] addresses = new Address[]{
    new Address("192.168.56.4", 5672)
};
ConnectionFactory factory = new ConnectionFactory();
factory.setUsername("root");
factory.setPassword("root");
// 创建连接
Connection conn = factory.newConnection(addresses);
// 创建信道
final Channel channel = conn.createChannel();
// 设置客户端最多接收未被ack的消息的个数
channel.basicQos(64);
Consumer consumer = new DefaultConsumer(channel) {
    @Override
    public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
        System.out.println("recv message: " + new String(body));
        try {
            TimeUnit.SECONDS.sleep(1);
        } catch(Exception e){
            e.printStackTrace();
        }
        channel.basicAck(envelope.getDeliveryTag(), false);
    }
};
channel.basicConsume("queueName", consumer);
// 等待回调函数执行完毕后，关闭资源
TimeUnit.SECONDS.sleep(5);
channel.close();
conn.close();
```

连接rabbitmq也可以用uri的方式

```java
ConnectionFactory factory = new ConnectionFactory();
factory.setUri("amqp://userName:password@ipAddress:portNumber/virtualHost");
Connection conn = factory.createChannel();
```

应该为每个线程开辟一个Channel，多线程间共享Channel实例是非线程安全的。

使用已关闭的Channel会抛出ShutdownSignalException

网页查看（可能需要手动安装）：http://ip:15672/

## 概念解释

![消息传递结构](images/MQ/消息传递结构.png)

- 生产者：创建消息的主体。
- 消费者：接收消息的主体。
- Broker：消息中间件的服务节点
- Exchange：交换器。生产者将消息发送到Exchange，Exchange将消息路由到一个或多个队列。
- RoutingKey：路由键。生产者将消息发送给交换器时，会指定一个RoutingKey，用来指定这个消息的路由规则。RoutingKey需要与交换器类型和绑定键(BindingKey)联合使用才能最终生效。
- Binding：绑定。通过绑定，将交换器与队列关联起来，通常会指定一个绑定键(BindingKey)。

### 交换器

交换器声明函数

```java
/* exchange：交换器名字
   type：交换器类型
   durable：是否可持久化。持久化可以将交换器存盘
   autoDelete：设置是否自动删除。交换器被绑定后，所有队列都解绑后，会自动删除。
   internal：设置是否内置。客户端程序无法直接发送消息到内置交换器。
   argument：一些结构化参数。
*/
Exchange.DeclareOk exchangeDeclare(String exchange, String type, boolean durable, boolean autoDelete, boolean internel, Map<String, Object> arguments) throws IOException;
// 检查交换器是否存在。不存在抛出异常，同时关闭Channel。
Exchange.DeclareOk exchangeDeclarePassive(String name) throws IOException;
```

交换器类型

- fanout(广播)：将收到的消息发送到所有绑定的队列
- direct(点对点)：将消息发送到BindingKey和RoutingKey完全匹配的队列
- topic(主题)：将消息发送到BindingKey和RoutingKey匹配的队列，匹配规则如下
    1. RoutingKey, BindingKey的格式为"word1.word2.word3"
    2. BindingKey中可以有"*"和"#"做模糊匹配。"*"用来匹配一个word，"#"用来匹配0-N个
- headers：根据消息的headers属性，匹配发送的队列。很少用。

![topic类型的交换器](images/MQ/topic类型的交换器.png)

使用fanout交换器实现发布/订阅功能

发送端

```java
//声明一个名为logs的类型为fanout的Exchange
channel.exchangeDeclare("logs", "fanout");
String message = "Hello World."
//对这个Exchange发送消息
channel.basicPublish(EXCHANGE_NAME, "", null, message.getBytes());
// 关闭资源
```

接收端

```java
//声明一个名为logs的类型为fanout的Exchange
channel.exchangeDeclare("logs", "fanout");
//创建并获取一个队列名称
String queueName = channel.queueDeclare().getQueue();
//绑定队列到Exchange
channel.queueBind(queueName,”logs“, "");
Consumer consumer = new DefaultConsumer(channel) {
    @Override
    public void handleDelivery(String consumerTag, Envelope envelope,
                               AMQP.BasicProperties properties, byte[] body) throws IOException {
        String message = new String(body, "UTF-8");
        System.out.println(" [x] Received '" + message + "'");
    }
};
channel.basicConsume(queueName, true, consumer);
// 关闭资源
```

### 队列

queueDeclare详解

```java
/* queue：队列名字
   durable：是否持久化
   exclusive：是否排他。排他则队列仅对首次声明它的连接可见，在连接断开时自动删除。
        1. 基于连接可见，同一连接的不同信道可以访问该连接创建的排他队列
        2. 如果一个连接声明了排他队列，其他连接不能声明同名的排他连接
        3. 即使该队列是持久化的，一旦连接关闭，排他队列扔会被删除
   autoDelete：队列被连接后，所有连接都断开了，会自动删除。
*/
Queue.DeclareOk queueDeclare(String queue, boolean durable, boolean exclusive, boolean autoDelete, Map<String, Object> arguments) throws IOException;
// 清空队列内容，但不删除
Queue.DeclareOk queuePurge(String queue) throws IOException;
```

生产者和消费者都能够使用queueDeclare声明队列。如果消费者在同一个信道上订阅了另一个队列，就无法声明队列了。必须取消订阅、将信道设置为“传输”，再声明队列。

### 生产者

发送消息

```java
channel.basicPublish("exchangeName", "routingKey", null, "hello".getBytes());
// 设置属性信息
channel.basicPublish("exchangeName", "routingKey",
    new AMQP.BasicProperties.Builder()
        .contentType("text/plain")
        .deliveryMode(2)
        .priority(1)
        .userId("hidden")
        .build()),
    "hello".getBytes());
// 带有headers的消息，过期时间的消息
Map<String, Object> headers = new HashMap<String, Object>();
headers.put("location", "here");
headers.put("time", "today");
channel.basicPublish("exchangeName", "routingKey",
    new AMQP.BasicProperties.Builder()
        .headers(headers)
        .expiration("60000")
        .build()),
    "hello".getBytes());
```

### 消费者

消费消息

消费模式分2种

- 推(push)模式 channel.basicConsume()
    - Consumer(接口)
    - DefaultConsumer(实现类)
- 拉(pull)模式 channel.basicGet()
    - 

```java
// 推模式
boolean autoAck = false;
channel.basicQos(64);
channel.basicConsume("queueName", autoAck, "myConsumerTag",
    new DefaultConsumer(channel) {
        @Override
        public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
            Strng routingKey = envelope.getRoutingKey();
            String contentType = properties.getContentType();
            long deliveryTag = envelope.getDeliveryTag();
            // 应答
            channel.basicAck(deliveryTag, false);
        });

/* queue：队列名
   autoAck：是否自动确认
   consumerTag：消费者标签，用来区分多个消费者
   noLocal：true表示不能将同一个Connection中生产者发送的消息传递给这个Connection的消费者
   exclusive：是否排他
   arguments：参数
   callback：回调函数，处理RabbitMQ推送来的消息。
*/
String basicConsume(String queue, boolean autoAck, String consumerTag, boolean noLocal, boolean exclusive, Map<String,Object> arguments, Consumer callback) throws IOException;

// 拉模式
GetResponse response = channel.basicGet("queueName", false);
String body = response.getBody();
channel.basicAck(response.getEnvelope().getDeliveryTag(), false);

```

### 消息

消息包含

1. 消息体(payload)。Body是真正需要传输的数据，一般是带有业务逻辑结构的数据。
2. 标签。Header是由生产者添加的各种属性的集合，包括Message是否被持久化、由哪个Message Queue接受、优先级是多少等。

在消息路由的过程中，消息的标签会丢弃，存入到队列中的消息只有消息体。

消息确认机制

autoAck参数为false时，消费者回复确认信号后，才会从内存中移除消息。

rabbitmq不会为未确认的消息设置过期时间，它判断此消息是否需要重新投递的唯一依据时，消费该消息的消费者连接是否已断开。

查看等待投递、未确认状态的消息数

```sh
rabbitmqctl list_queues name messages_ready messages_unacknowledged
```

拒绝消息

```java
/* 拒绝一条消息
   deliverTag 消息编号
   requeue true，则消息存入队列，false，消息从队列中移除。
*/
void basicReject(long deliveryTag, boolean requeue) throws IOException;

/* 批量拒绝消息
   multiple 为true，表示拒绝deliverTag编号之前所有未被消费者确认的消息。false，只拒绝编号为deliverTag的一条消息。
*/
void basicNack(long deliveryTag, boolean multiple, boolean requeue) throws IOException;
```

### 虚拟机（Virtual Host）

在RabbitMQ中虚拟机主要是用于控制权限颗粒度，RabbitMQ默认的虚拟机路径为/。一个虚拟机包含一组交换机、队列和绑定。

例如admin的virtual host为/、zs的virtual host为/test。假设RabbitMQ Server中有/test /hello /demo,那么admin可以操作全部虚拟机下的队列而zs只可以操作/test下的队列。

## 与spring整合

引入依赖

```xml
<dependency>
    <groupId>org.springframework.amqp</groupId>
    <artifactId>spring-rabbit</artifactId>
    <version>1.6.5.RELEASE</version>
    <exclusions>
        <exclusion>
            <artifactId>spring-context</artifactId>
            <groupId>org.springframework</groupId>
        </exclusion>
    </exclusions>
</dependency>

<!-- RabbitMQ -->
<dependency>
    <groupId>com.rabbitmq</groupId>
    <artifactId>amqp-client</artifactId>
    <version>3.5.1</version>
</dependency>
```

spring配置

首先是写一个RabbitMQ属性文件 rabbitMQ.properties

```
rmq.ip=127.0.0.1
rmq.producer.num=20
rmq.port=5672
rmq.manager.user=guest
rmq.manager.password=guest
```

spring引入properties文件：

```xml
<context:property-placeholder location="classpath:properties/*.properties" />

<!-- 或者 -->
<bean id="propertyConfigurer" class="com.trace.app.framework.toolsmodel.ConvertPwdPropertyConfigurer">
    <property name="locations">
        <list>
            <value>classpath:rabbitmq.properties</value>
        </list>
    </property>
    <property name="fileEncoding" value="utf-8"></property>
</bean>
```

应用上下文配置

```xml
<!-- 配置连接工厂 -->
<rabbit:connection-factory id="connectionFactory" virtual-host="test" host="${rmq.ip}"
                               username="${rmq.manager.user}" password="${rmq.manager.password}" port="${rmq.port}"/>
<!-- 配置连接工厂 第二种 -->
<!-- <bean id="connectionFactory"  class="org.springframework.amqp.rabbit.connection.CachingConnectionFactory">
    <property name="username" value="${rmq.manager.user}" />
    <property name="password" value="${rmq.manager.password}" />
    <property name="host" value="${rmq.ip}" />
    <property name="port" value="${rmq.port}" />
    <property name="virtualHost" value="test"/>
</bean> -->

<!-- 声明消息队列管理员管理该连接工厂 -->
<!--<bean id="rabbitAdmin"  class="org.springframework.amqp.rabbit.core.RabbitAdmin">-->
        <!--<constructor-arg ref="connectionFactory" />-->
<!--</bean>-->
<rabbit:admin connection-factory="connectionFactory"/>

<!-- 声明消息队列，消息系统监听队列 -->
<rabbit:queue id="messageQueue" durable="true" auto-delete="false" exclusive="false" name="messageQueue"/>

<!-- 交换机 绑定 队列 -->
<rabbit:topic-exchange name="messageExchange" durable="true" auto-delete="false" id="messageExchange" >
    <rabbit:bindings>
        <rabbit:binding pattern="message.*" queue="messageQueue"/>
    </rabbit:bindings>
</rabbit:topic-exchange>

<rabbit:template id="rabbitTemplate" exchange="messageExchange" connection-factory="connectionFactory"/>

<!--声明生产者 -->
<bean id="messageSender"
      class="com.myproject.MessageSender" name="MessageSender">
    <property name="amqpTemplate" ref="rabbitTemplate"/>
    <!-- <property name="routingKey" value="message.test" ></property> -->
</bean>

<!--声明消费者 -->
<bean id="messageConsumer" class="com.myproject.MessageConsumer" name="messageConsumer" />
<!-- 消费者监听队列 -->
<rabbit:listener-container
        connection-factory="connectionFactory" acknowledge="auto">
    <!-- 让消费者监听这个队列 -->
    <rabbit:listener ref="messageConsumer" queues="messageQueue" />
</rabbit:listener-container>
```

监听队列里的监听器在spring应用上下文被加载后自动启动，监听事件并作出反应。

主要有如下4中转发器类型标签

- rabbit:fanout-exchange
- rabbit:direct-exchange
- rabbit:topic-exchange
- rabbit:headers-exchange

测试

生产者

```java
/**
 * rabbitMQ生产者
 * */
@Service
public class MessageSender {
    private AmqpTemplate amqpTemplate;
    private String routingKey;

    public void sendDataToQueue(Object obj) {
        System.out.println("routingKey = " + this.routingKey);
        System.out.println("Obj = " + obj);
        amqpTemplate.convertAndSend(this.routingKey, obj);
    }
}
```

消费者

```java
/**
 * rabbitMQ消费者
 * */
public class MessageConsumer implements MessageListener {
    private Logger logger = LoggerFactory.getLogger(MessageConsumer.class);

    @Override
    public void onMessage(Message message) {
        logger.info("receive message:{}",message);
    }
}
```


## AMQP协议

包括三层

- Module Layer：位于协议最高层，定义了一些供客户端调用的命令。
- Session Layer：位于中间层，负责将客户端的命令发送给服务器，将服务端的应答返回给客户端。
- Transport Layer：位于最底层，主要传输二进制数据流。


疑问

1. 如果采用订阅发布模式，一个consumer绑定一个队列，不能多个consumer绑定1个队列吗？


## 参考资料

[官网](https://www.rabbitmq.com)

