```xml
<dependency>
    <groupId>org.apache.activemq</groupId>
    <artifactId>activemq-core</artifactId>
    <version>5.7.0</version>
</dependency>
```

```java
public class JmsConsumer {
    private static final String ACTIVEMQ_URL = "tcp://127.0.0.1:61616";
    private static final String QUEUE_NAME = "queue";

    public static void main(String[] args) throws JMSException, IOException {
        ActiveMQConnectionFactory factory = new ActiveMQConnectionFactory(ACTIVEMQ_URL);
        Connection connection = factory.createConnection();
        connection.start();
        Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
        Destination destination = session.createQueue(QUEUE_NAME);
        MessageConsumer messageConsumer = session.createConsumer(destination);
        // 同步阻塞
        while (true) {
            TextMessage textMessage = (TextMessage) messageConsumer.receive();
            if (textMessage != null) {
                System.out.println("消费者接受消息" + textMessage.getText());
            } else {
                break;
            }
        }
        // 异步非阻塞
        messageConsumer.setMessageListener(message -> {
            if (message instanceof TextMessage) {
                TextMessage textMessage = (TextMessage) message;
                try {
                    System.out.println("queue MessageListener " + textMessage.getText());
                } catch (JMSException e) {
                    e.printStackTrace();
                }
            }
        });
        messageConsumer.close();
        session.close();
        connection.close();
    }
}
```

```java
public class JmsConsumerOnTopic {
    private static final String ACTIVEMQ_URL = "tcp://127.0.0.1:61616";
    private static final String TOPIC_NAME = "topic";

    public static void main(String[] args) throws JMSException {
        ActiveMQConnectionFactory factory = new ActiveMQConnectionFactory(ACTIVEMQ_URL);
        Connection connection = factory.createConnection();
        connection.start();
        Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
        Topic topic = session.createTopic(TOPIC_NAME);
        MessageConsumer messageConsumer = session.createConsumer(topic);
        messageConsumer.setMessageListener(message -> {
            if (message instanceof TextMessage) {
                TextMessage textMessage = (TextMessage) message;
                try {
                    System.out.println("topic MessageListener " + textMessage.getText());
                } catch (JMSException e) {
                    e.printStackTrace();
                }
            }
        });
        messageConsumer.close();
        session.close();
        connection.close();
    }
}
```

```java
public class JmsProducer {
    private static final String ACTIVEMQ_URL = "tcp://127.0.0.1:61616";
    private static final String QUEUE_NAME = "queue";
    private static final Integer MESSAGE_NUMBER = 6;

    public static void main(String[] args) throws JMSException {
        ActiveMQConnectionFactory factory = new ActiveMQConnectionFactory(ACTIVEMQ_URL);
        Connection connection = factory.createConnection();
        connection.start();
        Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
        Queue queue = session.createQueue(QUEUE_NAME);
        MessageProducer messageProducer = session.createProducer(queue);
        for (int i = 0; i < MESSAGE_NUMBER; i++) {
            TextMessage textMessage = session.createTextMessage("queue message " + i);
            messageProducer.send(textMessage);
        }
        messageProducer.close();
        session.close();
        connection.close();
    }
}
```

```java
public class JmsProducerOnTopic {
    private static final String ACTIVEMQ_URL = "tcp://127.0.0.1:61616";
    private static final String TOPIC_NAME = "topic";
    private static final Integer MESSAGE_NUMBER = 6;
    public static void main(String[] args) {
        ActiveMQConnectionFactory factory = new ActiveMQConnectionFactory(ACTIVEMQ_URL);
        Connection connection = factory.createConnection();
        connection.start();
        Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
        Topic topic = session.createTopic(TOPIC_NAME);
        MessageProducer messageProducer = session.createProducer(topic);
        for(int i = 0;i < MESSAGE_NUMBER;i++){
            TextMessage textMessage = session.createTextMessage("topic message " + i);
            messageProducer.send(textMessage);
        }
        messageProducer.close();
        session.close();
        connection.close();
    }
}
```

