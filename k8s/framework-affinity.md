# 框架亲和性

完成框架安装以后, 框架的服务实际还没有被K8S调度起来, 因为Tars框架不知道应该调度到K8S哪些节点上, 因此还需要按照以下步骤操作, 完成K8S对框架的调度.

## 给节点打标签

通常K8S集群里面节点比较多, 而TARS可能只需要使用里面部分节点, 为了控制到底TARS服务可以运行在哪些节点上, 需要给节点打上特定的标签如下, 分两个层级:

- 如果希望部署的TARS框架以及后续发布的tars服务能被调度到某些节点, 则这些节点需要打上如下标签:

```tars.io/node.$namespace ```, 比如: ```tars.io/node.tars-dev```

**这里$namespace 是K8S的名字空间, 即上面helm安装TARS时指定的名字空间**

使用命令行打标签如下:

```
kubectl label nodes $node-name tars.io/node.tars-dev=
```

**注意必须打好标签, tars-test这个名字的空间的所有TARS服务才会被调度上去, 这一步必须手工执行!**

后续新增节点, 都需要对节点打上这个标签, 这个名字空间下的Tars框架才会调度服务到这个节点上!

- TARS框架本身也有应用的概念, 可以通过给节点继续打标签, 保证TARS某个应用下所有服务能调度到这些节点, 如下方式:

```tars.io/ability.$namespace.$app ```

注意:
>- $namespace 是K8S的名字空间, 即上面helm安装TARS时指定的名字空间
>- $app是TARS下面的应用概念, 即TARS某个应用下的服务可以部署在这些节点(这一步可以在web上控制亲和性)

**完成上述两步以后, 可以在K8S上看到, 除了es和tarslog之外, 其他服务已经启动了**

es和tarslog由于需要存储空间, 因此需要特别处理.

- 给es和tarslog分配LocalPV

目前的K8STARS支持LocalPV, 服务需要LocalPV时, 可以申请, 后续文档会介绍. 

这里Ees和tarslog由于没有合适的PV一直处于pending中, 只需给可以申请LocalPV的节点打上标签```tars.io/SupportLocalVolume ```

使用命令行打标签如下:

```
kubectl label nodes $node-name tars.io/SupportLocalVolume=
```

注意这里的node-name必须已经打上了```tars.io/node.tars-dev=```标签, 否则无效!

至此, TARS看框架已经安装完成, 可以打开: ${web_host} 看到tars web管理平台!
