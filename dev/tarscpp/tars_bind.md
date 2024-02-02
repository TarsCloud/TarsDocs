

## 背景

有一些服务在初始化过程中需要初始化大量数据, 这个时候我们希望服务不要绑定端口对外提供服务, 而是等初始化完毕后, 在绑定端口提供服务, 我们就需要有这个手工绑定的功能.

## 使用方式

我们在服务的`initialize`中执行:

```c++
//注意tarscpp版本,老版本可能没有这个函数
getBindAdapter(getServerBaseInfo().Application + "." + getServerBaseInfo().ServerName +".HelloObj")->enableManualListen();

//如果3.0.16之前的版本, 只能在模板中的<server>域内配置, manualListen = 1 来实现

```

然后你可以放心初始化的数据, 服务不会绑定业务端口, 当你的数据初始化完毕后, 你可以调用:
```c++
getBindAdapter(getServerBaseInfo().Application + "." + getServerBaseInfo().ServerName +".HelloObj")->manualListen();

```

从而完成端口的主动绑定.

