# 协议介绍

- [Readme.md](README.md)
- [CLA](cla.md)
- [LICENSE](LICENSE.md)

## 基础介绍 <span id="intro"></span>

- [简介](base/tars-intro.md)
- [基础概念](base/tars-concept.md)
- [基础通信协议 Tars](base/tars-protocol.md)
- [统一通信协议 Tup](base/tars-tup.md)
- [开发模式介绍](base/README.md)
- [模板配置](base/tars-template.md)
- [服务市场](base/market.md)
- [服务扩展](base/plugins.md)
- [框架版本说明](installation/version.md)

## 框架部署 <span id="deploy"></span>

- [部署总体介绍](installation/README.md)
- [Docker环境安装](installation/docker-install.md)
- [Mysql安装](installation/mysql.md)
- [框架源码部署(Linux/Mac)](installation/source.md)
- [框架源码部署(Windows)](installation/source-windows.md)
- [框架Docker部署](installation/docker.md)
- [框架节点部署](installation/node.md)
- [业务服务容器化](installation/service-docker.md)
- [框架K8SDocker 部署](installation/k8s-docker-1.md)
- [框架K8STARS 部署](installation/k8s-docker-2.md)
- [框架K8SFramework 部署(强烈推荐)](installation/k8s-docker-3.md)
- [框架更新及扩容](installation/expand.md)
- [TarsWeb说明](installation/web.md)
- [调用链升级注意事项](installation/train.md)
- [TarsWeb-v3.0.3升级说明](installation/web-update.md)


## 关键特性 <span id="important"></span>

- [业务配置](dev/tars-config.md)
- [服务监控](dev/tars-monitor.md)
- [无损发布/重启](dev/tars-lossless-patch.md)
- [调用链](dev/tars-trace.md)
- [IDC分组](dev/tars-idc-set.md)
- [鉴权功能](dev/tars-auth.md)
- [web用户体系](dev/tars-web-user.md)
- [web管理平台 API](dev/tars-web-api.md)

## TarsCPP <span id="TarsCPP"></span>

- [编译](dev/tarscpp/tars-compiler.md)
- [快速开发入门](dev/tarscpp/tarscpp.md)
- [使用指南](dev/tarscpp/tars-guide.md)
- [开发规范](dev/tarscpp/tars-spec.md)
- [服务线程说明文档](dev/tarscpp/tars-server-thread.md)
- [protobuf 协议支持文档](dev/tarscpp/tars-protobuf.md)
- [第三方协议支持](dev/tarscpp/tars-thirdparty-protocol.md)
- [HTTP1 支持](dev/tarscpp/tars-http1.md)
- [HTTP2 支持](dev/tarscpp/tars-http2.md)
- [TLS 通信支持](dev/tarscpp/tars-tls.md)
- [Push 功能说明](dev/tarscpp/tars_push.md)
- [Cookie 支持](dev/tarscpp/tars-cookie.md)
- [性能数据](dev/tarscpp/tars-performance.md)
- [2.x 版本变化](dev/tarscpp/tars-2.x-update.md)
- [3.x 版本变化](dev/tarscpp/tars-3.x-update.md)
- [协程版本说明](dev/tarscpp/tars-coroutine.md)
- [案例]
  - [框架快速入门](demo/tarscpp/tars_cpp_quickstart.md)
  - [Http 服务示例](demo/tarscpp/tars_cpp_http_demo.md)

## TarsJava <span id="TarsJava"></span>

- [快速开始](dev/tarsjava/tars-quick-start.md)
- [快速开发入门](dev/tarsjava/tarsjava.md)
- [使用指南]
  - [Tars 服务开发与上线](dev/tarsjava/tars-tutorials.md)
  - [HTTP 服务开发与上线](dev/tarsjava/tars-http-server.md)
  - [生成接口调用文件](dev/tarsjava/tars-reference.md)
- [性能测试]
  - [tars java 压测代码 ](dev/tarsjava/stress-testing.md)

## TarsGo <span id="TarsGo"></span>

- [基本介绍](dev/tarsgo/README.md)
- [快速开始](dev/tarsgo/tarsgo.md)
- [使用指南](dev/tarsgo/guide.md)
- [cmake 管理代码](dev/tarsgo/cmake.md)
- [pb2tarsgo](dev/tarsgo/pb2tarsgo.md)
- [性能数据](dev/tarsgo/performance.md)
- [使用示例](demo/tarsgo/README.md)

## TarsPHP <span id="TarsPHP"></span>

- [搭建 php 环境](dev/tarsphp/Environment/php.md)
- [快速开发入门](dev/tarsphp/tarsphp.md)
- [快速起步]
  - [搭建 HttpServer](dev/tarsphp/QuickStart/tars-http-server.md)
  - [搭建 TimerServer](dev/tarsphp/QuickStart/tars-timer-server.md)
  - [搭建 TcpServer](dev/tarsphp/QuickStart/tars-tcp-server.md)
  - [搭建 WebSocketServer](dev/tarsphp/QuickStart/tars-websocket-server.md)
  - [弹幕活动实战](dev/tarsphp/QuickStart/tars-act-demo.md)
- [框架简介]
  - [简介](dev/tarsphp/Framework/introduce.md)
  - [tars-server](dev/tarsphp/Framework/tars-server.md)
  - [tars-client](dev/tarsphp/Framework/tars-client.md)
  - [tars-config](dev/tarsphp/Framework/tars-config.md)
  - [tars-deploy](dev/tarsphp/Framework/tars-deploy.md)
  - [tars-extension](dev/tarsphp/Framework/tars-extension.md)
  - [tars-log](dev/tarsphp/Framework/tars-log.md)
  - [tars-monitor](dev/tarsphp/Framework/tars-monitor.md)
  - [tars-registry](dev/tarsphp/Framework/tars-registry.md)
  - [tars-report](dev/tarsphp/Framework/tars-report.md)
  - [tars-utils](dev/tarsphp/Framework/tars-utils.md)
  - [tars2php](dev/tarsphp/Framework/tars2php.md)
- [高阶应用]
  - [PHP 的 Swoole 框架如何接入 Tars](dev/tarsphp/Advanced/swoole-suport-tars.md)
  - [与 thinkphp 结合使用](dev/tarsphp/Advanced/thinkphp.md)
  - [与 Swoft 结合使用](dev/tarsphp/Advanced/swoft.md)
  - [与 Laravel 结合使用](dev/tarsphp/Advanced/laravel.md)
  - [与 Yii2 结合使用](dev/tarsphp/Advanced/yii2.md)
  - [持续集成方案](dev/tarsphp/Advanced/ci.md)
- [其他]
  - [常见问题](dev/tarsphp/Question/index.md)
  - [如何 Debug](dev/tarsphp/Question/debug.md)
  - [changelog](dev/tarsphp/Question/changelog.md)
  - [其他外部文档](dev/tarsphp/Question/outsource.md)

## Tars.js <span id="Tars.js"></span>

- [基本介绍](dev/tars.js/README.md)
- [脚手架](dev/tars.js/nodetools-cli.md)
- [快速开发入门](dev/tars.js/tars.js.md)
- [@tars/stream](dev/tars.js/tars-stream.md)
- [@tars/rpc](dev/tars.js/tars-rpc.md)
- [@tars/logs](dev/tars.js/tars-logs.md)
- [@tars/config](dev/tars.js/tars-config.md)
- [@tars/monitor](dev/tars.js/tars-monitor.md)
- [@tars/notify](dev/tars.js/tars-notify.md)
- [@tars/utils](dev/tars.js/tars-utils.md)
- [@tars/dyeing](dev/tars.js/tars-dyeing.md)
- [@tars/node-agent](dev/tars.js/tars-node-agent.md)
- [@tars/winston-tars](dev/tars.js/tars-winston-tars.md)
- [tars2node](dev/tars.js/tars2node.md)


## K8SFramework <span id="k8s"></span>

- [安装和使用说明]
  - [介绍](k8s/tarsk8s.md)
  - [特性](k8s/property.md)
  - [安装](k8s/install.md)
  - [升级](k8s/upgrade.md)
  - [云原生运维](k8s/kubectl.md)
  - [管理平台](k8s/tarsweb.md)
  - [证书](k8s/ca.md)
- [开发环境构建]
  - [Dockerfile 说明](k8s/dockerfile.md)
  - [服务发布流程说明](k8s/helm.md)
  - [制作基础编译镜像](k8s/base-compiler.md)
  - [制作业务服务镜像](k8s/exec-build.md)
  - [制作 Helm 包](k8s/exec-helm.md)
  - [发布业务镜像到 K8S 集群](k8s/exec-deploy.md)
  - [服务发布示例](k8s/example.md)
  - [如何调试业务服务](k8s/debug.md)

## 服务扩展 <span id="extend"></span>

- [接口及压测工具](plugins/benchmark.md)
- [网关服务](plugins/gateway.md)
- [缓存服务](plugins/dcache.md)
- [发送邮件服务](plugins/sendmail.md)
- [存储服务](plugins/storage.md)
- [计数服务](plugins/count.md)

## 常见问题 <span id="question"></span>

- [安装常见问题](question/Install_faq.md)
- [Issues](issues/tarscloud/tarscloud.md)
- [Issues-tarscpp](issues/tarscpp/tarscpp.md)
- [Issues-tarsjava](issues/tarsjava/tarsjava.md)
- [Issues-tarsgo](issues/tarsgo/tarsgo.md)
- [Issues-tarsphp](issues/tarsphp/tarsphp.md)

## 开源合作 <span id="cooperation">

- [TarsFramework 项目 Git 合作规范](cooperation/tars_framework_git_flows.md)

## 直播视频 <span id="video">

- [B 站 TARS 培训系列课程](video/bilibili_tars.md)

## 相关文章 <span id="articles">

- [TARS 技术文章](articles/technical_articles.md)

## 其它资源分享 <span id="resourcesSharing"></span>

- [下载](resources-sharing/summary.md)
- [Tars 介绍.pptx](resources-sharing/ppt/TARS.pptx)
- [TarsPHP 解密.pdf](resources-sharing/pdf/TARS微服务解密-PHP构建高性能WEB后台.pdf)
- [TarsJava 本地调试.pdf](resources-sharing/pdf/TarsJava服务端的本地调试--正风.pdf)
- [微服务在腾讯的业务实践.pptx](resources-sharing/ppt/微服务架构在腾讯系业务的实践.pptx)
