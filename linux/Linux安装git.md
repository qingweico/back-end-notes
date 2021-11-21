# 目录

[TOC]

## centos7.x

## 安装git

 ```bash
# 下载git源码
https://github.com/git/git/archive/v2.30.0.tar.gz
 ```

```bash
# 解压
tar -zxvf git-2.30.0.tar.gz
```

```bash
# 安装编译源码所需依赖
yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel gcc perl-ExtUtils-MakeMaker
```

```bash
# 卸载旧版本的git(安装依赖时，yum自动安装了git)(此步骤不要省略!!)
yum remove git
```

```bash
# cd
cd git-2.30.0
```

```bash
# 编译
make prefix=/usr/local/git all
```

```bash
# 安装
make prefix=/usr/local/git install
```

```bash
# 配置环境变量
vim /etc/profile
export PATH=$PATH:/usr/local/git/bin
```

```bash
# 配置生效
source /etc/profile
```

```bash
# 查看git版本
git --version
```

## 解决centos下git很慢的方法

```bash
vim /etc/hosts
```

[The Best IP Address, Email and Networking Tools](https://www.ipaddress.com)

将`github.global.ssl.fastly.net  `和`github.com `这两个网址解析得到的ip分别填入hosts文件中即可

```properties
199.232.69.194 github.global.ssl.fastly.net  
140.82.112.3 github.com 
```

```bash
# 重启网络刷新dns网络
service network restart
# window 刷新本地的DNS缓存 
ipconfig /flushdns
```

