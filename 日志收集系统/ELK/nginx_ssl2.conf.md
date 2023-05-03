```shell
server {
    listen 443 ssl;
    server_name vault.mige.com;
    
    ssl_certificate  /etc/nginx/cert/private.crt;
    ssl_certificate_key /etc/nginx/cert/private.key;

    ssl_session_timeout  5m;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers  HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers   on;
    large_client_header_buffers 4 32k;
    client_max_body_size 300m;
    client_body_buffer_size 512k;
    proxy_connect_timeout 600;
    proxy_read_timeout   600;
    proxy_send_timeout   600;
    proxy_buffer_size    128k;
    proxy_buffers       4 64k;
    proxy_busy_buffers_size 128k;
    proxy_temp_file_write_size 512k;

    location / {
       proxy_set_header Host $host;
       proxy_set_header X-Forwarded-Proto $scheme;
       proxy_set_header X-Forwarded-Port $server_port;
       proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
       proxy_http_version 1.1;
       proxy_redirect off;
       proxy_set_header X-Real-IP $remote_addr;
       proxy_pass http://10.160.19.61:8888;
       proxy_read_timeout 900s;
    }

    error_page   500 502 503 504  /50x.html;

    gzip on;
    gzip_min_length  5k;
    gzip_buffers     4 16k;
    gzip_http_version 1.0;
    gzip_comp_level 4;
    gzip_types       text/plain application/javascript application/x-javascript application/font-woff text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
    gzip_vary on;
}

server {
    listen 80;
    server_name vault.mige.com;
    
    rewrite ^(.*)$ https://$host$1 permanent;
}

```

