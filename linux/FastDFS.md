# FastDFS

[安装教程](https://github.com/happyfish100/fastdfs/wiki)

| centos               | 7.x                           |
| -------------------- | ----------------------------- |
| libfastcommon        | FastDFS分离出的一些公用函数包 |
| FastDFS              | FastDFS本体                   |
| fastdfs-nginx-module | FastDFS和nginx的关联模块      |
| nginx                | nginx1.15.4                   |

```bash
# 编译环境
yum install git gcc gcc-c++ make automake autoconf libtool pcre pcre-devel zlib zlib-devel openssl-devel wget vim -y
```

libfastcommon-1.0.42.tar.gz

fastdfs-nginx-module-1.22.tar.gz

fastdfs-6.04.tar.gz

安装包放在/opt目录下

全部解压到/usr/local/dfs目录中

### 安装libfastcommon

```bash
cd libfastcommon-1.0.42/
 #编译安装
./make.sh && ./make.sh install
```

### 安装FastDFS

```bash
cd fastdfs-6.04/
#编译安装
./make.sh && ./make.sh install
将conf目录下所有配置文复制(移动)到/etc/fdfs/
```

```bash
# 修改tracker.conf配置文件
#####################################
# 存储日志和数据的根目录
base_path =/usr/local/fastdfs/tracker
#####################################
```

```bash
# 启动tracker服务
/usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf
```

```bash
# 修改storage.conf配置文件
#####################################
group_name=upload
# 存储日志和数据的根目录
base_path=/usr/local/fastdfs/storage
store_path0=/usr/local/fastdfs/storage
tracker_server=192.168.0.105:22122
# http访问文件的端口 和nginx端口号保持一致
http.server_port=8888
#####################################
```

```bash
# 启动storage服务
/usr/bin/fdfs_启动storaged /etc/fdfs/storage.conf
```

## 安装fastdfs-nginx-module

```bash
cp /usr/local/dfs/fastdfs-nginx-module-1.22/src/mod_fastdfs.conf /etc/fdfs
```

## nginx

```bash
#添加fastdfs-nginx-module模块
./configure --add-module=/usr/local/dfs/fastdfs-nginx-module-1.22/src/
# 编译安装
make && make install
```

```bash
# 修改mod_fastdfs.conf配置文件
#####################################
# the base path to store log files
base_path=/usr/local/fastdfs/storage/tmp
tracker_server=192.168.0.105:22122
# the group name of the local storage server
group_name=upload
# url 是否包含组名(upload)
url_have_group_name = true
# the paths must be exist
# must same as storage.conf
store_path0=/usr/local/fastdfs/storage
#####################################
```

## 配置nginx访问

```bash
server {
    listen       8888;    ## 该端口为storage.conf中的http.server_port相同
    server_name  localhost;
    location upload/M00 {
        ngx_fastdfs_module;
    }
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
    root   html;
    }
}
```

