# 目录
> * [创建服务](#chapter-1)
> * [服务实现](#chapter-2)
> * [服务编译](#chapter-3)
> * [扩展接口](#chapter-4)
> * [远程调用](#chapter-5)
> * [其它](#chapter-6)

## 1 <span id="chapter-1"></span> 创建服务

请务必先阅读 [concept](../base/tars-concept.md) and [spec](../dev/tarscpp/tars-spec.md)

```text
/usr/local/tars/cpp/script/cmake_tars_server.sh [App] [Server] [Servant]
```

本例中执行：/usr/local/tars/cpp/script/cmake\_tars\_server.sh TestApp HelloServer Hello

命令执行后，会在当前目录的TestApp/HelloServer/src 目录下，生成下面文件：

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

### tars接口文件

定义tars接口文件的语法和使用，参见 [tars_tup](../base/tars-tup.md)

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

采用tars2cpp工具自动生成c++文件：/usr/local/tars/cpp/tools/tars2cpp hello.tars会生成hello.h文件，里面包含客户端和服务端的代码( 编译时会自动处理)。

### HelloImp是Servant的接口实现类

实现服务定义的tars件中的接口，如下：

HelloImp.h

```text
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

```text
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

### HelloServer是服务的实现类

如下:

HelloServer.h:

```text
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

```text
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

**每个Servant(Obj)对象对应一个业务处理线程, 因此如果是成HelloImp的成员变量, 并且只被当前的HelloImp对象处理, 是不需要加锁的**

## 3 <span id="chapter-3"></span> 服务编译

进入代码目录,首先做

```text
cd build
cmake ..
make -j4
make HelloServer-tar
make HelloServer-upload
```

## 4 <span id="chapter-4"></span> 扩展功能

Tars框架提供了接口定义语言的功能，可以在tars文件中，增加一下接口和方法，扩展服务的功能。

可以修改由cmake\_tars\_server.sh生成的tars文件，以下3个接口方法中，test是默认生成的，testHello是新增加的接口。

```text
module TestApp
{

interface Hello
{
    int test();
    int testHello(string sReq, out string sRsp);
};

}; 
```

使用/usr/local/tars/cpp/tools/tars2cpp hello.tars,重新生成hello.h。

修改HelloImp.h/HelloImp.cpp，实现新的接口代码。

其中HelloImp.h中继承Hello类的testHello方法：

```text
virtual int testHello(const std::string &sReq, std::string &sRsp, tars::TarsCurrentPtr current);
```

HelloImp.cpp实现testHello方法：

```text
int HelloImp::testHello(const std::string &sReq, std::string &sRsp, tars::TarsCurrentPtr current)
{
    TLOGDEBUG("HelloImp::testHellosReq:"<<sReq<<endl);
    sRsp = sReq;
    return 0;
}
```

重新make cleanall;make;make tar，会重新生成HelloServer.tgz发布包。

## 5 <span id="chapter-5"></span> 客户端同步/异步调用服务

在开发环境上，创建/home/tarsproto/\[APP\]/\[Server\]目录。

例如：/home/tarsproto/TestApp/HelloServer在刚才编写服务器的代码目录下，

执行 make release 这时会在/home/tarsproto/TestApp/HelloServer目录下生成h、tars和mk文件。

这样在有某个服务需要访问HelloServer时，就直接引用HelloServer服务make release的内容，不需要把HelloServer的tars拷贝过来（即代码目录下不需要存放HelloServer的tars文件）。

建立客户端代码目录，如TestHelloClient/。

编写main.cpp，创建实例并调用刚编写的接口函数进行测试。

同步方式：

```text
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

```text
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

编写makefile,里面/home/tarsproto/APP/Serve, 如下：

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

make出目标文件，上传到能访问服务器的环境中进行运行测试即

**也强烈建议你用cmake管理, 方式和服务端一样**

## 6 <span id="chapter-6"></span> 其它 

其他你可能需要知道的重点:
- examples下有几个非常重要的调用例子:同步, 异步, 协程, 代理模式, push模式, HTTP服务支持等, 建议仔细读一读
- 代码中的Communicator是管理客户端资源的, 建议做成全局, 如果不是独立的Client客户端, 而是在服务框架中, 直接获取框架提供好的Communicator, 参见ProxyServer
- 上述Client例子中`comm.stringToProxy("TestApp.HelloServer.HelloObj@tcp -h 10.120.129.226 -p 20001" , prx);`  指定了HelloServer的ip:port, 实际情况下, 当你的服务部署在框架上, 需要调用另外一个服务时, 只需要: `comm.stringToProxy("TestApp.HelloServer.HelloObj")`即可, 框架会自动寻址后端的HelloServer服务, 并自动完成容灾切换
