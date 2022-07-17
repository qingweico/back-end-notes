```shell
# 查看当前nginx的版本 #sbin
./nginx -v
```

```shell
# 关闭nginx ##sbin
./nginx -s stop
```

```shell
# 重新加载nginx配置文件 即在nginx启动时运行此命令才有效 ##sbin
./nginx -s reload
```

```nginx
server {
        listen       443 ssl;
        server_name  blog.qingweico.cn;

        ssl_certificate      cert/6460380_blog.qingweico.cn.pem;
        ssl_certificate_key   cert/6460380_blog.qingweico.cn.key;

        ssl_session_cache    shared:SSL:1m;
        ssl_session_timeout  5m;

       ssl_ciphers  HIGH:!aNULL:!MD5;
       ssl_prefer_server_ciphers  on;

        location / {
            root   dist;
            index  index.html index.htm;
        }
   }
```

