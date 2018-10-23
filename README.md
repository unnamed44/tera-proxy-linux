# tera-proxy-linux

## Разворачиваем tera-proxy на linux:

### CentOS 7.5

```
curl --silent --location https://rpm.nodesource.com/setup_10.x | sudo bash -
yum -y update && yum -y install mc zip p7zip nginx git epel-release nginx nodejs
```


```
setenforce 0
echo "SELINUX=disabled" > /etc/selinux/config
echo "SELINUXTYPE=targeted" >> /etc/selinux/config

systemctl disable firewalld && systemctl stop firewalld

iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT



```

`Download caali-hackerman tera-proxy`
```
curl -o /opt/tera-proxy.7z "https://cdn.discordapp.com/attachments/497353919228542977/503590642274926605/tera-proxy.7z"
cd /opt/
7za x tera-proxy.7z
```


```
mkdir -p /tmp/System32/drivers/etc
touch /tmp/System32/drivers/etc/hosts
```

`cat /etc/nginx/nginx.conf`
```
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;
include /usr/share/nginx/modules/*.conf;
events {
    worker_connections 1024;
}
http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log  /var/log/nginx/access.log  main;
    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 2048;
    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;
    include /etc/nginx/conf.d/*.conf;

    server {
        listen       123.123.123.123:80 default_server;
        server_name  launcher.tera-online.ru;
        location / {
            proxy_pass http://127.0.0.12:80;
        }
    }
}
```


`cat /etc/systemd/system/tera-proxy.service`
```
[Unit]
Description=Tera-Proxy
After=network-online.target

[Service]
Environment=SystemRoot=/tmp
Restart=on-failure
WorkingDirectory=/opt/tera-proxy/bin/lib/
ExecStart=/usr/bin/node /opt/tera-proxy/bin/lib/index.js

[Install]
WantedBy=multi-user.target
```



```
systemctl daemon-reload
systemctl enable nginx && systemctl restart nginx
systemctl enable tera-proxy && systemctl restart tera-proxy
systemctl status tera-proxy
```

```
tail -f /var/log/messages
```
