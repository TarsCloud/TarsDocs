## 框架集群化机制

企业版本框架中, 不再依赖数据, 此时tarsregistry采用了raft机制来实现集群化, 从而达到了2n+1台节点来完成故障容灾.

通常情况下, 由于采用了raft集群化设计, 必须至少部署三台tarsregistry(逻辑节点)才可以满足需求, 当然由于企业版本框架可以在同一台节点上部署多个tarsnode, 因此三台tarsregistry可以部署在同一个服务服务器.

**注意, 框架也可以单节点部署, 请参考框架单节点机制**

### 集群化优势

- 不再依赖第三方mysql数据库, 因此在部署, 运维上更加方便;
- 集群化以后, 对于2n+1台tarsregistry服务, 挂掉n台也不影响tarsregistry对外提供功能;
- 能够实现更高的可用性;
- 依赖集群化,能够实现业务服务`一主多备`的机制;

### 数据目录

tarsregistry的数据文件目录在: `/usr/local/app/tars/tarsnode/data/tars.tarsregistry/data/DB` 目录下

### 框架扩容

扩容主控节点非常简单, 只需要启动一个新的framework即可, 只是这时候locator需要增加自己当前主控的节点即可, 框架会自动感知并添加这个节点.

可以使用查看所有主控的信息:
```shell
tarsctl framework registry
```

### 框架缩容

如果要减少tarsregsitry, 只要停掉对应的框架, 然后web平台上下线对应的tarsregistry服务即可.

框架会自动感知, 并剔除掉下线的tarsregistry
