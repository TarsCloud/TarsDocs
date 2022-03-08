# Tars 深度融合 K8S

具体源码请访问: https://github.com/TarsCloud/K8SFramework

提供了一种 Tars 和 K8S 深度融合的方案, 使用 k8s 部署的情况强烈推荐这种模式!

主要说明如下:

- 使用了 CRD+Operator 的方式完成整合, 即可以用 kubectl 完成对 Tars 中所有资源的控制, 包括服务, 配置, 发布镜像等
- 不再使用 mysql 作为数据存储, 所有 tars 框架依赖的数据都存储在 etcd 中, 方便管理和维护
- 安装时首先安装 tars 控制器服务(tarscontroller), 然后可以在一个 k8s 集群中安装多个 tars 框架(每个都有自己独立的 namespace)
- K8SFramework 包含了通用的组件, tarsregistry, tarsstat 等组件, 以及 tarsweb, 即也可以通过 tarsweb 来管理 tars 业务服务(tarsweb 的版本>=v2.4.18)
- K8SFramework 支持了大量关键特性, 帮助你管理服务的部署, 比如业务服务亲和性设置, localpv
- LocalPv 的特性甚至可以用在某些数据存储服务上, 比如 mysql, es 等, 是得这些第三方组件部署在 K8S 上后不漂移!
- 使用文档也包含了 CICD 的说明, 你可以使用源码中提供好的 helm 包, 快速完成服务的部署(当然也可以通过 tarsweb 来完成)

[具体文档请参考](../k8s/tarsk8s.md)
