# 目录

> - [介绍](#chapter-1)
> - [前置说明](#chapter-2)
> - [使用说明](#chapter-3)

## 1 <span id="chapter-1"></span>介绍

在[docker 部署 Tars 的文档](./docker.md)中介绍了使用 docker 方式部署和启动框架的方式, 这种方案下, tarscloud/tarsnode 的镜像相当于一套虚拟机, 每个业务服务都是运行在这个镜像内部的.

但是有时候你希望服务隔离, 或者又希望服务环境能独立, 那么将业务服务独立到一个 docker 里面是可能更好的方式.

本文正是介绍如何将业务服务独立运行在 docker 里面.

## 2 <span id="chapter-2"></span>前置说明

无论你是使用源码部署还是 docker 部署, 都能够支持业务服务 docker 化, 当然前置条件如下:

### 版本前置

如果使用源码部署, 则:

- tarsframework>=v3.0.5
- tarsweb>=v2.4.27

如果使用 docker 部署, 则:

- tarscloud/framework>v3.0.6

### 服务器

服务器上必须安装 docker, 且 docker 的本地套接字地址最好默认是: `/var/run/docker.run` (通常安装都在这里, 如果不在这里, 则需要修改 Tars 框架中 tarsnode/tarsregistry 的模板, 指向实际的地址)

如果是 docker 部署方式, 请请映射宿主机的`/var/run/docker.run`到镜像中`/var/run/docker.run`, 比如:

```
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
    -v /var/run/docker.sock:/var/run/docker.sock \
    -p 3000:3000 \
    tarscloud/framework:v3.0.6

docker run -d \
    --name=tars-node \
    --net=tars \
    -e INET=eth0 \
    -e WEB_HOST="http://172.25.0.3:3000" \
    --ip="172.25.0.5" \
    -v /data/tars:/data/tars \
    -v /etc/localtime:/etc/localtime \
    -p 9000-9010:9000-9010 \
    -v /var/run/docker.sock:/var/run/docker.sock \
    tarscloud/tars-node:latest
```

## 3 <span id="chapter-3"></span>使用说明

主要使用流程如下:

- 业务服务可以运行在镜像中, 镜像在 web 平台->运维管理->镜像管理中配置
- 配置以后, 在服务的部署以及编辑界面, 都可以将服务设置为: 以容器方式启动
- 重启服务即可

注意事项:

- 业务服务以及 tarsregistry 所在的节点必须要能访问到镜像仓库
- 业务服务如果已经以非容器化方式启动, 变更为容器方式启动, 那么不需要再发布, 只需要重启即可, 反之亦然
- 容器的镜像你可以自己制作, 保证你的服务能在容器中运行即可
- 当然你也可以直接用官方的, 如果使用的官方的, 就需要注意你的编译环境是否和官方的匹配, 官方提供的运行镜像对应的编译环境是: tarscloud/base-compiler, 这个和 K8SFramework 版本保持一致.
- 官方几个语言的镜像如下:
  > - tarscloud/tars.cppbase
  > - tarscloud/tars.nodejsbase
  > - tarscloud/tars.javabase
  > - tarscloud/tars.php74base
