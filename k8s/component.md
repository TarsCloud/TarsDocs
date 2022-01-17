# TarsK8S 的组成

- tarscontroller

  > tars 框架在 k8s 平台的控制器程序

- tarsregistry

  > k8s tarsRegistry 与 tars 框架中的 tarsRegistry 提供相同的业务服务接口,具备相同的功能. 在实现上是通过 k8s list-watch 机制来感知集群内的各个服务状态,并对外提供服务
  > 简单说 k8s tarsRegistry 名字服务是和 k8s 打通了的

- tarsweb

  > k8s tarsWeb 是对外提供的管理界面
  > 你也可以通过 kubectl 来完成服务的控制

- tarsnotify

  > k8s tarsNotify 与 tars 中的 tarsNotify 服务有相同的业务服务接口,在实现上有细微的差别,但是对业务服务透明.

- tarsconfig

  > k8s tarsConfig 与 tars 中的 tarsConfig 服务有相同的业务服务接口.

- tarsproprety ,tarsstat, tarsqueryproperty ,tarsquerystat,tarslog

  > tarsProperty ,tarsStat, tarsQueryProperty ,tarsQueryStat,tarsLog 与 tars 中的同名服务使用相同代码构建，完全兼容.
  > 数据监控采用了 es, 不再采用 mysql 存储监控数据
  > 原则上 tarsproprety ,tarsstat, tarsqueryproperty ,tarsquerystat 也可以使用在非 K8S 版本的 Tars 框架中, 只需要配置好对应的模板, 连接 es 即可

- tarsagent

  > tarsagent 是 tarsk8S 新增的框架服务. 用于提供主机端口检测,日志清理等辅助功能.
  > tarsagent 是 daemonset, 每台节点都会部署一个

- tarsnode

  > k8s tarsnode 程序经过轻量化改造，集成在每一个业务服务镜像中.
  > 如果业务服务做为后台服务启动, 那么 tarsnode 则作为容器内的 1 号进程运行, 此时业务服务挂掉, pod 不会重建, tarsnode 会重新拉起业务服务(和非 K8S 版本一致)
  > 如果业务服务作为前台服务启动, 那么业务服务挂掉, 则 Pod 会重建
  > 业务服务这个配置在 TServer 中配置

- tarsimage

  > 此服务程序是 tars-k8s 框架新增的服务，用于提供镜像生成功能, web 平台上可以直接上传服务的 tgz 包, tarsimage 会把 tars 服务制作成 docker 镜像.
  > 通过 tarsimage, 你可以直接上传服务的 tgz 包(和非 K8S 版本一样), web 平台和 tarsimage 会配合, 生成镜像并发布, 减少等待时间, 方便调试使用.

- 删除了 tarspatch , tarsAdminRegistry
