## 关于TarsGo

- TarsGo 是基于 Golang 编程语言使用 Tars 协议的高性能 RPC 框架。随着 docker,k8s,etcd 等容器化技术的兴起，Go 语言变得流行起来。Go 的 goroutine 并发机制使 Go 非常适合用于大规模高并发后端服务程序的开发。 Go 语言具有接近 C/C++的性能和接近 python 的生产力。在腾讯，一部分现有的 C++开发人员正逐渐向 Go 转型，Tars 作为广泛使用的 RPC 框架，现已支持 C++/Java/Nodejs/Php，其与 Go 语言的结合已成为大势所趋。因此，在广大用户的呼声中我们推出了 Tarsgo,并且已经将它应用于腾讯地图、应用宝、互联网+以及其他项目中。
- 关于 Tars 的整体架构和设计理念，请阅读 [Tars 介绍](../../base/tars-intro.md)

## 功能特性

- tarsgo 工具: 服务代码脚手架，可生成使用make/cmake管理的服务初始化代码
- tars2go 工具: tars 文件自动生成并转换为 go 语言，包含用 go 语言实现的 RPC 服务端/客户端代码
- go 语言版本的 tars 的序列化和反序列化包
- 服务端支持心跳上报，统计监控上报，自定义命令处理，基础日志
- 客户端支持直接连接和路由访问，自动重新连接，定期刷新节点状态以及支持 UDP/TCP 协议
- 提供远程日志
- 提供特性监控上报
- 提供 set 分组
- 提供 protocol buffers 支持， 详见 [pb2tarsgo](pb2tarsgo.md)

## 快速开始

请查看[快速入门](../../hello-world/tarsgo.md)。