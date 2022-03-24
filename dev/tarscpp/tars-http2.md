# HTTP2 支持

Tars 使用[nghttp2 库](https://www.nghttp2.org/)支持了 http2 协议，对协议库做了 RPC 封装，使得对业务开发几乎透明，用起来非常方便。由于复用了 Tars rpc，因此也拥有了同步和异步以及超时的特性，并能够利用 tars stat 上报调用质量。

本文简单介绍一下使用 http2 的方法和步骤，包括同步调用和异步调用, 实例代码参见 tarscpp/example/HttpDemo。

**注意 tarscpp>=2.3 版本后接口变更成最新的了, 不在兼容老版本!并保持和 http1 接口一致性**

## http2 开启

只需要按照下面几个步骤设置：

### 编译 tars 框架，支持 nghttp2

tars 框架默认不开启 http2，开启 HTTP2:

```text
cmake .. -DTARS_HTTP2=ON
```

重新编译 tarscpp.

## http2 server 实现

see demo: cpp/example/HttpDemo/Http2Server

自定义协议, 采用 http2

```cpp
void HttpServer::initialize()
{
    addServant<Http2Imp>(ServerConfig::Application + "." + ServerConfig::ServerName + ".Http2Obj");
    addServantProtocol(ServerConfig::Application + "." + ServerConfig::ServerName + ".Http2Obj", &parseHttp2);
}

```

协议解析器函数, 注意, 每个连接创建和保存一个 TC_Http2Server, 作为 http2 的 session 控制器

```cpp
TC_NetWorkBuffer::PACKET_TYPE parseHttp2(TC_NetWorkBuffer&in, vector<char> &out)
{
    TC_Http2Server*sessionPtr = (TC_Http2Server*)(in.getContextData());

    if(sessionPtr == NULL)
    {
    	shared_ptr<TC_Http2Server> session(new TC_Http2Server());
	    in.setContextData(session.get());

	    session->settings(3000);

        TC_EpollServer::Connection *connection = (TC_EpollServer::Connection *)in.getConnection();
        Http2Imp::addHttp2(connection->getId(), session);

	    sessionPtr = session.get();
    }

	return sessionPtr->parse(in, out);
}

```

服务实现中, 根据连接 id 获取到 http2 的 session(TC_Http2Server 对象) 并解析协议, 回包即可

```cpp

int Http2Imp::doRequest(TarsCurrentPtr current, vector<char> &buffer)
{
    shared_ptr<TC_Http2Server> session = getHttp2(current->getUId());

	vector<shared_ptr<TC_Http2Server::Http2Context>> contexts = session->decodeRequest();

	for(size_t i = 0; i< contexts.size(); ++i)
	{
		shared_ptr<TC_Http2Server::Http2Context> context = contexts[i];

		vector<char> data;

		context->response.setHeader("X-Header", "TARS");
		context->response.setResponse(200, "OK", context->request.getContent());

		int ret = session->encodeResponse(context, data);
		if(ret != 0)
		{
			cout << "encodeResponse error:" << session->getErrMsg() << endl;
		}
		buffer.insert(buffer.end(), data.begin(), data.end());
	}

    return 0;
}

int Http2Imp::doClose(TarsCurrentPtr current)
{
    delHttp2(current->getUId());

    return 0;
}
```

## http rpc 的使用

只需要按照下面几个步骤设置：

### 编译 tars 框架，支持 nghttp2

tars 框架默认不开启 http2，开启 HTTP2:

```text
cmake .. -DTARS_HTTP2=ON
```

### 获取客户端代理，设置 http2 编解码函数

```cpp
CommunicatorPtr& comm = Application::getCommunicator();
ServantPrx prx = comm->stringToProxy<ServantPrx>("test.server.yourobj");

// set protocol
prox->tars_set_protocol(ServantProxy::PROTOCOL_HTTP2);
```

### 发起同步调用

```cpp
	shared_ptr<TC_HttpResponse> rsp;
	shared_ptr<TC_HttpRequest> req = std::make_shared<TC_HttpRequest>();
	req->setPostRequest("http://domain.com/hello", string("helloworld-") + TC_Common::tostr(i), true);

	prx->http_call("hello", req, rsp);
```

注意: 这里 http_call 第一个参数没有实际涵义, 只是用来监控用, 即在 web 管理平台接口统计看到函数名是"hello"

### 发起异步调用

```cpp
// 编写callback
class MyHttpCb : public HttpCallback
{
public:
	virtual int onHttpResponse(const shared_ptr<TC_HttpResponse> &rsp)
	{
		return 0;
	}
	virtual int onHttpResponseException(int expCode)
	{
		return 0;
	}
};

// 发起调用
shared_ptr<TC_HttpRequest> req = std::make_shared<TC_HttpRequest>();

string buff = string("helloworld-") + TC_Common::tostr(i);
req->setPostRequest("http://domain.com/hello", buff, true);

prx->http_call_async("hello", req, p);

```

注意: 这里 http_call_async 第一个参数没有实际涵义, 只是用来监控用, 即在 web 管理平台接口统计看到函数名是"hello"
