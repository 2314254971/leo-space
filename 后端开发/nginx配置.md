nginx配置代理实现域名解析（涉及https）

前提：证书和域名匹配才会安全



conf配置如下

```nginx
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

        # 江西省电力规划信息管理平台
    server {
                listen 80;
                server_name ai-asst.jxepdi.cn;  #域名
                ssl_certificate cert/server.pem;  #配置证书
                ssl_certificate_key cert/server.key;
                ssl_session_timeout 5m;
                ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
                ssl_ciphers HIGH:!RC4:!MD5:!aNULL:!eNULL:!NULL:!DH:!EDH:!EXP:+MEDIUM;
                ssl_prefer_server_ciphers on;
                charset utf-8;
                client_max_body_size 10000M;

                location / {
                        proxy_set_header Host $http_host;
                        proxy_set_header X-Real-IP $remote_addr;
                        proxy_set_header REMOTE-HOST $remote_addr;
                        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                        proxy_pass http://10.174.233.20:8080/;
                }

                error_page 500 502 503 504 /50x.html;
                location = /50x.html {
                        root html;
                }
   }
   server {
        listen 443 ssl;
        server_name ai-asst.jxepdi.cn # localhost修改为您证书绑定的域名。
        # ssl on;   #设置为on启用SSL功能。
        root html;
        index index.html index.htm;
        ssl_certificate cert/server.pem;   #将domain.pem替换成您证书的文件名。
        ssl_certificate_key cert/server.key;   #将domain.key替换成您证书的密钥文件名。
        ssl_session_timeout 5m;
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;  #使用此加密套件。
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;   #使用该协议进行配置。
        ssl_prefer_server_ciphers on;
        client_max_body_size 10000M;

        location / {
             proxy_set_header Host $http_host;
             proxy_set_header X-Real-IP $remote_addr;
             proxy_set_header REMOTE-HOST $remote_addr;
             proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
             proxy_pass http://10.174.233.20:8080/;
        }


    }

}
```



配置完后记得重启nginx

然后配置防火墙

```sh
sudo firewall-cmd --zone=public --add-port=80/tcp --permanent    # 将80端口添加到public区域
sudo firewall-cmd --reload    # 重新载入firewalld配置
firewall-cmd --list-all
```

