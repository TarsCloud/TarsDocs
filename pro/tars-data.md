##  框架数据说明

### 企业版中的数据构成

企业版本中, 不同组件会产生不用的组件, 包括如下数据:

- tarsregistry

tarsregistry主控服务是基于raft实现的, 内部记录了所有框架上部署的服务的元数据信息, 数据目录为:

```shell
$install-path/tarsnode/data/tars.tarsregistry/data
```

当tarsregistry扩容时, 数据会自动迁移扩容到新的节点中.

- tarspatch

tarspatch发布服务是基于raft实现的, 保存了所有的发布包, 数据目录为:
```shell
$install-path/tarsnode/data/tars.tarspatch/data
```

当tarspatch扩容时, 数据会自动迁移扩容到新的节点中.

当在管理平台删除发布记录时, 也会删除tarspatch中对应的记录.

- tarscollect
  tarscollect发布服务是基于raft实现的, 保存了所有采集的监控数据 

tarscollect的模板中, 可以修改每类数据的存储时间, 过期的数据会被删除:
```xml
  <collect>
    # tarsstat采集的调用数据保留时间(天)
    stat_retain_day=31 
    # tarspropery采集的属性上报数据保留时间(天)
    prop_retain_day=31
    # tarsnotify 采集的异常上报数据的保留条数(每个服务)
    notify_retain_count=1000
    # cpu负载数据保留时间(天)
    load_cpu_retain_day=31
    # 每节点内存数据保留时间(天)
    load_mem_retain_day=31
    # 每节点磁盘空间数据保留时间(天)
    load_disk_retain_day=31
    # 每节点机器时间和tarscollect leader节点时间的差异值, 保存时间
    load_time_diff_retain_day=31
    # 调用链数据的保存时间(天)
    trace_retain_day=7
  </collect>
```

- tarsnode

对于tarsnode而言, 会定时删除本节点上业务服务产生的日志文件, 通过在模板中配置保留时间:
```shell
  #日志文件保留天数
    retainDayLog=7
  #产生的core文件保留天数
    retainDayCore=7
```

### 监控数据说明

在开源版本中, tars的统计监控数据分散在mysql, es中, 为了更好的管理相关的数据, 企业版本tars框架将所有统计监控数据都存在了tarscollect组件中, 该组件基于raft实现了高可用.

收集的数据如下:
- 接口调用的数据
- 属性上报数据
- 节点时间对比数据
- 硬盘大小监控
- CPU监控
- 内存监控
- 调用链数

#### 接口调用数据结构

采用5个索引表+一个数据表的构成, 每个表对应rocksdb中的一个column, 每条数据会计算一个唯一值uniqueId:  md5(masterName + "|" + slaveName + "|" + interfaceName + "|" + masterIp + "|" + slaveIp)
数据表:
- stat_data, key为: 分钟时间.uniqueId, value为上报每条数据(CollectProp)

索引表:
- stat_masterName, key为 masterName@分钟时间.uniqueId
- stat_slaveName, key为 slaveName@分钟时间.uniqueId
- stat_interfaceName, key为 interfaceName@分钟时间.uniqueId
- stat_masterIp, key为 masterIp@分钟时间.uniqueId
- stat_slaveIp, key为 slaveIp@分钟时间.uniqueId

检索时:
- 根据时间和输入的参数, 分别检索索引表, 解析出每张表的key(分钟时间.uniqueId)做交集
- 然后根据keys, 去数据表中检索数据
- 再根据groupby字段做groupby, 得到最终的数据

如何设计分布式
- 每三台节点一个集群
- 按照时间+uniqueId, 分布式写入到不同的集群中
- 查询时, 每个集群都查找一遍keys, 然后合并keys
- 根据合并的keys查找, 去集群查找数据, 然后再合并数据
- 再根据合并的数据过groupby, 得到最终的结果

#### 属性上报数据结构
采用4个索引表+一个数据表的构成, 每个表对应rocksdb中的一个column, 每条数据会计算一个唯一值uniqueId:  md5(moduleName + "|" + propertyName + "|" + nodeName + "|" + policy)
数据表:
- prop_data, key为: 分钟时间.uniqueId, value为上报每条数据(CollectProp)

索引表:
- prop_moduleName, key为 moduleName@分钟时间.uniqueId
- prop_propertyName, key为 propertyName@分钟时间.uniqueId
- prop_nodeName, key为 nodeName@分钟时间.uniqueId
- prop_policy, key为 policy@分钟时间.uniqueId

检索时:
- 根据时间和输入的参数, 分别检索索引表, 解析出每张表的key(分钟时间.uniqueId)做交集
- 然后根据keys, 去数据表中检索数据
- 再根据groupby字段做groupby, 得到最终的数据

#### notify上报数据结构

根据 时间(可选), application, server_name, node_name(可选) 检索
- 根据application获取最新的 或者 指定时间范围的
- 根据application, server_name获取最新的 或者 指定时间范围的
- 根据application, server_name, node_name获取最新的 或者 指定时间范围的
数据表:
- notify_data, key为: appliedIndex, value为上报的数据

索引表:
- notify_application, key为 application@时间_appliedIndex
- notify_serverName, key为 application.server_name@时间_appliedIndex
- notify_nodeName, key为 application.server_name.node_name@时间_appliedIndex

检索时:
- 根据输入参数, 检索对应的索引表, 解析得到appliedIndex
- 根据appliedIndex, 去数据表中检索数据

### tarsnode上报数据
tarsnode 每隔5分钟会上报一次本机的数据, 包括以下数据:
- 本机的时间(微妙), 用于云端检查机器的时间的一致性
- 本机的内存使用情况
- 本机的cpu负载情况
- 本机的硬盘负载情况

### 节点时间对比数据

根据节点, 时间范围检索
- node_time_diff: key: 节点@时间(换成%Y%m%d%H%M%S), value: TimeDiff

### 硬盘大小监控

数据表:
- node_disk_device: 节点设备表, key: 节点@deviceName
- node_disk_info: 设备数据大小, key: 节点@deviceName@时间(换成%Y%m%d%H%M%S), value: FilesystemInfo

### CPU监控

数据表:
- node_cpu_info, cpu负载表, key: 节点@时间(换成%Y%m%d%H%M%S), value: CpuLoad

### 内存监控
数据表:
- node_mem_info: 内存负载表: key: 节点@时间(换成%Y%m%d%H%M%S), value: MemInfo 


### 调用链数据说明

tarscollect也收集了调用链相关的数据, 这部分数据结构相对比较复杂, 这里单独重点介绍:

- 日志记录(每条上报日志一条记录)

日志结构体:
>- trace: traceId, 每个调用链都有唯一的id 
>- span: 每次调用都有一个span, 每次调用都有唯一的span
>- parent: 父span, 用来关联调用的关系
>- master: 主调
>- slave: 被调
>- function: 接口名
>- time: 时间(毫秒)
>- type:  类型: cs(客户端发送), cr(客户端接收), ss(服务端发送), sr(服务端接收), ts(调用链开始), te(调用链结束)
>- ret: 返回值
>- data: 调用参数, json格式

存储结构:
- key: 日期(%Y%m%d) + traceId
- value: 日志结构体

- trace记录(一次完整的调用链归集)

- trace结构体
>- trace: traceId, 每个调用链都有唯一的id
>- tSpan: 调用链第一次触发时的span
>- tMaster: 调用链第一次触发时的主调
>- tsTime: 调用链开始的时间(毫秒)
>- teTime: 调用链结束的时间(毫秒)
>- sHash: 调用链中, 所有调用服务名称取hash, 用来归集服务图
>- fHash: 调用链中, 所有调用的函数名称取hash, 用来归集函数图
>- spans: 数组, 记录了本次调用链中所有的调用
>>- span
>>- parent
>>- master
>>- slave
>>- csTime
>>- srTime
>>- ssTime
>>- crTime
>>- csData
>>- srData
>>- ssData
>>- crData
>>- ret
>>- children: 子调用的span数组

存储结构:
- key: 日期(%Y%m%d) + traceId
- value: trace结构体

- Graph记录(一种调用链归集成一张图)

- graph结构体
>- graphId: 图的id, 图的关键字
>- type: server/function, 两种图, 一种是服务图, 一种是函数图
>- vertexes: 顶点数组, 代表调用者
>>- vertex: 顶点名称, 如果是服务图则为: application.server_name, 如果是函数图: application.server_name.doHello
>>- callCount: 调用次数
>>- callTime: 消耗时间
>- edges: 边, 代表调用信息
>>- fromVertex: 主调用名称, 如果是服务图则为: application.server_name, 如果是函数图: application.server_name.doHello
>>- toVertex: 被调用名称, 如果是服务图则为: application.server_name, 如果是函数图: application.server_name.doHello
>>- callCount: 调用次数
>>- callTime: 调用时间
>>- order

存储结构
- key: graphId
- value: graph结构体

- 其他索引表

除开关键的数据表以外, 还有几张检索的索引的表:
- function表, key: 服务+日期+接口
- traceId表, key: 服务+时间+traceId
- graphId表, key: 服务/接口+日期+graphId

- 对外暴露的接口

- listFunction: 根据日期(%Y%m%d), 服务, 查询这个服务的所有接口
- listTraceSummary: 根据日期(%Y%m%d), 开始时间, 结束时间, 服务, 查询这个范围内所有的 调用链id信息(调用链id, 开始时间, 结束时间)
- graphFunction: 根据日期(%Y%m%d), 接口名称, 查询服务接口调用链图
- graphServer: 根据时间, 服务名, 查询服务调用链图
- graphTrace: 根据日期(%Y%m%d), 调用链id查询, 查询具体某次调用链图
