
# 安装tars框架企业版本

使用容器方式完成tars企业版本框架的安装.

## 安装说明

- 正常情况下, 需要选取2n+1节点服务器, 从而完成框架组件的容灾;
- 如果有需要, 也可以在同一个服务器上安装三次, 每次安装在不同目录, 使用不同的节点名称;

这里镜像地址(${tars-framework-pro})请找管理员询问:


## 启动框架

```shell

docker run -d --rm --net=host \
    -e TARS_OPEN_FRAMEWORK=true \
    -e TARS_LOCATOR="tcp -h $ip1 -p $port1:tcp -h $ip2 -p $port2:tcp -h $ip3 -p $port3" \
    -e TARS_NODENAME="$nodename" \
    -e TARS_REGISTRY="tcp -h $ip1 -p 7891" \
    -e TARS_LOCALIP="$localip" \
    -e TARS_OPEN_WEB=true \
    -e TARS_START_PORT=9999 \
    --name framework-pro \
    ${tars-framework-pro}
```

说明:
- 至少需要启动三个容器, 组成framework集群
- `TARS_LOCATOR`指定了每个framework的地址, 需要自己配置好ip, port
- `TARS_REGISTRY`指定了当前启动的这个容器的tarsregistry地址, 必须是`TARS_LOCATOR`中的一个
- `TARS_LOCALIP`指定本机的ip, 如果支持多网卡, 可以使用"0.0.0.0"
- `TARS_NODENAME`指定当前节点的名称, 注意nodename必须不同, 而且唯一
- `TARS_OPEN_WEB`是否启动web管理平台
- `TARS_START_PORT`组件启用的起始端口, 如果不指定则随机分配端口
- 可以在同一机器上启动三个容器, 注意自己控制好地址接口
- 除了核心组件`tars.tarsregistry.QueryObj`的端口是分配的, 这里是: port1, port2, port3, 其他端口都是随机分配的

## 示例

- 在三台机器上启动容器
 
比如在ip分别为: `192.168.11.1, 192.168.11.2, 192.168.11.3` 上部署容器, 你可以分别在三台机器上启动

在节点`192.168.11.1`上启动:
```shell
docker run -d --rm --net=host \
-e TARS_OPEN_FRAMEWORK=true \
-e TARS_LOCATOR="tcp -h 192.168.11.1 -p 7890:tcp -h 192.168.11.2 -p 7890:tcp -h 192.168.11.3 -p 7890" \
-e TARS_NODENAME="tarsnode-1" \
-e TARS_REGISTRY="tcp -h 192.168.11.1 -p 7890" \
-e TARS_LOCALIP="192.168.11.1" \
-e TARS_OPEN_WEB=true \
-e TARS_START_PORT=9000 \
--name framework-pro \
${tars-framework-pro}
```

在节点`192.168.11.2`上启动:
```shell
docker run -d --rm --net=host \
-e TARS_OPEN_FRAMEWORK=true \
-e TARS_LOCATOR="tcp -h 192.168.11.1 -p 7890:tcp -h 192.168.11.2 -p 7890:tcp -h 192.168.11.3 -p 7890" \
-e TARS_NODENAME="tarsnode-2" \
-e TARS_REGISTRY="tcp -h 192.168.11.2 -p 7890" \
-e TARS_LOCALIP="192.168.11.2" \
-e TARS_OPEN_WEB=true \
-e TARS_START_PORT=9100 \
--name framework-pro \
${tars-framework-pro}
```

在节点`192.168.11.3`上启动:
```shell
docker run -d --rm --net=host \
-e TARS_OPEN_FRAMEWORK=true \
-e TARS_LOCATOR="tcp -h 192.168.11.1 -p 7890:tcp -h 192.168.11.2 -p 7890:tcp -h 192.168.11.3 -p 7890" \
-e TARS_NODENAME="tarsnode-3" \
-e TARS_REGISTRY="tcp -h 192.168.11.3 -p 7890" \
-e TARS_LOCALIP="192.168.11.3" \
-e TARS_OPEN_WEB=true \
-e TARS_START_PORT=9200 \
--name framework-pro \
${tars-framework-pro}
```

- 在一台机器上启动三个容器

比如在ip为`192.168.11.1`的机器上启动三个容器:

```shell
docker run -d --rm --net=host \
-e TARS_OPEN_FRAMEWORK=true \
-e TARS_LOCATOR="tcp -h 192.168.11.1 -p 7890:tcp -h 192.168.11.1 -p 7891:tcp -h 192.168.11.1 -p 7892" \
-e TARS_NODENAME="tarsnode-1" \
-e TARS_REGISTRY="tcp -h 192.168.11.1 -p 7890" \
-e TARS_LOCALIP="192.168.11.1" \
-e TARS_OPEN_WEB=true \
-e TARS_START_PORT=9000 \
--name framework-pro-1 \
${tars-framework-pro}
```

```shell
docker run -d --rm --net=host \
-e TARS_OPEN_FRAMEWORK=true \
-e TARS_LOCATOR="tcp -h 192.168.11.1 -p 7890:tcp -h 192.168.11.1 -p 7891:tcp -h 192.168.11.1 -p 7892" \
-e TARS_NODENAME="tarsnode-2" \
-e TARS_REGISTRY="tcp -h 192.168.11.1 -p 7891" \
-e TARS_LOCALIP="192.168.11.1" \
-e TARS_START_PORT=9100 \
--name framework-pro-2 \
${tars-framework-pro}
```


```shell
docker run -d --rm --net=host \
-e TARS_OPEN_FRAMEWORK=true \
-e TARS_LOCATOR="tcp -h 192.168.11.1 -p 7890:tcp -h 192.168.11.1 -p 7891:tcp -h 192.168.11.1 -p 7892" \
-e TARS_NODENAME="tarsnode-3" \
-e TARS_REGISTRY="tcp -h 192.168.11.3 -p 7892" \
-e TARS_LOCALIP="192.168.11.1" \
-e TARS_START_PORT=9200 \
--name framework-pro-3 \
${tars-framework-pro}

```

启动以后可以使用如下命令检查框架核心组件tarsregistry的状态:
```shell
docker exec ${containid} tarsctl framework status
```

注意:
- 以上例子中只有framework-pro-1才启动了TARS_OPEN_WEB, 因为如果都启动, 会导致端口冲突, 因为在启动了三个容器, 且使用了--net=host模式

# 增加节点 
当部署框架以后, 我们可以增加节点, 也以容器方式增加节点, 命令如下:

```shell

docker run -d --rm --net=host \
    -e TARS_OPEN_FRAMEWORK=false \
    -e TARS_LOCATOR="tcp -h $ip1 -p $port1:tcp -h $ip2 -p $port2:tcp -h $ip3 -p $port3" \
    -e TARS_NODENAME="$nodename" \
    -e TARS_LOCALIP="$localip" \
    -e TARS_OPEN_WEB=true \
    --name $nodename \
    ${tars-framework-pro}
```

例如:

```shell

docker run -d --rm --net=host \
-e TARS_OPEN_FRAMEWORK=false \
-e TARS_LOCATOR="tcp -h 192.168.11.1 -p 7890:tcp -h 192.168.11.1 -p 7891:tcp -h 192.168.11.1 -p 7892" \
-e TARS_NODENAME="tarsnode-4" \
-e TARS_LOCALIP="192.168.11.1" \
-e TARS_START_PORT=9200 \
--name tarsnode-4 \
${tars-framework-pro}
```