# TarsK8S 说明文档

## 项目介绍

TarsK8S 是为了将 Tars 部署在 K8S 平台上而做的适应性改造项目

TarsK8S 改造的原则:

   1. 改造仅限于 framework 程序（tarsregistry ,tarsnode , tarsnotify , tarsconfig）

2. 改造后的 framework 程序对业务服务暴露接口保持完全兼容



基于以上原则,现在的 Tars 服务程序在无需大改的情况下就可以迁移到 TarsK8S 平台



目前以下特性还未被支持:

- TarsK8S 暂时不支持 Set

- TarsK8S 暂时不能部署 DCache 服务



## 技术说明

Tars 深度整合 K8S 中, 主要说明如下:

- 使用了 CRD+Operator 的方式完成整合, 即可以用 kubectl 完成对 Tars 中所有资源的控制, 包括服务, 配置, 发布镜像等
- 安装时首先安装 tars 控制器服务(tarscontroller), 然后可以在一个 k8s 集群中安装多个 tars 框架(每个都有自己独立的 namespace)
- tars 框架不再依赖数据库, 所有数据存储都存放在 K8S 的 etcd, 通过 K8S 的 crd 来实现
- tars 框架包含了通用的组件, tarsregistry, tarsstat 等组件及 tarsweb, 可以通过 tarsweb 来管理 tars 业务服务(tarsweb 的版本>=v2.4.18)
- tars 框架支持了大量关键特性, 帮助你管理服务的部署, 比如业务服务亲和性设置, localpv 等
- 使用文档也包含了 CICD 的说明, 你可以使用源码中提供好的 helm 包, 快速完成服务的部署(当然也可以通过 tarsweb 来完成)

详细文档[请参见](https://tarscloud.github.io/TarsDocs/)K8SFramework 部分
