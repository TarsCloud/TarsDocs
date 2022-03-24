# Future/Promise 使用

在采用 tars2cpp 工具自动生成 c++文件时，相应的 file.tars 会自动生成 file.h 文件。在.h 文件里会生成你自定义接口的 RPC 方法，一共有四种：

- 同步 sync 方法；
- 异步 async 方法；
- Future/Promise 方法；
- 协程 coco 方法；

sync/async 方法在文档里都有[使用的样例](../../rumen/hello-world-1/tarscpp-kuai-su-ru-men.md)，对于不满足 sync/async，然后想在 Tars 下使用 Future/Promise 的同学看看此文或许会有帮助。

文章内容、样例都是基于 Tars 框架下提供的 Future/Promise 进行分析，与 boost、C++11、以及其他语言提供的 Future/Promise 不完全相同。

### **Future/Promise 是什么？**

Future 与 Promise 其实是二个完全不同的东西：

> Future：用来表示一个尚未有结果的对象，而产生这个结果的行为是异步操作；
>
> Promise：Future 对象可以使用 Promise 对象来创建（getFuture），创建后，Promise 对象保存的值可以被 Future 对象读取，同时将二个对象共享状态关联起来。可以认 Promise 为 Future 结果同步提供了一种手段；

简而言之就是：**他们提供了一套非阻塞并行操作的处理方案，当然，你也可以阻塞操作来等待 Future 的结果返回。**

### **Future/Promise 适用什么场景？**

通过一个虚拟的例子来说明：**你想买房，然后通过微信联系中介看看行情并询问一些信息，最后拿到所有的信息汇总后再评估。**

我们假如有中介 A、B、C，并且不考虑超时情况。

**同步的做法**：我们先微信询问 A，等待 A 的回复，接着询问 B，等待 B 的回复，最后询问 C，等待 C 的回复；

**异步的做法**：我们先微信询问 A，在等待 A 回复的同时，可以干干其他事情（比如看电视，处理工作），等到 A 回复后再依次询问 B，C；

**Future/Promise 的做法**同时给 A、B、C 发消息询问，等待回复的同时干其他事情，一直到所有回复都响应；

**根据经验，在这种场景下 Future/Promise 才是最合适的做法。**

因为对于这种场景，询问中介 A、B、C 是三个没有任何耦合的任务（简单理解就是顺序可以打乱的任务，相互之间无依赖，A-&gt;B-&gt;C，C-&gt;B-&gt;A 的结果一样），所使用 Future/Promise 的思想最适合。

### **Future/Promise 代码例子**

假设我们有一 Test 应用，他的 TestServer 内部 TestServant 提供了 Echo 服务的接口“EchoTest”。

```cpp
//省略了对应头文件
//回调函数
void handleAll(const promise::Future<promise::Tuple<promise::Future<TestServantPrxCallbackPromise::PromisetestPtr>,
                    promise::Future<TestServantPrxCallbackPromise::PromisetestPtr> > > &result)
{
    promise::Tuple<promise::Future<TestServantPrxCallbackPromise::PromisetestPtr>, promise::Future<TestServantPrxCallbackPromise::PromisetestPtr> > out = result.get();

    promise::Future<TestServantPrxCallbackPromise::PromisetestPtr> out1 = out.get<0>();
    const tars::TC_AutoPtr<TestServantPrxCallbackPromise::Promisetest> print1 = out1.get();

    promise::Future<TestServantPrxCallbackPromise::PromisetestPtr> out2 = out.get<1>();
    const tars::TC_AutoPtr<TestServantPrxCallbackPromise::Promisetest> print2 = out2.get();

    DEBUGLOG("handleAll:" << print1->_ret << "|" << print1->outStr << "|out2:" << print2->_ret << "|" << print2->outStr);
}

int main()
{
    map<string, string> ctx;
    TestServantPrx testPrx = Application::getCommunicator()->stringToProxy<TestServantPrx>("Test.TestServer.TestServant");

    promise::Future<TestServantPrxCallbackPromise::PromisetestPtr > result1 = testPrx->promise_async_EchoTest("manmanlu1", ctx);
    promise::Future<TestServantPrxCallbackPromise::PromisetestPtr > result2 = testPrx->promise_async_EchoTest("manmanlu2", ctx);

    promise::Future<promise::Tuple<promise::Future<TestServantPrxCallbackPromise::PromisetestPtr>, promise::Future<TestServantPrxCallbackPromise::PromisetestPtr> > > r =
        promise::whenAll(result1, result2);
    r.then(tars::TC_Bind(&handleAll));

    DEBUGLOG("Test Future-Promise done");
}
```

输出的结果为：

```text
Test Future-Promise done
handleAll:0|manmanlu1|out2:0|manmanlu2
```

可以看到异步回调正常触发。
