# TarsWeb 说明

TarsWeb>=v2.4.18 的版本能够支持 K8SFramework, 当然你其实可以也通过 kubectl 来控制所有的服务, 不需要使用 TarsWeb, 但是 TarsWeb 能够更方便你管理.

结合 K8SFramework 提供的功能, 它的主要特性包括:

- 服务的发布, 扩容, yaml 文件变更
- pod 漂移以后, 记录历史 pod/节点 ip, 能看到服务的漂移前的日志, 方便定位问题
- 记录了 pod 历史镜像, 访问回滚
- 服务流量和耗时统计, 属性上报的展示
- 配置文件修改, 变更以及回滚
- stat/property/调用链的等数据记录在 es 中, 因此 Tars 部署时, 会默认部署一个单节点的 es, 有需要你可以修改相关服务模板, 指向你自己的 es
- shell 方式进入 pod, 方便查看日志
- 支持服务以 tgz 包形式上传打包, web 会调用 tarsimage 制作镜像并发布
