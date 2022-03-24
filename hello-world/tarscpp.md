# 目录

> - [创建服务](#chapter-1)
> - [服务实现](#chapter-2)
> - [服务编译](#chapter-3)
> - [扩展接口](#chapter-4)
> - [远程调用](#chapter-5)
> - [其它](#chapter-6)

## 1 <span id="chapter-1"></span> 创建服务

请务必先阅读 [concept](../base/tars-concept.md) and [spec](../dev/tarscpp/tars-spec.md)

```text
/usr/local/tars/cpp/script/cmake_tars_server.sh [App] [Server] [Servant]
```

本例中执行：/usr/local/tars/cpp/script/cmake_tars_server.sh TestApp HelloServer Hello

命令执行后，会在当前目录的 TestApp/HelloServer/src 目录下，生成下面文件：

```text
HelloServer.h HelloServer.cpp Hello.tars HelloImp.h HelloImp.cpp CMakeLists.txt
```

编译服务

```
cd build;
cmake ..
make -j4
```

这些文件，已经包含了最基本的服务框架和默认测试接口实现。

## 2 <span id="chapter-3"></span> 服务实现

### tars 接口文件

定义 tars 接口文件的语法和使用，参见 [tars_tup](../base/tars-tup.md)

如下：

Hello.tars：

```text
module TestApp
{

interface Hello
{
    int test();
};

};

```

采用 tars2cpp 工具自动生成 c++文件：/usr/local/tars/cpp/tools/tars2cpp hello.tars 会生成 hello.h 文件，里面包含客户端和服务端的代码( 编译时会自动处理)。

### HelloImp 是 Servant 的接口实现类

实现服务定义的 tars 件中的接口，如下：

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

};
/////////////////////////////////////////////////////
#endif
```

HelloImp.cpp:

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
```

### HelloServer 是服务的实现类

如下:

HelloServer.h:

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

HelloServer.cpp

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
/////////////////////////////////////////////////////////////////
```

**每个 Servant(Obj)对象对应一个业务处理线程, 因此如果是成 HelloImp 的成员变量, 并且只被当前的 HelloImp 对象处理, 是不需要加锁的**

## 3 <span id="chapter-3"></span> 服务编译

进入代码目录,首先做

```cpp
cd build
cmake ..
make -j4
make HelloServer-tar
make HelloServer-upload
```

## 4 <span id="chapter-4"></span> 扩展功能

Tars 框架提供了接口定义语言的功能，可以在 tars 文件中，增加一下接口和方法，扩展服务的功能。

可以修改由 cmake_tars_server.sh 生成的 tars 文件，以下 3 个接口方法中，test 是默认生成的，testHello 是新增加的接口。

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

使用/usr/local/tars/cpp/tools/tars2cpp hello.tars,重新生成 hello.h。

修改 HelloImp.h/HelloImp.cpp，实现新的接口代码。

其中 HelloImp.h 中继承 Hello 类的 testHello 方法：

```cpp
virtual int testHello(const std::string &sReq, std::string &sRsp, tars::TarsCurrentPtr current);
```

HelloImp.cpp 实现 testHello 方法：

```cpp
int HelloImp::testHello(const std::string &sReq, std::string &sRsp, tars::TarsCurrentPtr current)
{
    TLOGDEBUG("HelloImp::testHellosReq:"<<sReq<<endl);
    sRsp = sReq;
    return 0;
}
```

重新 make cleanall;make;make tar，会重新生成 HelloServer.tgz 发布包。

## 5 <span id="chapter-5"></span> 客户端同步/异步调用服务

在开发环境上，创建/home/tarsproto/\[APP\]/\[Server\]目录。

例如：/home/tarsproto/TestApp/HelloServer 在刚才编写服务器的代码目录下，

执行 make release 这时会在/home/tarsproto/TestApp/HelloServer 目录下生成 h、tars 和 mk 文件。

这样在有某个服务需要访问 HelloServer 时，就直接引用 HelloServer 服务 make release 的内容，不需要把 HelloServer 的 tars 拷贝过来（即代码目录下不需要存放 HelloServer 的 tars 文件）。

建立客户端代码目录，如 TestHelloClient/。

编写 main.cpp，创建实例并调用刚编写的接口函数进行测试。

同步方式：

```cpp
#include <iostream>
#include "servant/Communicator.h"
#include "Hello.h"

using namespace std;
using namespace TestApp;
using namespace tars;

int main(int argc,char ** argv)
{
    Communicator comm;

    try
    {
        HelloPrx prx;
        comm.stringToProxy("TestApp.HelloServer.HelloObj@tcp -h 10.120.129.226 -p 20001" , prx);

        try
        {
            string sReq("hello world");
            string sRsp("");

            int iRet = prx->testHello(sReq, sRsp);
            cout<<"iRet:"<<iRet<<" sReq:"<<sReq<<" sRsp:"<<sRsp<<endl;

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

    return 0;
}
```

异步方式

```cpp
#include <iostream>
#include "servant/Communicator.h"
#include "Hello.h"

using namespace std;
using namespace TestApp;
using namespace tars;

class HelloCallBack : public HelloPrxCallback
{
public:
    HelloCallBack(){}

    virtual ~HelloCallBack(){}

    virtual void callback_testHello(tars::Int32 ret,  const std::string& sRsp)
    {
        cout<<"callback_testHello ret:"<< ret << "|sRsp:" << sRsp <<endl;
    }

    virtual void callback_testHello_exception(tars::Int32 ret)
    {
        cout<<"callback_testHello_exception ret:"<< ret <<endl;
    }
};

int main(int argc,char ** argv)
{
    Communicator comm;

    try
    {
        HelloPrx prx;
        comm.stringToProxy("TestApp.HelloServer.HelloObj@tcp -h 10.120.129.226 -p 20001" , prx);

        try
        {
            string sReq("hello world");
            HelloPrxCallbackPtr cb = new HelloCallBack();
            prx->async_testHello(cb, sReq);
            cout<<" sReq:"<<sReq<<endl;
        }
        catch(exception &ex)
        {
            cerr<<"ex:"<<ex.what() <<endl;
        }
        catch(...)
        {
            cerr<<"unknown exception."<<endl;
        }
    }
    catch(exception& e)
    {
        cerr<<"exception:"<<e.what() <<endl;
    }
    catch (...)
    {
        cerr<<"unknown exception."<<endl;
    }

    getchar();

    return 0;
}
```

编写 makefile,里面/home/tarsproto/APP/Serve, 如下：

```text
#-----------------------------------------------------------------------
APP         :=TestApp
TARGET      :=TestHelloClient
CONFIG      :=
STRIP_FLAG  := N

INCLUDE     += -I/home/tarsproto/TestApp/HelloServer/
LIB         +=
#-----------------------------------------------------------------------
include /usr/local/tars/cpp/makefile/makefile.tars
#-----------------------------------------------------------------------
```

make 出目标文件，上传到能访问服务器的环境中进行运行测试即

**也强烈建议你用 cmake 管理, 方式和服务端一样**

## 6 <span id="chapter-6"></span> 其它

其他你可能需要知道的重点:

- examples 下有几个非常重要的调用例子:同步, 异步, 协程, 代理模式, push 模式, HTTP 服务支持等, 建议仔细读一读
- 代码中的 Communicator 是管理客户端资源的, 建议做成全局, 如果不是独立的 Client 客户端, 而是在服务框架中, 直接获取框架提供好的 Communicator, 参见 ProxyServer
- 上述 Client 例子中`comm.stringToProxy("TestApp.HelloServer.HelloObj@tcp -h 10.120.129.226 -p 20001" , prx);` 指定了 HelloServer 的 ip:port, 实际情况下, 当你的服务部署在框架上, 需要调用另外一个服务时, 只需要: `comm.stringToProxy("TestApp.HelloServer.HelloObj")`即可, 框架会自动寻址后端的 HelloServer 服务, 并自动完成容灾切换
