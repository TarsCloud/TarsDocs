# 云原生运维

**TarsCloud K8SFramework** 采用了流行的  "自定义控制器模式", 并将所有 tars 的可运维对象都抽象为声明式对象. 您可以使用 kubectl 或其他兼容 Kubernetes 集群管理工具增删改查这些声明式对象来完成您的运维目标.

下文描述了一些常用的云原生操作流程:

> Q:  查询 tars 框架已部署的服务
> A:  执行如下操作列举出当所有 tars 服务
>
> ```shell
> kubectl get tserver -n ${namespace} -o wide
> ```

---

> Q: 修改 framewor 程序使用的 elasticsearch 节点地址
> A:  在安装完成后, elasticsearch 节点地址配置在 tars ttemplate tars.elk 中, 您可以执行如下命令
>
> ```shell
> kubectl edit ttemplate -n ${namespace} tars.elk
> ```
---

> Q: 将 tars.tarsregistry 的服务的副本数 扩容为 3 个
> A:  执行如下操作可以扩容 tserver 资源
>
> ```shell
> kubectl scale tserver -n ${namespace} tars-tarsregistry --replicas=3
> ```

---

> Q: 新部署服务 Test-TestServer
> A:  您可以新建 testserver.yaml 文件, 按 tserver 定义填充好字段值, 然后执行如下操作:
>
> ```shell
> kubectl apply -n ${namespace} -f testserver.yaml
> ```

---

> Q: 查看/修改账号密码
>
>A:  您可通过 md5(username) 找到对应账号的 taccount 对象, 然后修改其 spec.authorization.password 字段, 完成账号密码的重置
>
>```shell
>kubectl edit -n $(namespace) taccount 21232f297a57a5a743894a0e4a801fc3
>```

更多操作方式, 您可以参看 <<[特性](property.md) >>或 Kubernetes 官方文档获取更多信息
