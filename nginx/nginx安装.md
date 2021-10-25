# 目录

[TOC]

## 安装wget

```shell
# centos 7.x
yum -y install wget
```

## 安装PCRE库

```shell
cd /usr/local/
wget https://ftp.pcre.org/pub/pcre/pcre-8.00.tar.gz
tar -zxvf pcre-8.00.tar.gz
cd pcre-8.00
./configure
make && make install
```

## 安装openSSL库

```shell
cd /usr/local/
wget http://www.openssl.org/source/openssl-1.0.1j.tar.gz
tar -zxvf openssl-1.0.1j.tar.gz
cd openssl-1.0.1j
./config
make && make install
```

## 安装zlib库

```shell
cd /usr/local/
wget http://zlib.net/zlib-1.2.11.tar.gz
tar -zxvf zlib-1.2.11.tar.gz
cd zlib-1.2.11
./configure
make && make install
```

```bash
# 也可直接yum安装以上内容
# yum -y install make zlib-devel gcc-c++ libtool openssl openssl-devel
```

## 安装nginx

```shell
cd /usr/local/
wget http://nginx.org/download/nginx-1.18.0.tar.gz
tar -zxvf nginx-1.18.0.tar.gz
cd nginx-1.18.0
./configure
make && make install
# ln -s /lib64/libpcre.so.1.2.0 /lib64/libpcre.so.0
```

## 启动nginx

```shell
cd /usr/local/nginx/sbin
./nginx
```

