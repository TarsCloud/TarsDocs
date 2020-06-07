# 目录
> * [介绍](#main-chapter-1)
> * [cmake规范](#main-chapter-2)

# <a id="main-chapter-1"></a>介绍

阅读本文之前, 请必须阅读[基础概念](../../base/tars-concept.md)

本文介绍使用cmake来管理代码(tarsgo >= 1.1.3)

另外go版本请使用>=1.13以上版本, 并完成tarsgo的下载

## 2. <a id="main-chapter-2"></a> cmake规范

### 2.1. cmake使用

使用Tars实现的服务，可以使用cmake来管理go的代码编译

TARS框架提供了一个基础的tars-tools.cmake ($GOPATH/src/github.com/TarsCloud/TarsGo/cmake/tars-tools.cmake), 业务服务的CMakeLists.txt引用该文件即可.

TARS框架也提供了脚本\($GOPATH/src/github.com/TarsCloud/TarsGo/tars/tools/cmake\_tars\_server.sh\)可以自动生成空的服务框架和CMakeLists.txt

业务服务CMakeLists.txt示例如下:

```
execute_process(COMMAND go env GOPATH OUTPUT_VARIABLE GOPATH)

string(REGEX REPLACE "\n$" "" GOPATH "${GOPATH}")

include(${GOPATH}/src/github.com/TarsCloud/TarsGo/cmake/tars-tools.cmake)

cmake_minimum_required(VERSION 2.8)

project(DemoServer Go) # select GO compile

gen_server(Demo DemoServer)

# go mod init
# mkdir build
# cd build
# cmake ..
# make

```

如果源码下某子目录需要也能独立编译成可执行程序, 则该子目录添加对应的CMakeLists.txt如下(参考生成代码后的Client目录):
```
cmake_minimum_required(VERSION 2.8)

project(Client Go)

gen_server(Base Client)

```

编译服务:
```
mkdir build
cd build 
cmake ..
make -j4
```

### 2.2. 打包和上传服务

你可以一键打包和上传服务:
```
make HelloServer-tar
make HelloServer-upload
```

上传服务需要正确设置
```
cd build
cmake .. -DTARS_WEB_HOST=http://xxx.xxx.xxx.xxx:3000
```

这里TARS_WEB_HOST是你web平台地址

参考[基础概念](../../base/tars-concept.md)

**注意, 你也可以并行用GoLand这类IDE工具来管理代码**
