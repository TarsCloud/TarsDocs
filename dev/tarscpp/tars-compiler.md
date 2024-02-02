# TarsCpp 编译

## 1. 概述

使用TarsCpp实现服务之前, 需要先编译TarsCpp代码, 如果你是通过源码编译和安装的框架(TarsFramework), 那么TarsCpp应该已经被编译过.

如果TarsCpp发布了新版本, 通常你可以只升级TarsCpp, 而不需要升级TarsFramework.

## 2. 多平台支撑

TarsCpp 完成 linux, mac, windows(>=WIN7)三个 OS 系统的的支撑, 你可以在这些平台上编写和调试程序, 但是你发布到服务器上的程序必须对应服务器系统编译出来的.


### 2.1 cmake 安装

TarsCpp 在三个 OS 系统都是通过 cmake 来编译, 请先检查 cmake 版本号

```
cmake --version
```

**请使用 cmake > 3.2 以上的版本**

cmake 官网下载地址如下: https://cmake.org/download/

注意 cmake 编译过程中会根据开关自动下载 TARS 依赖的包, 比如我的 cmake 输出:

```sh
mkdir build
cd build
cmake ..

----------------------------------------------------
CMAKE_BUILD_TYPE:          Debug
PLATFORM:                  mac
INSTALL_PREFIX:            /usr/local/tars/cpp
----------------------------------------------------
TARS_MYSQL:                ON
TARS_HTTP2:                OFF
TARS_SSL:                  OFF
TARS_PROTOBUF:             OFF
----------------------------------------------------
CMAKE_SYSTEM_NAME:         Darwin
CMAKE_SYSTEM_PROCESSOR:    x86_64
CMAKE_CXX_COMPILER_ID:     Clang
ABI_STR:                   sysv
BF_STR:                    macho
CPU_STR:                   x86_64
JUMP_SRC:                  asm/jump_x86_64_sysv_macho_gas.S
MAKE_SRC:                  asm/make_x86_64_sysv_macho_gas.S
CMAKE_C_SIZEOF_DATA_PTR:   8
----------------------------------------------------
-- Configuring done
-- Generating done
-- Build files have been written to: /Users/jarod/centos/TarsCpp/cmake-build-debug
```

其中 TARS_MYSQL, TARS_HTTP2, TARS_SSL, TARS_PROTOBUF 可以根据需要打开或者关闭, 比如打开 SSL 的支持:

```
mkdir build
cd build
cmake .. -DTARS_SSL=ON
```

如此你编译的 TARS 就支持 ssl, [具体 ssl 说明请参见](./tars-tls.md)

**注意目前发现 cmake-3.16.4 版本会有 bug, 建议不要使用!**

## 3 linux & mac 编译

完成 cmake 安装或升级后, 下载源码:

```sh
git clone https://github.com/TarsCloud/TarsCpp.git --recursive
cd TarsCpp;
mkdir build;
cd build;
cmake ..
make -j4
make install
```

注意 make install 后相关的依赖库(包括 mysql, nghttp2, ssl)的头文件和 lib 库(静态库)都会安装到 /usr/local/tars/cpp/include & /usr/local/tars/cpp/lib 目录下

## 4 window 编译

下载源码

```
git clone https://github.com/TarsCloud/TarsCpp.git --recursive
```

windows 强烈建议使用 vs2019, vs2019 自动和 cmake 有整合, 使用 vs2019 打开 CMakeLists.txt 即可完成编译.

如果采用 vs2019 IDE 形式编译, 请注意修改 CMakeSettings.json(vs2019 加载 CMakeLists.txt 时生成)
修改: generator: Ninja -> Visual Studio 16 2019

如果你的 vs 低于 vs2019, 请使用如下方式编译:

```
cd TarsCpp;
mkdir vs_build;
cd vs_build;
cmake .. -G "Visual Studio 16 2019"
cmake --build . --config release
#install
cmake --build . --config release --target install
```

注意: Visual Studio 16 2019 可以选择你需要的版本, 可以用 cmake .. -G 查看

## 5 实现业务服务

完成TarsCpp以后, 你可以基于TarsCpp编译出来头文件和lib库实现自己的业务服务了, [请参考](../hello-world/tarscpp.md)
