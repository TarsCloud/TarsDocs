
# TarsK8S 的运维

在一般情况下，TarsK8S 的运维方式 与 tars 几乎一致或见名(界面)

## 伸缩 tarsregistry, tarsimage , tarsadmin,tarsweb
 因为tarsregistry, tarsimage ,tarsadmin, tarsweb 与业务服务部署方式不太一致.在tarsweb上是无法管理这些服务的, 当需要伸缩时，可以使用 kubectl 修改。
 另外，tarsregistry, tarsadmin 承担着同步集群状态数据到 tars_db 的工作 ，需要确保无论何时，必须有一个 tarsregistry pod,tarsadmin 存在，否则会导致 tars_db 记录的数据与实际状态不一致.

## 修改镜像参数
configmap/tars-image 中存储了镜像仓库地址，镜像仓库用户名，基础镜像Tag等信息. tars-tarsimage pod 在启动时会将 configmap/tars-image 挂载为环境变量，并在工作中使用

如果需要修改相关值，则需要先修改 configmap/tars-image ,然后重启所有 tars-tarimage pod
后续会升级为不需要重启 tars-tarsimage,延迟数秒后自动生效
后续可能会提供 tarsweb 提供管理接口 

此外,tarsimage 只支持单点运行,请保持  tarsimage pod数为 1个

