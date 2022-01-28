### Netty模型

- Netty抽象出两组线程池 BossGroup专门负责接受客户端连接, WorkerGroup专门负责网络读写操作
- NioEventLoop表示一个不断循环执行处理任务的线程, 每个NioEventLoop都有一个selector, 用于监听绑定在其上的socket网络通道
- NioEventLoop内部采用串行化设计, 从消息的读取和处理以及发送始终由IO线程NioEventLoop负责

>NioEventLoopGroup下包含多个NioEventLoop
>
>每个NioEventLoop中包含一个Selector 一个taskQueue
>
>每个NioEventLoop的Selector上可以注册监听多个NioChannel
>
>每个NioChannel 只会绑定在唯一的NioEventLoop上
>
>每个NioChannel都绑定有一个自己的ChannelPipeline

### 异步模型

- 异步的概念和同步相对, 当异步过程调用发出后, 调用者不能立即得到结果, 实际处理这个调用的组件在完成过后, 通过状态、通知和回调来通知调用者

- Netty中的I/O操作是异步的, 包括bind write connect 等操作会简单的返回一个ChannelFuture
- 调用者并不能立刻获得结果, 而是通过Future-Listener机制, 用户可以方便的主动获取或者通过通知机制获得IO操作结果
- Netty的异步模型是建立在future和callback之上的; Future的核心思想是: 在调用一个比较耗时的方法时立马返回一个Future, 后续可以通过Future去监控方法的处理过程
- Future表示异步的执行结果, 可以通过它提供的方法来检测执行是否完成

