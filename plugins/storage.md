## 服务说明

基于 raft 实现的数据存储服务, 能实现 2n+1 台节点的数据一致性存储.

服务有两个 servant:

- RaftObj: raft 端口接口
- StorageObj: 业务服务模块

使用者调用StorageObj 接口即可, RaftObj是提供给raft协议协商使用.

它的单机读写速度大概是mysql的十倍, 且能保持多机数据一致性, 当你这类需求时可以采用这个服务来存储数据.

同时它也提供给[web管理平台](./storage-web.md), 来快速访问数据
## 配置文件

服务的配置文件格式如下:
```xml
      <root>
        <raft>
            #选举超时时间(毫秒) 
            electionTimeoutMilliseconds = 3000
            #leader和从机间心跳时间(毫秒)
            heartbeatPeriodMilliseconds = 300
            #数据制作快照的时间(毫秒)
            snapshotPeriodSeconds       = 6000
            #同步数据时, 每个请求最大的日志条数
            maxLogEntriesPerRequest     = 100
            #同步数据时, 内存队列最大的数据条数
            maxLogEntriesMemQueue       = 3000
            #同部数据时, 正在传输的数据最大条数
            maxLogEntriesTransfering    = 1000
        </raft>
        #数据数据的路径
        storage-path=/storage-data
      </root>
```

## 部署说明

部署主要关注几点:
- 需要至少部署(2n+1)台节点, 至少三台节点, 少于三台节点无法正常工作, 其中有一台是leader, 其他是follower节点
- 其中最多可以有2n-1台节点down机, 集群也不会影响, 仍然能正常工作
- 当在TARS框架上增加节点时, 请注意一台一台增加, 不要同时增加多台, 观察日志节点正常后才能增加下一台
- 增加新节点会自动同步数据, 原则上不需要做任何处理
- 数据目录在配置文件中指定, 如果是docker部署注意映射宿主机目录
- 如果使用K8SFramework机制部署时, 请注意要开启LocalPV支持, 且增加mount路径(mount数据目录)

## 使用说明

- 使用c++, 底层数据存储用rockesdb, 实现mkey+ukey+data的存储方式
- 拿到tars协议文件, 通过StoragePrx即可完成服务的调用
- 支持两种存储方式, 一种是map类table存储格式, 一类是队列模式

### table模式
- mkey是主key, ukey是子key, data是实际数据, 使用vector<byte>, 因此可以存放二进制数据
- 支持多张表, 使用时需要调用createTable来创建表, createTable可以重复调用, 比如在服务每次启动时调用
- 注意mkey/ukey只能使用字符串(不能用二进制数据!!!), 且在存储是按照字符串从小到大排序的, 因此遍历时可以按照顺序遍历
- 支持set/get/del以及对应的批量操作
- 写数据接口最终都转发到leader来执行写处理
- 查询数据接口可以指定是否在leader执行, 如果不指定leader, 不保证写入的数据马上能查到, 因为数据同步有一定延时
- 如果查询每次指定leader会影响效率, 但是如果不指定leader会有数据延迟(通常都是毫秒级别) 
- 支持数据version版本一致性, 即写入数据时, 将读取到数据的version写回, 如果数据已经修改(存储的version变了), 则不予许写入
- 支持基于时间戳的覆盖(只能更大的时间戳覆盖小时间错的数据)
- 支持超时自动淘汰
- 在已知mkey的情况下, 可以根据ukey来遍历数据
- 如果数据格式json格式下, 即存储数据是一个json的object, 则支持更多的针对字段的操作, 注意字段一般只支持: number/string/bool/array
- 针对json的字段操作, 请查看tars协议文件
- 可以在tarsweb上, 发送命令查看和修改表的数据, 方便调试
 >- storage.get table mkey ukey
 >- storage.set table mkey ukey value
 >- storage.del table mkey ukey value

### queue模式
- 队列模式, 接口相对简单, 可以push和pop
- 根据数据在队列中的索引, 指定读取/写入/删除某一条数据
- 数据放入队列中, 也可以指定超时时间, 一旦超时则获取不到该条数据了

## 性能说明

- 因为raft协议几乎运行在一个线程上, 无法充分利用多核CPU 
- 存储的性能, buffer较小的情况下(<1k), 3机的存储集群, 大概能支持1w/s的写请求
- 如果读请求不经过leader, 三台机器的集群能支持10w/s的tps

## 未来计划支持的

- 目前一类数据只能在一套(2n+1)台节点的服务器上, 后续计划支持数据的自动分裂