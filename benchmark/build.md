# 目录

> - [介绍](#chapter-1)
> - [依赖环境](#chapter-2)
> - [构建步骤](#chapter-3)
> - [一键发布](#chapter-4)

# 1 <span id="chapter-1"></span>介绍

本节主要介绍 tb 工具编译时需要的开发环境, 以 centos7 来介绍.

**注意: 如果本机上之前已经安装 TarsCpp 环境, 那么需要升级到最新的 TarsCpp 版本(>=v2.4.0)**

# 2 <span id="chapter-2"></span>依赖环境

| 软件            | 软件要求                    |
| :-------------- | :-------------------------- |
| linux 内核版本: | 2.6.18 及以上版本           |
| gcc 版本:       | 4.8 及以上版本、glibc-devel |
| bison 工具版本: | 2.5 及以上版本              |
| flex 工具版本:  | 2.5 及以上版本              |
| cmake 版本：    | 3.2 及以上版本              |
| TarsCpp 版本：  | 最新 master 版本            |

## 2.1. 安装依赖包

源码编译过程需要安装:gcc, glibc, bison, flex, cmake

例如，在 Centos7 下，执行：

```sh
yum install glibc-devel gcc gcc-c++ bison flex cmake
```

## 2.2. 编译安装 TarsCpp 环境

```sh
git clone https://github.com/TarsCloud/TarsCpp.git --recursive
cd TarsCpp
mkdir build
cd build
cmake ..
make
make install
```

至此, Tars Cpp 的编译环境已经完成, 下一步可以实现 tb 工具了.

# 3 <span id="chapter-3"></span>工具和服务构建

主要编译步骤如下，编译成功后 tb 工具生成在 build/bin 目录下，它是一个可执行程序，可以直接压测[TARS 协议](tars-guide.md)/[HTTP 协议](http-guide.md)的服务。

```sh
git clone https://github.com/TarsCloud/TarsBenchmark.git
cd TarsBenchmark && mkdir build && cd build
cmake .. && make all
```

此外，还会生成两个压测服务可执行文件(NodeServer 和 AdminServer)，可以通过如下步骤发布到 Web 平台，就可以实现线上压测，节省开发体力。

# 4 <span id="chapter-4"></span>一键发布

采用最新版本的[TarsWeb](https://github.com/TarsCloud/TarsWeb), 就可以实现在线压测 Tars 服务，一键发布步骤如下:

```sh
./install.sh webhost token adminsip nodeip
```

参数说明

```text
webhost                  TarsWeb管理端的host或ip:port
token                    TarsWeb管理端的token，可以通过管理端获取http://webhost:3001/auth.html#/token
adminsip                 压测管理服务AdminServer部署的IP地址，AdminServer必须单点部署，建议和tarsregistry部署在一起。
nodeip                   压测节点服务NodeServer部署的IP地址，尽量和AdminServer分开, 部署成功之后建议在管理端扩容，部署的机器越多，支持并行压测能力就越强。
```
