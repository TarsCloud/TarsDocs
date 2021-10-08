
# TarsK8S 介绍

## 设计思路

TarsK8S使用CRD+Operator来完成Tars在K8S上的部署, 因此所有Tars服务以及资源在K8S上都对应了一个CRD来管理, 开发者可以不需要通过tarsweb来完成服务的控制, 通过kubectl即可完成资源的控制.

这些CRD包括:
- TAccount: 账号以及权限管理, 对应了web上的账号权限
- TConfig: 配置文件, 对应tars服务的配置文件(不是通过ConfigMap来实现的), 目的是尽可能兼容非K8S版本的Tars服务, 这样非K8S的tars服务能够平滑迁移到K8S
- TDeploy: 部署申请和流程
- TServer: tars服务, 每个tars服务对应一个TServer资源
- TEndpoint: 服务的端口申明, 对应的Adapter
- TImage: tars业务的发布镜像, 每个业务都对应了一个TImage资源, 里面记录了历史的发布镜像, 方便在web上回滚
- TExitRecord: 服务退出历史, 在web上可以参考服务之前在哪些节点运行, 可以看到历史的日志文件
- TTemplate: 模板文件, 对于非K8S版本模板文件
- TTree: 业务目录树
- TGateway: 网关obj记录

这些crd文件在 install/tarscontroller/crds目录下.

控制服务是tarscontroller(src/ControllerServer), 它根据用户apply的的yaml文件(yaml文件直接描述上述资源), 来转换成k8s自己的资源(statefullset等), 最终tars服务在K8S上的部署.

所有在web上对服务的变更, 原则上都可以通过kubectl来完成!
当年你需要自己首先这些资源的yaml文件时, 请参考这些crd文件!

## 部署方式

- 编译源码(K8SFramework), 并推送镜像到自己的仓库, [请参考](../Install.md)
- 或者直接使用官方镜像(即无需要自己编译K8SFramework源码)
- 然后通过helm完成tarscontroller的部署
- 再通过helm完成tars框架的部署, 一个K8S集群上可以部署多套TARS环境(每套还有自己独立的名字空间即可)
