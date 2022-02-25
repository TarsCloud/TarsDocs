# TarsK8S



## 项目介绍

TarsK8S 是为了将 Tars 部署在 K8S 平台上而做的适应性改造项目. 提供了包括部署、升级、扩缩容、备份恢复、配置变更的 Tars 服务全生命周期管理. 借助 TarsK8S, Tars 可以无缝运行在公有云或私有部署的 Kubernetes 集群上.

TarsK8S 暂未支持一下特性:

- Tars Set
- Tars DCache



## 项目设计

TarsK8S 使用自定义控制器模式来完成 Tars 在 K8S 集群的部署, 由 CRD, Controller, Framework 组成.

每个 K8S 集群可以部署一个 Controller , 并在不同的Namespace 中部署多套 Framework.  每套 Framework 互相独立.



CRD 包括:

+ tdeploy:  tdeploy 定义了一项 tars 服务部署申请的属性, 没提交一个 tdeploy 对象意味着在 k8s 集群中提交了一项 tars 服务部署申请.

- tserver:   tserver 定义了一项 tars 服务的属性,  每提交一个 tserver 对象意味则在 k8s 集群中部署可一项 tars 服务.

- tconfig:   tconfig 定义了一项 tars 服务配置属性, 每提交一个 tconfig 对象意味则为 某个 tars 业务服务增加了一项业务配置.
- ttemplate:  ttemplate 定义一项 tars 模板属性,  每提交一个 ttemplate 对象意味则在 k8s 集中部署了一个 tars 模板.
- timage:  每个 tserver 对象通过 label 与一个或多个timage 对象关联, 并在 timage 对象中记录服务发布版本及镜像地址等信息. 
- tendpoint:  crontroller 为每个 tserver 对象新建一个同名的 tendpoint 对象, 并在 tendpoint 中记录关联 tserver 的运行实时运行状态.
- texitrecord: crontroller 为每个 tserver 对象新建一个同名的 texitrecord 对象,并在texitrecord 中记录关联 tserver 的 pods 生命周期信息.
- tframeworkconfig:  每套 framework 中都有一个唯一的 tframeworkconfig 对象 记录 framework 级别的配置.
- ttree:  每套 framework 中都有一个唯一的 ttree 对象, 用于记录 tars business  与 tars app 的关联关系.  

  关于 crd 的更多细节请参考



Controller:

  controller 是自定义控制器模式的核心. 主要职责有:

+ 将 crd 对象映射调谐成 k8s 原生对象(statefulset , deployment, daemonset,service)
+ 校验 crd 对象字段合法性或者填充默认值
+ 校验各项 tars 运维操作的合法性
+ 多 crd 版本提供兼容转换

  关于 controller 的更多细节请参考



Framework:

​	framework 是指 tars 框架中的基础服务, 相比原生版本, tarsk8s 对这些服务程序代码进行了重构,使其能使用在 k8s 集群中工作,具体如下:

+ tarsweb:  tars 微服务框架的运维管理平台  

+ tafnode:  tars 微服务框架的节点守护程序

+ tarsregistry:   tars 微服务框架中的注册中心
+ tarsconfig:  tars 微服务框架的配置中心
+ tarsnotify:  tars 微服务框架的消息通知中心
+ tarslog :  tars 微服务框架的日志中心
+ tarsproperty, tarsqueryproperty:  提供 tars 微服务框架的特性监控数据接收和查询服务

+ tarsstat, tarsquerystat:  提供 tars 微服务框务的服务监控数据接收和查询服务
+ tarsimage:  tarsk8s  新增的 framework 服务, 提供镜像构建服务
+ tarselasticsearch:   tarsk8s  新增的 framework 服务, 用于替代原生 tars 中的 mysql, 上文提及的消息, 特性监控, 服务监控数据落地到 tarselasticsearch 中



  framework 程序重构原则如下:

 + framrework 之间使用数据结构的接口, 允许重新设计

 + 对业务程序暴露的数据结构和接口, 保证全面兼容
 + 业务程序不需要区分运行环境是否为 k8s 集群内

 以上原则可以保证绝大多数历史 tars 服务能通过 tarsk8s 无缝部署到 k8s 集群



关于 framework 的更多细节请参考



## 项目部署

 源码部署

 Helm部署



## 项目阶段

TarsK8S 目前仍然处于测试阶段, 但在充分了解各项特性并严格测试需求是否满足后, 可以用于生产环境.
