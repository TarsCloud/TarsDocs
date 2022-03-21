# 协议介绍

- [Readme.md](README.md)
- [CLA](cla.md)
- [LICENSE](LICENSE.md)

## 基础介绍 <span id="intro"></span>

- [简介](base/tars-intro.md)
- [基础概念](base/tars-concept.md)
- [基础通信协议 Tars](base/tars-protocol.md)
- [统一通信协议 Tup](base/tars-tup.md)

## 框架部署 <span id="deploy"></span>

- [部署总体介绍](installation/README.md)
- [Docker 环境安装](installation/docker-install.md)
- [Mysql 安装](installation/mysql.md)
- [Linux/Mac 源码部署](installation/source.md)
- [Windows 源码部署](installation/source-windows.md)
- [TarsDocker 部署](installation/docker.md)
- [业务服务容器化部署](installation/service-docker.md)
- [TarsNode 部署](installation/node.md)
- [K8S Docker 部署](installation/k8s-docker-1.md)
- [K8STARS 部署方案](installation/k8s-docker-2.md)
- [K8SFramework 深度融合方案(强烈推荐)](installation/k8s-docker-3.md)
- [Tars 框架更新及扩容](installation/expand.md)
- [TarsWeb 说明](installation/web.md)
- [调用链升级注意事项](installation/train.md)
- [框架版本说明](installation/version.md)

## 开发入门 <span id="enter"></span>

- [开发环境部署]
  - [开发模式介绍](env/README.md)
  - [TarsCPP](env/tarscpp.md)
  - [TarsGo](env/tarsgo.md)
  - [TarsJava](env/tarsjava.md)
  - [TarsPHP](env/tarsphp.md)
  - [Tars.js](env/tars.js.md)
- [HelloWorld]
  - [服务开发介绍](hello-world/README.md)
  - [TarsGo 快速入门](hello-world/tarsgo.md)
  - [TarsJava 快速入门](hello-world/tarsjava.md)
  - [TarsCPP 快速入门](hello-world/tarscpp.md)
  - [TarsPHP 快速入门](hello-world/tarsphp.md)
  - [Tars.js 快速入门](hello-world/tars.js.md)

## 压测工具 <span id="benchmark"></span>

- [工具介绍](benchmark/README.md)
- [压测工具编译](benchmark/build.md)
- [工具二次开发介绍](benchmark/develop.md)
- [Http 协议压测说明](benchmark/http-guide.md)
- [Tars 协议压测说明](benchmark/tars-guide.md)

## Tars 网关 <span id="gateway"></span>

- [网关介绍](gateway/README.md)
- [编译安装](gateway/install.md)
- [服务配置说明](gateway/config.md)
- [系统配置手册](gateway/operate.md)

## TarsCPP <span id="TarsCPP"></span>

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
- [协程版本说明](dev/tarscpp/tars-coroutine.md)
- [3.x 版本变化](dev/tarscpp/tars-3.x-update.md)
- [案例]
  - [框架快速入门](demo/tarscpp/tars_cpp_quickstart.md)
  - [Http 服务示例](demo/tarscpp/tars_cpp_http_demo.md)

## TarsJava <span id="TarsJava"></span>

- [快速开始](dev/tarsjava/tars-quick-start.md)
- [使用指南]
  - [Tars 服务开发与上线](dev/tarsjava/tars-tutorials.md)
  - [HTTP 服务开发与上线](dev/tarsjava/tars-http-server.md)
  - [生成接口调用文件](dev/tarsjava/tars-reference.md)
- [性能测试]
  - [tars java 压测代码 ](dev/tarsjava/stress-testing.md)

## TarsGo <span id="TarsGo"></span>

- [cmake 管理代码](dev/tarsgo/spec.md)
- [pb2tarsgo](dev/tarsgo/pb2tarsgo.md)
- [性能数据](dev/tarsgo/performance.md)
- [案例]
  - [服务端使用 context 示例](demo/tarsgo/context.md)
  - [tars 客户端示例](demo/tarsgo/client.md)
  - [tars 服务端示例](demo/tarsgo/server.md)
  - [zipkin 调用链追踪客户端示例](demo/tarsgo/zipkin-client.md)
  - [zipkin 调用链追踪服务端示例](demo/tarsgo/zipkin-server.md)

## TarsPHP <span id="TarsPHP"></span>

- [环境搭建]
  - [搭建 php 环境](dev/tarsphp/Environment/php.md)
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

## 其他重要特性 <span id="important"></span>

- [业务配置](dev/tars-config.md)
- [服务监控](dev/tars-monitor.md)
- [模板配置](dev/tars-template.md)
- [用户体系模块使用指引](dev/tars-web-user.md)
- [管理平台 API](dev/tars-web-api.md)
- [无损发布 重启](dev/tars-lossless-patch.md)
- [调用链](dev/tars-trace.md)
- [IDC 分组](dev/tars-idc-set.md)
- [鉴权功能](dev/tars-auth.md)

## K8SFramework <span id="k8s"></span>

- [安装和使用说明]
  - [介绍](k8s/tarsk8s.md)
  - [特性](k8s/property.md)
  - [快速部署](k8s/quick-deploy.md)
  - [源码部署](k8s/source-deploy.md)
  - [版本升级](k8s/upgrade.md)
  - [云原生运维](k8s/cloud-operator.md)
  - [管理平台](k8s/tarsweb-operator.md)
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

## 常见问题 <span id="question"></span>

- [安装常见问题](question/Install_faq.md)
- [TarsCPP 常见问题](question/tarscpp-question.md)
- [TarsJava 常见问题](question/tarsjava-question.md)
- [TarsPHP 常见问题](question/tarsphp-question.md)
- [TARS 每日问答](question/daily_question.md)

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
