# Config for nginx reverse proxy (not production ready)
```
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
    worker_connections 1024;
}

http {
    server {
        listen 80;
        server_name localhost 127.0.0.1;

        location /service-one {
            proxy_pass  http://service-one/;
            proxy_set_header X-Forwarded-For $remote_addr;
        }

        location /service-two {
            proxy_pass  http://service-two/;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
    }
}
```