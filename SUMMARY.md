# Table of contents

* [Readme.md](README.md)
* [CLA](cla.md)
* [LICENSE](license.md)

# Directory
> * [入门](#rumen)
> * [TarsCPP](#TarsCPP)
> * [TarsJava](#TarsJava)
> * [TarsGo](#TarsGo)
> * [Tarsjs](#Tars.js)
> * [TarsPHP](#TarsPHP)
> * [其他重要特性](#important)
> * [常见问题](#question)
> * [其它资源分享](#resourcesSharing)


## 入门 <a id="rumen"></a>

* [简介](base/tars-intro.md)
* [Tars部署]
  * [部署总体介绍](installation/README.md)
  * [Docker环境安装](installation/docker-install.md)
  * [Mysql安装](installation/mysql.md)
  * [Linux/Mac源码部署](installation/source.md)
  * [Windows源码部署](installation/source-windows.md)
  * [TarsDocker部署](installation/docker.md)
  * [K8s Docker部署](installation/k8s-docker-1.md)
  * [TarsNode部署](installation/node.md)
  * [Tars框架更新及扩容](installation/expand.md)
  * [TarsWeb说明](installation/web.md)
* [开发环境部署]
  * [开发模式介绍](env/README.md)
  * [TarsCPP](env/tarscpp.md)
  * [TarsGo](env/tarsgo.md)
  * [TarsJava](env/tarsjava.md)
  * [TarsPHP](env/tarsphp.md)
  * [Tars.js](env/tars.js.md)
* [基础概念](base/tars-concept.md)
* [HelloWorld]
  * [服务开发介绍](hello-world/README.md)
  * [TarsGo 快速入门](hello-world/hello-world.md)
  * [TarsJava 快速入门](hello-world/tarsjava.md)
  * [TarsCPP 快速入门](hello-world/tarscpp.md)
  * [TarsPHP 快速入门](hello-world/tarsphp.md)
  * [Tars.js 快速入门](hello-world/tars.js.md)
* [基础通信协议 Tars](base/tars-protocol.md)
* [统一通信协议 Tup](base/tars-tup.md)

## 压测工具 <a id="benchmark"></a>
* [工具介绍](benchmark/README.md)
* [压测工具编译](benchmark/build.md)
* [工具二次开发介绍](benchmark/develop.md)
* [Http协议压测说明](benchmark/http-guide.md)
* [Tars协议压测说明](benchmark/tars-guide.md)

## TarsCPP <a id="TarsCPP"></a>
* [使用指南](dev/tarscpp/tars-guide.md)
* [开发规范](dev/tarscpp/tars-spec.md)
* [服务线程说明文档](dev/tarscpp/tars-server-thread.md)
* [protobuf协议支持文档](dev/tarscpp/tars-protobuf.md)
* [第三方协议支持](dev/tarscpp/tars-thirdparty-protocol.md)
* [HTTP1支持](dev/tarscpp/tars-http1.md)
* [HTTP2支持](dev/tarscpp/tars-http2.md)
* [TLS通信支持](dev/tarscpp/tars-tls.md)
* [Cookie支持](dev/tarscpp/tars-cookie.md)
* [性能数据](dev/tarscpp/tars-performance.md)
* [2.0版本变化](dev/tarscpp/tars-2.0-update.md)
* [案例]
  * [框架快速入门](demo/tarscpp/tars_cpp_quickstart.md) 
  * [Http服务示例](demo/tarscpp/tars_cpp_http_demo.md)  
  * [Push功能说明](demo/tarscpp/tars_push.md) 
  * [协程说明](demo/tarscpp/tars_co.md) 

## TarsJava <a id="TarsJava"></a>
* [Tars-Spring使用说明](dev/tarsjava/tarsspring-shi-yong-shuo-ming.md)
* [Tars-Spring-boot使用说明]
  * [Tars-Spring-Cloud 使用说明](dev/tarsjava/tarsspringcloud-shi-yong-shuo-ming.md)
  * [使用指南](dev/tarsjava/shi-yong-zhi-nan.md)
* [案例]
  * [快速开发入门客户端示例](demo/tarsjava/quickstart-client.md) 
  * [快速开发入门服务端示例](demo/tarsjava/quickstart-server.md) 
  * [tars java 压测代码 ](demo/tarsjava/stress-server.md)
  * [spring boot服务端示例 ](demo/tarsjava/tars-spring-boot-server.md) 
  * [spring cloud客户端示例 ](demo/tarsjava/tars-spring-cloud-client.md)
  * [spring cloud服务端示例](demo/tarsjava/tars-spring-cloud-server.md) 
  * [tars-spring-server](demo/tarsjava/tars-spring-server.md) 

## TarsGo <a id="TarsGo"></a>
* [pb2tarsgo](dev/tarsgo/pb2tarsgo.md)
* [性能数据](dev/tarsgo/performance.md)
* [案例]
  * [服务端使用context示例](demo/tarsgo/context.md) 
  * [tars客户端示例](demo/tarsgo/client.md)
  * [tars服务端示例](demo/tarsgo/server.md) 
  * [zipkin调用链追踪客户端示例](demo/tarsgo/zipkin-client.md) 
  * [zipkin调用链追踪服务端示例](demo/tarsgo/zipkin-server.md) 

## TarsPHP <a id="TarsPHP"></a>
* [环境搭建]
  * [搭建php环境](dev/tarsphp/Environment/php.md)
  * [使用docker](dev/tarsphp/Environment/docker.md)
* [快速起步]
  * [搭建HttpServer](dev/tarsphp/QuickStart/tars-http-server.md)
  * [搭建TimerServer](dev/tarsphp/QuickStart/tars-timer-server.md)
  * [搭建TcpServer](dev/tarsphp/QuickStart/tars-tcp-server.md)
  * [搭建WebSocketServer](dev/tarsphp/QuickStart/tars-websocket-server.md)
  * [弹幕活动实战](dev/tarsphp/QuickStart/tars-act-demo.md)
* [框架简介]
  * [简介](dev/tarsphp/Framework/introduce.md)
  * [tars-server](dev/tarsphp/Framework/tars-server.md)
  * [tars-client](dev/tarsphp/Framework/tars-client.md)
  * [tars-config](dev/tarsphp/Framework/tars-config.md)
  * [tars-deploy](dev/tarsphp/Framework/tars-deploy.md)
  * [tars-extension](dev/tarsphp/Framework/tars-extension.md)
  * [tars-log](dev/tarsphp/Framework/tars-log.md)
  * [tars-monitor](dev/tarsphp/Framework/tars-monitor.md)
  * [tars-registry](dev/tarsphp/Framework/tars-registry.md)
  * [tars-report](dev/tarsphp/Framework/tars-report.md)
  * [tars-utils](dev/tarsphp/Framework/tars-utils.md)
  * [tars2php](dev/tarsphp/Framework/tars2php.md)
* [高阶应用]
  * [PHP的Swoole框架如何接入Tars](dev/tarsphp/Advanced/swoole-suport-tars.md)
  * [与thinkphp结合使用](dev/tarsphp/Advanced/thinkphp.md)
  * [与Swoft结合使用](dev/tarsphp/Advanced/swoft.md)
  * [与Laravel结合使用](dev/tarsphp/Advanced/laravel.md)
  * [与Yii2结合使用](dev/tarsphp/Advanced/yii2.md)
  * [持续集成方案](dev/tarsphp/Advanced/ci.md)
* [其他]
  * [常见问题](dev/tarsphp/Question/index.md)
  * [如何Debug](dev/tarsphp/Question/debug.md)
  * [changelog](dev/tarsphp/Question/changelog.md)
  * [其他外部文档](dev/tarsphp/Question/outsource.md) 

## Tars.js <a id="Tars.js"></a>
* [基本介绍](dev/tars.js/README.md)
* [@tars/stream](dev/tars.js/tars-stream.md)
* [@tars/rpc](dev/tars.js/tars-rpc.md)
* [@tars/logs](dev/tars.js/tars-logs.md)
* [@tars/config](dev/tars.js/tars-config.md)
* [@tars/monitor](dev/tars.js/tars-monitor.md)
* [@tars/notify](dev/tars.js/tars-notify.md)
* [@tars/utils](dev/tars.js/tars-utils.md)
* [@tars/dyeing](dev/tars.js/tars-dyeing.md)
* [@tars/node-agent](dev/tars.js/tars-node-agent.md)
* [@tars/winston-tars](dev/tars.js/tars-winston-tars.md)
* [tars2node](dev/tars.js/tars2node.md)

## 其他重要特性 <a id="important"></a>
* [业务配置](dev/tars-config.md)
* [服务监控](dev/tars-monitor.md)
* [模板配置](dev/tars-template.md)
* [Tars 用户体系模块使用指引](dev/tars-web-user.md)
* [管理平台 API](dev/tars-web-api.md)
* [调用链](dev/tars-call-chain.md)
* [IDC分组](dev/tars-idc-set.md)
* [鉴权功能](dev/tars-auth.md)

## 常见问题 <a id="question"></a>
* [安装常见问题](question/Install_faq.md)
* [TarsCPP 常见问题](question/tarscpp-question.md)
* [TarsJava 常见问题](question/tarsjava-question.md)
* [TarsPHP 常见问题](question/tarsphp-question.md)

## 开元合作 <a id="cooperation">
* [TarsFramework项目Git合作规范](cooperation/tars_framework_git_flows.md)

## 其它资源分享 <a id="resourcesSharing"></a>
* [下载](resources-sharing/summary.md)
* [Tars介绍.pptx](resources-sharing/ppt/TARS.pptx)
* [TarsPHP解密.pdf](resources-sharing/pdf/TARS微服务解密-PHP构建高性能WEB后台.pdf)
* [TarsJava本地调试.pdf](resources-sharing/pdf/TarsJava服务端的本地调试--正风.pdf)
* [微服务在腾讯的业务实践.pptx](resources-sharing/ppt/微服务架构在腾讯系业务的实践.pptx)