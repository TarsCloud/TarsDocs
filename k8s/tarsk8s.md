# 目录
- [TarsCloud K8SFramework](#tars-k8sframework)
    * [设计](#设计)
        + [CRD](#crd)
        + [Controller](#controller)
        + [Framework](#framework)
    * [特性](#特性)
    * [安装与升级](#安装与升级)
    * [运维](#运维)
    * [开发者](#开发者)
    * [Todo](#todo)

# TarsCloud K8SFramework

**TarsCloud K8SFramework** 是为了将 Tars 部署在 Kubernetes 集群而做的适应性改造项目.  
**TarsCloud K8SFramework** 提供了包括部署,升级,回滚,、伸缩容,配置变更的在内的 Tars 服务全生命周期管理.  
借助 **TarsCloud K8SFramework**, Tars 服务可以无缝运行在公有云或私有部署的 Kubernetes 集群.

## 设计

**TarsCloud K8SFramework** 使用 **Kubernetes Operator** 模式支持 Tars 服务在 Kubernetes 集群的部署和运维.  
具体由 CRD, Controller, Framework 三部分组成, 其关系如下:

### CRD

crd 是我们在 **Kubernetes** 集群中增加的一些自定义资源类型, 每种资源类型都抽象了 Tars 框架的概念或动作,  
对这些资源执行 "增删改查" 操作, 就是对 Tars 框架的查询,管理和运维. crd 类型有:

+ tserver: tserver 抽象了 tars 服务属性, 每个 tserver 对象代表着一项 tars 服务.
+ tconfig: tconfig 抽象了 tars 服务配置属性, 每个 tconfig 对象代表一项 tars 服务配置.
+ ttemplate: ttemplate 抽象了 tars 服务模板属性, 每个 ttemplate 对象代表了一项 tars 模板.
+ taccount: taccount 抽象了 tarsweb 用户, 每个 taccount 对象代表一个 tarsweb 账号.
+ timage: timage 抽象了 tars 服务版本发布, 每个 timage 对象都记录了其关联 tars 服务已发布版本,发布时间,镜像地址,镜像密钥等信息.
+ tendpoint: tendpont 抽象了 tars 服务运行状态, 每个 tendpoint 对象都记录了其关联 tserver 的运行实时运行状态.
+ texitedrecord: texitedrecord 抽象了 tars 服务的退出状态, 每个 texitedrecord 对象都记录了其关联 tserver pods 的生命周期信息
+ tframeworkconfig: 每套tars 框架其只有唯一的 tframeworkconfig 对象, 用于记录 tars 框架级别的配置信息
+ ttree: 每套 tars 集群且其只有唯一的 ttree 对象, 用于记录 tars business 与 tars app 的关联信息

### Controller

controller 是 **Kubernetes Operator** 模式的核心. 主要职责有:

+ 校验 crd 对象值合法性, 填充默认值, 添加,删除对象标签值
+ 持续监听 crd 对象的 "增删查改" 操作, 并将调谐成 Kubernetes 原生对象(statefulset,daemonset,service)
+ 提供 crd 版本兼容转换服务

### Framework

framework 是指 tars 框架中的基础服务, 相比原生 tars 框架, **TarsCloud Kubernetes** 增删了部分服务,其清单和功能说明如下:

+ elasticsearch: 单节点 elasticsearch 服务, 替代原生 Tars 中的 MySql, 用于存储服务监控,特性监控,消息通知等信息
+ tarsweb: 提供运维管理平台服务
+ tarsregistry: 提供注册中心服务
+ tarsconfig: 提供配置中心服务
+ tarsnotify: 提供消息中心服务
+ tarslog : 提供日志中心服务
+ tarsproperty, tarsqueryproperty: 提供特性监控数据汇聚和查询服务
+ tarsstat, tarsquerystat: 提供的服务监控数据汇聚和查询服务
+ tarsimage, tarskaniko: 提供镜像构建服务
+ tarskevent: 提供 kubernetes 集群的 event 监听服务
+ tarsnode: 节点守护程序

每个 **Kubernetes** 集群只可以部署一套 CRD 和 Controller, 可以在不同的命名空间分别部署 Framework.
每套 Framework 互相独立, 等价一套原生 Tars 集群

## 特性

**TarsCloud K8SFramework** 特性介绍请参考 <<[特性](docs/zh/property.md)>>

## 安装与升级

**TarsCloud K8SFramework** 以 Helm Cart 的形式对外发布  
您可以参考 <<[安装](docs/zh/install.md)>> 进行安装  
您可以参考 <<[升级](docs/zh/upgrade.md)>> 进行升级

## 运维

**TarsCloud K8SFramework** 支持两种运维管理方式  
普通开发部署人员 可以通过 <<[管理平台](docs/zh/tarsweb.md)>> 进行运维管理  
Kubernetes 集群管理员可以通过<<[云原生运维](docs/zh/kubectl.md)>> 进行运维管理

## 开发者

做为开发者,您可以参考 <<[构建](docs/zh/make.md)>> 从源码开始构建项目

## Todo

+ 时区处理:
  当前 **TarsCloud K8SFramework** 是通过在 Pod 中挂载 宿主机 /etc/localtime 文件尝试解决时区同步问题, 但是在某些宿主机无法按预期工作,我们正在探索更好的办法.

+ Tars SSL 支持:  
  当前 **TarsCloud K8SFramework** 未支持 Tars SSL, 我们会在后续版本提供支持

+ Tars Set 支持
  当前 **TarsCloud K8SFramework** 未支持 Tars Set, 我们会在后续版本提供支持

+ 文档计划
  我们会逐步增加 **TarsCloud K8SFramework**  项目的文档

+ 测试案例
  我们会逐步增加 **TarsCloud K8SFramework**  项目的测试案例, 包括针对 tarscontroller ,tarsframework ,helm安装,升级,降级的自动化测试

+ Tars DCache 支持
  当前 **TarsCloud K8SFramework** 未支持 Tars DCache, 我们会在后续版本提供支持

+ 调试工具
  Pod 中的服务调试运行比较困难,我们将会在基础镜像中添加 DebugTools, 便于在 Pod 中排查故障

+ 迁移工具
  您可能有很历史服务需要迁移部署到**TarsCloud K8SFramework** , 我们将会在项目添加迁移工具
