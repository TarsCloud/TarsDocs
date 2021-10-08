# TarsWeb说明

TarsWeb>=v2.4.8的版本能够支持K8SFramework, 当然你其实可以通过kubectl来控制所有的服务, 不需要使用TarsWeb, 但是TarsWeb能够更方便你管理.

结合K8SFramework提供的功能, 它的主要特性包括:

- 服务的发布, 扩容, yaml文件变更
- pod漂移以后, 记录历史pod/节点ip, 能看到服务的漂移前的日志, 方便定位问题
- 记录了pod历史镜像, 访问回滚
- 服务流量和耗时统计, 属性上报的展示
- 配置文件修改, 变更以及回滚
- stat/property/调用链的等数据记录在es中, 因此Tars部署时, 会默认部署一个单节点的es, 有需要你可以修改相关服务模板, 指向你自己的es
- shell方式进入pod, 方便查看日志