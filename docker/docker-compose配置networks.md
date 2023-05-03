# docker-compose配置networks

## 默认网络

例如, 假设有一个项目，目录名`myapp`,  `docker-compose.yml` 配置如下:

```bash
version: "3"
services:
  web:
    build: .
    ports:
      - "8000:8000"
  db:
    image: postgres
    ports:
      - "8001:5432"
```

当执行 `docker-compose up` 的时候。会发生以下事情：

1. 会创建一个名字是 `myapp_default`的网络（networks）
2. `web`这个容器会加入到 `myapp_default`网络中，并且在网络中的名称为：`web` 。
3. `db`这个容器会加入到 `myapp_default`网络中，并且在网络中的名称为：`db`。

这里，每个容器都能通过应用名找到对方，例如，`web`容器可以通过 `postgres://db:5432` 来使用 Pg数据库。

上面例子还有一个注意点就是端口号，注意区分`HOST_PORT`和`CONTAINER_PORT`，以上面的db为例：

- `8001` 是宿主机的端口
- `5432`（postgres的默认端口） 是容器的端口

当容器之间通讯时 ， 是通过 `CONTAINER_PORT` 来连接的。

这里有宿主机端口，那么容器就可以通过宿主机端口和外部应用连接。

## 更新容器

对已经启动的容器，再执行 `docker-compose up` 的时候，旧容器删除，然后创建一个新的容器。

新容器会加入到网络，相同的网络名称，但容器IP是不一样的。已经连接的其他容器会自己重连到新的容器IP上。

## 自定义网络

可能通过一级配置`networks`来自定义网络，可以创建更复杂的网络选项和配置，也可以用来连接已经存在的网络（不是通过compose创建的）

每个`service` 配置下也可以指定`networks`配置，来指定一级配置的网络。

例如：

```bash
version: "3"
services:

  proxy:
    build: ./proxy
    networks:
      - frontend
  app:
    build: ./app
    networks:
      - frontend
      - backend
  db:
    image: postgres
    networks:
      - backend

networks:
  frontend:
    # Use a custom driver
    driver: custom-driver-1
  backend:
    # Use a custom driver which takes special options
    driver: custom-driver-2
    driver_opts:
      foo: "1"
      bar: "2"
```

一级配置`networks` 用来创建自定义的网络 。这里配置了两个`frontend`和`backend` . 且自定义了网络类型。

每一个serviceg下，`proxy` , `app` , `db`都定义了一下`networks`配置。

1. `proxy` 只加入到 `frontend`网络。
2. `db` 只加入到`backend`网络。
3. `app`同时加入到 `frontend`和`backend` 。
4. `db`和`proxy`不能通讯，因为不在一个网络中。
5. `app`和两个都能通讯，因为`app`在两个网络中都有配置。
6. `db`和`proxy`要通讯，只能通过`app`这个应用来连接。

## 配置默认网络

不指定网络时，默认的网络也是可以配置的。不配置的话，默认是使用：`brige`，也可以修改为其他 的。

```php
version: "3"
services:

  web:
    build: .
    ports:
      - "8000:8000"
  db:
    image: postgres

networks:
  default:
    # Use a custom driver
    driver: custom-driver-1
```

## 指定一个已经存在的网络

多个容器，不在相同的配置中，也会有网络通讯的需求 。那么就可以使用公共的网络配置。

容器可以加入到已经存在的网络。

```dart
networks:
  default:
    external:
      name: my-pre-existing-network
```

这里`name`就是指定已经存在的网络名称。