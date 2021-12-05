# Tars 服务的节点亲和性

TarsK8S 还提供了调整节点亲和性的逻辑, 可以给节点打上以下标签后, 实现 tars 服务和节点的亲和性, 标签说明如下:

- App 级别节点标签: tars.io/ability.${namespace}.${App}
- Server 级标签 tars.io/ability.${namespace}.${App}-${Server}
- 添加独立字段表示亲和性选项

tars 服务 tserver 中, 可以使用如下方式来指定亲和性:

```yaml
k8s:
  abilityAffinity: AppOrServerPreferred
```

crd 文件描述如下:

```yaml
k8s:
  type: object
  properties:
    abilityAffinity:
      type: string
      enum: [AppRequired, ServerRequired, AppOrServerPreferred, None]
      default: AppOrServerPreferred
```

说明:

- AppRequired： 在满足其他条件后，节点必须有 tars.io/ability.${namespace}.${App} 标签
- ServerRequired 在满足其他条件后，节点必须有 tars.io/ability.${namespace}.${App}-${Server} 标签
- AppOrServerPreferred: 在满足其他条件后，优先选择有 tars.io/ability.${namespace}.${App}-${Server}, tars.io/ability.${namespace}.${App} 标签的节点. 如果节点没有 ability 标签， 则忽略
- None： 不对节点标签做要求
- 注意当然节点也需要拥有 namespace 的标签: tars.io/node.${namespace}, 表示这个 namespace 的服务能调度到这个节点

以下是打标签的例子:

```sh
kubectl label nodes vm-0-47-centos tars.io/node.tars-dev=
kubectl label nodes vm-0-47-centos tars.io/ability.tars-dev.debug-compiler
```
