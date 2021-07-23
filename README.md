[Click to View English Documents](https://tarscloud.github.io/TarsDocs_en/)

# TARS
---

- Twitter: [@TarsCloud](https://twitter.com/TarsCloud)
- [Mailing List](https://groups.google.com/g/tars-foundation-information)
- [官网](http://tarscloud.org/)
- [新闻](https://tarscloud.org/feed/newsroom)
- QQ技术交流群群：579079160、669339903
- 微信公众号: TarsCloud
- 微信ID: TARS01
- [联系我们](https://tarscloud.org/about/contacts)

## 目录

> * [简介](#chapter-1)
> * [支持平台](#chapter-2)
> * [支持语言](#chapter-3)
> * [子模块](#chapter-4)
> * [License](#chapter-5)



## 简介 <span id="chapter-1"></span>

TARS是Linux基金会的开源项目，它是基于名字服务使用TARS协议的高性能RPC开发框架，配套一体化的运营管理平台，并通过伸缩调度，实现运维半托管服务。

TARS是腾讯从2008年到今天一直在使用的后台逻辑层的统一应用框架，覆盖腾讯100多个产品。目前支持C++,Java,PHP,Nodejs,Go语言。该框架为用户提供了涉及到开发、运维、以及测试的一整套解决方案，帮助一个产品或者服务快速开发、部署、测试、上线。 它集可扩展协议编解码、高性能RPC通信框架、名字路由与发现、发布监控、日志统计、配置管理等于一体，通过它可以快速用微服务的方式构建自己的稳定可靠的分布式应用，并实现完整有效的服务治理。

目前该框架在腾讯内部，各大核心业务都在使用，颇受欢迎，基于该框架部署运行的服务节点规模达到上万个。

Tars详细介绍参见 [目录](SUMMARY.md)。

## 支持平台 <span id="chapter-2"></span>

目前运行的操作系统平台如下：

* Linux
* Windows
* Mac

## 支持语言 <span id="chapter-3"></span>

目前支持的开发语言如下：

* C++
* Java
* Nodejs
* PHP
* Go


## 子模块 <span id="chapter-4"></span>

目录             | 说明
------------------|----------------
framework         |c++实现的基础服务
cpp               |c++ rpc框架源码
java              |java rpc框架源码
go                |go rpc框架源码
nodejs            |nodejs rpc框架源码
php               |php rpc框架源码
tup               |tup 协议源码
web               |web管理平台
docker            |构建tars docker脚本

## License <span id="chapter-5"></span>

Tars的开源协议为BSD-3-Clause，详情参见 [LICENSE](LICENSE.md)。


