# 协程说明

**tarscpp 3.x全面启用对协程的支持!**

服务框架全面融合协程, 使得使用者可以方便的使用协程, 本文主要介绍如何使用协程.

关于协程, 你需要重点的了解以下几点概念:
- 目前基础库中协程的调度和切换使用的boost库中的源码实现(几个汇编文件)
- 协程本质上一种轻量的线程, 简单得理解你可以在线程中调度协程, 即启动/休眠/唤醒等
- 目前不支持跨线程调度协程, 即每个线程都有一个协程调度器, 通过该对象来调度该线程中的协程

# 框架和协程相关的类

* TC_CoroutineInfo, 协程信息类, 正常情况下业务代码不需要感知该对象
* TC_Coroutine, 协程类, 继承于线程类(TC_Thread), 用来给业务快速使用协程, 这个类主要在一个线程中启动一批协程
* TC_CoroutineScheduler, 协程调度器类, 负责管理和调度协程, 本质上就是管理和调度TC_CoroutineInfo
>- 如果希望自己创建和调度协程, 需要重点理解TC_CoroutineScheduler
>- 每个线程都有唯一的TC_CoroutineScheduler, 可以通过: TC_CoroutineScheduler::create/scheduler获取掉调度器, 从而自己控制协程创建等逻辑

* TC_Thread, 线程类增加了协程的支持
>- 使用它的startCoroutine方法启动以后, TC_Thread::run则在协程运行
>- 可以run中使用TC_CoroutineScheduler来自己实现协程的创建等逻辑

* TC_CoroutineQueue, 用于跨线程的协程的数据交互, 队列没有数据时, 协程会阻塞在epoller上, 当有网络事件或者其他协程调度时会被唤醒处理其他事件!


# 协程启动的方式

通常在业务服务中, 不需要自己去启动协程, 只需要根据服务模型配置服务模型即可, 服务框架会根据配置, 自动将服务的线程变成协程, 即处理业务代码在协程中执行, 此时发起rpc调用即使是同步方式, 会变成协程模式, 从而变成异步模式, 具体后续文档中会介绍.

当然如果你想自己控制启动协程, 可以参考以下方式.

## 线程转协程

如果你处于任何一个线程中, 你希望在当前线程能调度协程, 你可以参考以下示例代码
```c++
auto scheduler = TC_CoroutineScheduler::create();
scheduler->setPoolStackSize(10, 128*1024);
scheduler->go([&]()
{
    scheduler->setNoCoroutineCallback([=](TC_CoroutineScheduler* s)
    {
        s->terminate();
    });

    cout << "incoroutine" << endl;
});

scheduler->run();
```

说明:
- 理论上任何线程都能调度协程, 这时候你需要做的是创建协程调度器TC_CoroutineScheduler, 然后用协程调度器创建和调度协程
- 每个协程都要消耗一定的空间, 你可以用setPoolStackSize来控制线程中协程的个数以及每个协程消耗的内存大小, 这个内存大小指的协程的栈空间, 因此你使用协程的时候请注意不要在栈上分配太多内存
- setPoolStackSize需要在run之前执行
- 协程如果要被调度, 比如运行: scheduler->run(), 这是一个阻塞函数, 会阻塞当前线程, 同时会调度创建出来的协程
- 通常情况下, run不会返回, 但是如果希望结束调度, 可以调用terminate来结束协程的调度
- run函数会阻塞住, 注意调度器其实阻塞在epoller上, 因此TC_CoroutineScheduler可以通过getEpoller方法拿到epoller指针
- 原则上该epoller对象可以用于网络通信, 而框架上也是这样实现的, 因此协程调度和网络收发可以通过同一个epoller来调度
- 正是因为统一了epoller对象, 框架上能做到rpc调用和服务端线程, 完全在一个线程中调度(通过协程来调度唤醒), 从而网络收发是异步的且不切换线程, 一定程度降低了通信的延时
- setNoCoroutineCallback表示如果协程调度器发现没有任何协程可以调度时执行的函数, 上面示例中是调用了terminate方法结束了协程调度

## TC_Thread

自己创建调度器, 控制调度毕竟还是麻烦, 因此提供了扩展了TC_Thread是的更加方便.

TC_Thread在9.x以前版本中, 代表的线程, 当你需要实现线程的时候, 可以继承TC_Thread, 然后实现run方法, 调用start即可启动一个线程.

在9.x版本中, 你可以同样的方式继承TC_Thread, 但是调用startCoroutine方法启动协程, 即此时run方法处于协程中, 比如以下示例代码:

```c++

class CoThread : public TC_Thread
{
public:
	CoThread() {}

	virtual void run()
	{
		cout << "in coroutine" << endl;	
	}
};

//创建了一个线程
CoThread* a = new CoThread();

//以协程方式启动的
a->startCoroutine(10, 128*1024, true);

//等待线程结束
a->join();

delete a;
```

说明:
- startCoroutine的前两个参数是控制该线程中, 协程池的个数和栈大小, 第三个参数是表示: 如果没有活跃的协程可调度时, 线程是否自动退出(如果为false, 即使没有协程了, 也不会退出).
- 此时run本质在协程中调度起来的, 因此你在run中可以调度更多的协程

进一步, 在协程中启动更多的协程:
```c++

class CoThread1 : public TC_Thread
{
public:
	CoThread1() {}

	virtual void run()
	{
        //使用调度器以协程方式启动其他协程
		TC_CoroutineScheduler::scheduler()->go(std::bind(&CoThread1::doCo, this));
	}

	void doCo()
	{
		CoThreadDo = true;
	}
};

CoThreadDo = false;

CoThread1* a = new CoThread1();

a->startCoroutine(10, 128*1024, true);

a->getThreadControl().join();

ASSERT_TRUE(CoThreadDo);

delete a;

```

说明:
- 在run中可以拿到调度器, 以协程方式启动其他协程
- 注意协程调度器对于本线程来说是唯一的, 就是在同一个线程中地方都可以拿到当前线程的调度器
- TC_CoroutineScheduler提供了sleep, yield, put等函数, 可以用来调度协程
- 每个线程都有一个id, 这个id在本线程中协程是唯一的, 跨线程的协程id不唯一
- TC_CoroutineScheduler每个线程都有自己独立的对象, 通过create创建, 通过scheduler获取
- 这个例子中由于启动的startCoroutine, 因此TC_CoroutineScheduler已经被创建出来了, 在run中只需要使用即可

## TC_Coroutine

这个类可以一次创建多个协程出来, 拥有TC_Thread之后, 这个类其实用得相对较少, 示例代码如下:

```c++

class MyCoroutine : public TC_Coroutine
{
protected:
	void handle()
	{
		++_count;

		this->go(std::bind(&MyCoroutine::co_test, this));
	}

	void co_test()
	{
		++_count;
	}

public:
	static atomic<int> _count;
};

atomic<int> MyCoroutine::_count{0};

MyCoroutine::_count = 0;

MyCoroutine co;

co.setCoroInfo(10, 200, 128*1024);

co.start();

co.join();

```

说明:
- 业务可以继承这个类并实现handle方法
- handle会被调用10次, 即创建了多个协程则被调用多少次

# 服务模型和协程

服务模型扩展成四种, 同时服务模型下层到tc_epoll_server中, 即最底层的服务也能设置协程模型, 示例可以参考源码中: unittest/test_tc_epoller_server.cpp, 四个服务模型中和协程相关的重点是:
- NET_THREAD_QUEUE_HANDLES_CO
- NET_THREAD_MERGE_HANDLES_THREAD
- NET_THREAD_MERGE_HANDLES_CO

说明:
- 这三种模型, 实际处理业务都在协程中执行, 即协程调度器已经都创建了
- NET_THREAD_QUEUE_HANDLES_CO & NET_THREAD_MERGE_HANDLES_CO 两种模式下, 服务端业务处理线程处于协程状态, 这一点非常重要, 可以和rpc的通信器配合, 后续会介绍到
- NET_THREAD_MERGE_HANDLES_THREAD这种模式, 虽然服务器已经处于协程中了, 但是业务线程不是默认感知到, 没有设置ServantProxyThreadData::getData()->_sched


对于底层的epollserver, 可以通过api控制服务的模型和协程参数:
```c++
//设置服务模型
TC_EpollServer::setOpenCoroutine

//设置协程池梳理和栈大小
TC_EpollServer::setCoroutineStack
```

对于rpc服务而言, 可以通过参数控制模型, 修改模板即可
```xml
<taf>
    <application>
        <server>
            coroutinememsize=1073741824
            coroutinestack=131072
            opencoroutine=0
        </server>
    </application>
</taf>
```
注意: 
- opencoroutine得值从0~3, 分别对应TC_EpollServer::SERVER_OPEN_COROUTINE得值
- coroutinememsize表示协程池总共可以分配的协程栈的内存
- coroutinestack每个协程分配的栈空间大小
# 通信器和协程

通信器是rpc调用的客户端资源集合体, 理解它的模型是非常关键的.

## 普通的通信器模型

9.x以前的版本我们称之为普通的通信器模型, 具体说明如下:

- 通信器内部其实有独立的网络线程(可能多个), 网络线程主要就负责网络数据的收发.
- 业务线程发送数据时, 会唤醒网络线程去发送
>- 如果是同步调用模式, 业务线程会线程锁阻塞, 当网络线程收到回包以后, 会通过锁唤醒业务线程
>- 如果是异步调用模式, 业务线程会继续执行, 当网络线程收到回包丢给异步callback线程(该线程个数也可以配置), 然后在异步回调线程中执行回调
- 以上流程中, 可以看到当发送数据时, 会有多次线程切换, 如果加上服务器端的线程切换, 一次rpc会至少会涉及到4次线程的切换.

## 协程模式下的通信器

协程的版本诞生可以减少线程的切换调度, 降低rpc的延时, 协程模式的网络通信器确实很复杂很多, 我们来看一下协程模式下通信器的设计:
- 普通通信器网络线程其实是独立的, 和业务线程没有任何关系, 但是协程模式下, 如果业务线程是协程且开启了协程网络通信器, 那么每个业务线程中都有自己独立的网络通信器;
- 此时网络通信器不再自带网络线程, 而是直接使用了业务线程来收发网络数据, 即网络通信器的网络epoller直接使用了业务线程中协程调度器的epoller, 这样将协程调度和网络调度融合和统一了!

这种模式下具体的rpc逻辑如下:
- 同步调用
>- 业务线程将数据放入队列中以后, 直接调用网络通信器发包(当然第一次会自动创建网路通信器), 同时业务线程的协程调度器的底层epoller对象会传递给网路通信器
>- 如果响应包没有回来, 则yield(注意业务线程处于协程中, yield即表示自身放弃调度)
>- 当网络回包回来以后, epoller会感知到, 接收数据分析数据是哪个协程发送的, 然后put(coroId), 唤醒协程, 从而之前业务线程会在yield中醒过来
>- 整个过程, 对于客户端而言, 全部在业务线程中执行

- 异步调用
>- 异步调用的模式和同步调用模式几乎一样, 只是发起rpc的协程, 发送包以后并不yield, 而是继续执行

### 如何开启协程模式的通信器

并不是只要业务线程启用了协程, 就默认开启了协程模式的通信器, 还需要做以下处理:

```c++
ServantProxyThreadData::getData()->_sched = TC_CoroutineScheduler::scheduler();
```

只有这样设置以后, 通信器才会感知到业务处于协程模式, 且启用协程网络通信器.

**注意老版本协程模式相当于 opencoroutine=1 **


NET_THREAD_QUEUE_HANDLES_CO & NET_THREAD_MERGE_HANDLES_CO 两种模式下, 服务端的业务处理线程, 默认就已经设置了, 这样在服务器业务线程中发起rpc时, 本质上是协程模式, 网络收发都在业务线程中处理的!

### 协程模式下有哪些影响

- 业务线程必须开启协程(即存全局的协程调度器, 同时设置了 ServantProxyThreadData::getData()->_sched调度为线程全局调度器), 这样通信器才能感知到协程模式;
- 由于每个业务线程都有自己的网路通信器, 原则上, 内存消耗会加大(当然这点内存可以忽略不计);
- 每个网络通信器都会自己独立的连接, 这样会带来一个prx背后对应了多条服务的连接, 加大了服务的连接数, 当然服务现在都是epoller实现, 这几乎不是一个问题;
- 协程模式, 每个协程有自己的栈空间, 因此会加大内存消耗!

