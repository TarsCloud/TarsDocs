[TOC]

# TarsK8S

## 项目介绍

TarsK8S 是为了将 Tars 部署在 K8S 平台上而做的适应性改造项目. 提供了包括部署、升级、扩缩容、备份恢复、配置变更的 Tars 服务全生命周期管理. 借助 TarsK8S, Tars 服务可以无缝运行在公有云或私有部署的
K8S 集群上.

TarsK8S 暂不支持以下特性:

+ tars set
+ tars dcache

关于 TarsK8S 的更多特性请参考 [<<TarsK8S 特性>>](property.md)

## 项目设计

TarsK8S 使用自定义控制器模式来完成 Tars 在 K8S 集群的部署, 由 CRD, Controller, Framework 组成.

每个 K8S 集群可以部署一套 Controller , 并在不同的命名空间中部署多套 Framework. 每套 Framework 互相独立.

### CRD

+ tdeploy:  tdeploy 定义了一项 tars 服务部署申请的属性, 每提交一个 tdeploy 对象意味着在 k8s 集群中提交了一项 tars 服务部署申请.
+ tserver:   tserver 定义了一项 tars 服务的属性, 每提交一个 tserver 对象意味则在 k8s 集群中部署可一项 tars 服务.
+ tconfig:   tconfig 定义了一项 tars 服务配置属性, 每提交一个 tconfig 对象意味则为 某个 tars 业务服务增加了一项业务配置.
+ ttemplate:  ttemplate 定义一项 tars 模板属性, 每提交一个 ttemplate 对象意味则在 k8s 集中部署了一个 tars 模板.
+ timage:  每个 tserver 对象通过 label 与一个或多个timage 对象关联, 并在 timage 对象中记录服务发布版本及镜像地址等信息.
+ tendpoint:  crontroller 从每个 tserver 对象衍生一个同名 tendpoint 对象, 并在 tendpoint 中记录关联 tserver 的运行实时运行状态.
+ texitedrecord: crontroller 从每个 tserver 对象衍生一个同名 texitrecord 对象, 并在 texitedrecord 中记录 tserver pods 的生命周期信息
+ tframeworkconfig:  每套 framework 中有且其只有唯一的 tframeworkconfig 对象 记录 framework 级别的配置
+ ttree:  每套 framework 中且其只有唯一的 ttree 对象, 用于记录 tars business 与 tars app 的关联信息

crd 的更多细节请参考 [<<TarsK8S 特性>>](property.md)

### Controller

controller 是自定义控制器模式的核心. 主要职责有:

+ 将 crd 对象调谐成 k8s 原生对象(statefulset , deployment, daemonset,service)
+ 校验 crd 对象值合法性, 填充默认值, 添加,删除标签
+ 校验运维操作的合法性
+ 提供 crd 版本兼容转换服务

controller 的更多细节请参考 [<<TarsK8S 特性>>](property.md)

### Framework

framework 是指 tars 框架中的基础服务, 相比原生版本, tarsk8s 对这些服务程序进行了代码重构, 使其能使用在 k8s 集群中工作,这些程序的具体功能如下:

+ tarsweb:  tars 微服务框架的运维管理平台
+ tafnode:  tars 微服务框架的节点守护程序
+ tarsregistry:   tars 微服务框架中的注册中心
+ tarsconfig:  tars 微服务框架的配置中心
+ tarsnotify:  tars 微服务框架的消息通知中心
+ tarslog :  tars 微服务框架的日志中心
+ tarsproperty, tarsqueryproperty:  提供 tars 微服务框架的特性监控数据接收和查询服务
+ tarsstat, tarsquerystat:  提供 tars 微服务框务的服务监控数据接收和查询服务
+ tarsimage:  tarsk8s 新增的 framework 服务, 提供镜像构建服务
+ tarskevent:  tarsk8s 新增的 framework 服务, 用于监听 k8s 集群的 event 资源变动
+ tarselasticsearch:  tarsk8s 新增的 framework 服务, 前文提及的消息, 特性监控, 服务监控, evnet 数据都会落地到 tarselasticsearch 中

framework 程序重构原则如下:

+ 允许重新设计 framrework 程序之间的 rpc 数据结构和接口
+ 兼容 frameowork 程序与业务程序 rpc 数据结构和接口
+ 业务程序对是否运行环境在 k8s 集群内无感知

以上原则可以保证绝大多数历史 tars 服务能通过 tarsk8s 无缝部署到 k8s 集群

## 项目阶段

TarsK8S 处于公开测试阶段, 在充分了解各项特性并确认需求能被满足后, 可用于生产环境

## 项目部署

关于项目部署的细节请参考 <<[TarsK8S 部署](deploy.md)>>

## 项目测试

tarsk8s 支持 e2e 测试, 请参考 <<TarsK8S 测试>>

## 兼容性政策

