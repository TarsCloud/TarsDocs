# 3.x大版本说明

3.x版本是tarscpp在底层, 尤其是网络层做的一次重大版本升级, 重点支持了以下特性:

- 框架中网络层, 统一使用: TC_Transceiver, TC_Transceiver接口层面使用回调机制且默认支持ssl, 和tc_epoller配合, 可以快速开发任何网络模型
- 协程处理提炼到公共类中, 可以参考文件: tc_coroutine.h 
- tc_epoller完善接口, 网络和协程调度都通过tc_epoller统一调度
- 服务模型变更为4四种模型
- tc_http_async支持了ssl
- 支持了无损发布
## 网络层说明

- TC_Transceiver: 3.x版本在网络层做了完整的统一, 客户端和服务端的网络收发都通过统一的类处理了: TC_Transceiver, 具体可以参考TC_Transceiver的头文件说明
- TC_Epoller: 完善了TC_Epoller的处理机制, 说明如下:
>- TC_Epoller本身继承于TC_TimerBase, 因此它本身也是一个定时器, 但是定时任务的执行都在epoller的loop循环中
>- TC_Epoller事件回调改成了注册监听回调机制, 通过子类EpollInfo类来注册事件的回调
>- 协程调度底层也是通过TC_Epoller来实现, 这样保证了协程/网络能统一调度, 这个是3.x版本最大的设计变更!!!

## 协程说明

- 协程类统一提炼到公共库中(tc_coroutine.h), 代码中涉及到协程的类主要以下几个:
>- TC_CoroutineInfo, 协程信息类, 正常情况下业务代码不需要感知该对象
>- TC_Coroutine, 协程类, 继承于线程类(TC_Thread), 用来给业务快速使用协程, 这个类主要在一个线程中启动一批协程
>- TC_CoroutineScheduler, 协程调度器类, 负责管理和调度协程, 本质上就是管理和调度TC_CoroutineInfo
>>- 如果希望自己创建和调度协程, 需要重点理解TC_CoroutineScheduler
>>- 每个线程都有唯一的TC_CoroutineScheduler, 可以通过: TC_CoroutineScheduler::create/scheduler获取掉调度器, 从而自己控制协程创建等逻辑
>- TC_Thread, 线程类增加了协程的支持
>>- 使用它的startCoroutine方法启动以后, TC_Thread::run则在协程运行
>>- 可以run中使用TC_CoroutineScheduler来自己实现协程的创建等逻辑
>- TC_CoroutineQueue, 用于跨线程的协程的数据交互, 队列没有数据时, 协程会阻塞在epoller上, 当有网络事件或者其他协程调度时会被唤醒处理其他事件!

## 服务模型说明

由于框架底层支持了协程, 因此服务模型也做了变更, 支持更多的服务模型, 目的是充分利用协程, 同时协程模型的优点时能降低延时. 四种服务模型说明如下:
- NET_THREAD_QUEUE_HANDLES_THREAD
    独立网路线程 + 独立handle线程: 网络线程负责收发包, 通过队列唤醒handle线程中处理, 这个和老版本是一样的服务模型
- NET_THREAD_QUEUE_HANDLES_CO
    独立网路线程 + 独立handle线程: 网络线程负责收发包, 通过队列唤醒handle线程中处理, handle线程中启动协程处理, 这个模型和老版本中启用协程后的模型相同
- NET_THREAD_MERGE_HANDLES_THREAD
    合并网路线程 + handle线程(线程个数以业务线程配置为准, 网络线程配置无效): 连接分配到不同线程中处理(如果是UDP, 则网络线程竞争接收包), 这种模式下延时最小, 相当于每个包的收发以及业务处理都在一个线程中
- NET_THREAD_MERGE_HANDLES_CO
    合并网路线程 + handle线程(线程个数以处理线程配置为准, 网络线程配置无效): 连接分配到不同线程中处理(如果是UDP, 则网络线程竞争接收包), 每个包会启动协程来处理

## 关于协程网络的混合调度问题

3.x版本一个重大的改进就是将协程的调度阻塞在epoller上, 通过唤醒epoller来完成协程的调度, 这个逻辑和网络层的事件唤醒保持了一致, 这样做得好处是网络收发等事件可以和协程事件混合在一起调度.

这样最直接的影响是: 客户端网络线程可以和服务端的处理线程混合在同一个线程中处理, 在3.x的rpc调用过程中, 如果服务模型是: NET_THREAD_QUEUE_HANDLES_CO or NET_THREAD_MERGE_HANDLES_CO情况下, 服务端收到网络请求后, 发起rpc到rpc回调, 都在同一个服务端的逻辑线程中处理的, 从而大幅度减小了线程切换, 降低了rpc的延时!