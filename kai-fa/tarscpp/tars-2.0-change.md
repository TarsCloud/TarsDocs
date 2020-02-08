# TarsCpp 2.0变化 

## 1. 概述

TarsCpp2.0针对1.x版本做了重大的更新, 主要包括以下几个方面:
- 多平台支撑
- 删除不常用功能
- 完善协议解析器
- 增加常用工具类(文档待完善)

## 2. 多平台支撑

TarsCpp2.0完成linux, mac, windows(>=WIN7)三个OS系统的的支撑, 你可以在这些平台上编写和调试程序, 但是你发布到服务器上的程序必须对应服务器系统编译出来的.

注意:TarsCpp2.0在接口层面有细微改动, 部分代码可能需要修改才能编译过.

## 3. 剔除的功能

针对1.X版本的中历史问题, 本次做了修改:
- 去掉promise功能, 建议使用协程, 协程已经完成多平台, 多cpu的适配(如果有碰到问题, 请提交issues)
- 去掉TC_Atomic, 请使用std::atomic
- 修改协议解析器, 见下文
- 修改TC_ThreadPool的实现, 使用更加方便

## 4. 协议解析器

TarsCpp2.0 对于协议解析做了比较大的变动, 主要的目的是减小网络包处理时的内存拷贝, 经过优化后, TarsCpp2.0相对1.x版本性能有30%以上的提升.

协议解析器是TarsCpp服务模型一个重要的设计理念, 它使得使用TarsCpp实现的Server几乎能支持任何协议, 包括你自定义的网络协议.

### 4.1 服务器端的协议解析

我们知道可以通过TarsCpp快速实现一个HTTP服务, 参见[http server](../../an-li/tarscpp/tars_cpp_http_demo.md), 其中最核心的是:

```
void HttpServer::initialize()
{
    addServant<HttpImp>(ServerConfig::Application + "." + ServerConfig::ServerName + ".HttpObj");
    addServantProtocol(ServerConfig::Application + "." + ServerConfig::ServerName + ".HttpObj",&HttpProtocol::parseHttp);
}
```

即指定了Servant(这里是HttpObj) 的协议解析函数: ```HttpProtocol::parseHttp```, 这个函数你可以自己实现, 在TarsCpp1.x版本中它的函数定义如下:
```
typedef std::function<int(string &, string &)> protocol_functor;
```

例如:
```
int parseHttp(string &in, string &out)
```

说明:
- in: 网络输入buffer
- out: 解析一个完成的数据包
- 返回值: int, 表示解析状态:
>- TC_EpollServer::PACKET_FULL: 解析出一个完整的包, 将这个完整的包放入out参数中, 并截断in, out会在Servant的doRequest中拿到
>- TC_EpollServer::PACKET_LESS: 包没有收全, 需要继续接收
>- TC_EpollServer::PACKET_ERR: 包解析出错, 框架会关闭当前连接

在这个协议解析中, 实际上有几次内存copy, 比如截断in, copy到out, 都会带来不小的性能损失, 因此TarsCpp2.0针对这个问题做了改进.

TarsCpp2.0的协议器的原型如下:
```
typedef std::function<PACKET_TYPE(TC_NetWorkBuffer &, vector<char> &)> protocol_functor;
```
例如:
```
TC_NetWorkBuffer::PACKET_TYPE parseHttp(TC_NetWorkBuffer &in, vector<char> &out)
```

说明:
- in: TC_NetWorkBuffer管理着所有输入buffer, 内部采用链表管理
- out: 解析一个完成的数据包(测试表明, 在内存处理方面, vector<char>比string稍微快一点)
- 返回值: TC_NetWorkBuffer::PACKET_TYPE, 表示解析状态:
>- TC_NetWorkBuffer::PACKET_FULL: 解析出一个完整的包, 你实现具体解析器函数时需要将完整的包放入out参数中, out会在Servant的doRequest中拿到
>- TC_NetWorkBuffer::PACKET_LESS: 包没有收全, 需要继续接收
>- TC_NetWorkBuffer::PACKET_ERR: 包解析出错, 框架会关闭当前连接

为了避免内存copy, TC_NetWorkBuffer中设计几个函数, 这几个函数是你可能需要用到的:

- getBufferLength: 获取当前buffer中接收当时还没有解析的数据的长度
- getBufferPointer: 获取链表上, 第一个有效的buffer的数(指针和长度)
- getBuffers: 获取所有buffer(会有内存copy, 使用时请注意)
- getHeader: 获取前几个字节的二进制流(注意指针不移动, 即不改变数据内容)
- moveHeader: 指针后移, 通常配合getHeader使用
- getValueOf[1-4]: 获取头部几个字节, 并用字节序转换成具体类型
- parseHttp: http协议解析


#### 实例:

输入的二进制协议为: 头部四个字节(字节序)包总长 + 具体内容

```

static TC_NetWorkBuffer::PACKET_TYPE pushResponse(TC_NetWorkBuffer &in, vector<char> &out)
{
	size_t len = sizeof(tars::Int32);

    //包总长小于4个字节, 包没有收全
	if (in.getBufferLength() < len)
	{
		return TC_NetWorkBuffer::PACKET_LESS;
	}

	string header;
    //获取头部4个字节
	in.getHeader(len, header);

	assert(header.size() == len);

	tars::Int32 iHeaderLen = 0;

    //转换头部4个字节为字节序, 存放在iHeaderLen中
	::memcpy(&iHeaderLen, header.c_str(), sizeof(tars::Int32));

	iHeaderLen = ntohl(iHeaderLen);

    //包太小或者太长, 直接抛出异常,框架会关闭连接
	if (iHeaderLen > 100000 || iHeaderLen < sizeof(unsigned int))
	{
		throw TarsDecodeException("packet length too long or too short,len:" + TC_Common::tostr(iHeaderLen));
	}

    //已经收取的包长度小于iHeaderLen, 还没有收全
	if (in.getBufferLength() < (uint32_t)iHeaderLen)
	{
		return TC_NetWorkBuffer::PACKET_LESS;
	}

    //已经收全了, 获取iHeaderLen的buffer, 放入out中
	in.getHeader(iHeaderLen, out);

    //往后移动iHeaderLen个字节
    in.moveHeader(iHeaderLen);

    //返回包已经收全
    return TC_NetWorkBuffer::PACKET_FULL;
}

```

**实际这个协议已经实现了, 可以直接使用TC_NetWorkBuffer::parseBinary4**

这个例子也可以参考: examples/CustomDemo/CustomServer的实现

注意:启用第三方协议, 在web管理平台上, 部署时协议需要选择: 非TARS 协议!!

### 4.2 客户端端的协议解析

对于Tars框架而言, 客户端调用方都被Communicator类管理(这个类所有语言都有), 通过Communicator创建远程服务端的代理, 从而完成和服务器端的通信.

正常情况下, Tars客户端和Tars服务端都通过Tars协议来完成通信, 但是在某些场合, 你的服务可能需要调用其他一个服务, 这时通常的做法是你需要自己实现协议解析和网络通信, 这时Tars的Communicator可以做到设置协议解析器, 从而完成Tars客户端和其他服务的通信(非Tars协议).

还是以CustomServer为例说明, 客户端创建了一个CustomServer的代理:

```
string sObjName = "TestApp.CustomServer.TestPushServantObj@tcp -h 127.0.0.1 -t 60000 -p 9300";

_prx = _comm.stringToProxy<ServantPrx>(sObjName);

```

然后设置代理的协议, 用于请求包和响应包的解析:

```
	ProxyProtocol prot;
    prot.requestFunc = pushRequest;
    prot.responseFunc = pushResponse;

    _prx->tars_set_protocol(prot);
```

发送请求时使用```rpc_call_async```:
```
ResponseRequest rsp;
_prx->rpc_call(_prx->tars_gen_requestid(), "doCustomFunc", buf.c_str(), buf.length(), rsp);
```

说明:
- 同步(rpc_call)和异步(rpc_call_async)都支持, 上面是同步调用的方式, 异步调用后文会介绍
- buf是具体的请求包内容
- pushRequest & pushResponse 用于组装协议

pushRequest的函数定义如下:

```
typedef std::function<void(const RequestPacket &, shared_ptr<TC_NetWorkBuffer::SendBuffer>&)> request_protocol_functor;
```
说明:
- RequestPacket是Tars服务底层请求结构体, 具体可以参见[tars_protorol](../tars_protocol.md)
- 协议解析器的目的就是将RequestPacket, 序列化到SendBuffer中
- _prx代理的成员函数: rpc_call & rpc_call_async 用于发送自定义协议的数据到服务器端, 它的函数原型如下:
```
    virtual void rpc_call(uint32_t requestId, const string& sFuncName, const char* buff, uint32_t len, ResponsePacket &rsp);

    virtual void rpc_call_async(uint32_t requestId, const string& sFuncName, const char* buff, uint32_t len, const ServantProxyCallbackPtr& callback, bool bCoro = false);
```  
                              `
注意: 参数requestId, sFuncName, buffer赋值到RequestPacket的iRequestId, sFuncName, sBuffer 

例如:

//整体包结构是: 4个字节长度+4个字节的RequestId+数据buffer
static void pushRequest(const RequestPacket& request, shared_ptr<TC_NetWorkBuffer::SendBuffer>& sbuff)
{
    //数据包总长度
    unsigned int net_bufflength = htonl(request.sBuffer.size()+8);
    unsigned char * bufflengthptr = (unsigned char*)(&net_bufflength);

	vector<char> buffer;
	buffer.resize(request.sBuffer.size()+8);

	memcpy(buffer.data(), bufflengthptr, sizeof(unsigned int));

    //requestId
    unsigned int netrequestId = htonl(request.iRequestId);
    unsigned char * netrequestIdptr = (unsigned char*)(&netrequestId);

	memcpy(buffer.data() + sizeof(unsigned int), netrequestIdptr, sizeof(unsigned int));
	memcpy(buffer.data() + sizeof(unsigned int) * 2, request.sBuffer.data(), request.sBuffer.size());

    //加入到发送buffer中, 这个buffer会发送到服务器端
	sbuff->addBuffer(buffer);
}

```

接下来看响应包接收的处理, 响应包是解析解析函数定义:
```
typedef std::function<PACKET_TYPE(TC_NetWorkBuffer &, ResponsePacket &)> response_protocol_functor;
```
说明:
- ResponsePacket是Tars服务底层响应结构体, 具体可以参见[tars_protorol](../tars_protocol.md)
- 协议解析器的目的就是将输入buffer, 解析成ResponsePacket

以CustomClient中customResponse为例:
```

//The response packet decoding function decodes the data received from the server according to the specific format and resolves it to theResponsePacket
static TC_NetWorkBuffer::PACKET_TYPE customResponse(TC_NetWorkBuffer &in, ResponsePacket& rsp)
{
	size_t len = sizeof(tars::Int32);

	if (in.getBufferLength() < len)
	{
		return TC_NetWorkBuffer::PACKET_LESS;
	}

	string header;
	in.getHeader(len, header);

	assert(header.size() == len);

	tars::Int32 iHeaderLen = 0;

	::memcpy(&iHeaderLen, header.c_str(), sizeof(tars::Int32));

	iHeaderLen = ntohl(iHeaderLen);

	if (iHeaderLen > 100000 || iHeaderLen < (int)sizeof(unsigned int))
	{
		throw TarsDecodeException("packet length too long or too short,len:" + TC_Common::tostr(iHeaderLen));
	}

	if (in.getBufferLength() < (uint32_t)iHeaderLen)
	{
		return TC_NetWorkBuffer::PACKET_LESS;
	}

	in.moveHeader(sizeof(iHeaderLen));

    //parse requestId
	tars::Int32 iRequestId = 0;
	string sRequestId;
	in.getHeader(sizeof(iRequestId), sRequestId);
	in.moveHeader(sizeof(iRequestId));

	rsp.iRequestId = ntohl(*((unsigned int *)(sRequestId.c_str())));
	len =  iHeaderLen - sizeof(iHeaderLen) - sizeof(iRequestId);
    
    //parse buffer
	in.getHeader(len, rsp.sBuffer);
	in.moveHeader(len);

    return TC_NetWorkBuffer::PACKET_FULL;
}
```

异步调用的例子:
```
class CustomCallBack : public ServantProxyCallback
{
public:
    virtual int onDispatchResponse(const RequestPacket &req, const ResponsePacket &rsp)
	{
		cout << "async response:" << rsp.sBuffer.data() << endl;
	}

    virtual int onDispatchException(const RequestPacket &req, const ResponsePacket &rsp)
	{
		cout << "async exception:" << rsp.iRet << endl;
	}
};
CustomCallBackPtr cb = new CustomCallBack();
prx->rpc_call_async(prx->tars_gen_requestid(), "doCustomFunc", buf.c_str(), buf.length(), cb);
```

最后说明:
- 这种协议解析器的设计, 可以使得服务方和调用都能方便的支持任何协议, 当然协议设计比如满足下一条要求
- 由于异步特性的存在, 这种协议必须是通过iRequestId(int类型)来匹配请求包和响应包的
