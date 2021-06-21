# 安装curl

```bash
# 下载到/opt
wget https://curl.haxx.se/download/curl-7.55.1.tar.gz
```

```bash
# 解压到/usr/local/
tar -zxvf curl-7.55.1.tar.gz -C /usr/local
```

```bash
# 安装
cd /usr/local
./configure
make && make install
```

