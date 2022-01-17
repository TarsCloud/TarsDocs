# TarsK8S 的运维

在一般情况下，TarsK8S 的运维方式 与 tars 几乎一致或见名(界面)

## 伸缩 tarsregistry, tarsimage , tarsadmin,tarsweb

tarsregistry, tarsimage ,tarsadmin, tarsweb 与 tars 业务服务部署方式不太一致(这些服务是都是普通服务, 不是 tars 服务, 不存在 servant), 只能以 K8S 方式来管理

## 修改镜像参数

configmap/tars-image 中存储了镜像仓库地址，镜像仓库用户名，基础镜像 Tag 等信息. tars-tarsimage pod 在启动时会将 configmap/tars-image 挂载为环境变量，并在工作中使用

如果需要修改相关值，则需要先修改 configmap/tars-image ,然后重启所有 tars-tarimage pod
后续会升级为不需要重启 tars-tarsimage,延迟数秒后自动生效
后续可能会提供 tarsweb 提供管理接口

此外,tarsimage 只支持单点运行,请保持 tarsimage pod 数为 1 个
