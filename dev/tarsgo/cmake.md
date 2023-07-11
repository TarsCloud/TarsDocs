# 目录
> * [介绍](#main-chapter-1)
> * [cmake规范](#main-chapter-2)

# <span id="main-chapter-1"></span>介绍

阅读本文之前, 请必须阅读[基础概念](../../base/tars-concept.md)

本文介绍使用cmake来管理代码(tarsgo >= 1.1.3)

另外go版本请使用>=1.13以上版本, 并完成tarsgo的下载

## 2. <span id="main-chapter-2"></span> cmake规范

使用Tars实现的服务，可以使用cmake来管理go的代码编译
### 2.1 创建服务

运行tarsgo脚手架，自动创建使用`cmake`构建服务必须的文件。如要使用makefile，[可参考这里](../../hello-world/tarsgo.md)
```text
tarsgo cmake App Server Servant GoModuleName
例如：
tarsgo cmake TestApp HelloGo SayHello github.com/Tars/test
```

例如: 
```sh
MacBook-Pro-2:OrderServer jarod$ tarsgo cmake Cloud OrderServer Order pay
🚀 Creating server Cloud.OrderServer, please wait a moment.

go: creating new go.mod: module pay
go: to add module requirements and sums:
        go mod tidy

CREATED OrderServer/CMakeLists.txt (397 bytes)
CREATED OrderServer/Order.tars (166 bytes)
CREATED OrderServer/Order_imp.go (602 bytes)
CREATED OrderServer/client/CMakeLists.txt (161 bytes)
CREATED OrderServer/client/client.go (419 bytes)
CREATED OrderServer/cmake/CMakeDetermineGoCompiler.cmake (1615 bytes)
CREATED OrderServer/cmake/CMakeGoCompiler.cmake.in (273 bytes)
CREATED OrderServer/cmake/CMakeGoInformation.cmake (230 bytes)
CREATED OrderServer/cmake/CMakeTestGoCompiler.cmake (49 bytes)
CREATED OrderServer/cmake/golang.cmake (2444 bytes)
CREATED OrderServer/cmake/tars-tools.cmake (9754 bytes)
CREATED OrderServer/config/config.conf (714 bytes)
CREATED OrderServer/debugtool/dumpstack.go (411 bytes)
CREATED OrderServer/go.mod (20 bytes)
CREATED OrderServer/main.go (484 bytes)
CREATED OrderServer/start.sh (115 bytes)

>>> Great！Done! You can jump in OrderServer
>>> Tips: After editing the Tars file, execute the following cmd to automatically generate golang files.
>>>       /bin/tars2go *.tars
$ cd OrderServer
$ ./start.sh
🤝 Thanks for using TarsGo
📚 Tutorial: https://doc.tarsyun.com/
```

完成服务创建以后, 你就可以编译代码了, 编译方式如下:
```
cd OrderServer
make build
cd build
cmake ..
make
```

### 2.2. cmake使用说明


TarsGo 框架`v1.3.3`之前版本提供了一个基础的tars-tools.cmake ($GOPATH/src/github.com/TarsCloud/TarsGo/cmake/tars-tools.cmake), 业务服务的CMakeLists.txt引用该文件即可.

TarsGo 框架`v1.3.3`开始为方便go 1.17版本移除src目录，会在使用`tarsgo`脚手架创建项目是直接把`tars-tools.cmake`放入项目根目录的cmake目录中。并方便旧项目升级cmake编译脚本提供了`tarsgo upgrade cmake`一键升级CMakeLists.txt

TARS框架也提供了脚手架`tarsgo`可以自动生成空的服务框架和CMakeLists.txt

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

### 2.3. 打包和上传服务

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
