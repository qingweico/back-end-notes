使用 Kubernetes 大规模部署应用程序，可以提升整体资源利用率，提高集群稳定性，还能提供快速的集群扩容能力，甚至还可以实现集群根据压力自动扩容

Pod 是 Kubernetes 中能够创建和部署应用的最小单元，我们可以通过 Pod IP 来访问到某一个应用实例，但需要注意的是，如果没有经过特殊配置，Pod IP 并不是固定不变的，会在 Pod 重启后会发生变化

Java 进程的内存占用除了堆之外，还包括

- 直接内存
- 元数据区
- 线程栈大小 Xss * 线程数
- JIT 代码缓存
- GC、编译器使用额外空间
- ...

使用 NMT 打印各部分区域大小，从而判断到底是哪部分内存区域占用了过多内存，或是可能有内存泄露问题

```properties
java -XX:NativeMemoryTracking=smmary/detail -XX:+UnlockDiagnosticVMOptions -XX:+PrintNMTStatistics
```

对于 JDK>8u191 的版本，我们可以设置下面这些 JVM 参数，来让 JVM 自动根据容器内存限制来设置堆内存用量

```properties
# 相当于把 Xmx 和 Xms 都设置为了容器内存 limit 的 50%
XX:MaxRAMPercentage=50.0 -XX:InitialRAMPercentage=50.0 -XX:MinRAMPercentage=50.0
```

#### CPU 资源配置不适配容器的问题

通过 -XX:+PrintFlagsFinal 开关，来确认 ActiveProcessorCount 是不是符合我们的期望，并且确认 CICompilerCount、ParallelGCThreads 等重要参数配置是否合理