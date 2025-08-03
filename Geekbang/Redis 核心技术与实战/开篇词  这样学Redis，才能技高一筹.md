为了保证数据的可靠性，Redis 需要在磁盘上读写 AOF 和 RDB，但在高并发场景里，这就会直接带来两个新问题：一个是写 AOF 和 RDB 会造成 Redis 性能抖动，另一个是 Redis 集群数据同步和实例恢复时，读 RDB 比较慢，限制了同步和恢复速度

- 一个可行的解决方案就是使用非易失内存 NVM，因为它既能保证高速的读写，又能快速持久化数据

### Redis 知识全景图

![79da7093ed998a99d9abe91e610b74e7](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/79da7093ed998a99d9abe91e610b74e7.webp)

“两大维度”就是指系统维度和应用维度，“三大主线”也就是指高性能、高可靠和高可扩展（可以简称为“三高”）	

一些优雅的系统设计规范，例如 run-to-complete 模型、epoll 网络模型

- 高性能主线，包括线程模型、数据结构、持久化、网络框架；
- 高可靠主线，包括主从复制、哨兵机制；
- 高可扩展主线，包括数据分片、负载均衡

### Redis 的问题画像图

![70a5bc1ddc9e3579a2fcb8a5d44118b4](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/70a5bc1ddc9e3579a2fcb8a5d44118b4.webp)

Redis 是一个非常优秀的系统，它在 CPU 使用、内存组织、存储持久化和网络通信这四大方面的设计非常经典，而这些，基本涵盖了一个优秀的后端系统工程师需要掌握的核心知识和关键技术
