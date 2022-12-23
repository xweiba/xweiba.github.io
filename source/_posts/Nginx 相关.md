---
title: Nginx 相关
date: 2021-05-18 13:07:23
tags:
 - Nginx
categories:
 - Nginx
---

## 直接返回文件内容
```nginx
location ^~ /get_json_file{
	default_type application/json;
	add_header Content-Type 'application/json; charset=utf-8';
	alias D:/resouces/code/java/test.json;
}

location ~ ^/get_json {
    default_type application/json;
    return 200 '{"status":"success","result":"hello world!"}';
}
```

## 负载均衡
1. 配置 `conf/nginx.conf`
- 默认轮询 添加下列配置 
```
http{
    ....
    upstream tomcats {
        server 127.0.0.1:8080
        server 127.0.0.1:9080
    }
    ...
}
server{
    ...
    location / {
        proxy_pass http://tomcats;
    }
}
```

## 错误记录
重载配置 `./nginx -c ../conf/nginx.conf -s reload `, 报错: `nginx: [emerg] unknown directive`. 
```
原因是因为因为nginx.conf配置被windows下的记事本编辑过, 编码变成了UTF-8+BOOM, nginx 报错.
```

## reload 不生效
![](https://ws1.sinaimg.cn/large/b08fb805gy1g5lbeagrsnj20na05bmxc.jpg) 
浏览器端禁用缓存, 可发起新连接

## http 转发至 https
```nginx
http {
    # 添加dns解析地址, nginx在proxy_pass转发时如果使用变量, 当此变量是域名时有很大几率会出现无法解析导致502问题
    # 错误日志为: no resolver defined to resolve ***
    resolver 8.8.8.8;
}
# 443 
server {
    listen 443 ssl;
    server_name xxx.com;
    ssl on;   #设置为on启用SSL功能。
    root html;
    index index.html index.htm;
    ssl_certificate cert/xxx.pem;   #将domain name.pem替换成您证书的文件名。
    ssl_certificate_key cert/xxx.key;   #将domain name.key替换成您证书的密钥文件名。
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;  #使用此加密套件。
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;   #使用该协议进行配置。
    ssl_prefer_server_ciphers on;

    location = / {
        proxy_set_header Host $http_host;
        proxy_redirect     off;
        proxy_pass         http://xxx/xxx/;
        proxy_set_header   Host             $host;
        proxy_set_header   X-Real-IP        $remote_addr;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        client_max_body_size    1000m;
    }
}


service {
    listen       80;
    server_name  xxx.xxx.com;

    location / {
        # GET 请求走301重定向, 非GET请求使用proxy_pass
        if ($request_method ~ ^(POST|DELETE|OPTIONS)$) {
            # 注意使用变量转发时一定要添加resolver配置
            proxy_pass https://$host;
            break ;
        }
        rewrite ^(.*)$   https://$host$1 permanent;
    }
}
```