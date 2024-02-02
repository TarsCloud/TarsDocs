# TARS

- Twitter: [@TarsCloud](https://twitter.com/TarsCloud)
- [Mailing List](https://groups.google.com/g/tars-foundation-information)
- [官网](http://tarscloud.org/)
- [新闻](https://tarscloud.org/feed/newsroom)
- QQ 技术交流群群：733605310, 579079160(满), 669339903(满)
- 微信公众号: TarsCloud
- [联系我们](https://tarscloud.org/about/contacts)

## 目录

> - [简介](#chapter-1)
> - [支持平台](#chapter-2)
> - [支持语言](#chapter-3)
> - [子模块](#chapter-4)
> - [License](#chapter-5)

## 简介 <span id="chapter-1"></span>

TARS 是 Linux 基金会的开源项目，它是基于名字服务使用 TARS 协议的高性能 RPC 开发框架，配套一体化的运营管理平台，并通过伸缩调度，实现运维半托管服务。

TARS 是腾讯从 2008 年到今天一直在使用的后台逻辑层的统一应用框架，覆盖腾讯 100 多个产品。目前支持 C++,Java,PHP,Nodejs,Go 语言。该框架为用户提供了涉及到开发、运维、以及测试的一整套解决方案，帮助一个产品或者服务快速开发、部署、测试、上线。 它集可扩展协议编解码、高性能 RPC 通信框架、名字路由与发现、发布监控、日志统计、配置管理等于一体，通过它可以快速用微服务的方式构建自己的稳定可靠的分布式应用，并实现完整有效的服务治理。

目前该框架在腾讯内部，各大核心业务都在使用，颇受欢迎，基于该框架部署运行的服务节点规模达到上万个。

Tars 详细介绍参见 [目录](SUMMARY.md)。

如果是国内用户, 可以使用 [http://doc.tarsyun.com](http://doc.tarsyun.com) 来访问文档

## 支持平台 <span id="chapter-2"></span>

目前运行的操作系统平台如下：

- Linux
- Windows
- Mac

## 支持语言 <span id="chapter-3"></span>

目前支持的开发语言如下：

- C++
- Java
- Nodejs
- PHP
- Go

## 子模块 <span id="chapter-4"></span>

| 目录      | 说明                  |
| --------- | --------------------- |
| framework | c++实现的基础服务     |
| cpp       | c++ rpc 框架源码      |
| java      | java rpc 框架源码     |
| go        | go rpc 框架源码       |
| nodejs    | nodejs rpc 框架源码   |
| php       | php rpc 框架源码      |
| tup       | tup 协议源码          |
| web       | web 管理平台          |
| docker    | 构建 tars docker 脚本 |

## License <span id="chapter-5"></span>

Tars 的开源协议为 BSD-3-Clause，详情参见 [LICENSE](LICENSE.md)。
