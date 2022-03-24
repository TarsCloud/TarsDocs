# 目录

> - [介绍](#main-chapter-1)
> - [cmake 规范](#main-chapter-2)
> - [Makefile 规范](#main-chapter-3)

# 介绍

阅读本文之前, 请必须阅读[基础概念](../../base/tars-concept.md)

C++的开发服务的管理方式有两种: Makefile 和 cmake 管理

tarscpp >=2.1.0 版本建议以 cmake 管理方式为主, 不再默认支持 Makefile 模式了

## 1. <span id="main-chapter-1"></span> cmake 规范

### 1.1. cmake 使用

使用 Tars 实现的服务，强烈建议使用 cmake 规范。

TARS 框架提供了一个基础的 tars-tools.cmake (/usr/local/tars/cpp/makefile/tars-tools.cmake), 业务服务的 CMakeLists.txt 引用该文件即可.

TARS 框架也提供了脚本\(/usr/local/tars/cpp/script/cmake_tars_server.sh\)可以自动生成空的服务框架和 CMakeLists.txt

业务服务 CMakeLists.txt 示例如下:

```cmake
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

源码在 src 目录下, 对应的 CMakeLists.txt 如下:

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

- TARS_SSL, TARS_HTTP2 等需要匹配 tars 编译的开关, 如果 tars 编译时没打开, 这里也应该关闭:

```
#off
cmake .. -DTARS_SSL=OFF -DTARS_HTTP2=OFF
#on
cmake .. -DTARS_SSL=ON -DTARS_HTTP2=ON

```

tars 编译打开关闭类似, 请参见[http2](tars-http2.md), [ssl](tars-tls.md)

### 1.2. 管理多个服务

使用 cmake 管理服务, 可以一个目录下可以管理多个服务, 每个服务一个目录即可, 在根 CMakeLists.txt 中, 调用:

```
add_subdirectory(other)
```

### 1.3. 引用 tars 文件

当需要包含其他服务的 tars 文件，在 CMakeLists.txt 引用对应的服务的 tars 目录即可, 比如:

例如：

```text
include_directories(/home/tarsprotol/App/OtherServer)
```

### 1.4. 引用 lib 库

当需要引用 lib 库时, 比如引用 mysql:

```
link_libraries(mysqlclient)
```

如果目录下其他服务引用的库不一样, 你可以修改每个服务自己的 CMakeLists.txt

### 1.5. release tars 文件

为了方便其他服务使用当前服务的 tars 文件, 你可以执行如下命令(注意 build 目录下执行):

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

这里 TARS_WEB_HOST 是你 web 平台地址

参考[基础概念](../../base/tars-concept.md)

### 1.6. 第三方包

tars 编译时, 根据设置会自动编译 mysql openssl nghttp2 gtest gpref 等常用库, 这些库安装时都会安装到 /usr/local/tars/cpp/thirdparty 目录下.

如果你需要引用这些库, 只需要在 CMakeLists.txt 中引用即可, 非常方便!

## 2. <span id="main-chapter-2"></span> Makefile 规范

使用 Tars 实现的服务，tarscpp 1.x 版本提供了 Makefile 的管理规范, >=2.1.0 不再维护, 建议新版本用户升级到 cmake 管理方式.

TARS 框架提供了一个 makefile.tars 的基础 Makefile，采用 Tars 编写的服务包含该 Makefile 会有效的帮助你对 Makefile 的维护；

TARS 框架也提供了脚本\(安装目录/script/create_tars_server.sh\)可以自动生成空的服务框架和 Makefile；

### 2.1. Makefile 使用原则

原则上一个目录只能是一个 Server 或者程序，即 Makefile 只能有一个 Target；

需要包含其他库时，根据依赖关系倒序 include 在 Makefile 文件底部；

例如：

```text
include /home/tarsproto/TestApp/HelloServer/HelloServer.mk
include /usr/local/tars/cpp/makefile/makefile.tars
```

makefile.tars 必须包含。

### 2.2. Makefile 模板解释

APP：程序的名字空间（即 Application）

TARGET：Server 名称；

CONFIG：配置文件名称，make tar 时将该文件包含在 tar 包中；

INCLUDE：其他需要包含的路径；

LIB： 需要的库

Test.HelloServer 的 makefile 实例如下：

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

其他变量请阅读 makefile.tars。

### 2.3. Makefile 使用

make help：可以看到 makefile 所有使用功能。

make tar：生成发布文件

make release：copy tars 文件到/home/tarsproto 相应目录，并自动生成相关的 mk 文件

make clean：清除

make cleanall：清除所有
