# RocketMq

## 介绍

RocketMQ是一种开源的分布式消息传递系统 ,它最初是由阿里巴巴集团开发的 RocketMQ具有高吞吐量、低延迟、高可靠性和强大的扩展性等特点 ,广泛应用于解决大规模分布式系统中的消息传递问题 它能够处理大规模数据的消息传递 ,并且在分布式系统中支持容错和容灾 

RocketMQ的主要特性包括: 

1. **分布式架构**: RocketMQ支持分布式部署 ,可根据需要进行横向扩展 ,以满足不同规模的应用需求 
2. **高吞吐量**: RocketMQ能够处理大量消息 ,具有较高的吞吐量 ,适用于需要处理大规模数据的场景 
3. **可靠性**: RocketMQ支持数据的持久化存储 ,确保消息的可靠传递 它提供了多种方式来保证消息传递的可靠性 ,如同步双写、刷盘策略等 
4. **灵活的消息模型**: RocketMQ支持多种消息模型 ,包括发布/订阅模式和点对点模式 ,以满足不同场景下的需求 
5. **丰富的特性**: RocketMQ提供了诸如顺序消息、事务消息、延迟消息、重试队列、消息轨迹等丰富的特性 ,可以满足复杂应用场景下的需求 
6. **监控与管理**: RocketMQ提供了丰富的监控和管理工具 ,方便用户对消息传递系统进行监控、管理和调优 

RocketMQ广泛应用于各种场景 ,包括电子商务、金融、物联网、大数据分析等领域 ,用于实现异步解耦、流量削峰、日志采集、分布式事务等应用 它在分布式系统中的消息传递方面具有重要作用 ,使得系统之间的通信更加可靠和高效 

## 作用

- 削峰限流
- 异步
- 解耦合

## 定义

缓存中间件 

- redis
- memcache

数据库中间件

- mycat
- canal

面向消息的中间件(MOM : Message Oriented Middleware) 是指利用高效可靠的消息传递机制进行与平台无关(跨平台)的数据交流, 并基于数据通信来积极性分布式系统的集成, 通过提供消息传递和消息排队模型在分布式环境下提供应用的解耦, 弹性伸缩, 冗余存储, 流量削峰, 异步通信, 数据同步等

# 架构

![RocketMQ系统架构](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/202310132354132.png)

### 消费模式

Mq的消费模式可以分为两种 一种是 push 一种是pull(push模式也是基于pull)

- push 主动推送消息给客户端 时效性好但是缺点就是如果客户端没有做好流量限制一旦服务端推送大量消息 会导致客户端消息积压甚至崩溃
- pull 主动拉取服务端数据 客户端可以根据消费能力进行消费 但是拉取时间间隔控制多少是个问题
