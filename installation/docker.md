# 目录

> - [介绍](#chapter-1)
> - [Docker 部署 Tars 开发环境](#chapter-2)
> - [Docker 部署 Tars 生产环境](#chapter-3)
> - [问题检查](#chapter-4)
> - [镜像加速](#chapter-5)
> - [docker-compose 部署开发环境](#chapter-6)
> - [docker 版本说明](#chapter-7)
> - [docker 高级方式](#chapter-8)

## 1 <span id="chapter-1"></span>介绍

本节主要介绍采用 docker 来完成框架的部署:

- framework: Tars 框架 Docker 制作脚本, 制作的 docker 包含了框架核心服务和 web 管理平台
- tars-node: tarsnode 节点的镜像，包含各语言的运行时环境，可以将服务发布到 tars-node 容器中, tarsnode 每台机器都存在, 它连接到 framework
- 部署完成后, 打开 framework 主节点上 web 管理平台, 你可以通过 web 管理平台部署和发布服务, 将这些服务发布到 tarsnode 所在的机器上

Docker 开发环境部署可以很方便的在本地拉起服务开始服务的部署、开发和测试。开发环境部署采用单机多容器的部署方式模拟生产环境的服务部署结构。Docker 生产环境部署为生产主机部署 Tars 服务提供参考，相关参数需要根据具体环境变更调整。

开始操作之前，请确保你的服务上已经安装了 docker 环境, 如果没有, 可以参考[docker install](docker-install.md)

## 2 <span id="chapter-2"></span>Docker 部署服务开发环境

**如果你想源码自己编译 docker, 请参见 [Install](source.md)**

当然你可以在多台机器上搭建一套环境, 将你的业务服务发布到这套环境上测试.

如果你没有多台机器, 你又需要完整的搭建环境, 你可以通过以下方式来完成:

- 使用 docker 将 framework/tarsnode 部署在同一台机器上;
- tarsnode 你可以启动多个 docker, 每个 ip 都不同, 表示多台节点机器
- 由于使用 docker, 为了保证 framework/tarsnode 这些 docker 的网络互通, 你需要创建虚拟网络, 以连接这些 docker(这里是 docker 的知识, 有需要自己百度)
- 如果使用--net=host 方式, 你无法在同一台机器上部署 framework/tarsnode, 因为他们使用了相同的端口, 会带来端口冲突

#### 2.1 创建 docker 虚拟网络

为了方便虚拟机、Mac、Linux 主机等各种环境下的 docker 部署，在本示例中先创建虚拟网络，模拟现实中的局域网内网环境(注意 docker 都还是在同一台机器, 只是 docker 的虚拟 ip 不同, 模拟多机)

```sh
# 创建一个名为tars的桥接(bridge)虚拟网络，网关172.25.0.1，网段为172.25.0.0
docker network create -d bridge --subnet=172.25.0.0/16 --gateway=172.25.0.1 tars
```

#### 2.2 启动 MySQL

- 为框架运行提供 MySQL 服务，若使用宿主机或现有的 MySQL 可以跳过此步骤，建议框架和应用使用不同的 MySQL 服务。
- 注意 MySQL 的 IP 和 root 密码，后续构建中需要使用

```
docker run -d -p 3306:3306 \
    --net=tars \
    -e MYSQL_ROOT_PASSWORD="123456" \
    --ip="172.25.0.2" \
    -v /data/framework-mysql:/var/lib/mysql \
    -v /etc/localtime:/etc/localtime \
    --name=tars-mysql \
    mysql:5.6
```

- 如果使用 MySQL 8，启动时需要关闭 ssl

```
docker run -d -p 3306:3306 \
    --net=tars \
    -e MYSQL_ROOT_PASSWORD="123456" \
    --ip="172.25.0.2" \
    -v /data/framework-mysql:/var/lib/mysql \
    -v /etc/localtime:/etc/localtime \
    --name=tars-mysql \
    mysql:8.0.27 \
    --tls-version=invalid
```

如果你使用的不是 8.0.27 版本，可以通过 `docker logs tars-mysql` 查看启动日志，确认是否有警告或者错误，或者可以尝试以下参数

```
docker run -d -p 3306:3306 \
    --net=tars \
    -e MYSQL_ROOT_PASSWORD="123456" \
    --ip="172.25.0.2" \
    -v /data/framework-mysql:/var/lib/mysql \
    -v /etc/localtime:/etc/localtime \
    --name=tars-mysql \
    mysql:8.0.27 \
    --ssl=0
```

由于 MySQL 8 开始，默认的 authentication plugin 从 mysql_native_password 修改为 caching_sha2_password，更详细的信息，可以查看 MySQL 官网

[upgrading-from-previous-series](https://dev.mysql.com/doc/refman/8.0/en/upgrading-from-previous-series.html)

鉴于以上原因，需要手动修改 root 用户的 plugin，以此来兼容旧的 mysqlclient 能正常连接 MySQL 8，登录 MySQL 执行以下语句

```
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456';
FLUSH PRIVILEGES;
```

为了验证 MySQL 是否正常启动且能正常连接，可通过 host 中的 mysql 客户端进行登录验证

```
mysql -h 172.25.0.2 -u root -p
```

也可以使用后面已经下载启动的 tars-framework docker 节点进行验证，可以等下再回来操作；

执行 tars-framework 中的 mysql-tool

```
docker exec -it tars-framework /bin/bash

cd /usr/local/tars/cpp/deploy/

./mysql-tool --host=172.25.0.2 --user="root" --pass="123456" --port=3306 --check
```

#### 2.3 使用 tarscloud/framework 部署框架

1. 拉取镜像

最新版本

```sh
docker pull tarscloud/framework:latest
```

指定版本:

```sh
docker pull tarscloud/framework:v{x.y.z}
```

说明:

- 使用指定版本，如：`v2.4.17`，便于开发和生产环境的部署，后期需要升级时可选择更新的版本 tag，升级之前请先查看 GitHub 的 changelog，避免升级到不兼容的版本造成损失。
- 注意这里的 Tag 不是源码 TarsFramework 的 tag 号, 而是 Tars 这个 GIT 仓库的 tag 号, 因为 tarscloud/framework 还包含了 TarsWeb
- 当部署好以后, TarsWeb 页面的右上角显示了内置的 TarsFramework&TarsWeb 的版本号, 以便你对应源码.

2. 启动镜像(目前只考虑了 linux 上, 时间和本机同步)

```sh
# 挂载的/etc/localtime是用来设置容器时区的，若没有可以去掉
# 3000端口为web程序端口
# 3001端口为web授权相关服务端口(docker>=v2.4.7可以不暴露该端口)
docker run -d \
    --name=tars-framework \
    --net=tars \
    -e MYSQL_HOST="172.25.0.2" \
    -e MYSQL_ROOT_PASSWORD="123456" \
    -e MYSQL_USER=root \
    -e MYSQL_PORT=3306 \
    -e REBUILD=false \
    -e INET=eth0 \
    -e SLAVE=false \
    --ip="172.25.0.3" \
    -v /data/framework:/data/tars \
    -v /etc/localtime:/etc/localtime \
    -p 3000:3000 \
    -p 3001:3001 \
    tarscloud/framework:v3.0.4
```

安装完毕后, 访问 `http://${your_machine_ip}:3000` 打开 web 管理平台

3. 目录说明

创建时, 会将 docker 的目录/data/tars 映射到宿主机目录/data/framework, 启动 docker 后, 请检查宿主机目录: /data/tars, 正常情况下会有创建以下几个目录:

- app_log: tars 服务的日志目录
- tarsnode-data: tarsnode/data 目录(存放发布到 docker 的业务服务), 保证 docker 重启, 数据不丢失
- web_log: web 中 tars-node-web 模块的日志(主机才有)
- demo_log: web 中 tars-user-system 模块的日志(主机才有), (docker>=v2.4.7 这个目录下无内容了)
- patchs: 上传的发布包(主机才有)

如果这几个目录没有创建, 你可以手工创建, 再重启 docker.

4. 参数解释

MYSQL_IP: mysql 数据库的 ip 地址

MYSQL_ROOT_PASSWORD: mysql 数据库的 root 密码

INET: 网卡的名称(ifconfig 可以看到, 比如 eth0), 表示框架绑定本机 IP, 注意不能是 127.0.0.1

REBUILD: 是否重建数据库,通常为 false, 如果中间装出错, 希望重置数据库, 可以设置为 true

SLAVE: 是否是从节点, 可以部署多台机器, 通常一主多从即可.

MYSQL_USER: mysql 用户, 默认是 root

MYSQL_PORT: mysql 端口

5. 启动框架从节点

**如果希望多节点部署, 则在不同机器上执行 docker run ...即可, 注意参数设置!**

```sh
docker run -d \
    --name=tars-framework-slave \
    --net=tars \
    -e MYSQL_HOST="172.25.0.2" \
    -e MYSQL_ROOT_PASSWORD="123456" \
    -e MYSQL_USER=root \
    -e MYSQL_PORT=3306 \
    -e REBUILD=false \
    -e INET=eth0 \
    -e SLAVE=true \
    --ip="172.25.0.4" \
    -v /data/framework-slave:/data/tars \
    -v /etc/localtime:/etc/localtime \
    tarscloud/framework:v3.0.4
```

**注意:SLAVE 参数不同**

#### 2.4 Docker 部署 Tars 应用节点

**Tars 应用节点镜像默认为集合环境(Java+GoLang+NodeJs+PHP)的镜像，如果需要可登陆 Docker Hub 查看各语言相关 tag**

1. 拉取镜像

最新版本:

```sh
docker pull tarscloud/tars-node:latest
```

2. 启动 Node(目前只考虑了 linux 上, 时间和本机同步)

最新版本:

```sh
docker run -d \
    --name=tars-node \
    --net=tars \
    -e INET=eth0 \
    -e WEB_HOST="http://172.25.0.3:3000" \
    --ip="172.25.0.5" \
    -v /data/tars:/data/tars \
    -v /etc/localtime:/etc/localtime \
    -p 9000-9010:9000-9010 \
    tarscloud/tars-node:latest
```

- 初始开放了 9000~9010 端口供应用使用，若不够可自行添加
- Node 启动之后会自动向框架 172.25.0.3 进行注册，部署完成之后在框架的 运维管理-》节点管理 中可以看到 IP 为 `172.25.0.5` 的节点启动

**注意, 如果在同一台机器上采用--net=host, 同时启动 framework 和 tars-node 镜像, 是不行的, 因为 framework 中也包含了一个 tars-node, 会导致端口冲突, 启动不了**

## 3 <span id="chapter-3"></span>Docker 部署服务生产环境

概要说明:

- tarscloud/framework:v{x.y.y} 部署一主, 多从
- tarscloud/framework:v{x.y.y} 以--net=host 的方式启动, 即和宿主机相同网络
- 节点应用服务器使用 tarscloud/tars-node:latest, 也以--net=host 方式启动
- tarscloud/tars-node 有多个不同的标签如下:
  > - tarscloud/tars-node:stable, tarscloud/tars-node:latest, tarscloud/tars-node:full, 全环境, 即 jdk, php, nodejs 的运行时环境都已经安装
  > - tarscloud/tars-node:cpp, 跑 cpp/go 服务
  > - tarscloud/tars-node:java, 安装了 jdk
  > - tarscloud/tars-node:nodejs, 安装了 nodejs
  > - tarscloud/tars-node:php, 安装了 php

如果想自己构建镜像, 请参考:
https://github.com/TarsCloud/TarsDocker

### 3.1 Docker 部署 Tars 框架服务

```bash
# 挂载的/etc/localtime是用来设置容器时区的，若没有可以去掉
# --net=host 代表docker使用宿主机网络
# INET=eth0 eth0为网卡名称，tars脚本会根据网卡名称获取IP并将服务绑定到获取到的IP上
docker run -d \
    --name=tars-framework \
    --net=host \
    -e MYSQL_HOST="Host IP For MySQL Service" \
    -e MYSQL_ROOT_PASSWORD="Your Root Password" \
    -e MYSQL_USER=root \
    -e MYSQL_PORT=3306 \
    -e REBUILD=false \
    -e SLAVE=false \
    -e INET=eth0 \
    -v /etc/localtime:/etc/localtime \
    -v /tmp/test/data:/data/tars \
    tarscloud/framework:v3.0.4
```

**注意目录映射, 保证了 docker 重启, 数据不会丢失**

### 3.2 Docker 部署 Tars 应用节点

在每台节点机器上运行 docker:

```bash
# 挂载的/etc/localtime是用来设置容器时区的，若没有可以去掉
# --net=host 代表docker使用宿主机网络
# INET=eth0 eth0为网卡名称，tars脚本会根据网卡名称获取IP并将服务绑定到获取到的IP上
docker run -d \
    --name=tars-node \
    --net=host \
    -e INET=eth0 \
    -e WEB_HOST="The Accessible Http Address and Port Of Your Tars Framework" \
    -v /data/tars:/data/tars \
    -v /etc/localtime:/etc/localtime \
    tarscloud/tars-node:latest
```

**注意目录映射, 保证了 docker 重启, 数据不会丢失**

说明:

- 需要注意网络的联通性, 运行 tarsnode 的机器网络必须和 framework 网络连通
- tarsnode 和 framework 不能部署在同一台机器上, 端口会冲突
- 除了 cpp/go 服务, 其他语言服务无法部署在 framework 里面, 因为 framework 的 docker 不带有 nodejs/java/php 的运行环境

## 4 <span id="chapter-4"></span>问题检查

如果 docker 运行后, 仍然无法打开管理平台, 可以如下检查:

- 关闭 docker, 注意需要用 docker stop .... 来关闭(docker 的使用请自行搜索)
- 启动镜像, 注意不要 -d 参数

```sh
docker --name=tars-framework \
    --net=tars \
    -e MYSQL_HOST="172.25.0.2" \
    -e MYSQL_ROOT_PASSWORD="123456" \
    -e MYSQL_USER=root \
    -e MYSQL_PORT=3306 \
    -e REBUILD=false \
    -e INET=eth0 \
    -e SLAVE=false \
    --ip="172.25.0.3" \
    -v /data/framework:/data/tars \
    -v /etc/localtime:/etc/localtime \
    -p 3000:3000 \
    tarscloud/framework:v3.0.4
```

- 查看 docker 输出是否有明显问题
- node 的问题检查与框架一致

如果 web 平台打开, 但是显示错误, 就需要检查 web 的问题, 可以进入 docker, 请参考[检查 web 的问题](web.md)中的检查 web 问题

## 5 <span id="chapter-5"></span>镜像加速

- 目前已搭建了 `docker.tarsyun.com/tarscloud/framework` 和 `docker.tarsyun.com/tarscloud/tars-node` 的镜像加速

## 6 <span id="chapter-6"></span>开发环境 docker-compose

- 下面是使用 docker-compose 直接拉起开发环境的示例，仅供学习参考
- ./source/Shanghai 是 linux 时区设置文件，需要自行获取

```yaml
version: "3"

services:
  mysql:
    image: mysql:5.6
    container_name: tars-mysql
    ports:
      - "3307:3306"
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: "123456"
    volumes:
      - ./mysql/data:/var/lib/mysql:rw
      - ./source/Shanghai:/etc/localtime
    networks:
      internal:
        ipv4_address: 172.25.1.2
  framework:
    image: tarscloud/framework:v3.0.4
    container_name: tars-framework
    ports:
      - "3000:3000"
    restart: always
    networks:
      internal:
        ipv4_address: 172.25.1.3
    environment:
      MYSQL_HOST: "172.25.1.2"
      MYSQL_ROOT_PASSWORD: "123456"
      MYSQL_USER: "root"
      MYSQL_PORT: 3306
      REBUILD: "false"
      INET: eth0
      SLAVE: "false"
    volumes:
      - ./framework/data:/data/tars:rw
      - ./source/Shanghai:/etc/localtime
    depends_on:
      - mysql
  node:
    image: tarscloud/tars-node:latest
    container_name: tars-node
    restart: always
    networks:
      internal:
        ipv4_address: 172.25.1.5
    volumes:
      - ./node/data:/data/tars:rw
      - ./source/Shanghai:/etc/localtime
    environment:
      INET: eth0
      WEB_HOST: http://172.25.1.3:3000
    ports:
      - "9000-9010:9000-9010"
    depends_on:
      - framework
networks:
  internal:
    driver: bridge
    ipam:
      config:
        - subnet: 172.25.1.0/16
```

## 7 <span id="chapter-7"></span>docker 版本说明

说明:

- docker 内部主要包含了: https://github.com/TarsCloud/TarsFramework 和 https://github.com/TarsCloud/TarsWeb 的服务.
- TarsFramework 和 TarsWeb 都作为 submodule 挂在了 https://github.com/TarsCloud/Tars 下面.
- 因此为了更好的管理 docker 版本, docker 的版本标签对应: https://github.com/TarsCloud/Tars 的 Tag
- https://github.com/TarsCloud/Tars 打 tag 时会自动编译并推送 docker 到 docker hub 上

以上执行方式, 从 tarscloud/framework:v2.4.0 以后才支持

## 8 <span id="chapter-8"></span>docker 高级使用方式

以 docker 方式启动, 业务服务实际上发布以后时运行在 tarsnode 的容器中的, 即 tarsnode 容器相当于一台虚拟机, 内部可能同时运行多个业务服务.

但是新版(tarsframework>v3.0.5, tarsweb>v2.4.27, tarscloud/framework>v3.0.6)的版本, 业务可以以独立容器运行, 此时需要在 tarsweb 运维管理->镜像管理中配置仓库和基础镜像, 然后服务配置中设置为以容器方式运行即可, 这样每个服务都可以运行在独立的容器中.

注意, 如果你的 tarsnode 是以容器方式运行的, 则启动时需要映射 docker 的 socket:

```
-v /var/run/docker.sock:/var/run/docker.sock
```

具体请看文档 [服务 docker 化文档](./service-docker.md)
