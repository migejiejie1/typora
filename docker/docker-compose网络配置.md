# docker-compose网络配置

## 未显式声明

*docker-compose中未显式声明，会生成默认的网络*

```yml
version: "3.4"
services:
  redis-web:
    image: redis-web:1.0
    container_name: redis-web
    restart: always
    environment:
      REDIS_HOST: redis-app
    ports:
      - 8001:8001
    depends_on:
      - redis-app
  redis-app:
    image: redis:latest
    container_name: redis-app
    restart: always
```

*启动的容器会被加入test_default中，其中`test为docker-compose的父文件夹名`*

```sh
[root@vm02 test]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
fba1a241ba5f        bridge              bridge              local
662b6e28d109        docker_gwbridge     bridge              local
e5180989ca67        host                host                local
91f9327572be        none                null                local
be43cf4b2125        test_default        bridge              local
```

## networks关键字自定义网络

```bash
version: '3.4'
services:
  web-1:
    image: redis-web:1.0
    container_name: web1
    networks:
      - front
      - back
  redis:
    image: redis
    container_name: redisdb
    networks:
      - back

networks:
  front:
    driver: bridge
  back:
    driver: bridge
    driver_opts:
      foo: "1"
      bar: "2"
```



```sh
[root@vm02 network-test]# docker network ls
NETWORK ID          NAME                 DRIVER              SCOPE
ca9419193d95        network-test_back    bridge              local
7c38ab9beba4        network-test_front   bridge              local
```

## 配置默认网络

*新建一个network*

```yml
version: '3.4'
services:
  web-1:
    image: redis-web:1.0
    container_name: web1
  redis:
    image: redis
    container_name: redisdb
networks:
  default:
    driver: bridge
```



```sh
[root@vm02 network-test]# docker network ls
NETWORK ID          NAME                   DRIVER              SCOPE
f1390c710b8c        network-test_default   bridge              local
```

## 使用现有网络

*新建一个network*

```sh
docker network create net-a --driver bridge
```



```yml
version: '3.4'
services:
  web-1:
    image: redis-web:1.0
    container_name: web1
  redis:
    image: redis
    container_name: redisdb
networks:
  default:
    external:
      name: net-a
```

## docker-compose中network_mode

*配置方式*

```bash
network_mode: "bridge"
network_mode: "host"
network_mode: "none"
network_mode: "service:[service name]"
network_mode: "container:[container name/id]"
```

*下面的docker-compose将不会生成新的网络*

```yml
version: '3.4'
services:
  web-1:
    network_mode: bridge
    image: redis-web:1.0
    container_name: web1
  redis:
    network_mode: bridge
    image: redis
    container_name: redisdb
```