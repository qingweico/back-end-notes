[es下载列表](https://www.elastic.co/cn/downloads/past-releases#elasticsearch)

- 修改es核心配置文件elasticsearch.yml
- 修改JVM参数(jvm.options)

```bash
# es不允许root用户操作
useradd esuser
chown -R esuser:esuser /usr/local/elasticsearch-x.x.x
su esuser
whoami
# 以后台方式启动(elasticsearch-x.x.x/bin)
./elasticsearch -d
```

elasticsearch启动常见错误

max number of threads [xxxx] for user [xx] is too low, increase to at least [xxxx]

每个进程同时打开文件数太小

```bash
# 查看命令
ulimit -Hn
ulimit -Sn
```

```bash
# /etc/security/limits.conf
* soft  nproc  4096
* hard  nproc  4096
```

max file descriptors [xxxx] for elasticsearch process is too low, increase to at least [xxxx]

最大线程数太低

```bash
# 查看命令
ulimit -Hu
ulimit -Su
```

```bash
# /etc/security/limits.conf
* soft  nofile 65536
* hard  nofile 65536
```

max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]

```bash
# vim /etc/sysctl.conf
vm.max_map_count=262145
# 刷新配置
sysctl -p
```

索引index对应数据库中的表; 文档document对应数据库中行(记录); 字段 fields: 列
