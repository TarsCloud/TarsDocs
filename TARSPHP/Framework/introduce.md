# TARSPHP Framework模块

## tars-client

tars-client中提供了对tars服务进行调用的php能力，包括：

* 调用远程服务的实例；
* 主调上报
* 自动寻址模块

[详细说明](./tars-client.md)

## tars-server
tars-server提供了底层的server框架,同时支持如下特性

* 基于swoole1.x/2.x的高性能服务
* 支持tup协议和tars流两种协议模式
* 支持http、tcp、timer三种server
* 上报、监控、日志的集成
* tars平台发布支持

[详细说明](./tars-server.md)

## tars-config 

从tars平台的配置服务拉取配置文件的能力模块。

[详细说明](./tars-config.md)

## tars-deploy 

进行tars-server业务代码打包的模块。

[详细说明](./tars-deploy.md)

## tars-extension

tars底层依赖的php扩展代码
[详细说明](./tars-extension.md)
 
## tars-log

tars进行远程日志写入的模块
[详细说明](./tars-log.md)
 
## tars-monitor

tars进行主调上报和特性上报的功能模块
[详细说明](./tars-monitor.md)
 
## tars-registry
 
tars进行主控寻址的功能模块
[详细说明](./tars-registry.md)
 
## tars-report
 
tars进行keep-alive服务保活上报的模块
[详细说明](./tars-report.md)

## tars-utils
 
tars进行配置文件解析的模块
[详细说明](./tars-utils.md)

## tars2php 

代码自动生成的工具,可以自动生成server和client端的代码。
[详细说明](./tars2php.md)