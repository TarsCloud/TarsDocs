# 目录
> * [介绍](#chapter-1)
> * [依赖环境](#chapter-2)
> * [环境构建](#chapter-3)

# 1 <a id="chapter-1"></a>介绍

本节主要介绍Tars Cpp服务开发和编译时需要的开发环境, 以centos7来介绍.

**注意: 如果本机上之前源码编译部署过Tars, 那么Tars Cpp的开发环境就自动可以工作了**

# 2 <a id="chapter-1"></a>依赖环境

| 软件 | 软件要求 |
| :--- | :--- |
| linux内核版本: | 2.6.18及以上版本 |
| gcc版本: | 4.8 及以上版本、glibc-devel |
| bison工具版本: | 2.5及以上版本 |
| flex工具版本: | 2.5及以上版本 |
| cmake版本： | 2.8.8及以上版本 |
| mysql版本： | 4.1.17及以上版本 |


## 2.1. 编译包依赖下载安装介绍

源码编译过程需要安装:gcc, glibc, bison, flex, cmake

例如，在Centos7下，执行：
```
yum install glibc-devel gcc gcc-c++ bison flex cmake
```

## 2.2. Mysql依赖库安装

Tars代码编译需要依赖mysql头文件和静态库, 依赖路径如下:

- 头文件: /usr/local/mysql/include
- 库路径: /usr/local/mysql/lib

如果系统已经存在mysql头文件和静态库, 则可跳过次步骤, 否则建议: 

```
rpm -ivh https://repo.mysql.com/mysql57-community-release-el7.rpm
yum install -y mysql-devel 
mkdir -p /usr/local/mysql && ln -s /usr/lib64/mysql /usr/local/mysql/lib && ln -s /usr/include/mysql /usr/local/mysql/include && echo "/usr/local/mysql/lib/" >> /etc/ld.so.conf && ldconfig 
```

**注意:Tars默认使用mysql静态链接, 避免每台机器上都需要mysql动态库**

# <a id="chapter-1"></a>环境构建

```text
git clone https://github.com/TarsCloud/TarsCpp.git --recursive
cd TarsCpp
cmake .
make
make install
```

至此, Tars Cpp的编译环境已经完成, 下一步可以实现Cpp的Tars服务了.