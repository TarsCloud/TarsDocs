# 目录

> - [介绍](#chapter-1)
> - [Docker 部署 Tars 框架](#chapter-2)
> - [Docker 部署 Tars Node](#chapter-3)
> - [问题检查](#chapter-4)
> - [镜像加速](#chapter-5)
> - [docker-compose](#chapter-6)
> - [论坛邀请](#chapter-7)

# 1 <a id="chapter-1"></a>介绍

本节主要介绍采用 docker 来完成框架的部署, 有两种 docker 可用:

- framework: Tars 框架 Docker 制作脚本, 制作的 docker 包含了框架核心服务和 web 管理平台
- tars: Tars 框架 Docker 制作脚本, 和 framework 比, 增加了 java, nodejs 等运行时支持, 即可以把 java, nodejs 服务发布到 docker 里面(docker 里面安装了 jdk, node, php 环境)

首先确保你的服务上已经安装了 docker 环境, 如果没有, 可以参考[docker install](docker-install.md)

## 2 <a id="chapter-2"></a>Docker 部署 Tars 框架

**如果你想源码自己编译 docker, 请参见 [Install](source.md)**

使用 docker 安装 Tars 框架, 有两个镜像可供选择: framework / tars

**注意: 区别在于是否希望把业务服务部署在镜像内(不推荐, 不方便 Tars 框架升级)**

### 2.1 创建 docker 虚拟网络

为了方便虚拟机、Mac、Linux 主机等各种环境下的 docker 部署，在本示例中先创建虚拟网络，模拟现实中的局域网内网环境

```sh
# 创建一个名为tars的桥接(bridge)虚拟网络，网关172.25.0.1，网段为172.25.0.0
docker network create -d bridge --subnet=172.25.0.0/16 --gateway=172.25.0.1 tars
```

### 2.2 启动 MySQL

- 为框架运行提供 MySQL 服务，若使用宿主机或现有的 MySQL 可以跳过此步骤，建议框架和应用使用不同的 MySQL 服务。
- 注意 MySQL 的 IP 和 root 密码，后续构建中需要使用

```
docker run -d \
    --net=tars \
    -e MYSQL_ROOT_PASSWORD="123456" \
    --ip="172.25.0.2" \
    -v /data/framework-mysql:/var/lib/mysql \
    -v /etc/localtime:/etc/localtime \
    --name=tars-mysql \
    mysql:5.6
```

### 2.3 使用 tarscloud/framework 部署

1. 拉取镜像

最新版本
```sh
docker pull tarscloud/framework:latest
```

稳定版本:
```sh
docker pull tarscloud/framework:stable
```

2. 启动镜像(目前只考虑了 linux 上, 时间和本机同步)

最新版本:
```sh
# 挂载的/etc/localtime是用来设置容器时区的，若没有可以去掉
# 3000端口为web程序端口
# 3001端口为web授权相关服务端口
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
    tarscloud/framework:latest
```

稳定版本:
```sh
# 挂载的/etc/localtime是用来设置容器时区的，若没有可以去掉
# 3000端口为web程序端口
# 3001端口为web授权相关服务端口
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
    tarscloud/framework:stable
```

安装完毕后, 访问 `http://${your_machine_ip}:3000` 打开 web 管理平台

3. 目录说明

创建时, 会将 docker 的目录/data/tars 映射到宿主机目录/data/tars, 启动 docker 后, 请检查宿主机目录: /data/tars, 正常情况下会有创建以下几个目录:

- app_log: tars 服务的日志目录
- tarsnode-data: tarsnode/data 目录(存放发布到 docker 的业务服务), 保证 docker 重启, 数据不丢失
- web_log: web 中 tars-node-web 模块的日志(主机才有)
- demo_log: web 中 tars-user-system 模块的日志(主机才有)
- patchs: 上传的发布包(主机才有)

如果这几个目录没有创建, 你可以手工创建, 再重启 docker.

4. 参数解释

MYSQL_IP: mysql 数据库的 ip 地址

MYSQL_ROOT_PASSWORD: mysql 数据库的 root 密码

INET: 网卡的名称(ifconfig 可以看到, 比如 eth0), 表示框架绑定本机 IP, 注意不能是 127.0.0.1

REBUILD: 是否重建数据库,通常为 false, 如果中间装出错, 希望重置数据库, 可以设置为 true

SLAVE: 是否是从节点, 可以部署多台机器, 通常一主一从即可.

MYSQL_USER: mysql 用户, 默认是 root

MYSQL_PORT: mysql 端口

5. 启动框架从节点

**如果希望多节点部署, 则在不同机器上执行 docker run ...即可, 注意参数设置!**

最新版本:
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
    docker.tarsyun.com/tarscloud/framework:stable
```

稳定版本:
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
    docker.tarsyun.com/tarscloud/framework:stable
```

**注意:SLAVE 参数不同**

## 3 <a id="chapter-3"></a>Docker 部署 Tars Node

1. 拉取镜像

最新版本:

```sh
docker pull tarscloud/tars-node:latest
```

稳定版本
```sh
docker pull tarscloud/tars-node:stable
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
    -v /data/node:/data/tars \
    -v /etc/localtime:/etc/localtime \
    -p 9000-9010:9000-9010 \
    tarscloud/tars-node:latest
```

稳定版本:
```sh
docker run -d \
    --name=tars-node \
    --net=tars \
    -e INET=eth0 \
    -e WEB_HOST="http://172.25.0.3:3000" \
    --ip="172.25.0.5" \
    -v /data/node:/data/tars \
    -v /etc/localtime:/etc/localtime \
    -p 9000-9010:9000-9010 \
    tarscloud/tars-node:stable
```

- 初始开放了 9000~9010 端口供应用使用，若不够可自行添加
- Node 启动之后会自动向框架 172.25.0.3 进行注册，部署完成之后在框架的 运维管理-》节点管理 中可以看到 IP 为 `172.25.0.5` 的节点启动

## 4 <a id="chapter-4"></a>问题检查

如果 docker 运行后, 仍然无法打开管理平台, 可以如下检查:

- 关闭 docker, 注意需要用 docker stop .... 来关闭(docker 的使用请自行搜索)
- 启动镜像, 注意不要 -d 参数

最新版本:
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
    -p 3001:3001 \
    tarscloud/framework:stable
```

稳定版本:
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
    -p 3001:3001 \
    tarscloud/framework:stable
```

- 查看 docker 输出是否有明显问题
- node 的问题检查与框架一致

如果 web 平台打开, 但是显示错误, 就需要检查 web 的问题, 可以进入 docker, 请参考[检查 web 的问题](web.md)中的检查 web 问题

## 5 <a id="chapter-5"></a>镜像加速

- 目前已搭建了 `docker.tarsyun.com/tarscloud/framework` 和 `docker.tarsyun.com/tarscloud/tars-node` 的镜像加速

**此方法仅适用于 linux 环境**

```sh
curl -sSL https://doc.tarsdoc.com/docker_set_mirror.sh | sh -s https://w1mnep2c.mirror.aliyuncs.com
systemctl restart docker
```

## 6 <a id="chapter-6"></a>docker-compose

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
    image: tarscloud/framework:stable
    container_name: tars-framework
    ports:
      - "3000:3000"
      - "3001:3001"
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
    image: tarscloud/tars-node:stable
    container_name: tars-node
    restart: always
    networks:
      internal:
        ipv4_address: 172.25.1.5
    volumes:
      - ./node/data:/data/:rw
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

## 7 <a id="chapter-7"></a>论坛邀请

欢迎大家加入 TarsDoc 论坛一起探讨在 Tars 使用过程中遇到的问题和新的体会

[论坛传送门](https://bbs.tarsdoc.com/forum.php)
