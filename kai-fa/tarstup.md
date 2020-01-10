# 统一通信协议 TarsTup

## TUP概述

### TUP是什么

TUP（Tars Uni-Protocol的简称），Tars统一协议，是基于Tars编码的命令字（Command）层协议的封装。

### TUP能做什么

1.支持java、c++等语言

2.支持对象的序列化和反序列化

3.支持协议动态扩展

4.提供put/get泛型接口，快速实现客户端/服务端的编解码

5.序列化的数据可用于网络传输或者持久化存储

6.支持直接调用Tars的服务端

### TUP不能做什么

1.只做协议封装，不包含网络层

2.不支持数据压缩（可在业务层处理）

3.不支持加密协议（可在业务层处理）

### 依赖和约束

1.依赖Tars协议，TUP用到的结构体对象必须通过Tars定义后生成

2.依赖各个语言的代码生成工具，如：tars2cpp/tars2java等

3.TarsUniPacket中封装的Tars相关的接口（如getTars.../setTars...），只有在调用Tars服务是需要用到

4.使用过程中，使用UniPacket完成请求与相应的数据传递，其中ServantName（服务对象名） 与FuncName（接口名）为必须设定的参数，否则编码失败

5.不建议get/put太多数据，如果有比较多数据，建议在tars文件中组成一个struct，然后put/get到UniPacket中，便于提高效率和减少网络包大小

6.UniPacket编码后的结果在包头包含了4个字节网络字节序的包长信息，长度包括包头，接收方收到包后需根据包头的内容，判断包长，确保包完整后，传入解码接口进行解码（无需去掉包头）

7.Tars c++语言的string 类型接口不能含二进制数据，二进制数据用vector传输

## TUP使用

### 类结构图

![](../assets/tars_tupclass.png)

### 类的使用

1.RequestPacket：请求包及回应包的基类，通过tars文件定义生成，和Tars服务的基础包一致，一般不直接使用。

2.UniAttribute：属性类，用户可以通过对该类的对象进行操作，添加属性和获取属性，类提供了put/get泛型接口，并可实现编解码。编码序列化后的字节流可用于压缩、加密，网络传输或持久化存储，在需要的时候反序列化出原对象。

3.UniPacket：请求回应包类，继承于UniAttribute，可以添加请求的属性值，设置需要请求远程对象及方法名，编码后发送到服务端，服务端解码后可获取属性参数进行处理。服务端处理完请求后同样通过该类的对象返回结果，客户端解码获取处理结果。

4.TarsUniPacket：Tars请求回应包类，继承于UniPacket，调用Tars远程服务的时候使用，用户添加属性及设置相关属性后，进行编码，组成请求包通过网络发到Tars服务进行处理。Tars服务端收到TUP协议的请求，处理完后会以该类的对象组返回包返回给客户端。客户端收包后使用该类进行解码处理，获取结果。

### 使用TUP协议调用Tars服务

1.客户端调用时，使用TarsUniPacket对象进行请求包的参数设置及输入参数赋值，其中必须指定的请求参数信息包括：

```text
setRequestId();           设置消息id，递增

setServantName("");       设置远程对象名称

setFuncName("");          设置远程接口名称

setTarsPacketType();       包类型版本，TUP协议默认为3
```

针对特定远程接口的调用，只需通过put接口对输入参数进行赋值，属性名称为tars接口定义的参数名称，比如对于接口：

```text
int testFunc(string inputString, int inputInt, out string outputString);
```

输入参数赋值的方式是：

```text
TarsUniPacket<> req;

req.put<string>("inputString", "testInput");

req.put<Int32>("inputInt", 12345);

req.encode(buff);
```

TarsUniPacket对象必须为tars定义的每个输入参数设置属性值，否则服务端处理请求时会返回缺少某个属性值的异常错误，输出参数也可以作为输入，但是不是必选。

put接口的模板类型选用tars参数定义的对应的类型，但枚举类型例外，需换用Int32作为模板类型赋属性值。

2、TUP返回包同样使用TarsUniPacket对象进行解码，解码后使用getTarsResultCode\(\)接口获取tars服务的处理结果，0为成功，非0为失败，失败的原因可以通过getTarsResultDesc\(\)接口获取错误描述。

返回成功的结果包的各输出参数使用tars定义的输出参数名称作为属性名称去获取，接口的返回值使用空字符串的属性名去获取。

如上述接口获取返回结果的方式是：

```text
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

## TUP各版本接口介绍

### Linux c++

#### 类接口

UniAttribute类

| 公共接口 | 功能描述 |
| :--- | :--- |
| template void put\(const string& name, const T& t\) | 添加属性值 |
| template void get\(const string& name, T& t\) | 获取属性值 |
| template T get\(const string& name\) | 获取属性值 |
| template void getByDefault\(const string& name, T& t, const T& def\) | 获取属性值\(忽略异常，def为缺省值\) |
| template T getByDefault\(const string& name, const T& def\) | 获取属性值\(忽略异常，def为缺省值\) |
| void clear\(\) | 清除全部属性值 |
| void encode\(string& buff\) | 将属性对象编码到字节流 |
| void encode\(vector& buff\) | 将属性对象编码到字节流 |
| void encode\(char\* buff, size\_t & len\) | 将属性对象编码到字节流 |
| void decode\(const char\* buff, size\_t len\) | 将字节流解码 |
| void decode\(const vector& buff\) | 将字节流解码 |
| const map&lt;string, vector &gt;& getData\(\) const | 获取已有的属性 |
| bool isEmpty\(\) | 判断属性集合是否为空 |
| size\_t size\(\) | 获取属性集合大小 |
| bool containsKey\(const string & key\) | 判断属性是否存在 |

UniPacket类

| 公共接口 | 功能描述 |
| :--- | :--- |
| void setVersion\(short iVer\) | 设置协议版本号 |
| UniPacket createResponse\(\) | 通过请求包生成回应包，生成过程会从请求包获取请求ID、对象名称、方法名等回填到回应包中 |
| void encode\(string& buff\) | 将对象编码到字节流 |
| void encode\(vector& buff\) | 将对象编码到字节流 |
| void encode\(char\* buff, size\_t & len\) | 将对象编码到字节流 |
| void decode\(const char\* buff, size\_t len\) | 将字节流解码，其中len传入buffer长度，输出解码结果的长度 |
| tars::Short getVersion\(\) const | 获取协议版本号 |
| tars::Int32 getRequestId\(\) const | 获取消息ID |
| void setRequestId\(tars::Int32 value\) | 设置请求ID |
| const std::string& getServantName\(\) const | 获取对象名称 |
| void setServantName\(const std::string& value\) | 设置对象名称\(编码时对象名不能为空，否则编码失败\) |
| const std::string& getFuncName\(\) const | 获取方法名 |
| void setFuncName\(const std::string& value\) | 设置方法名\(编码时方法名不能为空，否则编码失败\) |

TarsUniPacket类

| 公共接口 | 功能描述 |
| :--- | :--- |
| void setTarsVersion\(tars::Short value\) | 设置协议版本 |
| void setTarsPacketType\(tars::Char value\) | 设置调用类型 |
| void setTarsMessageType\(tars::Int32 value\) | 设置消息类型 |
| void setTarsTimeout\(tars::Int32 value\) | 设置超时时间 |
| void setTarsBuffer\(const vectortars::Char& value\) | 设置参数编码内容 |
| void setTarsContext\(const map&lt;std::string, std::string&gt;& value\) | 设置上下文 |
| void setTarsStatus\(const map&lt;std::string, std::string&gt;& value\) | 设置特殊消息的状态值 |
| tars::Short getTarsVersion\(\) const | 获取协议版本 |
| tars::Char getTarsPacketType\(\) const | 获取调用类型 |
| tars::Int32 getTarsMessageType\(\) const | 获取消息类型 |
| tars::Int32 getTarsTimeout\(\) const | 获取超时时间 |
| const vectortars::Char& getTarsBuffer\(\) const | 获取参数编码后内容 |
| const map&lt;std::string, std::string&gt;& getTarsContext\(\) const | 获取上下文 |
| const map&lt;std::string, std::string&gt;& getTarsStatus\(\) const | 获取特殊消息的状态值 |
| tars::Int32 getTarsResultCode\(\) const | 获取Tars服务处理结果码，0为成功，非0为失败 |
| string getTarsResultDesc\(\) const | 获取Tars服务处理结果描述 |

#### 使用注意

以上接口调用出错将抛出runtime\_error 异常。

#### 使用例子

参见cpp/test/testServant/testTup/下的示例程序

### Java

#### 类接口

UniAttribute类

UniPacket类

TarsUniPacket类

#### 使用注意

1.目前TUP支持基本类型, TarsStruct，已经存放基本类型或TarsStruct的map和list。对数组只支持byte\[\]，放入别的类型会抛IllegalArgumentException异常；

2.put和get方法调用出错将抛出ObjectCreateException 异常；



