# 使用指南

## 1. C++服务端

下面通过一个完整的例子介绍如何使用 TARS 来实现自己的服务。

以下代码示例描述了客户端发送 hello world 字符串到服务端，服务端返回 hello word 字符串的例子。

### 1.1. 定义接口

编写 tars 文件如下，Hello.tars：

```cpp
module TestApp
{

interface Hello
{
    int test();
    int testHello(string sReq, out string sRsp);
};

};
```

### 1.2. 编译接口文件

采用 tars2cpp 工具自动生成 c++文件：/usr/local/tars/cpp/tools/tars2cpp hello.tars 会生成 hello.h 文件，该文件被客户端和服务端包含；

### 1.3. 接口实现

服务端代码实现 tars 文件中的接口：

HelloImp.h

```cpp
#ifndef _HelloImp_H_
#define _HelloImp_H_

#include "servant/Application.h"
#include "Hello.h"

/**
 * HelloImp继承hello.h中定义的Hello对象
 *
 */
class HelloImp : public TestApp::Hello
{
public:
    /**
     *
     */
    virtual ~HelloImp() {}

    /**
     * 初始化，Hello的虚拟函数，HelloImp初始化时调用
     */
    virtual void initialize();

    /**
     * 析构，Hello的虚拟函数，服务析构HelloImp退出时调用
     */
    virtual void destroy();

    /**
     * 实现tars文件中定义的test接口
     */
    virtual int test(tars::TarsCurrentPtr current) { return 0;};

    /**
     * 实现tars文件中定义的test接口
     */
    virtual int testHello(const std::string &sReq, std::string &sRsp, tars::TarsCurrentPtr current);

};
/////////////////////////////////////////////////////
#endif

```

HelloImp.cpp

```cpp
#include "HelloImp.h"
#include "servant/Application.h"

using namespace std;

//////////////////////////////////////////////////////
void HelloImp::initialize()
{
    //initialize servant here:
    //...
}

//////////////////////////////////////////////////////
void HelloImp::destroy()
{
    //destroy servant here:
    //...
}

int HelloImp::testHello(const std::string &sReq, std::string &sRsp, tars::TarsCurrentPtr current)
{
    TLOGDEBUG("HelloImp::testHellosReq:"<<sReq<<endl);
    sRsp = sReq;
    return 0;
}
```

服务的框架代码

HelloServer.h 如下：

```cpp
#ifndef _HelloServer_H_
#define _HelloServer_H_

#include <iostream>
#include "servant/Application.h"

using namespace tars;

/**
 * HelloServer继承框架的Application类
 **/
class HelloServer : public Application
{
public:
    /**
     *
     **/
    virtual ~HelloServer() {};

    /**
     * 服务的初始化接口
     **/
    virtual void initialize();

    /**
     * 服务退出时的清理接口
     **/
    virtual void destroyApp();
};

extern HelloServer g_app;

////////////////////////////////////////////
#endif

```

HelloServer.cpp 如下：

```cpp
#include "HelloServer.h"
#include "HelloImp.h"

using namespace std;

HelloServer g_app;

/////////////////////////////////////////////////////////////////
void
HelloServer::initialize()
{
    //initialize application here:

    //添加Servant接口实现类HelloImp与路由Obj绑定关系
    addServant<HelloImp>(ServerConfig::Application + "." + ServerConfig::ServerName + ".HelloObj");
}
/////////////////////////////////////////////////////////////////
void
HelloServer::destroyApp()
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
////////////////////////////////////////////////////////////////
```

说明：

> - 对象 HelloImp 对服务的每个线程都有一个具体的实例，并且该 HelloImp 接口处理过程中，如果只是对 HelloImp 的成员变量来操作则是线程安全的，如果 HelloImp 接口中需要访问全局的对象，则需要加锁；
> - ServerConfig::Application+“.”+ ServerConfig::ServerName + ".HelloObj"表示服务的 Hello 对象的名称，后续客户端通过这个名称来访问服务；
> - 所有函数最有一个参数都是 TarsCurrentPtr，通过该结构体可以获取请求包的所有原始内容；
> - HelloServer 是自己实现的服务类，继承于服务框架的 tars::Application 类；

### 1.4. ServerConfig

服务框架中有全局的结构 ServerConfig，它记录了服务的基本信息。

ServerConfig 的成员变量都是静态的，在服务框架初始化时会自动从服务配置文件中初始化这些参数。

ServerConfig 的定义如下：

```cpp
struct ServerConfig
{
    static std::string Application;         //应用名称
    static std::string ServerName;          //服务名称,一个服务名称含一个或多个服务标识
    static std::string BasePath;            //应用程序路径，用于保存远程系统配置的本地目录
    static std::string DataPath;            //应用程序数据路径用于保存普通数据文件
    static std::string LocalIp;             //本机IP
    static std::string LogPath;             //log路径
    static int         LogSize;             //log大小(字节)
    static int         LogNum;              //log个数()
    static std::string LogLevel;            //log日志级别
    static std::string Local;               //本地套接字
    static std::string Node;                //本机node地址
    static std::string Log;                 //日志中心地址
    static std::string Config;              //配置中心地址
    static std::string Notify;              //信息通知中心
    static std::string ConfigFile;          //框架配置文件路径
    static int         ReportFlow;          //是否服务端上报所有接口stat流量 0不上报 1上报(用于非tars协议服务流量统计)
    static int         IsCheckSet;          //是否对按照set规则调用进行合法性检查 0,不检查，1检查
    static bool        OpenCoroutine;	    //是否启用协程处理方式
    static size_t      CoroutineMemSize;    //协程占用内存空间的最大大小
    static uint32_t    CoroutineStackSize;  //每个协程的栈大小(默认128k)
};

```

参数说明：

> - Application：应用名称，如果配置文件没有配置，默认为 UNKNOWN；
> - ServerName：服务名称，默认为可执行文件名名称；
> - BasePath：基本路径，通常表示可执行文件的路径，默认为”./” ；
> - DataPath：数据文件路径，通常表示存在服务自己的数据，比如 mmap 文件之类，默认为”./”；
> - LocalIp：本地 ip，默认是本机非 127.0.0.1 的第一块网卡 IP；
> - LogPath：日志文件路径，日志的写法请参考后续；
> - LogSize：滚动日志文件的大小；
> - LogNum：滚动日志文件的个数；
> - LogLevel：滚动 log 日志级别；
> - Local：服务可以有管理端口，可以通过管理端口发送命令给服务，该参数表示绑定的管理端口的地址，例如 tcp -h 127.0.0.1 -p 8899，如果没有设置则没有管理端口；
> - Node：本地 NODE 地址，如果设置，则定时给 NODE 发送心跳，否则不发送心跳，通常只有发布到框架上面的服务才有该参数；
> - Log：日志中心地址，例如：tars.tarslog.LogObj@tcp –h .. –p …，如果没有配置，则不记录远程日志；
> - Config：配置中心地址，例如：tars.tarsconfig.ConfigObj@tcp –h … -p …，如果没有配置，则 addConfig 函数无效，无法从远程配置中心拉取配置；
> - Notify：信息上报中心地址，例如：tars.tarsnotify.NotifyObj@tcp –h … -p …，如果没有配置，则上报的信息直接丢弃；
> - ConfigFile：框架配置文件路径
> - IsCheckSet：是否对按照 set 规则调用进行合法性检查 0,不检查，1 检查
> - OpenCoroutine：是否启用协程处理方式，默认为 0，不启用
> - CoroutineMemSize：协程占用内存空间的最大大小
> - CoroutineStackSize：每个协程的栈大小\(默认 128k\)

服务的配置文件格式如下：

```xml
<tars>
  <application>
    <server>
       #本地node的ip:port
       node=tars.tarsnode.ServerObj@tcp -h 10.120.129.226 -p 19386 -t 60000
       #应用名称
       app=TestApp
       #服务名称
       server=HelloServer
       #本机ip
       localip=10.120.129.226
       #管理端口
       local=tcp -h 127.0.0.1 -p 20001 -t 3000
       #服务可执行文件,配置文件等
       basepath=/usr/local/app/tars/tarsnode/data/TestApp.HelloServer/bin/
       #服务数据目录
       datapath=/usr/local/app/tars/tarsnode/data/TestApp.HelloServer/data/
       #日志路径
       logpath=/usr/local/app/tars/app_log/
       #滚动日志大小
       logsize=10M
       #配置中心的地址
       config=tars.tarsconfig.ConfigObj
       #通知上报的地址[可选]
       notify=tars.tarsnotify.NotifyObj
       #远程日志的地址[可选]
       log=tars.tarslog.LogObj
       #服务停止的超时时间
       deactivating-timeout=2000
       #日志等级
       logLevel=DEBUG
    </server>
  </application>
</tars>
```

### 1.5. Adapter

Adapter 对应代码中的 TC_EpollServer::BindAdapter，表示了绑定端口。

服务新增一个绑定端口，则新建立一个 BindAdapter，并设置相关的参数和处理对象则可以非常方便的完成对这个端口上的处理，通常用这个功能来完成在其他协议的支撑。

对于 TARS 服务而言，在服务配置文件中增加 adapter 项，即可以完成 TARS 增加一个 Servant 处理对象。

Adapter 配置如下：

```xml
<tars>
  <application>
    <server>
       #配置绑定端口
       <TestApp.HelloServer.HelloObjAdapter>
            #允许的IP地址
            allow
            #监听IP地址
            endpoint=tcp -h 10.120.129.226 -p 20001 -t 60000
            #处理组
            handlegroup=TestApp.HelloServer.HelloObjAdapter
            #最大连接数
            maxconns=200000
            #协议
            protocol=tars
            #队列大小
            queuecap=10000
            #队列超时时间毫秒
            queuetimeout=60000
            #处理对象
            servant=TestApp.HelloServer.HelloObj
            #当前线程个数
            threads=5
       </TestApp.HelloServer.HelloObjAdapter>
    </server>
  </application>
</tars>
```

关注 servant 项，在 HelloServer::initialize\(\)中，完成配置和代码中对象的对应，如下：

```cpp
void HelloServer::initialize ()
{
    //增加对象
    addServant<HelloImp>(ServerConfig::Application+“.”+ ServerConfig::ServerName + ".HelloObj");
}
```

### 1.6. 服务启动

服务启动命令如下：

```text
HelloServer --config=config.conf
```

注意：config.conf 为配置文件，服务端的配置文件和客户端的配置文件必须合并到这一个文件中。

就服务而言，可以单独运行，不需要发布到 TARS 系统框架上。

完整的如下：

```xml
<tars>
  <application>
    enableset=n
    setdivision=NULL
    <server>
       #本地node的ip:port
       node=tars.tarsnode.ServerObj@tcp -h 10.120.129.226 -p 19386 -t 60000
       #应用名称
       app=TestApp
       #服务名称
       server=HelloServer
       #本机ip
       localip=10.120.129.226
       #管理端口
       local=tcp -h 127.0.0.1 -p 20001 -t 3000
       #服务可执行文件,配置文件等
       basepath=/usr/local/app/tars/tarsnode/data/TestApp.HelloServer/bin/
       #服务数据目录
       datapath=/usr/local/app/tars/tarsnode/data/TestApp.HelloServer/data/
       #日志路径
       logpath=/usr/local/app/tars/app_log/
       #滚动日志大小
       logsize=10M
       #配置中心的地址
       config=tars.tarsconfig.ConfigObj
       #通知上报的地址[可选]
       notify=tars.tarsnotify.NotifyObj
       #远程日志的地址[可选]
       log=tars.tarslog.LogObj
       #服务停止的超时时间
       deactivating-timeout=2000
       #日志等级
       logLevel=DEBUG
        #配置绑定端口
       <TestApp.HelloServer.HelloObjAdapter>
            #允许的IP地址
            allow
            #监听IP地址
            endpoint=tcp -h 10.120.129.226 -p 20001 -t 60000
            #处理组
            handlegroup=TestApp.HelloServer.HelloObjAdapter
            #最大连接数
            maxconns=200000
            #协议
            protocol=tars
            #队列大小
            queuecap=10000
            #队列超时时间毫秒
            queuetimeout=60000
            #处理对象
            servant=TestApp.HelloServer.HelloObj
            #当前线程个数
            threads=5
       </TestApp.HelloServer.HelloObjAdapter>
    </server>
    <client>
       #主控的地址
       locator=tars.tarsregistry.QueryObj@tcp -h 10.120.129.226 -p 17890
       #同步超时时间
       sync-invoke-timeout=3000
       #异步超时时间
       async-invoke-timeout=5000
       #刷新ip列表的时间间隔
       refresh-endpoint-interval=60000
       #上报数据的时间间隔
       report-interval=60000
       #异步线程数
       asyncthread=3
       #模版名称
       modulename=TestApp.HelloServer
    </client>
  </application>
</tars>
```

## 2. C++客户端

客户端可以不用写任何协议通信代码即可完成远程调用。客户端代码同样需要包含 hello.h 文件。

### 2.1. 通信器

完成服务端以后，客户端需要对服务端完成收发包的工作。客户端对服务端完成收发包的操作是通过通信器（communicator）来实现的。

\*\* 注意:一个 Tars 服务只能使用一个 Communicator，用 Application::getCommunicator\(\)获取即可（若不是 Tars 服务，要使用通讯器，则自己创建，也只能一个）。

通信器是客户端的资源载体，包含了一组资源，用于收发包、状态统计等功能。

通信器的初始化如下：

```cpp
TC_Config conf("config.conf");
CommunicatorPtr c = new Communicator();
//用配置文件初始化通信器
c-> setProperty(conf);
//直接用属性初始化
c->setProperty("property", "tars.tarsproperty.PropertyObj");
c->setProperty("locator", "tars.tarsregistry.QueryObj@tcp -h ... -p ...");
```

说明：

> - 通信器的配置文件格式后续会介绍；
> - 通信器缺省不采用配置文件也可以使用，所有参数都有默认值；
> - 通信器也可以直接通过属性来完成初始化；
> - 如果需要通过名字来获取客户端调用代理，则必须设置 locator 参数；

通信器属性说明：

> - locator: registry 服务的地址，必须是有 ip port 的，如果不需要 registry 来定位服务，则不需要配置；
> - sync-invoke-timeout：调用最大超时时间（同步），毫秒，没有配置缺省为 3000
> - async-invoke-timeout：调用最大超时时间（异步），毫秒，没有配置缺省为 5000
> - refresh-endpoint-interval：定时去 registry 刷新配置的时间间隔，毫秒，没有配置缺省为 1 分钟
> - stat：模块间调用服务的地址，如果没有配置，则上报的数据直接丢弃；
> - property：属性上报地址，如果没有配置，则上报的数据直接丢弃；
> - report-interval：上报给 stat/property 的时间间隔，默认为 60000 毫秒；
> - asyncthread：异步调用时，回调线程的个数，默认为 1；
> - modulename：模块名称，默认为可执行程序名称；

通信器配置文件格式如下：

```xml
<tars>
  <application>
    #proxy需要的配置
    <client>
        #地址
        locator                     = tars.tarsregistry.QueryObj@tcp -h 127.0.0.1 -p 17890
        #同步最大超时时间(毫秒)
        sync-invoke-timeout         = 3000
        #异步最大超时时间(毫秒)
        async-invoke-timeout        = 5000
        #刷新端口时间间隔(毫秒)
        refresh-endpoint-interval   = 60000
        #模块间调用
        stat                        = tars.tarsstat.StatObj
        #属性上报地址
        property                    = tars.tarsproperty.PropertyObj
        #report time interval
        report-interval             = 60000
        #网络异步回调线程个数
        asyncthread                 = 3
        #模块名称
        modulename                  = Test.HelloServer
    </client>
  </application>
</tars>
```

使用说明：

> - 当使用 Tars 框架做服务端使用时，通信器不要自己创建，直接采用服务框架中的通信器就可以了，例如：Application::getCommunicator\(\)-&gt;stringToProxy\(...\)，对于纯客户端情形，则要用户自己定义个通信器并生成代理（proxy）；
> - getCommunicator\(\)是框架 Application 类的静态函数，随时可以获取；
> - 对于通信器创建出来的代理，也不需要每次需要的时候都 stringToProxy，初始化时建立好，后面直接使用就可以了；
> - 代理的创建和使用，请参见下面几节；
> - 对同一个 Obj 名称，多次调用 stringToProxy 返回的 Proxy 其实是一个，多线程调用是安全的，且不会影响性能；
> - 可以通过 Application::getCommunicator\(\)-&gt;getEndpoint（”obj”）获取 bj 对应 ip 列表。另外一种获取方式为直接从通信器生成的 proxy 获取如 Application::getCommunicator\(\)-&gt;stringToProxy\(…\) -&gt;getEndpoint\(\)

### 2.2. 超时控制

超时控制是对客户端 proxy（代理）而言。上节中通信器的配置文件中有记录：

```text
#同步最大超时时间(毫秒)
sync-invoke-timeout          = 3000
#异步最大超时时间(毫秒)
async-invoke-timeout         = 5000
```

上面的超时时间对通信器生成的所有 proxy 都有效。

如果需要单独设置超时时间，设置如下：

针对 proxy 设置超时时间：

```cpp
ProxyPrx  pproxy;
//设置该代理的同步的调用超时时间 单位毫秒
pproxy->tars_timeout(3000);
//设置该代理的异步的调用超时时间 单位毫秒
pproxy->tars_async_timeout(4000);
```

针对接口设置超时时间：

```cpp
//设置该代理的本次接口调用的超时时间.单位是毫秒，设置单次有效
pproxy->tars_set_timeout(2000)->a();
```

### 2.3. 调用

本节会详细阐述 tars 的远程调用的方式。

首先简述 tars 客户端的寻址方式，其次会介绍客户端的调用方式，包括但不限于单向调用、同步调用、异步调用、hash 调用等。

#### 2.3.1. 寻址方式简介

Tars 服务的寻址方式通常可以分为如下两种方式，即服务名在主控注册和不在主控注册，主控是指专用于注册服务节点信息的名字服务（路由服务）。 名字服务中的服务名添加则是通过操作管理平台实现的。

对于没有在主控注册的服务，可以归为直接寻址方式，即在服务的 obj 后面指定要访问的 ip 地址。客户端在调用的时候需要指定 HelloObj 对象的具体地址：

即：Test.HelloServer.HelloObj@tcp -h 127.0.0.1 -p 9985

Test.HelloServer.HelloObj：对象名称

tcp：tcp 协议

-h：指定主机地址，这里是 127.0.0.1

-p：端口地址，这里是 9985

如果 HelloServer 在两台服务器上运行，则 HelloPrx 初始化方式如下：

```cpp
HelloPrx pPrx = c->stringToProxy<HelloPrx>("Test.HelloServer.HelloObj@tcp -h 127.0.0.1 -p 9985:tcp -h 192.168.1.1 -p 9983");
```

即，HelloObj 的地址设置为两台服务器的地址。此时请求会分发到两台服务器上（分发方式可以指定，这里不做介绍），如果一台服务器 down，则自动将请求分到另外一台，并定时重试开始 down 的那一台服务器。

对于在主控中注册的服务，服务的寻址方式是基于服务名进行的，客户端在请求服务端的时候则不需要指定 HelloServer 的具体地址，但是需要在生成通信器或初始化通信器的时候指定 registry\(主控中心\)的地址。

这里指定 registry 的地址通过设置通信器的参数实现，如下：

```cpp
CommunicatorPtr c = new Communicator();
c->setProperty("locator", "tars.tarsregistry.QueryObj@tcp -h .. -p ..")
```

由于依赖 registry 的地址，因此 registry 必须也能够容错，这里 registry 的容错方式与上面中一样，指定两个 registry 的地址即可。

#### 2.3.2. 单向调用

所谓单项调用，表示客户端只管发送数据，而不接收服务端的响应，也不管服务端是否接收到请求。

```cpp
TC_Config conf("config.conf");
CommunicatorPtr c = new Communicator();
//用配置文件初始化通信器
c-> setProperty(conf);
//生成客户端代理
HelloPrx pPrx = c->stringToProxy<HelloPrx>("Test.HelloServer.HelloObj@tcp -h 127.0.0.1 -p 9985");
//发起远程调用
string s = "hello word";
string r;
pPrx->async_testHello(NULL, s);
```

#### 2.3.3. 同步调用

请看如下调用示例：

```cpp
TC_Config conf("config.conf");
CommunicatorPtr c = new Communicator();
//用配置文件初始化通信器
c-> setProperty(conf);
//生成客户端代理
HelloPrx pPrx = c->stringToProxy<HelloPrx>("Test.HelloServer.HelloObj@tcp -h 127.0.0.1 -p 9985");
//发起远程同步调用
string s = "hello word";
string r;
int ret = pPrx->testHello(s, r);
assert(ret == 0);
assert(s == r);
```

上述例子中表示：客户端向 HelloServer 的 HelloObj 对象发起远程同步调用。

#### 2.3.4. 异步调用

定义异步回调对象：

```cpp
struct HelloCallback : public HelloPrxCallback
{
//回调函数
virtual void callback_testHello(int ret, const string &r)
{
    assert(r == "hello word");
}

virtual void callback_testHello_exception(tars::Int32 ret)
{
    assert(ret == 0);
    cout << "callback exception:" << ret << endl;
}
};

TC_Config conf("config.conf");
CommunicatorPtr c = new Communicator();
//用配置文件初始化通信器
c-> setProperty(conf);
//生成客户端代理
HelloPrx pPrx = c->stringToProxy<HelloPrx>("Test.HelloServer.HelloObj@tcp -h 127.0.0.1 -p 9985");
//定义远程回调对象
HelloPrxCallbackPtr cb = new HelloCallback;

//发起远程调用
string s = "hello word";
string r;
pPrx->async_testHello(cb, s);
```

注意：

> - 当接收到服务端返回时，HelloPrxCallback 的 callback_testHello 会被响应。
> - 如果调用返回异常或超时，则 callback_testHello_exception 会被调用，ret 的值定义如下：

```cpp
//定义TARS服务给出的返回码
const int TARSSERVERSUCCESS       = 0;       //服务器端处理成功
const int TARSSERVERDECODEERR     = -1;      //服务器端解码异常
const int TARSSERVERENCODEERR     = -2;      //服务器端编码异常
const int TARSSERVERNOFUNCERR     = -3;      //服务器端没有该函数
const int TARSSERVERNOSERVANTERR  = -4;      //服务器端没有该Servant对象
const int TARSSERVERRESETGRID     = -5;      //服务器端灰度状态不一致
const int TARSSERVERQUEUETIMEOUT  = -6;      //服务器队列超过限制
const int TARSASYNCCALLTIMEOUT    = -7;      //异步调用超时
const int TARSINVOKETIMEOUT       = -7;      //调用超时
const int TARSPROXYCONNECTERR     = -8;      //proxy链接异常
const int TARSSERVEROVERLOAD      = -9;      //服务器端超负载,超过队列长度
const int TARSADAPTERNULL         = -10;     //客户端选路为空，服务不存在或者所有服务down掉了
const int TARSINVOKEBYINVALIDESET = -11;     //客户端按set规则调用非法
const int TARSCLIENTDECODEERR     = -12;     //客户端解码异常
const int TARSSERVERUNKNOWNERR    = -99;     //服务器端位置异常
```

#### 2.3.5. 指定 set 方式调用

目前框架已经支持业务按 set 方式进行部署，按 set 部署之后，各个业务之间的调用对开业务发来说是透明的。但是由于有些业务有特殊需求，需要在按 set 部署之后，客户端可以指定 set 名称来调用服务端，因此框架则按 set 部署的基础上增加了客户端可以指定 set 名称去调用业务服务的功能。

详细使用规则如下，

假设业务服务端 HelloServer 部署在两个 set 上，分别为 Test.s.1 和 Test.n.1。那么客户端指定 set 方式调用如下：

```cpp
TC_Config conf("config.conf");
CommunicatorPtr c = new Communicator();
//用配置文件初始化通信器
c-> setProperty(conf);
//生成客户端代理
HelloPrx pPrx_Tests1 = c->stringToProxy<HelloPrx>("Test.HelloServer.HelloObj@tcp -h 127.0.0.1 -p 9985","Test.s.1");

HelloPrx pPrx_Testn1 = c->stringToProxy<HelloPrx>("Test.HelloServer.HelloObj@tcp -h 127.0.0.1 -p 9985","Test.n.1");

//发起远程调用
string s = "hello word";
string r;

int ret = pPrx_Tests1->testHello(s, r);

int ret = pPrx_Testn1->testHello(s, r);
```

注意：

> - 指定 set 调用的优先级高于客户端和服务端自身启用 set 的优先级，例如，客户端和服务端都启用了 Test.s.1，如果客户端创建服务端代理实例时指定了 Test.n.1，那么实际请求就会发送到 Test.n.1 的节点上（前提是这个 set 有部署服务端）。
> - 创建的代理实例也是只需调用一次

#### 2.3.6. Hash 调用

由于服务可以部署多台，请求也是随机分发到服务上，但是在某些场合下，希望某些请求总是在某一台服务器上，对于这种情况 Tars 提供了简单的方式实现：

假如有一个根据 QQ 号查询资料的请求，如下：

```cpp
QQInfo qi = pPrx->query(uin);
```

通常情况下，同一个 uin 调用，每次请求落在的服务器地址都不一定，但是采用如下调用：

```cpp
QQInfo qi = pPrx->tars_hash(uin)->query(uin);
```

则可以保证，每次 uin 的请求都在某一台服务器。

注意：

> - 这种方式并不严格，如果后台某台服务器 down，则这些请求会迁移到其他服务器，当它正常后，会迁移回来。
> - Hash 参数必须是 int，对于 string 来说，Tars 基础库\(util 目录下\)也提供了方法：tars::hash\(\)\("abc"\)，具体请参见 util/tc_hash_fun.h

## 3. 异步嵌套

异步嵌套代表如下情况：

> - A 异步调用 B，B 接收到请求后再异步调用 C，等 C 返回后，B 再将结果返回 A。

通常情况下面，B 接收到请求后，在接口处理完毕以后就需要返回应答给 A，因此如果 B 在接口中又发起异步请求到 C，则无法实现。

因此需要采用下面方法实现跨服务的异步调用，具体可以参考 examples/QuickStartDemo/ProxyServer 示例。

还是采用 hello world 的例子，client 发起请求给 Proxy，Proxy 接收到请求后，异步发起 testHello 给 HelloServer，请求返回后，Proxy 再将 HelloServer 返回的结果给 client。

这个处理过程中，关键的逻辑在 ProxyServer。以下代码是 B 中的逻辑处理：

```cpp
//ProxyServer中的异步回调对象
class HelloCallback : public HelloPrxCallback
{

public:
    HelloCallback(TarsCurrentPtr &current)
    : _current(current)
    {}

    virtual void callback_testHello(tars::Int32 ret,  const std::string& sOut)
    {
        Proxy::async_response_testProxy(_current, ret, sOut);
    }
    virtual void callback_testHello_exception(tars::Int32 ret)
    {
        TLOGERROR("HelloCallback callback_testHello_exception ret:" << ret << endl);

        Proxy::async_response_testProxy(_current, ret, "");
    }

    TarsCurrentPtr _current;
};

//定义ProxyServer中接口
tars::Int32 ProxyImp::testProxy(const std::string& sIn, std::string &sOut, tars::TarsCurrentPtr current)
{
    try
    {
        current->setResponse(false);

        TestApp::HelloPrxCallbackPtr cb = new HelloCallback(current);

        _prx->tars_set_timeout(3000)->async_testHello(cb,sIn);
    }
    catch(std::exception &ex)
    {
        current->setResponse(true);

        TLOGERROR("ProxyImp::testProxy ex:" << ex.what() << endl);
    }

    return 0;
}
```

说明：

> - 回调对象 HelloCallback 保存了上下文 current；
> - 回调对象收到 HelloServer 返回的请求以后，通过 Proxy::async_response_testProxy 再将请求返回 client；
> - 在 ProxyServer 的接口 testProxy 实现中，需要设置不自动回复 current-&gt;setResponse\(false\);
> - 这里 testProxy 的返回值，返回参数其实都没有意义了，无论返回什么都可以；
> - 如果回调对象中需要其他参数，可以再构造的时候传进去；
> - 回调对象必须是 new 出来放在智能指针中，例如：HelloPrxCallbackPtr cb = new HelloCallback\(current\); 它的生命周期，业务不用管理，框架层会自动管理；

## 4. 染色

### 4.1. 功能概述

染色功能的主要作用是可以在某服务某接口中对特定用户号码的消息进行染色，方便地实时察看该用户引起后续所有相关调用消息流的日志。

染色日志打开后，要看染色日志需要到 tarslog 所在的服务器上查看，具体的路径请参考：

滚动日志，即程序中调用 LOG 宏所写的日志全部向 tarslog 打印一份，日志文件为：tars_dyeing.dyeing_roll_yyyymmdd.log，每天一个文件，比如：

/usr/local/app/tars/remote_app_log/tars_dyeing/dyeing/tars_dyeing.dyeing_roll_20161227.log

按天日志,即程序中调用 DLOG、FDLOG 以及 FFDLOG 所写的日志全部向 tarslog 打印一份，日志文件为:tars_dyeing.dyeing_day_yyyymmdd.log，每天一个文件，比如：

/usr/local/app/tars/remote_app_log/tars_dyeing/dyeing/tars_dyeing.dyeing_day_20161227.log

### 4.2. 规则说明

染色日志有主动打开和被动打开两种方法。

所谓主动打开指的是，在请求的客户端打开框架中的染色日志开关。

具体使用步骤如下：

> - 在客户端程序适当的地方埋下锚点，该锚点用来根据某条件来决定是否打开染色日志开关--tars.TarsDyeingSwitch。染色作用范围从打开日志开始，到该开关析构为止。
> - 调用开关的 enableDyeing 方法即为打开染色日志。后续对应该请求所有日志（包括本服务，以及后面调用的所有服务的日志额外打印一份到 tarslog。
> - 被调服务根据请求标志位打开染色开关，该被调服务打印日志到 tarslog。如果该被调服务还要调用其他服务，则自动将该标志位传递给下个服务。该请求完毕后，自动关闭染色日志。

所谓被动打开指的是，在请求的服务端预先设定染色条件，判断传递的 key 值，由服务端来打开本服务的染色日志开关。 具体使用步骤如下：

> - 被染色的入口接口，需在 Tars 接口定义的时候，对用户关键字指定为 routekey
> - 染色的方法：.通过管理命令接口，往目标服务设置需要染色的用户号码、远程对象和接口（接口参数可选）。
> - 服务收到匹配的请求（匹配用户号码、远程对象和接口）后，对请求包进行染色处理。
> - 对已经染色的请求，相关的系统日志及按天日志，除了正常的打印外，会额外再集中打印一份到本地及远程的染色日志目录，目录位置在 tars 日志根目录的/tars_dyeing 目录下。
> - 服务处理过程如果继续调用其他 tars 服务，请求将通过状态字段继续传递染色信息，被调服务收到请求后也会以以上的方式打印染色日志，并将染色状态继续往下传递。

### 4.3. 主动打开使用实例

主动打开的使用方法：

```cpp
#include <iostream>
#include "servant/Communicator.h"
#include "Hello.h"
#include "servant/TarsLogger.h"
#include "util/tc_option.h"
#include "util/tc_file.h"

#include <string>
using namespace std;
using namespace TestApp;
using namespace tars;

int main(int argc,char ** argv)
{
try
{
    CommunicatorPtr comm =new Communicator();
    comm->setProperty("locator", "tars.tarsregistry.QueryObj@tcp -h 10.120.129.226 -p 17890 -t 10000");
    TarsRollLogger::getInstance()->setLogInfo("TestApp", "HelloServer", "./log", 100000, 10, comm, "tars.tarslog.LogObj");
    TarsRollLogger::getInstance()->sync(false);
    TarsTimeLogger::getInstance()->setLogInfo(comm, "tars.tarslog.LogObj", "TestApp", "HelloServer", "./log");
    {
            //在打开染色日志之前，打印日志，这条日志只会打印到本地日中。
            TLOGDEBUG    (__FILE__ << "|" << __LINE__ <<"Test Before Dyeing"  <<endl);
            DLOG        <<__FILE__ << "|" << __LINE__ <<"D/Test Before Dyeing"<<endl;
            FDLOG("T_D")<<__FILE__ << "|" << __LINE__ <<"F/Test Before Dyeing"<<endl;
    }
         try
         {
        	{

        	   //声明一个日志打开开关，调用enableDyeing打开染色日志
        	   TarsDyeingSwitch dye;
        	   dye.enableDyeing();

        	   //在打开染色日志之后，打印日志，会在本地日志和染色日志中看到
        	   {
        	      TLOGDEBUG    (__FILE__ << "|" << __LINE__ <<"Test Before Dyeing before call other function"  <<endl);
        	      DLOG        <<__FILE__ << "|" << __LINE__ <<"D/Test Before Dyeing before call other function"<<endl;
        	      FDLOG("T_D")<<__FILE__ << "|" << __LINE__ <<"F/Test Before Dyeing before call other function"<<endl;
        	   }


        	   string sReq("hello");
        	   std::string sServant="TestApp.HelloServer.HelloObj";
        	   TestApp::HelloPrx prx = comm->stringToProxy<TestApp::HelloPrx>(sServant);
        	   tars::Int32 iRet = prx->test();
        	   string sRsp;
        	   prx->testHello(sReq,sRsp);


        	   TLOGDEBUG    (__FILE__ << "|" << __LINE__ <<"Test Before Dyeing after call other function"  <<endl);
        	   DLOG        <<__FILE__ << "|" << __LINE__ <<"D/Test Before Dyeing after call other function"<<endl;
        	   FDLOG("T_D")<<__FILE__ << "|" << __LINE__ <<"F/Test Before Dyeing after call other function"<<endl;
        	}

        	{
        	   //染色日志开关已经析构，染色功能失效，以后的日志不会打印到染色日志中
        	   TLOGDEBUG    (__FILE__ << "|" << __LINE__ <<"~Dyeing"<<endl);
        	   DLOG        <<__FILE__ << "|" << __LINE__ <<"D/~Dyeing"<<endl;
        	   FDLOG("T_D")<<__FILE__ << "|" << __LINE__ <<"F/~Dyeing"<<endl;
        	}
        }
        catch(exception &ex)
        {
             cerr << "ex:" << ex.what() << endl;
        }
        catch(...)
        {
             cerr << "unknown exception." << endl;
        }
    }
    catch(exception& e)
    {
        cerr << "exception:" << e.what() << endl;
    }
    catch (...)
    {
        cerr << "unknown exception." << endl;
    }
    sleep(10); //等待异步写日志线程同步日志数据到logserver
    return 0;
}

```

在 tars_dyeing.dyeing_roll_20161227.log

```text
//这条日志是服务端使用TLOGDEBUG打印的滚动日志
10.120.129.226|TestApp.HelloServer|2016-12-27 11:30:47|7670|DEBUG|main.cpp|37Test Before Dyeing before call other function
//这条日志是服务端使用TLOGDEBUG打印的滚动日志
10.120.129.226|TestApp.HelloServer|2016-12-27 11:30:47|7670|DEBUG|main.cpp|59Test Before Dyeing after call other function
```

在 tars_dyeing.dyeing_day_20161227.log

```text
//使用DLOG打印的滚动日志
10.120.129.226|TestApp.HelloServer|2016-12-27 11:30:47|main.cpp|38D/Test Before Dyeing before call other function
10.120.129.226|TestApp.HelloServer|2016-12-27 11:30:47|main.cpp|60D/Test Before Dyeing after call other function
//使用FLOG打印的滚动日志
10.120.129.226|TestApp.HelloServer|2016-12-27 11:30:47|main.cpp|39F/Test Before Dyeing before call other function
10.120.129.226|TestApp.HelloServer|2016-12-27 11:30:47|main.cpp|61F/Test Before Dyeing after call other function
```

### 4.4. 被动打开的使用方法：

即将被染色的接口定义

```cpp
interface HelloRouteObj
{
    int testHello(routekey string sInput, out string sOutput);
};
```

可以通过框架命令进行染色，染色的命令格式为：

```text
tars.setdyeing dyeingKey dyeingServant [dyeingInterface]
```

三个参数分别为用户号码（routekey 对应的值），远程对象名称，接口名称（可选）。 假设以上接口的远程对象为 TestApp.HelloServer.HelloObj testHello 可以通过管理平台发布命令：tars.setdyeing 123456 TestApp.HelloServer.HelloObj testHello

当有 sInput 为 123456 的请求发到该服务，出了正常的日志输出外，还将集中打印到本地系统日志：

/usr/local/app/tars/app_log/tars_dyeing/dyeing_20161227.log TestApp.HelloServer\|2016-12-27 15:38:49\|11454\|DEBUG\|HelloImp::testHello sReq:123456

本地按条日志：

/usr/local/app/tars/app_log/tars_dyeing/dyeing_20161227.log TestApp.HelloServer\|2016-12-27 15:38:49\|11454\|DEBUG\|HelloImp::testHello sReq:123456

远程日志将打印到 tarslog 所在机器： 远程系统日志： /usr/local/app/tars/remote_app_log/tars_dyeing/dyeing/tars_dyeing.dyeing_roll_20161227.log

远程按天日志： /usr/local/app/tars/remote_app_log/tars_dyeing/dyeing/tars_dyeing.dyeing_day_20161227.log

其中，日志中首字段为染色请求处理过程的服务名称，后续其他被调服务的相关日志也打印到同一文件，通过首字段区分不同服务的日志。

## 5. tars 协议数据包大小

目前 tars 协议对数据包的大小进行了限制。

通信器（客户端）对发出去的包大小没有限制，对收到的回包有限制，默认是 10000000 字节（接近 10M）。

服务端对发送的包没有限制，对收到的包有大小限制，默认是 100000000 字节（接近 100M）。

### 5.1. 修改客户端接收数据包大小

通过修改 ServantProxy 的 tars_set_protocol 来修改数据包的大小。

```cpp
ProxyProtocol prot;
prot.responseFunc = ProxyProtocol::tarsResponseLen<100000000>;
prot.requestFunc  = ProxyProtocol::tarsRequest;
ccserverPrx ->tars_set_protocol(prot);
```

100000000 表示限制的大小，单位是字节。

ccserverPrx 是全局唯一的，只要设置一次即可。

为了写代码方便，建议在业务线程的 initialize 里面设置一次。

要先调用 stringToProxy 然后再设置。

```cpp
prot.requestFunc = ProxyProtocol::tarsRequest //必须有,默认不是这个函数。
```

如果是 tup 方式调用。设置 len

```cpp
prot.responseFunc  = ProxyProtocol:: tupResponseLen<100000000>;
```

### 5.2. 修改服务端接收数据包大小

通过设置 ServantProtocol 的形式来修改数据包大小。

```cpp
addServantProtocol(ServerConfig::Application + "." + ServerConfig::ServerName + ".BObj",AppProtocol::parseLenLen<100000000>);
```

建议在 server 的 initialize 里面设置，在 addServant 之后设置。

## 6. Tars 定义的返回码

```cpp
//定义TARS服务给出的返回码
const int TARSSERVERSUCCESS       = 0;       //服务器端处理成功
const int TARSSERVERDECODEERR     = -1;      //服务器端解码异常
const int TARSSERVERENCODEERR     = -2;      //服务器端编码异常
const int TARSSERVERNOFUNCERR     = -3;      //服务器端没有该函数
const int TARSSERVERNOSERVANTERR  = -4;      //服务器端没有该Servant对象
const int TARSSERVERRESETGRID     = -5;      //服务器端灰度状态不一致
const int TARSSERVERQUEUETIMEOUT  = -6;      //服务器队列超过限制
const int TARSASYNCCALLTIMEOUT    = -7;      //异步调用超时
const int TARSINVOKETIMEOUT       = -7;      //调用超时
const int TARSPROXYCONNECTERR     = -8;      //proxy链接异常
const int TARSSERVEROVERLOAD      = -9;      //服务器端超负载,超过队列长度
const int TARSADAPTERNULL         = -10;     //客户端选路为空，服务不存在或者所有服务down掉了
const int TARSINVOKEBYINVALIDESET = -11;     //客户端按set规则调用非法
const int TARSCLIENTDECODEERR     = -12;     //客户端解码异常
const int TARSSERVERUNKNOWNERR    = -99;     //服务器端位置异常
```

## 7. 业务配置

Tars 服务框架提供了从 tarsconfig 拉取服务的配置到本地目录的功能。

使用方法很简单，在 Server 的 initialize 中，调用 addConfig 拉取配置文件。

以 HelloServer 为例：

```cpp
HelloServer::initialize()
{
    //增加对象
    addServant<HelloImp>(ServerConfig::Application+“.”+ ServerConfig::ServerName + ".HelloObj");

    //拉取配置文件
    addConfig("HelloServer.conf");
}
```

说明：

> - HelloServer.conf 配置文件可以在 web 管理平台上配置；
> - HelloServer.conf 拉取到本地后， 可以通过 ServerConfig::BasePath + “HelloServer.conf”表示配置文件的绝对路径；
> - 配置文件的管理都在 web 管理平台上，同时 web 管理平台可以主动 push 配置文件到 Server；
> - 配置中心支持 ip 级别的配置，即一个服务部署在多台服务上，只有部分不同（与 IP 相关），这种情况下，配置中心可以支持配置文件的合并，同时支持在 web 管理平台查看和修改；

注意：

> - 对于没有发布到管理平台上的服务，需要在服务的配置文件中指定 Config 的地址，否则不能使用远程配置。

## 8. 日志

Tars 服务框架提供了宏定义方式用于记录系统的滚动日志/按天日志。这几个宏都是线程安全的，可以随意使用。

### 8.1. TLOGXXX 说明

TLOGXXX 用于记录滚动日志，用于调试服务，XXX 分别包括了 DEBUG/WARN/ERROR/NONE 四种级别，含义如下，

> - INFO： 信息级别，框架内部的日志都是以 INFO 级别打印，除非是错误
> - DEBUG：调试级别，最低级别；
> - WARN： 警告级别；
> - ERROR：错误级别；

使用方式：

```cpp
TLOGINFO("test" << endl);
TLOGDEBUG("test" << endl);
TLOGWARN("test" << endl);
TLOGERROR("test" << endl);
```

说明：

> - 可以在 web 管理设置服务 LOG 的当前级别;
> - Tars 框架的日志都通过 info 打印，设置成 info 后可以看见 Tars 的框架日志;
> - TLOGXXX 的按大小滚动，可以在服务的模板配置文件中修改滚动大小和个数，通常都不用动;
> - TLOGXXX 表示循环日志，是不会发送到远程 tarslog 服务的;
> - TLOGXXX 的文件名称和服务名称相关，通常是 app.server.log;
> - TLOGXXX 是全局的单件，任何地方可以使用，不过如果在框架完成对 LOG 初始化前就使用，则直接 cout 出来了;
> - 在使用的地方需要\#include "servant/TarsLogger.h"

TLOGXXX 是宏，它的定义如下：

```cpp
#define LOG             (TarsRollLogger::getInstance()->logger())

#define LOGMSG(level,msg...) do{if(LOG->IsNeedLog(level)) LOG->log(level)<<msg;}while(0)

#define TLOGINFO(msg...)  LOGMSG(TarsRollLogger::INFO_LOG,msg)
#define TLOGDEBUG(msg...) LOGMSG(TarsRollLogger::DEBUG_LOG,msg)
#define TLOGWARN(msg...)  LOGMSG(TarsRollLogger::WARN_LOG,msg)
#define TLOGERROR(msg...) LOGMSG(TarsRollLogger::ERROR_LOG,msg)
```

TarsRollLogger::getInstance\(\)-&gt;logger\(\)返回的类型是 TC_RollLogger\*，因此可以通过它对 LOG 进行设置，比如: 设置为 Info 级别：

```cpp
TarsRollLogger::getInstance()->logger()->setLogLevel(TC_RollLogger::INFO_LOG);
```

LOG 记录日志默认是异步的，也可以设置为同步：

```cpp
TarsRollLogger::getInstance()->sync(true);
```

在任何 ostream 的地方也可以使用 LOG，例如：

```cpp
ostream &print(ostream &os);
```

可以如下使用：

```cpp
print(LOG->debug());
```

### 8.2. DLOG/FDLOG

用于记录按天日志，即所谓的按天日志，主要用于记录重要的业务信息；

> - DLOG 是默认的按条日志，FDLOG 可以指定按条日志的文件名称；
> - DLOG/FDLOG 的日志会自动上传到 tarslog，可以设置为不上传到 tarslog；
> - DLOG/FDLOG 可以修改滚动的时间，比如按分钟，小时等；
> - DLOG/FDLOG 记录日志默认是异步的，如果有必要也可以设置为同步，但是对于远程上传到 tarslog 一定是异步的，不能设置为同步；
> - DLOG/FDLOG 可以设置不记录到本地，只上传到远程；
> - 在使用的地方需要\#include "servant/TarsLogger.h"

### 8.3. 代码示例

```cpp
CommunicatorPtr c = new Communicator();

string logObj = "tars.tarslog.LogObj@tcp -h 127.0.0.1 -p 20500";

//初始化本地滚动日志
TarsRollLogger::getInstance()->setLogInfo("Test", "TestServer", "./");

//初始化时间日志
TarsTimeLogger::getInstance()->setLogInfo(c, logObj , "Test", "TestServer", "./");

//如果是Tars服务，不需要上面部分的代码，框架已经自动完成初始化，不用业务自己初始化

//缺省的按天日志按分钟滚动
TarsTimeLogger::getInstance()->initFormat("", "%Y%m%d%H%M");

//缺省的按天日志不用上传到服务器
TarsTimeLogger::getInstance()->enableRemote("", false);

//abc2文件按小时滚动
TarsTimeLogger::getInstance()->initFormat("abc2", "%Y%m%d%H");

//abc2文件不用上传到服务器
TarsTimeLogger::getInstance()->enableRemote("abc2", false);

//abc3文件不记本地
TarsTimeLogger::getInstance()->enableLocal("abc3", false);

int i = 100000;
while(i--)
{
    //与上一个一样
    TLOGDEBUG(i << endl);

    //error级别
    TLOGERROR(i << endl);

    DLOG << i << endl;

    FDLOG("abc1") << i << endl;
    FDLOG("abc2") << i << endl;
    FDLOG("abc3") << i << endl;

    if(i % 1000 == 0)
    {
        cout << i << endl;
    }
    usleep(10);
}
```

## 9. 服务管理

Tars 服务框架可以支持动态接收命令，来处理相关的业务逻辑，例如：动态更新配置等。

框架中定义两个宏：

> - TARS_ADD_ADMIN_CMD_PREFIX：添加前置的命令处理方法，在所有 Normal 方法之前执行，多个前置方法之间顺序不确定
> - TARS_ADD_ADMIN_CMD_NORMAL：添加 Normal 命令处理方法，在所有前置方法最后执行，多个 Normal 方法之间顺序不确定

通过使用这两个宏，可以注册命令处理接口，当通过 web 管理平台向服务发送响应的命令的时候，注册的接口会被调用。

通常注册的接口处理有两种：全局的接口处理和基于对象的接口处理。

下面以 HelloServer 为例，介绍管理命令的使用。

### 9.1. 全局接口处理

所谓全局接口处理表示，该处理接口是和服务相关的，而不是和 HelloImp 这种对象相关的。

假设 HelloServer 增加一个功能，需要接收管理命令，将 FDLOG 某配置文件设置为不上传到远程 tarslog，这种处理与 HelloImp 对象是没有关系的，因此属于全局的变动，处理步骤：

在 HelloServer 类中增加处理函数，用于处理该命令，注意函数必须是这种声明方式：

```cpp
bool HelloServer::procDLOG(const string& command, const string& params, string& result)
{
    TarsTimeLogger::getInstance()->enableLocal(params, false);
    return false;
}
```

在 HelloServer::initialize\(\)中注册该函数：

```cpp
void HelloServer::initialize()
{
    addServant(...);
    addConfig(…);

	//注册处理函数：
    TARS_ADD_ADMIN_CMD_NORMAL("DISABLEDLOG", HelloServer::procDLOG);
}
```

说明:

> - 可以在 web 管理平台上直接给服务发送命令，例如：DISABLEDLOG，表示对缺省的按条日志设置为不写本地，再例如 DISABLEDLOG test，表示对 test 的按条日志不写本地；
> - 命令处理函数的声明方式必须一样：
> - 返回值为 bool：表示该命令是否往下传，即如果在一个命令上注册了多个接口函数，如果返回 false，则执行当前的接口后，后续接口都不在调用；
> - 第一个参数 command：表示注册的命令字符串，这个例子中表示：DISABLEDOG；
> - 第二个参数 params：表示命令参数，会将命令按空格分解出来，后面那部分内容，例如 test；
> - 注册宏采用 TARS_ADD_ADMIN_CMD_NORMAL 或 TARS_ADD_ADMIN_CMD_PREFIX 取决于你对命令执行顺序的要求；
> - 注册宏第一个参数表示命令字（不能包含空格），第二个参数表示对该命令执行的函数；

## 9.2. 基于对象接口处理

所谓基于对象接口的处理表示该命令接口是针对服务中的对象的。

例如针对 HelloImp 对象，这个时候如果发送一个命令给服务，则每个线程中的 HelloImp 的命令接口都会被执行一次，并且执行这些接口的过程和执行 HelloImp 的自身的接口是互斥的（即线程安全的）。

假设 HelloImp 有成员变量 string \_hello，需要发送命令通知每个 HelloImp 对象将\_hello 修改为自定义的值，步骤如下：

在 HelloImp 中增加处理函数

```cpp
bool HelloImp::procHello(const string& command, const string& params, string& result)
{
    _hello = params;
    return false;
}
```

在 HelloImp 的初始化中注册函数：

```cpp
void HelloImp::initialize()
{
    //注册处理函数：
    TARS_ADD_ADMIN_CMD_NORMAL("SETHELLO", HelloImp::procHello);
}
```

完成上面操作即可，在 web 页面上发送 SETHELLO test，则将\_hello 赋值为 test；

## 9.3. 发送管理命令

服务的管理命令的发送方式：通过 web 管理平台，将 TARS 服务发布到平台上，通过管理平台发送命令；

TARS 服务框架目前内置了多种命令：

> - tars.help //查看所有管理命令
> - tars.loadconfig //从配置中心, 拉取配置下来: tars.loadconfig filename
> - tars.setloglevel //设置滚动日志的等级: tars.setloglevel \[NONE, ERROR, WARN, DEBUG\]
> - tars.viewstatus //查看服务状态
> - tars.connection //查看当前链接情况
> - tars.loadproperty //使配置文件的 property 信息生效
> - tars.setdyeing //设置染色信息 tars.setdyeing key servant \[interface\]

## 10. 统计上报

所谓的上报统计信息是 Tars 框架内部，向 tarsstat 上报调用耗时等信息的逻辑。不需用户开发，在程序初始化时正确设置相关信息后，框架内部（包括客户端和服务端）即可自动上报。

客户端调用上报接口后，实际先暂存在内存中，当到达某个时间点后才正式上报到 tarsstat 服务\(默认是 1 分钟上报一次\)。我们称两个上报时间点之间的时间为一个统计区间，在一个统计区间相同 key 进行累加、对比等操作。 示例代码如下

```cpp
//初始化通信器
CommunicatorPtr pcomm = new Communicator();
//初始化tarsregistry服务地址
pcomm->setProperty("locator", "tars.tarsregistry.QueryObj@tcp -h xxx.xxx.xxx.xx -p xxxx"
//初始化stat服务
pcomm->setProperty("stat", "tars.tarsstat.StatObj");
//设置上报间隔
pcomm->setProperty("report-interval", "1000");
//设置上报主调名称
pcomm->setProperty("modulename", "Test.TestServer_Client");
```

说明：

> - 如果主调的服务是在 web 管理系统上部署的，不需要定义 Communicator，也不需要设置 tarsregistry、tarsstat 等配置，服务会自动上报
> - 如果主调的服务或程序不是在 web 管理系统上部署，则需要定义 Communicator，设置 tarsregistry、tarsstat 等配置，这样在 web 管理系统上才能查看被调服务的服务监控时，才有流量
> - 上报数据是定时上报的，可以在通信器的配置中设置;

## 11. 异常上报

为了更好监控，TARS 框架支持在程序中将异常直接上报到 tarsnotify，并可以在 WEB 管理页面上查看到。

框架提供的三种宏用户上报不同种类的异常：

```cpp
//上报普通信息
TARS_NOTIFY_NORMAL(info)
//上报警告信息
TARS_NOTIFY_WARN(info)
//上报错误信息
TARS_NOTIFY_ERROR(info)
```

Info 为字符串, 可以直接上报字符串到 tarsnotify，页面可以看到上报的字符串，后续可以根据上报的信息进行报警。

## 12. 属性统计

为了方便业务做统计，TARS 框架中也支持能够在 web 管理平台看上报的信息展示。

目前支持的统计类型包括以下几种：

> - 求和（sum）
> - 平均（avg）
> - 分布（distr）
> - 最大值（max）
> - 最小值（min）
> - 计数（count）

示例代码如下：

```cpp
//初始化通信器
Communicator _comm;
//初始化property服务地址
_comm.setProperty("property", "tars.tarsproperty.PropertyObj@ tcp -h xxx.xxx.xxx.xxx -p xxxx");

//初始化分布数据范围
vector<int> v;
v.push_back(10);
v.push_back(30);
v.push_back(50);
v.push_back(80);
v.push_back(100);

//创建test1属性，该属性用到了上诉所有的集中统计方式，注意distrv的初始化
PropertyReportPtr srp = _comm.getStatReport()->createPropertyReport("test1",
PropertyReport::sum(),
PropertyReport::avg(),
PropertyReport::count(),
PropertyReport::max(),
PropertyReport::min(),
PropertyReport::distr(v));

//上报数据，property只支持int类型的数据上报
int iValue = 0;
for ( int i = 0; i < 10000000; i++ )
{
        sleep(1);
     srp->report(rand() % 100 );
}
```

说明：

> - 上报数据是定时上报的，可以在通信器的配置中设置，目前是 1 分钟一次;
> - 创建 PropertyReportPtr 的函数：createPropertyReport 的参数可以是任何统计方式的集合，例子中是用到 6 六种统计方式，通常情况下可能只需要用到一种或两种;
> - 注意调用 createPropertyReport 时，必须在服务启动以后创建并保存好创建的对象，后续拿这个对象 report 即可，不要每次使用的时候 create;
