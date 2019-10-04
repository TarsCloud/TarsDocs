# TarsCPP 快速入门

## 创建服务

### 运行tars脚本

```text
/usr/local/tars/cpp/script/create_tars_server.sh [App] [Server] [Servant]
```

本例中执行：/usr/local/tars/cpp/script/create\_tars\_server.sh TestApp HelloServer Hello

命令执行后，会在当前目录的TestApp/HelloServer/ 目录下，生成下面文件：

```text
HelloServer.h HelloServer.cpp Hello.tars HelloImp.h HelloImp.cpp makefile
```

这些文件，已经包含了最基本的服务框架和默认测试接口实现。

### tars接口文件

定义tars接口文件的语法和使用，参见tars\_tup.md。

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

采用tars2cpp工具自动生成c++文件：/usr/local/tars/cpp/tools/tars2cpp hello.tars会生成hello.h文件，里面包含客户端和服务端的代码。

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

## 服务编译

进入代码目录,首先做

```text
make cleanall
make	
make tar
```

## 扩展功能

Tars框架提供了接口定义语言的功能，可以在tars文件中，增加一下接口和方法，扩展服务的功能。

可以修改由create\_tars\_server.sh生成的tars文件，以下3个接口方法中，test是默认生成的，testHello是新增加的接口。

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

## 客户端同步/异步调用服务

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

编写makefile,里面包含刚才通过make release生成的/home/tarsproto/APP/Server目录下的mk文件，如下：

```text
#-----------------------------------------------------------------------
APP         :=TestApp
TARGET      :=TestHelloClient
CONFIG      :=
STRIP_FLAG  := N

INCLUDE     += 
LIB         +=
#-----------------------------------------------------------------------
include /home/tarsproto/TestApp/HelloServer/HelloServer.mk
include /usr/local/tars/cpp/makefile/makefile.tars
#-----------------------------------------------------------------------
```

make出目标文件，上传到能访问服务器的环境中进行运行测试即

