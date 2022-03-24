# HTTP 协议支持示例

## 概述

请务必先阅读 [concept](../../base/tars-concept.md) and [spec](../../dev/tarscpp/tars-spec.md)

TARS 服务框架默认情况下只支持 TARS 自有的 tars 协议，但是在实际的应用场景中，需要在 TARS 服务框架中支持其他协议，例如 HTTP，这种情况下就不能用通信器来发送据，需要业务自己来实现这部分代码。对于自定义的协议， 处理方式也类似

具体程序示例，实例代码参见 tarscpp/example/HttpDemo

开发第三方协议服务端,要实现协议解析器并将其加载到服务中,同时需要建立一个非 TAF 框架的服务对象,该类继承于 Servant 类,通过重载 Servant 类中的 doRequest 方法来建立协议处理器。 而客户端要访问服务，需要通过调用 proxy 的 rpc 函数，在调用之前，要为 proxy 设置请求包编码函数和响应包解码函数。

![](../../assets/tars_cpp_third_protocol.png)

图中的黑色线代表了数据流向：数据（客户端）-〉请求包的编码器（客户端）-〉协议解析器（服务端）-〉doRequest 协议处理器（服务端）-〉生成返回数据（服务端）-〉响应包的解码器（客户端）-〉响应数据（客户端）

其中请求包的编码器（客户端）负责对客户端发送的数据进行打包，协议解析器（服务端）负责对收到的数据进行解析并交给协议处理器（服务端）去处理并生成返回数据，而响应包的解码器（客户端）负责对返回的数据进行解码。

## 服务端 Http 协议实例

/usr/local/tars/cpp/script/cmake_tars_server.sh TestApp HttpServer Http

在目录下会生成六个文件，将 http.tars 删除（因为不是 tars 协议），然后手动的实现一些方法

以 HelloServer 为例，需要支持 http 协议

在 HttpImp 中修改继承自 Servant 类的 doRequest 方法，该方法为第三方服务的处理器，该处理器负责处理协议解析器传送给其的数据，并负责生成返回给客户端的 response

HttpImp.h

```cpp
#ifndef _HttpImp_H_
#define _HttpImp_H_

#include "servant/Application.h"

/**
 *
 *
 */
class HttpImp : public Servant
{
public:
    /**
     *
     */
    virtual ~HttpImp() {}

    /**
     *
     */
    virtual void initialize();

    /**
     *
     */
    virtual void destroy();

    /**
     *
     */
    int doRequest(TarsCurrentPtr current, vector<char> &buffer);

};
/////////////////////////////////////////////////////
#endif
```

HttpImp.cpp

```cpp
#include "HttpImp.h"
#include "servant/Application.h"

using namespace std;

//////////////////////////////////////////////////////
void HttpImp::initialize()
{
    //initialize servant here:
    //...
}

//////////////////////////////////////////////////////
void HttpImp::destroy()
{
    //destroy servant here:
    //...
}

int HttpImp::doRequest(TarsCurrentPtr current, vector<char> &buffer)
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

在其中 HttpServer 类的 initialize\(\)，加载服务对象 HttpImp，并设置第三方协议解析器 parse。 我们在函数中实现 HttpProtocol::parse 函数，用于解析协议。

```cpp
#ifndef _HttpServer_H_
#define _HttpServer_H_

#include <iostream>
#include "servant/Application.h"

using namespace tars;

/**
 *
 **/
class HttpServer : public Application
{
public:
    /**
     *
     **/
    virtual ~HttpServer() {};

    /**
     *
     **/
    virtual void initialize();

    /**
     *
     **/
    virtual void destroyApp();
};

extern HttpServer g_app;

////////////////////////////////////////////
#endif
```

```cpp
#include "HttpServer.h"
#include "HttpImp.h"

using namespace std;

HttpServer g_app;

void
HttpServer::initialize()
{
    //initialize application here:
    //...

    addServant<HttpImp>(ServerConfig::Application + "." + ServerConfig::ServerName + ".HttpObj");
    addServantProtocol(ServerConfig::Application + "." + ServerConfig::ServerName + ".HttpObj",&TC_NetWorkBuffer::parseHttp);
}
/////////////////////////////////////////////////////////////////
void
HttpServer::destroyApp()
{
    //destroy application here:
    //...
}
/////////////////////////////////////////////////////////////////
int
main(int argc, char* argv[])
{
    try
    {
        g_app.main(argc, argv);
        g_app.waitForShutdown();
    }
    catch (std::exception& e)
    {
        cerr << "std::exception:" << e.what() << std::endl;
    }
    catch (...)
    {
        cerr << "unknown exception." << std::endl;
    }
    return -1;
}
/////////////////////////////////////////////////////////////////
```
