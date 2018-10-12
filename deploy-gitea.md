<!-- TITLE: 安装部署 Gitea -->


域名 git.intxt.net
## 添加名为 git 的用户

```
sudo adduser git
# ENTER A PASSWORD
# Then, keep hitting Enter for the other prompts
# Add git to the sudoers group
sudo adduser git sudo
```

下载 gitea，放置在 /usr/local/bin 目录

创建 /etc/gitea 存放配置文件
创建 /var/lib/gitea 作为 gitea 的主要工作目录

```
sudo mv gitea /usr/local/bin
sudo mkdir /etc/gitea
mkdir /var/lib/gitea
sudo chown git:git /var/lib/gitea -R
```

## mysql

```
SET GLOBAL innodb_file_per_table = ON,
           innodb_file_format = Barracuda,
           innodb_large_prefix = ON;
DROP DATABASE IF EXISTS gitea;
CREATE DATABASE IF NOT EXISTS gitea CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;

CREATE USER 'gitea'@'localhost' IDENTIFIED BY 'xxxxxx';
GRANT ALL ON gitea.* TO gitea@localhost;
```

## nginx 反向代理

```
server {
    listen 80;
    listen [::]:80;
    server_name git.intxt.net;

    # Redirect all HTTP requests to HTTPS with a 301 Moved Permanently response.
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name git.intxt.net;

    # certs sent to the client in SERVER HELLO are concatenated in ssl_certificate
    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;
    ssl_session_timeout 1d;
    ssl_session_cache shared:GITSSL:50m;
    ssl_session_tickets off;

    # Diffie-Hellman parameter for DHE ciphersuites, recommended 2048 bits
    ssl_dhparam /path/to/dhparam.pem;

    # intermediate configuration. tweak to your needs.
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers 'ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA:ECDHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES256-SHA:ECDHE-ECDSA-DES-CBC3-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:DES-CBC3-SHA:!DSS';
    ssl_prefer_server_ciphers on;

    # HSTS (ngx_http_headers_module is required) (15768000 seconds = 6 months)
    add_header Strict-Transport-Security max-age=15768000;

    # OCSP Stapling ---
    # fetch OCSP records from URL in ssl_certificate and cache them
    ssl_stapling on;
    ssl_stapling_verify on;

    resolver 8.8.8.8;

    location / {
        proxy_pass http://localhost:3000;
    }
}
```


试运行

```
sudo su - git
export GITEA_WORK_DIR=/var/lib/gitea
gitea web
```


访问 http://xx.xx.xx.xx:3000/install ，填入mysql，邮箱，域名，url 等配置项，安装完成后，就可以通过域名访问了（前面先配置好了反向代理）。
使用内建 ssh 服务器，监听 22 端口。（我的系统的 ssh 端口不是 22，正好让出来给 gitea 用，这样 ssh 协议的 git 仓库地址里就不用带端口号——默认22——好看一点）。

`ctrl-c`终止试运行。用 systemd 以守护进程的方式运行 gitea

```
sudo vim /etc/systemd/system/gitea.service
```
填入以下内容

```
[Unit]
Description=Gitea (Git with a cup of tea)
After=syslog.target
After=network.target
After=mysqld.service
#After=postgresql.service
#After=memcached.service
#After=redis.service

[Service]
# Modify these two values and uncomment them if you have
# repos with lots of files and get an HTTP error 500 because
# of that
###
#LimitMEMLOCK=infinity
#LimitNOFILE=65535
RestartSec=2s
Type=simple
User=git
Group=git
WorkingDirectory=/var/lib/gitea/
ExecStart=/usr/local/bin/gitea web
Restart=always
Environment=USER=git HOME=/home/git GITEA_WORK_DIR=/var/lib/gitea
# If you want to bind Gitea to a port below 1024 uncomment
# the two values below
###
CapabilityBoundingSet=CAP_NET_BIND_SERVICE
AmbientCapabilities=CAP_NET_BIND_SERVICE

[Install]
WantedBy=multi-user.target
```

注意为了监听 22 端口需要加上的这两行

```
CapabilityBoundingSet=CAP_NET_BIND_SERVICE
AmbientCapabilities=CAP_NET_BIND_SERVICE
```

之后准备启动

```
sudo systemctl daemon-reload
sudo systemctl enable gitea
sudo systemctl start gitea
```

`sudo systemctl status gitea` 查看运行状态

日志文件位于 `/var/lib/gitea/log` 目录。

