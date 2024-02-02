

## 背景

目前的服务端模型中, 默认情况下, 请求过来时, 服务端业务处理线程谁空闲谁来处理当前请求, 因此同一个链接的请求可能被任何一个线程处理.

但是在某些场合下, 我们希望同一个链接的请求都被同一个线程执行, 从而保证连接上请求的有序性, 我们可以启用队列模式来实现这个能力.

## 使用方式

我们在服务的`initialize`中执行:

```c++
//注意tarscpp版本,老版本可能没有这个函数
getBindAdapter(ServerConfig::Application + "." + ServerConfig::ServerName +".HelloObj")->enableQueueMode();

//如果3.0.15之前的版本, 可以如下:
getEpollServer()->getBindAdapter(ServerConfig::Application + "." + ServerConfig::ServerName +".HelloObjAdapter")->enableQueueMode();

```

总之, 你需要获取到服务的BindAdapter, 然后通过bindAdapter来启用队列模式.

执行后, 队列模式则被启用.

## 注意

- 如果服务模型是: NET_THREAD_MERGE_HANDLES_CO or NET_THREAD_MERGE_HANDLES_THREAD, 这两种模式下, 网络线程和业务处理线程其实是一个, 这时候默认就启用了队列模式
