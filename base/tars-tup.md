# 统一通信协议 TarsTup

## TARS 底层协议

在介绍 TUP 之前, 先介绍 Tars 服务之间通讯的底层协议格式, 具体参见 RequestF.tars & BaseF.tars

```RequestF.tars
module tars
{
    //请求包体
    struct RequestPacket
    {
        1  require short        iVersion;
        2  require byte         cPacketType  = 0;
        3  require int          iMessageType = 0;
        4  require int          iRequestId;
        5  require string       sServantName = "";
        6  require string       sFuncName    = "";
        7  require vector<byte> sBuffer;
        8  require int          iTimeout     = 0;
        9  require map<string, string> context;
        10 require map<string, string> status;
    };

    //响应包体
    struct ResponsePacket
    {
        1 require short         iVersion;
        2 require byte          cPacketType  = 0;
        3 require int           iRequestId;
        4 require int           iMessageType = 0;
        5 require int           iRet         = 0;
        6 require vector<byte>  sBuffer;
        7 require map<string, string> status;
        8 optional string        sResultDesc;
        9 optional map<string, string> context;
    };
};
```

```BaseF.tars
module tars
{
    ////////////////////////////////////////////////////////////////
    // 定义协议的版本号

    const short TARSVERSION  = 0x01;
    const short TUPVERSION  = 0x03;

    ////////////////////////////////////////////////////////////////
    // 定义消息的类型

    // 普通调用
    const byte TARSNORMAL  = 0x00;

    // 单向调用
    const byte TARSONEWAY  = 0x01;

    ////////////////////////////////////////////////////////////////
    // TARS定义的返回码

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

    /////////////////////////////////////////////////////////////////
    // 定义按位的消息状态类型,可复合

    const int TARSMESSAGETYPENULL     = 0x00;    //无状态
    const int TARSMESSAGETYPEHASH     = 0x01;    //HASH消息
    const int TARSMESSAGETYPEGRID     = 0x02;    //灰度消息
    const int TARSMESSAGETYPEDYED     = 0x04;    //染色消息
    const int TARSMESSAGETYPESAMPLE   = 0x08;    //stat采样消息
    const int TARSMESSAGETYPEASYNC    = 0x10;    //异步调用程序
    //const int TARSMESSAGETYPELOADED = 0x20;    //按负载值调用程序
    //const int TARSMESSAGETYPESETED = 0x40;     //按set规则调用类型，此字段后面将不使用
    const int TARSMESSAGETYPESETNAME = 0x80;     //按setname规则调用类型
    const int TARSMESSAGETYPETRACK   = 0x100;    //track调用链消息
    /////////////////////////////////////////////////////////////////
}
```

RequestPacket & ResponsePacket 是两个 Tars 服务通信的底层协议, 简单的说如果不通过 Tars 的 Communicator 来通信, 你可以自己组包来完成和 Tars 服务的通信(当然这会有相当的难度, 需要你对 Tars 底层协议非常熟悉), 因此为了方便 TUP 构建出来解决这种问题.

## TUP 概述

### TUP 是什么

TUP（Tars Uni-Protocol 的简称），Tars 统一协议，是基于 Tars 编码的命令字（Command）层协议的封装。

它的存在最早是为了方便各语言客户端调用 Tars 服务, 只提供了编解码, 网路通讯需要自己实现, 当然如果 Tars 提供了这个语言的客户端, 那么就不需要再使用 TUP 协议来调用 Tars 服务了.

在正式使用中, 我们有这种场景, 以供大家参考:

- 后端服务使用 Tars 服务实现
- 实现一个 HTTP+TUP 协议的全异步代理, 即入口是 HTTP+TUP, 出口是 TARS 协议的代理
- Android/Ios 通过 TUP 协议, 并通过这个代理服务, 完成和后台任何 Tars 服务的通信

**当然, 我们未来会提供一种机制, 可以采用 http+json 来完成后端 Tars 服务的通信**

### TUP 能做什么

1.支持 java、c++等语言

2.支持对象的序列化和反序列化

3.支持协议动态扩展

4.提供 put/get 泛型接口，快速实现客户端/服务端的编解码

5.支持直接调用 Tars 的服务端

### TUP 不能做什么

1.只做协议封装，不包含网络层

2.不支持数据压缩（可在业务层处理）

3.不支持加密协议（可在业务层处理）

### 依赖和约束

1.依赖 Tars 协议，TUP 用到的结构体对象必须通过 Tars 定义后生成

2.依赖各个语言的代码生成工具，如：tars2cpp/tars2java 等

3.TarsUniPacket 中封装的 Tars 相关的接口（如 getTars.../setTars...），只有在调用 Tars 服务是需要用到

4.使用过程中，使用 UniPacket 完成请求与相应的数据传递，其中 ServantName（服务对象名） 与 FuncName（接口名）为必须设定的参数，否则编码失败

5.不建议 get/put 太多数据，如果有比较多数据，建议在 tars 文件中组成一个 struct，然后 put/get 到 UniPacket 中，便于提高效率和减少网络包大小

6.UniPacket 编码后的结果在包头包含了 4 个字节网络字节序的包长信息，长度包括包头，接收方收到包后需根据包头的内容，判断包长，确保包完整后，传入解码接口进行解码（无需去掉包头）

7.Tars c++语言的 string 类型接口建议不要包含二进制数据，二进制数据用 vector 传输

## TUP 使用

### 类结构图

![](../assets/tars_tupclass.png)

### 类的使用

1.RequestPacket：请求包及回应包的基类，通过 tars 文件定义生成，和 Tars 服务的基础包一致，一般不直接使用。

2.UniAttribute：属性类，用户可以通过对该类的对象进行操作，添加属性和获取属性，类提供了 put/get 泛型接口，并可实现编解码。编码序列化后的字节流可用于压缩、加密，网络传输或持久化存储，在需要的时候反序列化出原对象。

3.UniPacket：请求回应包类，继承于 UniAttribute，可以添加请求的属性值，设置需要请求远程对象及方法名，编码后发送到服务端，服务端解码后可获取属性参数进行处理。服务端处理完请求后同样通过该类的对象返回结果，客户端解码获取处理结果。

4.TarsUniPacket：Tars 请求回应包类，继承于 UniPacket，调用 Tars 远程服务的时候使用，用户添加属性及设置相关属性后，进行编码，组成请求包通过网络发到 Tars 服务进行处理。Tars 服务端收到 TUP 协议的请求，处理完后会以该类的对象组返回包返回给客户端。客户端收包后使用该类进行解码处理，获取结果。

### 使用 TUP 协议调用 Tars 服务

以下是 C++版本例子, 其他语言类似.

1.客户端调用时，使用 TarsUniPacket 对象进行请求包的参数设置及输入参数赋值，其中必须指定的请求参数信息包括：

```cpp
setRequestId();           设置消息id，递增

setServantName("");       设置远程对象名称

setFuncName("");          设置远程接口名称

setTarsPacketType();       包类型版本，TUP协议默认为3
```

针对特定远程接口的调用，只需通过 put 接口对输入参数进行赋值，属性名称为 tars 接口定义的参数名称，比如对于接口：

```text
int testFunc(string inputString, int inputInt, out string outputString);
```

输入参数赋值的方式是：

```cpp
TarsUniPacket<> req;

req.put<string>("inputString", "testInput");

req.put<Int32>("inputInt", 12345);

req.encode(buff);
```

**这里 inputString 对应 testFunc 的第一个参数, 以此类推**

TarsUniPacket 对象必须为 tars 定义的每个输入参数设置属性值，否则服务端处理请求时会返回缺少某个属性值的异常错误，输出参数也可以作为输入，但是不是必选。

put 接口的模板类型选用 tars 参数定义的对应的类型，但枚举类型例外，需换用 Int32 作为模板类型赋属性值。

2、TUP 返回包同样使用 TarsUniPacket 对象进行解码，解码后使用 getTarsResultCode\(\)接口获取 tars 服务的处理结果，0 为成功，非 0 为失败，失败的原因可以通过 getTarsResultDesc\(\)接口获取错误描述。

返回成功的结果包的各输出参数使用 tars 定义的输出参数名称作为属性名称去获取，接口的返回值使用空字符串的属性名去获取。

如上述接口获取返回结果的方式是：

```cpp
TarsUniPacket<> rsp;

rsp.decode(recvBuff, recvLen);

if(rsp.getTarsResultCode() == 0)
{
    int ret = rsp.get<int32_t>("");						//获取返回值
    string retString = rsp.get<string>("outputString"); //获取输出参数
}
else
{
    cout <<　rsp.getTarsResultDesc() << endl;
}
```

## TUP 各版本接口介绍

### Linux c++

#### 类接口

UniAttribute 类

| 公共接口                                                             | 功能描述                             |
| :------------------------------------------------------------------- | :----------------------------------- |
| template void put\(const string& name, const T& t\)                  | 添加属性值                           |
| template void get\(const string& name, T& t\)                        | 获取属性值                           |
| template T get\(const string& name\)                                 | 获取属性值                           |
| template void getByDefault\(const string& name, T& t, const T& def\) | 获取属性值\(忽略异常，def 为缺省值\) |
| template T getByDefault\(const string& name, const T& def\)          | 获取属性值\(忽略异常，def 为缺省值\) |
| void clear\(\)                                                       | 清除全部属性值                       |
| void encode\(string& buff\)                                          | 将属性对象编码到字节流               |
| void encode\(vector& buff\)                                          | 将属性对象编码到字节流               |
| void encode\(char\* buff, size_t & len\)                             | 将属性对象编码到字节流               |
| void decode\(const char\* buff, size_t len\)                         | 将字节流解码                         |
| void decode\(const vector& buff\)                                    | 将字节流解码                         |
| const map&lt;string, vector &gt;& getData\(\) const                  | 获取已有的属性                       |
| bool isEmpty\(\)                                                     | 判断属性集合是否为空                 |
| size_t size\(\)                                                      | 获取属性集合大小                     |
| bool containsKey\(const string & key\)                               | 判断属性是否存在                     |

UniPacket 类

| 公共接口                                        | 功能描述                                                                              |
| :---------------------------------------------- | :------------------------------------------------------------------------------------ |
| void setVersion\(short iVer\)                   | 设置协议版本号                                                                        |
| UniPacket createResponse\(\)                    | 通过请求包生成回应包，生成过程会从请求包获取请求 ID、对象名称、方法名等回填到回应包中 |
| void encode\(string& buff\)                     | 将对象编码到字节流                                                                    |
| void encode\(vector& buff\)                     | 将对象编码到字节流                                                                    |
| void encode\(char\* buff, size_t & len\)        | 将对象编码到字节流                                                                    |
| void decode\(const char\* buff, size_t len\)    | 将字节流解码，其中 len 传入 buffer 长度，输出解码结果的长度                           |
| tars::Short getVersion\(\) const                | 获取协议版本号                                                                        |
| tars::Int32 getRequestId\(\) const              | 获取消息 ID                                                                           |
| void setRequestId\(tars::Int32 value\)          | 设置请求 ID                                                                           |
| const std::string& getServantName\(\) const     | 获取对象名称                                                                          |
| void setServantName\(const std::string& value\) | 设置对象名称\(编码时对象名不能为空，否则编码失败\)                                    |
| const std::string& getFuncName\(\) const        | 获取方法名                                                                            |
| void setFuncName\(const std::string& value\)    | 设置方法名\(编码时方法名不能为空，否则编码失败\)                                      |

UniPacket 集成至 UniAttribute, 你可以构建自己的服务并使用 UniPacket 来完成服务通信, 这里 setServantName 可以作为协议主命令字, getFuncName 作为协议子命令字, UniPacket 底层包结构即 RequestPackage.

TarsUniPacket 类

| 公共接口                                                                | 功能描述                                        |
| :---------------------------------------------------------------------- | :---------------------------------------------- |
| void setTarsVersion\(tars::Short value\)                                | 设置协议版本                                    |
| void setTarsPacketType\(tars::Char value\)                              | 设置调用类型                                    |
| void setTarsMessageType\(tars::Int32 value\)                            | 设置消息类型                                    |
| void setTarsTimeout\(tars::Int32 value\)                                | 设置超时时间                                    |
| void setTarsBuffer\(const vectortars::Char& value\)                     | 设置参数编码内容                                |
| void setTarsContext\(const map&lt;std::string, std::string&gt;& value\) | 设置上下文                                      |
| void setTarsStatus\(const map&lt;std::string, std::string&gt;& value\)  | 设置特殊消息的状态值                            |
| tars::Short getTarsVersion\(\) const                                    | 获取协议版本                                    |
| tars::Char getTarsPacketType\(\) const                                  | 获取调用类型                                    |
| tars::Int32 getTarsMessageType\(\) const                                | 获取消息类型                                    |
| tars::Int32 getTarsTimeout\(\) const                                    | 获取超时时间                                    |
| const vectortars::Char& getTarsBuffer\(\) const                         | 获取参数编码后内容                              |
| const map&lt;std::string, std::string&gt;& getTarsContext\(\) const     | 获取上下文                                      |
| const map&lt;std::string, std::string&gt;& getTarsStatus\(\) const      | 获取特殊消息的状态值                            |
| tars::Int32 getTarsResultCode\(\) const                                 | 获取 Tars 服务处理结果码，0 为成功，非 0 为失败 |
| string getTarsResultDesc\(\) const                                      | 获取 Tars 服务处理结果描述                      |

TarsUniPacket 继承 UniPacket, 当你需要和 Tars 服务通信时, 你可以使用 TarsUniPacket 来完成服务通信, TarsUniPacket 底层包结构即 RequestPackage.

#### 使用注意

以上接口调用出错将抛出 runtime_error 异常。

#### 使用例子

参见 cpp/test/testServant/testTup/下的示例程序

### Java

#### 类接口

UniAttribute: 与 c++相似

UniPacket: 与 c++相似

TarsUniPacket: 与 c++相似

#### 使用注意

1.目前 TUP 支持基本类型, TarsStruct，已经存放基本类型或 TarsStruct 的 map 和 list。对数组只支持 byte\[\]，放入别的类型会抛 IllegalArgumentException 异常；

2.put 和 get 方法调用出错将抛出 ObjectCreateException 异常；
