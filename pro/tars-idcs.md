
## 多数据中心管理机制

背景: 当我们有多个IDC, 且IDC间无内网专线, 需要通过外网通讯时, 我们可以采取多数据中心管理机制来实现.

### 设计目标

多数据中心管理机制实现以下目标:
- 只在`主数据中心`部署一套tars框架, 其他未部署TARS框架的数据中心叫`子数据中心`, `子数据中心`的服务器只需要部署tarsnode, 连接到`主数据中心`;
- `主数据中心`和`子数据中心`的节点机器可以都可以连接到tars框架, 并被框架所管理;
- 数据中心内部节点间通信都是通过内网, 但是`主数据中心`和`子数据中心`间通信需要通过外网;
- 每个数据中心只有极少数节点具有外网能力, 且和其他数据中心的具备外网的节点可以互通;
- 不同数据中心中的节点机器的nodeid(node-name)不能相同, 默认是用的ip, 通常使用字符串代替, 通过启动tarsnode时在参数`--nodeid=`中指定;

### 部署方式

为了更好理解, 多数据中心管理机制, 先来理解一下系统是如何部署的:

- 多个数据中心有一个数据中心要作为`主数据中心`, `主数据中心`内部部署了Tars框架, 其他集群为`子数据中心`, 子数据中心的节点只需要部署tarsnode
- 同时我们扩展了tarsnode的功能, 使用这个组件是用来桥接数据中心, 它部署在每个数据中心中有外网的机器上, 需要和其他数据中心有外网的tarsnode相互连接;

- 如果想启用tarsnode桥接功能, 启动时需要通过启动参数来控制:
>- `主数据中心`的tarsnode要配置连接到`主数据中心`的tarsregistry的内网地址上(即tarsnode的配置, locator指向主集群tarsregistry的内网地址);
>- `子数据中心`的tarsnode都要配置连接到`主数据中心`的tarsnode的外网地址上(即tarsnode的配置, locator指向主集群tarsnode的外网地址);
>- `子数据中心`的tarsnode都要配置连接到当前`子数据中心`的tarsnode上(即tarsnode的配置, locator指向当前子集群tarssnode的内网地址);

总结: 支持多数据中心管理, 需要在每个数据中心中选择有外网的机器部署tarsnode, 并开启式stub模式, 并且tarssnode之间需要相互连接, 以便数据中心间的服务能够相互发现.

### tarssnode功能介绍
> tarsnode提供了管理节点上的业务服务的能力, 这个功能和开源版本基本一致;
> tarsnode链接到tarsregistry后, 能实时同步tarsregistry中的数据, 并提供tarsregistry.QueryObj类似的服务发现接口(兼容了QueryObj的接口)
> tarsnode的服务发现机制通过tars.tarsnode.ServerObj端口来完成;
> 通过扩展, tarsnode支持了数据中心的桥接, 即tarsnode可以连接到另外数据中心的tarsnode上, 通过其他数据中心的tarsnode连接到主控, 并完成相关的功能, 从而实现数据中心的跨外网联通和管理机制;
>- 开通扩展以后, tarsnode会开启新的端口tars.tarsnode.StubObj, 这个端口提供了数据转发的功能,从而完成数据中心数据接收和转发, 因此这个端口需要开通外网;
>- 开通扩展以后并完成数据中心联通以后, 框架上的业务服务可以无缝的调用其他数据中心的服务;
- 开通扩展, 每个tarsnode需要指定一个stub名称, 用于区分数据中心, 即同一个数据中心中, 开通扩展的tarsnode的stub名称相同, 从而区分tarsnode是否是同一个数据中心的;
- tarsnode的名字在启动时, 通过命令行参数`--stubname=`指定, 同时配置文件中的locator, 指定上游tarsregistry或tarsnode的地址;
- tarsnode如果连接上游的tarsnode, tarsnode会感知, 并且上报到tarsregistry中;
- tarssnode会从上游同步tarsregistry的数据下来, 因此tarsnode也提供类似tarsregistry的服务发现的能力;
- tarsnode需要绑定两个端口
>- ServerObj端口提供类似tarsregistry的服务发现的功能, tarsnode被下游tarsnode连接时, 下游tarsnode配置文件中的locator指向该地址: 例如: tars.tarsnode.ServerObj@tcp -h -p ;
>- StubObj端口提供类似协议转发的请求(需要绑定内网和外网), 将内网请求转发到外部其他机房tarsnode的外网地址, 或者 将外部tarsnode过来的请求转发到内网对应的服务, 该端口通常会绑定在服务器的`0.0.0.0`上, 外网需要自己做好映射
>- 需要通过tarsctl工具或者web平台, 配置每个tarsnode提供的StubObj的内网地址和外网地址, 以便系统能识别能正常转发请求;

- tarsnode启动, 如果未开启扩展功能, 命令行参数启动如下:
```
./tarsnode --nodeid=[nodename] --config=[config file]
```
参数说明:
>- nodeid, tarsnode的节点名称;
>- config, 服务配置文件地址

- tarsnode启动, 如果开启了扩展功能, 命令行参数启动如下:
```
./tarsnode --nodeid=[nodename] --config=[config file] --open-stub --stubname=xxx --StubObj="tcp -h 0.0.0.0 -p 3333" 
```
参数说明:
>- nodeid, tarsnode的节点名称;
>- config, 服务配置文件地址
>- stubname, stub的名称, 同一个内网集群的所有stub必须一样, 如果是主机房, 请务必设置stubname为空
>- open-stub, 开启stub功能
>- StubObj, 提供协议转发功能的端口, 需要绑定内网和外网地址, 因此通常绑定在0.0.0.0上

- 启动后, tarsnode 会将自己的名称, ServerObj和StubObj地址上报到tarsregistry;
- 需要通过tarsctl或者web平台, 配置tarsnode的StubObj内外网地址(因为通常配置是0.0.0.0, 所以需要手工配置实际的内外网地址), 以便tarsnode能相互感知对方, 例如:
```shell
./tarsctl stub update --node-name=[nodename] --inner-address="tcp -h xxx -p yyy" --outer-address="tcp -h xxx -p yyy"
```
通过以上命令行, 指定StubObj对应的内网和外网实际ip地址

数据中心穿透机制和IDC/SET的关系
- IDC/SET启用强烈建议必须在同一个数据中心内网才生效, 避免穿透网络, 一旦发生网络穿透, 请求转发结果不可预期;
- tarsstub解析时, 只会在同一个stub内部去匹配IDC/SET的逻辑, 如果都找不到服务端, 则会返回本stub的内网地址, 从而请求方会将请求发送给其他子集群的stub;
- 子集群的stub服务, 会在本集群内部做寻址, 然后按照IDC逻辑去寻址转发, 从而丢失了SET的属性, 带来结果的不可预期;


## tarsnode穿透tarsstub到tarsAdminRegistry的长连接实现机制

整个链条如下: tarsnode -> tarsstub -> tarsstub -> tarsAdminRegistry

先看正常情况的流程:
- tarsnode->tarsAdminRegistry, tarsnode 会和每个tarsAdminRegistry建立长连接, 用于心跳和数据同步
- tarsnode会定时调用reportNode上报心跳, tarsAdminRegistry会记录node-name对应的链接
- 当tarsAdminRegistry收到web请求, 给tarsnode发送命令时, 通过node-name找到长连接, 并使用tars的push callback机制下发push命令
- tarsnode收到push通知处理完毕后, 调用sendPushResponse上报处理结果, 下发的命令和上报的处理结果通过一个requestId字段来对应
- tarsAdminRegistry收到sendPushResponse后, 通过requestId找到对应的请求, 然后找到web请求对应的current, 发送响应给web

中间增加tarsstub之后, 重点关注中间节点tarsstub的逻辑
- 下游的服务对每个上游的服务, 建立连接;
- 每个链接都对应一个adminPrx;
- 下游服务会定时在每个连接上调用reportNode上报心跳, 上游服务会记录node-name对应的链接, 下游服务以此来确认服务的有效性;
- 中间的tarsstub会记录所有调用过的reportNode的节点, 并在每个连接上定时上报给上游, 以此类推到tarsAdminRegistry;
- tarsAdminRegistry会记录连接上reportNode过的节点名称, 当下行消息时, 会根据节点名称获取一个连接, 并下发push命令;
- 当下游到中间tarsstub连接中断后, tarsstub会感知到, 并通知所有上游服务, 链接上节点已经失效, 上游服务会继续上报, 以此类推到tarsAdminRegistry;
- 当中间的tarsstub收到上游服务的推送消息时,需要将连接的上游的adminPrx的名称记录下来, 放在消息中推送到下游, 并记录映射关系表;
- 当下游的tarsnode/tarsstub上报结果时, 能根据关系表匹配当初的下行消息,找到这条消息上行时对应的adminPrx的名称, 并使用这个adminPrx继续上报, 直到上游的tarsAdminRegistry
 