# 介绍

## 服务说明

实现以下几种功能:
- 自增计数, 每次调用自增计数
- 指定范围循环自增计数, 每次调用自增计数, 当到最大值则从最小值开始重新计数
- 唯一随机字符串, 可以设置字符串过期时间

基于 raft 实现的数据存储服务, 能实现 2n+1 台节点的数据一致性存储.

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
        #起始计数
        start-count = 100000
        storage-path=/count-data
      </root>
```

## 部署说明

部署主要关注几点:

- 需要至少部署(2n+1)台节点, 至少三台节点, 少于三台节点无法正常工作, 其中有一台是 leader, 其他是 follower 节点
- 其中最多可以有 2n-1 台节点 down 机, 集群也不会影响, 仍然能正常工作
- 当在 TARS 框架上增加节点时, 请注意一台一台增加, 不要同时增加多台, 观察日志节点正常后才能增加下一台
- 增加新节点会自动同步数据, 原则上不需要做任何处理
- 数据目录在配置文件中指定, 如果是 docker 部署注意映射宿主机目录
- 如果使用 K8SFramework 机制部署时, 请注意要开启 LocalPV 支持, 且增加 mount 路径(mount 数据目录)

## 使用说明

- 使用 c++, 底层数据存储用 rockesdb
- 拿到 tars 协议文件, 通过 CountPrx 即可完成服务的调用
- 计数接口`count`/`circleCount`数据接口最终都转发到 leader 来执行写处理
- 查询数据接口可以指定是否在 leader 执行, 如果不指定 leader, 不保证写入的数据马上能查到, 因为数据同步有一定延时
- 如果查询每次指定 leader 会影响效率, 但是如果不指定 leader 会有数据延迟(通常都是毫秒级别)
- 可以在 tarsweb 上, 发送命令查看和修改表的数据, 方便调试
  > - count.get sBusinessName sKey
  > - count.set sBusinessName sKey value default
  > - count.circle sBusinessName sKey value min max 

## 服务说明

服务有两个 servant:

- RaftObj: raft 端口接口
- CountObj: 业务服务模块

使用者调用 CountObj 接口即可, RaftObj 是提供给 raft 协议协商使用.

读接口都带有 leader 参数, 表示是否一定要从 leader 读取数据, 对于实时性以及数据准确性要求不那么高的请求, 可以设置 leader 为 false, 这样能提高整体集群的通信效率.
