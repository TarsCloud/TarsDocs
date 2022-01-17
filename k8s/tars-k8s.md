# TarsK8S 介绍

## 设计思路

TarsK8S 使用 CRD+Operator 来完成 Tars 在 K8S 上的部署, 因此所有 Tars 服务以及资源在 K8S 上都对应了一个 CRD, 开发者即可以通过 tarsweb 来完成服务的控制, 也可以通过 kubectl 即可完成资源的控制.

这些 CRD 包括:

- TAccount: 账号以及权限管理, 对应了 web 上的账号权限
- TConfig: 配置文件, 对应 tars 服务的配置文件(不是通过 ConfigMap 来实现的), 目的是尽可能兼容非 K8S 版本的 Tars 服务, 这样非 K8S 的 tars 服务能够平滑迁移到 K8S
- TDeploy: 部署申请和流程
- TServer: tars 服务, 每个 tars 服务对应一个 TServer 资源
- TEndpoint: 服务的端口申明, 对应的 Adapter
- TImage: tars 业务的发布镜像, 每个业务都对应了一个 TImage 资源, 里面记录了历史的发布镜像, 方便在 web 上回滚
- TExitRecord: 服务退出历史, 如果服务的 pod 漂移过, 在 web 上可以查询服务之前在哪些节点运行, 可以看到历史的日志文件, 方便定位问题
- TTemplate: 模板文件, 对于非 K8S 版本模板文件
- TTree: 业务目录树
- TGateway: 网关 obj 记录
- TFrameworkConfig: 框架的基础配置(K8SFramework >= 1.2.0)

这些 crd 文件在 install/tarscontroller/crds 目录下.

控制服务是 tarscontroller(src/ControllerServer), 它根据用户 apply 的的 yaml 文件(yaml 文件直接描述上述资源), 来转换成 k8s 自己的资源(statefullset 等), 最终 tars 服务在 K8S 上的部署.

所有在 web 上对服务的变更, 原则上都可以通过 kubectl 来完成!

当年你需要自己使用这些资源时, 请参考这些 crd 文件!

## 部署方式

- 编译源码(K8SFramework), 并推送镜像到自己的仓库, [请参考](../k8s/source-install.md)
- 或者直接使用官方镜像(即无需要自己编译 K8SFramework 源码)
- 然后通过 helm 完成 tarscontroller 的部署
- 再通过 helm 完成 tars 框架的部署, 一个 K8S 集群上可以部署多套 TARS 环境(每套有自己独立的名字空间即可)
- K8SFramework 还支持亲和性配置, K8S 集群中的多大 Tars 环境可以使用不同的节点, 互相不冲突.
