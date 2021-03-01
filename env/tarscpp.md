# 目录
> * [介绍](#chapter-1)
> * [依赖环境](#chapter-2)
> * [环境构建](#chapter-3)

# 1 <span id="chapter-1"></span>介绍

本节主要介绍Tars Cpp服务开发和编译时需要的开发环境, 以centos7来介绍.

**注意: 如果本机上之前源码编译部署过Tars, 那么Tars Cpp的开发环境就自动可以工作了**

# 2 <span id="chapter-1"></span>依赖环境

| 软件 | 软件要求 |
| :--- | :--- |
| linux内核版本: | 2.6.18及以上版本 |
| gcc版本: | 4.8 及以上版本、glibc-devel |
| bison工具版本: | 2.5及以上版本 |
| flex工具版本: | 2.5及以上版本 |
| cmake版本： | 2.8.8及以上版本 |
| mysql版本： | 5.6及以上版本 |


## 2.1. 编译包依赖下载安装介绍

源码编译过程需要安装:gcc, glibc, bison, flex, cmake

例如，在Centos7下，执行：
```
yum install glibc-devel gcc gcc-c++ bison flex cmake
```

## 2.2. 下载源码

```text
git clone https://github.com/TarsCloud/TarsCpp.git --recursive
cd TarsCpp
mkdir build
cd build
cmake ..
make
make install
```

至此, Tars Cpp的编译环境已经完成, 下一步可以实现Cpp的Tars服务了.

你如果要开启SSL, HTTP2等的支持, 你可以:

```
cmake .. -DTARS_SSL=ON -DTARS_HTTP2=ON
make
make install
```

关闭:
```
cmake .. -DTARS_SSL=OFF -DTARS_HTTP2=OFF
make
make install
```

注意默认情况下 examples的demo服务都没有编译, 如果希望编译这些demo服务, 请开启:
```
cmake .. -DONLY_LIB=OFF
```
