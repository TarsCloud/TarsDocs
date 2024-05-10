
# 企业版本说明

## 主要功能说明

针对开源版本存在的多项问题，我们推出了企业版本，企业版本对于核心的框架服务几乎重写, 实现如下目标:

- 更合理的架构设计
- 更高的可靠性
- 更优的可管理性

为了更好的长久维护Tars框架, 使用企业版本框架我们会收取一定的费用, 如果有需要请和管理员(ruanshudong@qq.com)联系!

### 高可用性

实现了raft机制, 针对核心有状态服务的实现了高可用的容灾机制, 部署时采用2n+1台机器来完成, 包括:
- tarsregistry
- tarspatch
- tarscollect

从而去掉了对mysql的依赖, 框架存储数据分两部分:
- 核心的元数据信息, 主要包括服务的基本信息等, 这部分核心数据, tarsregistry采用了raft机制实现了2n+1的容灾机制, 自带了数据存储功能;
- 各类上报的监控数据, 比如调用统计, 属性上报, 异常上报等, 企业版新增了tarscollect服务来存储, 基于raft实现并实现了2n+1的容灾机制;


### 命令行控制终端

实现了全面统一的命令行控制终端tarsctl, 框架即可以通过tarsweb来操控框架, 也可以通过tarsctl命令行命令来操控框架;

同时tarsctl/web只需要访问tarsAdminRegistry即可, 相比于开源版本(web需要访问整个database), 减少了系统的复杂度, 提高了系统的安全性.

### 业务一主多备机制

框架支持了业务服务的一主多备机制, 只有主节点服务对外提供服务, 即任何业务服务可以部署多节点, 且其中一个节点为主其他节点为备, 当主机挂了后备机会自动抢主

对于某些业务, 需要有冷备服务的节点, 可以启用这个功能.

### 多机房外网通信机制

框架支持了多数据中心管理, 即一套框架可以管理多个机房的tarsnode, 少量机器可以外网即可, 同时机房间的业务服务可以无缝调用

### 监控数据统一化

框架收集的各类数据统一到tarscollect中, 并通过tarsweb/tarsctl可以查看这些数据, 包括以下数据:
>- 接口调用数据;
>- 属性上报数据;
>- 节点时间对比数据;
>- 调用链数据;
>- 节点硬盘大小;
>- 节点CPU数据;
>- 节点内存大小;

相比于开源版(数据缓存在es和mysql中), 数据统一到tarscollect中, 降低了数据的复杂度, 提高了数据的可靠性.

### 单机器多tarsnode机制

单机器, 多节点的部署能力, 即一台机器上可以部署多个节点

相对于开源版(tarsnode一个节点只能部署一个), 可以使得部署更加灵活.

### 第三服务管理机制

可以通过TARS框架管理其他非TARS服务的启停, 在web上配置好启停的脚本即可支持.

### IPLIST更新实时性

实现了tarsregistry中核心源数据的实时同步机制, 所有的tarsnode都能实时同步核心数据, 业务服务可以locator可以指定本机的tarsnode, 可以将ip list的更新时间降低至秒级.

增加这个功能后, 可以降低tarsregistry的压力, 也可以提高ip list的更新实时性, 业务服务可以在秒级内感知到ip list的变化.

### TarsPython的支持

实现了tars-python库, 底层基于c++实现, 上层封装的python来实现, 由于stdc++和python本身的兼容性, 需要针对gcc版本和python独立编译版本!

## 框架组件说明

tars核心组件服务包括以下服务:
- tarsnode: 节点管理, 在每台机器上都需要部署tarsnode, 部署框架时会自动部署tarsnode
- tarsAdminRegistry: 框架管理服务, 对外提供api, tarsweb/tarsctl都通过该服务和框架交互
- tarsregistry: 主控服务, 主要提供名字发现能力以及框架核心数据的存储服务, 基于raft实现, 至少需要2n+1台机器
- tarspatch: 发布服务, 自带存储, 所有发布包都会在这个服务中存储, 基于raft实现, 至少2n+1台机器
- tarsconfig: 配置服务, 管理业务服务的配置
- tarsnotify: 异常节点收集服务, 框架产生的异常会上报到这个服务, 这个服务会写入到tarscollect中, 业务服务也可以上报自己的异常
- tarslog: 远程日志服务, 业务服务可以打印按天日志, 会自动写入汇总到该服务, 该服务通常只部署一个节点
- tarsstat: 调用统计上报服务, rpc产生的调用统计信息会上报到这个服务, 这个服务会写入到tarscollect中
- tarsproperty: 属性上报服务, 业务服务可以上报自己的属性, 这个服务会写入到tarscollect中
- tarscollect: 统计数据收集服务, 基于raft实现, 至少2n+1台机器

相比开源版去掉了对数据库mysql的依赖, 并去掉了tarsquerystat/tarsqueryproperty, 增加了tarscollect服务.