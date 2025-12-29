```shell
# hosts 文件添加一行
echo "127.0.0.1 rocketmq-broker" | sudo tee -a /etc/hosts
```

```shell
# 删除 hosts 文件特定的一行文件
sudo sed -i '' '/rocketmq-broker/d' /etc/hosts
```

```shell
# 刷新 DNS 缓存
sudo dscacheutil -flushcache
sudo killall -HUP mDNSResponder
```

