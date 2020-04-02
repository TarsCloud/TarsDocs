


# HTTP支持

tars内置了对http的支持，对协议库做了RPC封装，使得对业务开发几乎透明，用起来非常方便。由于复用了 tars rpc，因此也拥有了同步和异步以及超时的特性，并能够利用tars stat上报调用质量。

本文简单介绍一下使用http的方法和步骤，包括同步调用和异步调用, 实例代码参见examples/HttpDemo。

## http server实现

see demo: examples/HttpDemo/HttpServer

自定义协议, 采用http, 具体可以[参考http服务端的文档()

```

void HttpServer::initialize()
{
    addServant<HttpImp>(ServerConfig::Application + "." + ServerConfig::ServerName + ".HttpObj");
    addServantProtocol(ServerConfig::Application + "." + ServerConfig::ServerName + ".HttpObj", &TC_NetWorkBuffer::parseHttp);
}

```
```
int HttpImp::doRequest(JceCurrentPtr current, vector<char> &buffer)
{
    TC_HttpRequest request; 
    vector<char> v = current->getRequestBuffer();
    string sBuf;
    sBuf.assign(&v[0],v.size());
    request.decode(sBuf);
    TC_HttpResponse rsp;
    string s="hello";
    rsp.setResponse(s.c_str(),s.size());
    rsp.encode(buffer);
   
    return 0;
}
```
## http rpc的使用

### 获取客户端代理，设置http编解码函数

```text
CommunicatorPtr& comm = Application::getCommunicator();
ServantPrx prx = comm->stringToProxy<ServantPrx>("test.server.yourobj");

// set protocol
prx->tars_set_protocol(ServantProxy::PROTOCOL_HTTP1, 10);
```

### 关于连接复用

虽然http支持长短连接, 但是http无法连接复用, 即一条连接上只能并行跑一个包(请求和响应), 因此tars rpc专门支持了串行模式.

例如:
```
prx->tars_connection_serial(10)
```

相当于启动了10个http连接, 在连接上完成http请求, 正常的tars的rpc不要启用这个参数!!!

这里使用一条函数启用了prx对http的支持, 其中第二个参数10, 即串行连接个数
```
prx->tars_set_protocol(ServantProxy::PROTOCOL_HTTP1, 10);
```

实际上, 这条语句相当于:

```
ProxyProtocol proto;
proto.requestFunc = tars::http1Request;
proto.responseExFunc = tars::http1Response;
prx->tars_set_protocol(proto);
prx->tars_connection_serial(10);

```

### 发起同步调用

```text
	shared_ptr<TC_HttpResponse> rsp;
	shared_ptr<TC_HttpRequest> req = std::make_shared<TC_HttpRequest>();
	req->setPostRequest("http://domain.com/hello", string("helloworld-") + TC_Common::tostr(i), true);
    //你可以设置http头支持, keep-alive
	req->setHeader("Connection", "keep-alive");

	prx->http_call("hello", req, rsp);
```

注意: 这里http_call第一个参数没有实际函数, 只是用来监控用, 即在web管理平台接口统计看到函数名是"hello"

### 发起异步调用

```text
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
注意: 这里http_call_async第一个参数没有实际函数, 只是用来监控用, 即在web管理平台接口统计看到函数名是"hello"

