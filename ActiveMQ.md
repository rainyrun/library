# ActiveMQ

遵循 JMS 规范

有两种类型

- 点对点
- 发布/订阅

有5种不同的消息正文格式

- StreamMessage Java原始值的数据流
- MapMessage 一套名称-值对
- TextMessage 一个字符串对象
- ObjectMessage 一个序列化的 Java对象
- BytesMessage 一个字节的数据流

使用

添加依赖

```xml
<dependency>
    <groupId>org.apache.activemq</groupId>
    <artifactId>activemq-all</artifactId>
</dependency>
```

## Queue(点对点)

queue 是点对点模式，一个消息被一个消费者消费。消息储存在服务器。

生产者

```java
// 第一步：创建ConnectionFactory对象，需要指定服务端ip及端口号。
//brokerURL服务器的ip及端口号
ConnectionFactory connectionFactory = new ActiveMQConnectionFactory("tcp://192.168.25.130:61616");
// 第二步：使用ConnectionFactory对象创建一个Connection对象。
Connection connection = connectionFactory.createConnection();
// 第三步：开启连接，调用Connection对象的start方法。
connection.start();
// 第四步：使用Connection对象创建一个Session对象。
//第一个参数：是否开启事务。true：开启事务，第二个参数忽略。
//第二个参数：当第一个参数为false时，才有意义。消息的应答模式。1、自动应答2、手动应答。一般是自动应答。
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
// 第五步：使用Session对象创建一个Destination对象（topic、queue），此处创建一个Queue对象。
//参数：队列的名称。
Queue queue = session.createQueue("queue-test");
// 第六步：使用Session对象创建一个Producer对象。
MessageProducer producer = session.createProducer(queue);
// 第七步：创建一个Message对象，创建一个TextMessage对象。
/*TextMessage message = new ActiveMQTextMessage();
message.setText("hello activeMq,this is my first test.");*/
TextMessage textMessage = session.createTextMessage("hello activeMq,this is my first test.");
// 第八步：使用Producer对象发送消息。
producer.send(textMessage);
// 第九步：关闭资源。
producer.close();
session.close();
connection.close();
```

消费者

```java
// 1.创建一个连接工厂 （Activemq的连接工厂）参数：指定连接的activemq的服务
ConnectionFactory connectionFactory = new ActiveMQConnectionFactory("tcp://192.168.25.130:61616");
// 2.获取连接
Connection connection = connectionFactory.createConnection();
// 3.开启连接
connection.start();
// 4.根据连接对象创建session
// 第一个参数：表示是否使用分布式事务（JTA）
// 第二个参数：如果第一个参数为false,第二个参数才有意义；表示使用的应答模式 ：自动应答，手动应答.这里选择自动应答。
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
// 5.根据session创建Destination(目的地，queue topic,这里使用的是queue)
Queue queue = session.createQueue("queue-test");
// 6.创建消费者
MessageConsumer consumer = session.createConsumer(queue);
//7.接收消息
//第一种
//          while(true){
//              //接收消息 （参数的值表示的是超过一定时间 以毫秒为单位就断开连接）
//              Message message = consumer.receive(10000);
//              //如果message为空，没有接收到消息了就跳出
//              if(message==null){
//                  break;
//              }
//              
//              if(message instanceof TextMessage){
//                  TextMessage messaget = (TextMessage)message;
//                  System.out.println(">>>获取的消息内容："+messaget.getText());//获取消息内容
//              }
//          }

//第二种：
//设置监听器,其实开启了一个新的线程。
consumer.setMessageListener(new MessageListener() {
    //接收消息，如果有消息才进入，如果没有消息就不会进入此方法
    @Override
    public void onMessage(Message message) {
        if(message instanceof TextMessage){
            TextMessage messaget = (TextMessage)message;
            try {
                //获取消息内容
                System.out.println(">>>获取的消息内容："+messaget.getText());
            } catch (JMSException e) {
                e.printStackTrace();
            }
        }
    }
});
Thread.sleep(10000);//睡眠10秒钟。
// 9.关闭资源
consumer.close();
session.close();
connection.close();
```

## Topic(订阅/发布)

topic 是发布订阅方式，一个消息被多个消费者消费。消息不储存在服务器。

生产者

```java
// 1.创建一个连接工厂 （Activemq的连接工厂）参数：指定连接的activemq的服务
ConnectionFactory connectionFactory = new ActiveMQConnectionFactory("tcp://192.168.25.130:61616");
// 2.获取连接
Connection connection = connectionFactory.createConnection();
// 3.开启连接
connection.start();
// 4.根据连接对象创建session
// 第一个参数：表示是否使用分布式事务（JTA）
// 第二个参数：如果第一个参数为false,第二个参数才有意义；表示使用的应答模式 ：自动应答，手动应答.这里选择自动应答。
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
// 5.根据session创建Destination(目的地，queue topic,这里使用的是topic)
Topic topic = session.createTopic("topic-test");//---------------------
// 6.创建生产者
MessageProducer producer = session.createProducer(topic);
// 7.构建消息对象，（构建发送消息的内容） 字符串类型的消息格式（TEXTMessage）
TextMessage textMessage = new ActiveMQTextMessage();
textMessage.setText("发送消息123");// 消息的内容
// 8.发送消息
producer.send(textMessage);
// 9.关闭资源
producer.close();
session.close();
connection.close();
```

消费者

```java
// 1.创建一个连接工厂 （Activemq的连接工厂）参数：指定连接的activemq的服务
ConnectionFactory connectionFactory = new ActiveMQConnectionFactory("tcp://192.168.25.130:61616");
// 2.获取连接
Connection connection = connectionFactory.createConnection();
// 3.开启连接
connection.start();
// 4.根据连接对象创建session
// 第一个参数：表示是否使用分布式事务（JTA）
// 第二个参数：如果第一个参数为false,第二个参数才有意义；表示使用的应答模式 ：自动应答，手动应答.这里选择自动应答。
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
// 5.根据session创建Destination(目的地，queue topic,这里使用的是queue)
Topic topic = session.createTopic("topic-test");//---------------------
// 6.创建消费者
MessageConsumer consumer = session.createConsumer(topic);
// 7.接收消息
while(true){
    //接收消息 （参数的值表示的是超过一定时间 以毫秒为单位就断开连接）
    Message message = consumer.receive(100000);
    //如果message为空，没有接收到消息了就跳出
    if(message==null){
        break;
    }
    
    if(message instanceof TextMessage){
        TextMessage messaget = (TextMessage)message;
        System.out.println(">>>获取的消息内容："+messaget.getText());//获取消息内容
    }
}

// 第二种：
// 设置监听器,其实开启了一个新的线程。
//      consumer.setMessageListener(new MessageListener() {
//          // 接收消息，如果有消息才进入，如果没有消息就不会进入此方法
//          @Override
//          public void onMessage(Message message) {
//              if (message instanceof TextMessage) {
//                  TextMessage messaget = (TextMessage) message;
//                  try {
//                      // 获取消息内容
//                      System.out.println(">>>获取的消息内容：" + messaget.getText());
//                  } catch (JMSException e) {
//                      e.printStackTrace();
//                  }
//              }
//          }
//      });
//Thread.sleep(10000);// 睡眠10秒钟。

// 9.关闭资源
consumer.close();
session.close();
connection.close();
```

## Activemq整合spring

加入依赖

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jms</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context-support</artifactId>
</dependency>
```

配置文件applicationContext-activemq.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
    xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
    http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.2.xsd
    http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.2.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.2.xsd
    http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.2.xsd">
    
    <bean id="targetConnection" class="org.apache.activemq.ActiveMQConnectionFactory">
        <property name="brokerURL" value="tcp://192.168.25.130:61616"></property>
    </bean>
    <!-- 通用的connectionfacotry 指定真正使用的连接工厂 -->
    <bean id="connectionFactory" class="org.springframework.jms.connection.SingleConnectionFactory">
        <property name="targetConnectionFactory" ref="targetConnection"></property>
    </bean>
    <!-- 接收和发送消息时使用的类 -->
    <bean class="org.springframework.jms.core.JmsTemplate">
        <property name="connectionFactory" ref="connectionFactory"></property>
    </bean>
    <bean id="queueDestination" class="org.apache.activemq.command.ActiveMQQueue">
        <constructor-arg name="name" value="item-change-queue"></constructor-arg>
    </bean> 
    <!-- <bean id="topicDestination" class="org.apache.activemq.command.ActiveMQTopic">
        <constructor-arg name="name" value="item-change-topic"></constructor-arg>
    </bean> -->

    <!-- 监听器 -->
    <bean id="myMessageListener" class="com.itheima.activemq.spring.MyMessageListener"></bean>
    <!-- 监听容器，作用：启动线程做监听 -->
    <bean class="org.springframework.jms.listener.DefaultMessageListenerContainer">
        <property name="connectionFactory" ref="connectionFactory"></property>
        <property name="destination" ref="topicDestination"></property>
        <property name="messageListener" ref="myMessageListener"></property>
    </bean>
</beans>
```

发送消息

```java
public class Producer {
    @Test
    public void send() throws Exception{
        //1.初始化spring容器
        ApplicationContext context = new ClassPathXmlApplicationContext("classpath:applicationContext-activemq.xml");
        //2.获取到jmstemplate的对象
        JmsTemplate jmsTemplate = context.getBean(JmsTemplate.class);
        //3.获取destination
        Destination destination = (Destination) context.getBean(Destination.class);
        //4.发送消息
        jmsTemplate.send(destination, new MessageCreator() {
            
            @Override
            public Message createMessage(Session session) throws JMSException {
                return session.createTextMessage("通过spring发送的消息123");
            }
        });
        Thread.sleep(100000);
    }
}
```

接收消息

```java
public class MyMessageListener implements MessageListener {
    @Override
    public void onMessage(Message message) {
        if(message instanceof TextMessage){
            TextMessage textMessage = (TextMessage)message;
            String text;
            try {
                text = textMessage.getText();
                System.out.println(text);
            } catch (JMSException e) {
                e.printStackTrace();
            }
        }
    }
}
```

