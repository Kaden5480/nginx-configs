# nginx-configs
Custom nginx configs which I use.

## Prerequisites
- [nginx](https://pkgs.org/download/nginx)
- [libnginx-mod-http-headers-more-filter](https://pkgs.org/download/libnginx-mod-http-headers-more-filter)

## Examples
### TLS config
```nginx
# /etc/nginx/conf.d/20-tls.conf

ssl_certificate     /etc/letsencrypt/live/example.com/fullchain.pem;
ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;

ssl_protocols TLSv1.2 TLSv1.3;
```

### Site Configs
Redirecting HTTP to HTTPS
```nginx
# /etc/nginx/sites-enabled/example.com.conf

server {
    listen 80;
    server_name _;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name example.com;
    include /etc/nginx/conf.d/20-tls.conf;

    root /var/www;
}
```

Redirecting www
```nginx
# /etc/nginx/sites-enabled/www.example.com.conf

server {
    listen 443 ssl;
    server_name www.example.com;
    include /etc/nginx/conf.d/20-tls.conf;

    return 301 https://example.com$request_uri;
}
```

Reverse proxy
```nginx
# /etc/nginx/sites-enabled/example.com.conf

server {
    listen 443 ssl;
    server_name example.com;
    include /etc/nginx/conf.d/20-tls.conf;

    # Optional other path
    location / {
        root /var/www;
    }

    # Requests to /api/* will be proxied
    location /api {
        include /etc/nginx/conf.d/10-proxy-params.conf;
        proxy_pass http://127.0.0.1:8080;
    }
}
```
