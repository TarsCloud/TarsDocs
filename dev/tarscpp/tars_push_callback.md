
相对于普通的push, 使用者需要自己编解码协议, 本文介绍tarscpp中另外一种push callback的方式, 这种方式可以大幅度降低服务间push的开发难度.

## 背景
当服务A和服务B间有交互,  当A主动发起连接B, 此时A作为客户端, B作为服务端, 当B希望主动给A push消息时, 我们可以采取tarscpp中的第三方协议来完成, 但是这种模式下, 主要有两个缺点:

服务的协议完全自定义的, 提高了开发难度
服务协议非普通的tars协议, 如果一个普通的tars协议服务希望具备这个功能就比较难做到
因此tarscpp框架提供了一种全新的push callback模式来解决这两个问题.

## 接口定义
我们先看看具体的使用案例, 首先定义服务B的接口如下:
```c++
interface Hello
{
    int registerPush();
};
```
可以看到这个接口比较简单, 就是提供A调用, 用于注册push.

在定义服务A和B之间的push接口, 这个定义非常重要, 是整个push callback的精髓, 需要仔细理解.

```c++
interface Push
{
    int pushMsg(out string sRsp);
};
```
### 服务端实现

完成接口定义后, 我们再来看服务B的核心实现, 首先服务B在Hello的实现中, 实现registerPush, 头文件HelloImp核心定义如下:

```c++
 /**
  * 连接关闭
  * @param current 
  * @return 
  */
 virtual int doClose(tars::TarsCurrentPtr current);

  /**
   * 客户端请求注册push通知
   */
  virtual int registerPush(tars::TarsCurrentPtr current);
```
实现文件如下:

```c++
int HelloImp::doClose(tars::TarsCurrentPtr current)
{
   g_app._pushThread.delCurrent(current);
}
int HelloImp::registerPush(tars::TarsCurrentPtr current)
{
   g_app._pushThread.addCurrent(current);
    return 0;
}
```

说明:
- push是个长连接,需要关注连接关闭事件, 因此可以看到实现了doClose方法, 将current去掉;
- 而registerPush是服务A主动调用, 此时服务B将current保存起来, 用于后续push;
- current表示了请求的上下文, 如果是同一个连接上来的请求, current->getUId()的值相同的, 框架层也是利用这个Id给对应的连接回消息
- _pushThread是一个自己实现的push线程类, 这个你可以根据实际情况去实现

接下来我们看看_pushThread 到底做了哪些工作.

_pushThread是自己实现的一个对象, 由于示例比较简单, 它会启动一个线程然后使用保存下来的current给上来的请求发送push消息, 核心示例代码如下:

```c++
void PushThread::addCurrent(CurrentPtr &current)
{
 std::lock_guard<std::mutex> lock(_mutex);

 _currents[current->getUId()] = current;
}

void PushThread::delCurrent(CurrentPtr &current)
{
 std::lock_guard<std::mutex> lock(_mutex);

 _currents.erase(current->getUId());
}

void PushThread::run()
{
 while(!_terminate)
 {
  std::unique_lock<std::mutex> lock(_mutex);

  for(auto it : _currents)
  {
   TestApp::Push::async_response_push_pushMsg(it.second, 0, "push message");
  }

  _cond.wait_for(lock, std::chrono::milliseconds(1000));
 }

}

```
其中最核心的一行代码:
```c++
TestApp::Push::async_response_push_pushMsg(it.second, 0, "push message");
```

分析:

- async_response_push_pushMsg函数是根据前面Push接口自动生成的, 匹配了Push接口的pushMsg函数
- 第一参数是current, 代表了是哪个连接上来的, 第二个参数是pushMsg的返回值, 第三个参数是pushMsg的out返回参数, 可以有多个返回参数, 这种push方式, 不需要有输入参数.

可以看到利用这个方法就可以给客户端返回想要的数据.

- 客户端实现
对于客户端而言, 获取服务端的通知也非常简单, 代码示例如下:

```c++
class PushCallbackImp : public PushPrxCallback
{
public:
 virtual void callback_pushMsg(tars::Int32 ret,  const std::string& sRsp)
 {
  LOG_CONSOLE_DEBUG << ret << ", " << sRsp << endl;
 }
};

_comm = new Communicator();

HelloPrx pPrx = _comm->stringToProxy<HelloPrx>(helloObj);
pPrx->tars_set_push_callback(new PushCallbackImp());
pPrx->registerPush();
```
分析:

- 客户端为了收取push消息, 核心就是要注册tars_set_push_callback一个回调;
- PushPrxCallback就是代码生成中的类, 实现对应的callback_pushMsg 即可完成push回调!

可以看到, 客户端的操作也非常简单!

## 其他说明
利用push callback机制, 可以非常方便完成服务间的push机制, 但是需要注意几点

- registerPush可以定时调用, 保持A和B之间连接的活跃度
- 需要注意B有多台服务器的场景, 每次调用registerPush都是轮训的, 这个时候就需要考虑清楚push机制该如何设计, 这个本文就不展开,使用者可以自己仔细思考一下
- tarsgo也已经支持该模式, 其他语言暂时未实现该机制.