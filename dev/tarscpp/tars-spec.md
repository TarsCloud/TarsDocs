# 目录
> * [介绍](#main-chapter-1)
> * [cmake规范](#main-chapter-2)
> * [Makefile规范](#main-chapter-3)

# 介绍

阅读本文之前, 请必须阅读[基础概念](../../base/tars-concept.md)

C++的开发服务的管理方式有两种: Makefile 和 cmake管理

tarscpp >=2.1.0版本建议以cmake管理方式为主, 不再默认支持Makefile模式了

## 1. <span id="main-chapter-1"></span> cmake规范

### 1.1. cmake使用

使用Tars实现的服务，强烈建议使用cmake规范。

TARS框架提供了一个基础的tars-tools.cmake (/usr/local/tars/cpp/makefile/tars-tools.cmake), 业务服务的CMakeLists.txt引用该文件即可.

TARS框架也提供了脚本\(/usr/local/tars/cpp/script/cmake\_tars\_server.sh\)可以自动生成空的服务框架和CMakeLists.txt

业务服务CMakeLists.txt示例如下:

```
cmake_minimum_required(VERSION 2.8)

project(Demo-DemoServer)

option(TARS_MYSQL "option for mysql" ON)
option(TARS_SSL "option for ssl" OFF)
option(TARS_HTTP2 "option for http2" OFF)

if(WIN32)
    include (c:\\tars\\cpp\\makefile\\tars-tools.cmake)
else()
    include (/usr/local/tars/cpp/makefile/tars-tools.cmake)
endif()

set(TARS_WEB_HOST "http://127.0.0.1:4001")

include_directories(/usr/local/taf/cpp/thirdparty/include)
link_directories(/usr/local/taf/cpp/thirdparty/lib)

#include_directories(/home/tarsprotol/App/OtherServer)

add_subdirectory(src)

#add_subdirectory(other)

#link_libraries(mysqlclient)

```

源码在src目录下, 对应的CMakeLists.txt如下:
```
cmake_minimum_required(VERSION 2.8)

project(Test-HelloServer)

gen_server(Test HelloServer)

```

编译服务:
```
mkdir build
cd build 
cmake ..
make -j4
```

注意:
- TARS_SSL, TARS_HTTP2等需要匹配tars编译的开关, 如果tars编译时没打开, 这里也应该关闭:
```
#off
cmake .. -DTARS_SSL=OFF -DTARS_HTTP2=OFF
#on
cmake .. -DTARS_SSL=ON -DTARS_HTTP2=ON

```

tars编译打开关闭类似, 请参见[http2](tars-http2.md), [ssl](tars-tls.md)

### 1.2. 管理多个服务

使用cmake管理服务, 可以一个目录下可以管理多个服务, 每个服务一个目录即可, 在根CMakeLists.txt中, 调用:

```
add_subdirectory(other)
```

### 1.3. 引用tars文件

当需要包含其他服务的tars文件，在CMakeLists.txt引用对应的服务的tars目录即可, 比如:

例如：

```text
include_directories(/home/tarsprotol/App/OtherServer)
```

### 1.4. 引用lib库

当需要引用lib库时, 比如引用mysql:

```
link_libraries(mysqlclient)
```

如果目录下其他服务引用的库不一样, 你可以修改每个服务自己的CMakeLists.txt

### 1.5. release tars文件

为了方便其他服务使用当前服务的tars文件, 你可以执行如下命令(注意build目录下执行):

```
make HelloServer-release
```

### 1.5. 打包和上传服务

你可以一键打包和上传服务:
```
make HelloServer-tar
make HelloServer-upload
```

上传服务需要正确设置
```
set(TARS_WEB_HOST "http://127.0.0.1:4001")
```

你需要如下设置:
```
cd build
cmake .. -DTARS_WEB_HOST=http://xxx.xxx.xxx.xxx:3000
```

这里TARS_WEB_HOST是你web平台地址

参考[基础概念](../../base/tars-concept.md)

### 1.6. 第三方包

tars编译时, 根据设置会自动编译mysql openssl nghttp2 gtest gpref等常用库, 这些库安装时都会安装到 /usr/local/tars/cpp/thirdparty目录下.

如果你需要引用这些库, 只需要在CMakeLists.txt中引用即可, 非常方便!

## 2. <span id="main-chapter-2"></span> Makefile规范

使用Tars实现的服务，tarscpp 1.x版本提供了Makefile的管理规范, >=2.1.0不再维护, 建议新版本用户升级到cmake管理方式.

TARS框架提供了一个makefile.tars的基础Makefile，采用Tars编写的服务包含该Makefile会有效的帮助你对Makefile的维护；

TARS框架也提供了脚本\(安装目录/script/create\_tars\_server.sh\)可以自动生成空的服务框架和Makefile；

### 2.1. Makefile使用原则

原则上一个目录只能是一个Server或者程序，即Makefile只能有一个Target；

需要包含其他库时，根据依赖关系倒序include在Makefile文件底部；

例如：

```text
include /home/tarsproto/TestApp/HelloServer/HelloServer.mk
include /usr/local/tars/cpp/makefile/makefile.tars
```

makefile.tars必须包含。

### 2.2. Makefile模板解释

APP：程序的名字空间（即Application）

TARGET：Server名称；

CONFIG：配置文件名称，make tar时将该文件包含在tar包中；

INCLUDE：其他需要包含的路径；

LIB： 需要的库

Test.HelloServer的makefile实例如下：

```text
#-----------------------------------------------------------------------

APP           := TestApp
TARGET        := HelloServer
CONFIG        := HelloServer.conf
STRIP_FLAG    := N
TARS2CPP_FLAG  :=

INCLUDE       += 
LIB           += 

#-----------------------------------------------------------------------

include /home/tarsproto/TestApp/HelloServer/HelloServer.mk
include /usr/local/tars/cpp/makefile/makefile.tars
```

关键的变量，通常不用，但是业务可以在这些变量后面添加自己的值：

```text
RELEASE_TARS：需要发布在/home/tarsproto/目录下面的tars文件，如果需要把自己.h也发布到/home/tarsproto则可以如下：
RELEASE_TARS += xxx.h
CONFIG：配置文件名称，其实后面可以增加自己需要的文件，这样在调用make tar时也会把该文件包含到tar包中；
```

其他变量请阅读makefile.tars。

### 2.3. Makefile使用

make help：可以看到makefile所有使用功能。

make tar：生成发布文件

make release：copy tars文件到/home/tarsproto相应目录，并自动生成相关的mk文件

make clean：清除

make cleanall：清除所有

