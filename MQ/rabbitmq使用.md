![RabbitMQ](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/RabbitMQ.png)

**Broker**: 接收和分发消息的应用 RabbitMQ Server 就是 Message Broker

**Virtual host**: 把 AMQP 的基本组件划分到一个虚拟的分组中 当多个不同的用户使用同一个 RabbitMQ Server 提供的服务时 可以划分出多个host 每个用户可以在自己的 vhost 创建 exchange / queue等

**Connection**: publisher / consumer 和 broker 之间的 TCP 连接

**Channel**: 如果每一次访问 RabbitMQ 都建立一个 Connection 在消息量大的时候建立 TCP Connection 的开销将是巨大的 效率也低 Channel 是在connection 内部建立的逻辑连接 如果应用程序支持多线程 通常每个thread 创建单独的 channel 进行通信 AMQP method 包含 channel id 帮助客户端 和 message broker 识别 channel 所以 channel 之间是完全隔离的 Channel 作为轻量级的 Connection 极大减少了操作系统建立 TCP connection 的开销

**Exchange**: message到达broker的第一站,根据分发规则匹配查询表中的routing key,分发消息到queue中 

**Queue**: 消息最终送到此处 等待consumer 取走

**Binding**: exchange和queue之间的虚拟连接,binding中可以包含routing key。Binding消息被保存到exchange中的查询表中,用于message的分发依据
