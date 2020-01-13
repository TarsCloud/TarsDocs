
# 目录
> * [介绍](#chapter-1)
> * [Docker部署Tars框架](#chapter-2)
> * [安装示例](#chapter-3)
> * [问题检查](#chapter-4)

# 1 <a id="chapter-1"></a>介绍

本节主要介绍采用docker来完成框架的部署, 有两种docker可用:
- framework: Tars框架Docker制作脚本, 制作的docker包含了框架核心服务和web管理平台
- tars: Tars框架Docker制作脚本, 和framework比, 增加了java, nodejs等运行时支持, 即可以把java, nodejs服务发布到docker里面(docker里面安装了jdk, node, php环境)

首先确保你的服务上已经安装了docker环境, 如果没有, 可以参考[docker install](docker-huan-jing-an-zhuang.md)

## 2 <a id="chapter-2"></a>Docker部署Tars框架

**如果你想源码自己编译docker, 请参见 [Install](source.md)**

使用docker安装Tars框架, 有两个镜像可供选择: framework / tars

**注意: 区别在于是否希望把业务服务部署在镜像内(不推荐, 不方便Tars框架升级)**

### 2.1 使用tarscloud/framework部署

1. 拉取镜像
```sh
docker pull tarscloud/framework:stable
```

2. 启动镜像(目前只考虑了linux上, 时间和本机同步)
```sh
docker run -d --net=host -e MYSQL_HOST=xxxxx -e MYSQL_ROOT_PASSWORD=xxxxx \
        -e MYSQL_USER=root -e MYSQL_PORT=3306 \
        -eREBUILD=false -eINET=eth0 -eSLAVE=false \
        -v/data/tars:/data/tars \
        -v/etc/localtime:/etc/localtime \
        tarscloud/framework:stable
```

3. 目录说明

创建时, 会将docker的目录/data/tars映射到宿主机目录/data/tars, 启动docker后, 请检查宿主机目录: /data/tars, 正常情况下会有创建以下几个目录:
- app_log: tars服务的日志目录
- tarsnode-data: tarsnode/data目录(存放发布到docker的业务服务), 保证docker重启, 数据不丢失
- web_log: web中tars-node-web模块的日志(主机才有)
- demo_log: web中tars-user-system模块的日志(主机才有)
- patchs: 上传的发布包(主机才有)

如果这几个目录没有创建, 你可以手工创建, 再重启docker.

### 2.2 使用tarscloud/tars部署

1. 拉取镜像
```sh
docker pull tarscloud/tars:stable
```

2. 启动镜像(目前只考虑了linux上, 时间和本机同步)
```sh
docker run -d --net=host -e MYSQL_HOST=xxxxx -e MYSQL_ROOT_PASSWORD=xxxxx \
        -e MYSQL_USER=root -e MYSQL_PORT=3306 \
        -eREBUILD=false -eINET=eth0 -eSLAVE=false \
        -v/data/tars:/data/tars \
        -v/etc/localtime:/etc/localtime \
        tarscloud/tars:stable
```

映射路径同tarscloud/framework

### 2.3 参数解释

MYSQL_IP: mysql数据库的ip地址

MYSQL_ROOT_PASSWORD: mysql数据库的root密码

INET: 网卡的名称(ifconfig可以看到, 比如eth0), 表示框架绑定本机IP, 注意不能是127.0.0.1

REBUILD: 是否重建数据库,通常为false, 如果中间装出错, 希望重置数据库, 可以设置为true

SLAVE: 是否是从节点, 可以部署多台机器, 通常一主一从即可.

MYSQL_USER: mysql用户, 默认是root

MYSQL_PORT: mysql端口

**如果希望多节点部署, 则在不同机器上执行docker run ...即可, 注意参数设置!**

**这里必须使用 --net=host, 表示docker和宿主机在相同网络** 

详细说明可以参见 [Install](source.md)

安装完毕后, 访问 `http://${your_machine_ip}:3000` 打开web管理平台

## 3 <a id="chapter-3"></a>安装示例

举例, 安装两台节点, 一台数据库(假设: 主[192.168.7.151], 从[192.168.7.152], mysql:[192.168.7.153])

主节点上执行(192.168.7.151)
```
docker run -d --net=host -e MYSQL_HOST=192.168.7.153 -e MYSQL_ROOT_PASSWORD=xxxxx \
        -e MYSQL_USER=root -e MYSQL_PORT=3306 \
        -eREBUILD=false -eINET=eth0 -eSLAVE=false \
        -v/data/tars:/data/tars \
        -v/etc/localtime:/etc/localtime \
        tarscloud/framework:stable

```
主节点执行完毕后, 从节点执行:
```
docker run -d --net=host -e MYSQL_HOST=192.168.7.153 -e MYSQL_ROOT_PASSWORD=xxxxx \
        -e MYSQL_USER=root -e MYSQL_PORT=3306 \
        -eREBUILD=false -eINET=eth0 -eSLAVE=true \
        -v/data/tars:/data/tars \
        -v/etc/localtime:/etc/localtime \
        tarscloud/framework:stable
```

**注意:SLAVE参数不同**

## 4 <a id="chapter-4"></a>问题检查

如果docker运行后, 仍然无法打开管理平台, 可以如下检查:
- 关闭docker, 注意需要用docker stop .... 来关闭(docker的使用请自行搜索)
- 启动镜像, 注意不要 -d 参数

```sh
docker run --net=host -e MYSQL_HOST=xxxxx -e MYSQL_ROOT_PASSWORD=xxxxx \
        -e MYSQL_USER=root -e MYSQL_PORT=3306 \
        -eREBUILD=false -eINET=eth0 -eSLAVE=false \
        -v/data/tars:/data/tars \
        -v/etc/localtime:/etc/localtime \
        tarscloud/framework:stable
```
- 查看docker输出是否有明显问题

如果web平台打开, 但是显示错误, 就需要检查web的问题, 可以进入docker, 请参考[检查web的问题](web.md)中的检查web问题
