
# 安装tars框架企业版本

使用容器方式完成tars企业版本框架的安装.

## 安装说明

- 正常情况下, 需要选取2n+1节点服务器, 从而完成框架组件的容灾;
- 如果有需要, 也可以在同一个服务器上安装三次, 每次安装在不同目录, 使用不同的节点名称;

## 启动框架

```shell

docker run -d --net=host --restart=always \
    -e TARS_OPEN_FRAMEWORK=true \
    -e TARS_LOCATOR="tcp -h $ip1 -p $port1:tcp -h $ip2 -p $port2:tcp -h $ip3 -p $port3" \
    -e TARS_NODENAME="$nodename" \
    -e TARS_REGISTRY="tcp -h $ip1 -p 7891" \
    -e TARS_LOCALIP="$localip" \
    -e TARS_START_PORT=9999 \
    -e TARS_MODE=cluster \
    -v /data/tars:/data/tars \
    --name framework-pro \
    docker.tarsyun.com/tars-pro/framework-pro
```

说明:
- 至少需要启动三个容器, 组成framework集群
- `TARS_OPEN_FRAMEWORK`: 部署framework, 如果设置为false, 则只启动节点(请参考后续)
- `TARS_LOCATOR`: 多节点的framework的tarsregistry.QueryObj的地址, 需要自己配置ip:port
- `TARS_REGISTRY`: :当前框架的的tarsregistry地址, 必须是`TARS_LOCATOR`中的一个
- `TARS_LOCALIP`: 本机的ip, 如果支持多网卡, 可以使用"0.0.0.0"
- `TARS_NODENAME`: 当前节点的名称, 注意nodename必须不同, 而且唯一
- `TARS_START_PORT`: 组件启用的起始端口, 如果不指定则随机分配端口, 否则从这个端口开始分配端口
- `TARS_MODE`: 运行模式, 默认是集群模式(cluster), 也可以设置为单节点方式(single)
- 可以在同一机器上启动三个容器, 注意自己控制好地址接口
- 除了核心组件`tars.tarsregistry.QueryObj`的端口是分配的, 这里是: port1, port2, port3, 其他端口都是随机分配的
- 每台节点, 都启动了web管理平台, 你可以通过: `http://${框架ip}:3000`, 打开管理平台

### 示例

- 在三台机器上启动容器, 并且三台容器构建成一个集群
 
比如在ip分别为: `192.168.11.1, 192.168.11.2, 192.168.11.3` 上部署容器, 你可以分别在三台机器上启动

在节点`192.168.11.1`上启动:
```shell
docker run -d --net=host --restart=always \
-e TARS_OPEN_FRAMEWORK=true \
-e TARS_LOCATOR="tcp -h 192.168.11.1 -p 7890:tcp -h 192.168.11.2 -p 7890:tcp -h 192.168.11.3 -p 7890" \
-e TARS_NODENAME="tarsnode-1" \
-e TARS_REGISTRY="tcp -h 192.168.11.1 -p 7890" \
-e TARS_LOCALIP="192.168.11.1" \
-e TARS_START_PORT=9000 \
-v /data/tars:/data/tars \
-v /etc/localtime:/etc/localtime \
--name framework-pro \
docker.tarsyun.com/tars-pro/framework-pro
```

在节点`192.168.11.2`上启动:
```shell
docker run -d --net=host --restart=always \
-e TARS_OPEN_FRAMEWORK=true \
-e TARS_LOCATOR="tcp -h 192.168.11.1 -p 7890:tcp -h 192.168.11.2 -p 7890:tcp -h 192.168.11.3 -p 7890" \
-e TARS_NODENAME="tarsnode-2" \
-e TARS_REGISTRY="tcp -h 192.168.11.2 -p 7890" \
-e TARS_LOCALIP="192.168.11.2" \
-e TARS_START_PORT=9100 \
-v /data/tars:/data/tars \
-v /etc/localtime:/etc/localtime \
--name framework-pro \
docker.tarsyun.com/tars-pro/framework-pro
```

在节点`192.168.11.3`上启动:
```shell
docker run -d --net=host --restart=always \
-e TARS_OPEN_FRAMEWORK=true \
-e TARS_LOCATOR="tcp -h 192.168.11.1 -p 7890:tcp -h 192.168.11.2 -p 7890:tcp -h 192.168.11.3 -p 7890" \
-e TARS_NODENAME="tarsnode-3" \
-e TARS_REGISTRY="tcp -h 192.168.11.3 -p 7890" \
-e TARS_LOCALIP="192.168.11.3" \
-e TARS_START_PORT=9200 \
-v /data/tars:/data/tars \
-v /etc/localtime:/etc/localtime \
--name framework-pro \
docker.tarsyun.com/tars-pro/framework-pro
```

- 在一台机器上启动三个容器, 并且构建成集群

比如在ip为`192.168.11.1`的机器上启动三个容器:

```shell
docker run -d --net=host --restart=always \
-e TARS_OPEN_FRAMEWORK=true \
-e TARS_LOCATOR="tcp -h 192.168.11.1 -p 7890:tcp -h 192.168.11.1 -p 7891:tcp -h 192.168.11.1 -p 7892" \
-e TARS_NODENAME="tarsnode-1" \
-e TARS_REGISTRY="tcp -h 192.168.11.1 -p 7890" \
-e TARS_LOCALIP="192.168.11.1" \
-e TARS_START_PORT=9000 \
-v /data/tars:/data/tars \
-v /etc/localtime:/etc/localtime \
--name framework-pro-1 \
docker.tarsyun.com/tars-pro/framework-pro
```

```shell
docker run -d --net=host --restart=always \
-e TARS_OPEN_FRAMEWORK=true \
-e TARS_LOCATOR="tcp -h 192.168.11.1 -p 7890:tcp -h 192.168.11.1 -p 7891:tcp -h 192.168.11.1 -p 7892" \
-e TARS_NODENAME="tarsnode-2" \
-e TARS_REGISTRY="tcp -h 192.168.11.1 -p 7891" \
-e TARS_LOCALIP="192.168.11.1" \
-e TARS_START_PORT=9100 \
-v /data/tars:/data/tars \
-v /etc/localtime:/etc/localtime \
--name framework-pro-2 \
docker.tarsyun.com/tars-pro/framework-pro
```


```shell
docker run -d --net=host --restart=always \
-e TARS_OPEN_FRAMEWORK=true \
-e TARS_LOCATOR="tcp -h 192.168.11.1 -p 7890:tcp -h 192.168.11.1 -p 7891:tcp -h 192.168.11.1 -p 7892" \
-e TARS_NODENAME="tarsnode-3" \
-e TARS_REGISTRY="tcp -h 192.168.11.3 -p 7892" \
-e TARS_LOCALIP="192.168.11.1" \
-e TARS_START_PORT=9200 \
-v /data/tars:/data/tars \
-v /etc/localtime:/etc/localtime \
--name framework-pro-3 \
docker.tarsyun.com/tars-pro/framework-pro

```

启动以后可以使用如下命令检查框架核心组件tarsregistry的状态:
```shell
docker exec ${containid} tarsctl framework status
```

可以通过`http://192.168.11.1:3000`, `http://192.168.11.2:3000`,`http://192.168.11.3:3000`, 打开管理平台!

- 在一台机器上以单节模式启动容器

```shell
docker run -d --net=host --restart=always \
-e TARS_OPEN_FRAMEWORK=true \
-e TARS_LOCATOR="tcp -h 192.168.11.1 -p 7890" \
-e TARS_NODENAME="master" \
-e TARS_LOCALIP="192.168.11.1" \
-e TARS_START_PORT=9200 \
-e TARS_MODE=single \
-v /data/tars:/data/tars \
-v /etc/localtime:/etc/localtime \
--name framework-pro \
docker.tarsyun.com/tars-pro/framework-pro

```

## 增加节点 
当部署框架以后, 我们可以增加节点, 也以容器方式增加节点, 命令如下:

```shell

docker run -d --net=host --restart=always \
    -e TARS_OPEN_FRAMEWORK=false \
    -e TARS_LOCATOR="tcp -h $ip1 -p $port1:tcp -h $ip2 -p $port2:tcp -h $ip3 -p $port3" \
    -e TARS_NODENAME="$nodename" \
    -e TARS_LOCALIP="$localip" \
    -v /data/tars:/data/tars \
    -v /etc/localtime:/etc/localtime \
    --name $nodename \
    docker.tarsyun.com/tars-pro/framework-pro
```

例如:

```shell

docker run -d --net=host --restart=always \
-e TARS_OPEN_FRAMEWORK=false \
-e TARS_LOCATOR="tcp -h 192.168.11.1 -p 7890:tcp -h 192.168.11.1 -p 7891:tcp -h 192.168.11.1 -p 7892" \
-e TARS_NODENAME="tarsnode-4" \
-e TARS_LOCALIP="192.168.11.1" \
-e TARS_START_PORT=9200 \
-v /data/tars:/data/tars \
-v /etc/localtime:/etc/localtime \
--name tarsnode-4 \
docker.tarsyun.com/tars-pro/framework-pro
```


