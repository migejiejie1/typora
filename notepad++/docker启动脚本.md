```shell

docker run \
    --name wiki-element \
	-p 8080:80 \
    -d \
    --restart always \
    wiki/wiki-element:master.5

docker run \
    --name wiki-element \
	-p 8080:80 \
    -d \
    --restart always \
    wiki/wiki-element:master.5
	

docker run \
    --name nginx \
    -d \
    -v /data/nginx/nginx.conf:/etc/nginx/nginx.conf \
    -v /data/nginx/html:/usr/share/nginx/html \
    --restart always \
    -p 80:80 \
    nginx:latest
	
docker run \
 --name my-custom-nginx-container \
 -d nginx


version: '3'

services:
  nginx:
    container_name: nginx
    image: nginx:1.20.1
    restart: always
    privileged: true
    environment:
      - TZ='Asia/Shanghai'
    ports:
      - "80:80"
    volumes:
      - ./data/nginx.conf:/etc/nginx/nginx.conf
      - ./data/conf.d:/etc/nginx/conf.d
      - ./data/html:/usr/share/nginx/html
      - ./data/log:/var/log/nginx


docker run \
  --name jenkinsci \
  -d \
  -p 8080:8080 \
  -p 50000:50000 \
  --mount 'src=jenkinsci-data,target=/var/jenkins_home' \
  --mount 'type=bind,src=/var/run/docker.sock,target=/var/run/docker.sock' \
  --restart always \
  jenkinsci/blueocean
```

