# TarsCpp 2.x 变化

## 1. 概述

TarsCpp2.0 针对 1.x 版本做了重大的更新, 主要包括以下几个方面:

- 多平台支撑
- 删除不常用功能
- 完善协议解析器
- 各种扩展功能的 bug 修改

  2.x 的主要的修改如下:

- 多平台支撑, TarsCpp2.0 完成 linux, mac, windows(>=WIN7)三个 OS 系统的的支撑
- 依赖包自动拉取: 如果开启 mysql, ssl, http2 的支持, cmake 会自动拉取依赖包并编译(linux, mac), windows 目前还有待完善
- 去掉 promise 功能, 建议使用协程, 协程已经完成多平台, 多 cpu 的适配(如果有碰到问题, 请提交 issues)
- 去掉 TC_Atomic, 请使用 std::atomic
- 修改协议解析器的接口, 大幅度提高网络解析的包的效率, [参见第三方协议](tars-thirdparty-protocol.md), 注意:接口不再向下兼容
- 修改 TC_ThreadPool 的实现, 使用更加方便
- 完善 ssl 支持, 减少内存 copy 次数提高效率, 以及服务端和客户端可以指定不同端口采用不同的证书
- 完善 http2 的支持, 修复 bug
- 修改鉴权的加密算法为 3des, 为后续多平台支持做基础
- 完善各种 demo, 参见 cpp/examples

## 2. 多平台支撑

TarsCpp2.0 完成 linux, mac, windows(>=WIN7)三个 OS 系统的的支撑, 你可以在这些平台上编写和调试程序, 但是你发布到服务器上的程序必须对应服务器系统编译出来的.

注意:TarsCpp2.0 在接口层面有细微改动, 部分代码可能需要修改才能编译过.

### 2.1 cmake 安装

TarsCpp2.0 在三个 OS 系统都是通过 cmake 来编译, 请先检查 cmake 版本号

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

如此你编译的 TARS 就支持 ssl, [具体 ssl 说明请参见](../tars-tls.md)

**注意目前发现 cmake-3.16.4 版本会有 bug, 建议不要使用!**

### 2.2 linux & mac 编译

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

### 2.3 window 编译

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

另外: windows 版本目前开启 TARS_MYSQL, TARS_HTTP2, TARS_SSL, TARS_PROTOBUF 还有问题, 还在解决中.
