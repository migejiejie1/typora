```shell
upstream kibana{ 
      server 127.0.0.1:5601; 
}


server {
    listen 80;
    server_name  localhost;
    return 301 https://$host$request_uri; 
}

server {
        listen       443 ssl;
        server_name  localhost;
		
		ssl on;
		ssl_certificate /usr/local/nginx/conf/cert/server.crt;
        ssl_certificate_key /usr/local/nginx/conf/cert/server.key;
        ssl_session_timeout 5m;
        ssl_protocols TLSv1.1 TLSv1.2 TLSv1.3; 
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4; 
        ssl_prefer_server_ciphers on;
		
        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
		    add_header Access-Control-Allow-Origin *;
            #proxy_set_header Host $host:$server_port;
			proxy_set_header Host $host:$proxy_port;
            proxy_set_header x-forwarded-for $proxy_add_x_forwarded_for;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_connect_timeout 300;
            proxy_send_timeout    300;
            proxy_read_timeout    300;
            proxy_buffer_size     16k;
            proxy_buffers         4 64k;
            proxy_busy_buffers_size 128k;
            proxy_temp_file_write_size 128k;
            proxy_pass http://kibana/;
            root /usr/local/nginx/html;
            index index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }   
    }


```

