## Tars 协议用例压测指南

> - [接口定义实例](#chapter-1)
> - [JSON 协议使用](#chapter-2)
> - [自定义协议使用](#chapter-3)
> - [压测停止](#chapter-4)
> - [压测结果](#chapter-5)

tb 工具支持两种协议(json 协议和自定义协议)方式来压测目标 tars service，一般推荐使用 json 协议。

以下步骤通过 2 个示例描述了如何使用 tb 客户端，之后持续发送 test 接口请求到服务端，服务端正确返回结果给 tb 客户端，tb 周期性统计回包情况。

### 1.1. <span id="chapter-1"></span>接口定义实例

编写 tars 文件如下，Demo.tars：

```cpp
module tars
{
    struct DemoReq
    {
        0 require  string aa;
        1 optional int    bb;
        2 optional vector<byte>  cc;
    };

    struct DemoRsp
    {
        0 require  DemoReq  rr;
        1 optional bool     ok;
    };

    interface DemoObj
    {
        int test(int a, int b);
        int echo(DemoReq req, out DemoRsp rsp);
    };
};
```

### 1.2. <span id="chapter-2"></span>Json 协议使用说明

##### 1.2.1. 用例文件生成

使用 tars2case 工具自动生成 tb 工具所需的输入文件，工具根据各 interface 的 rpc 函数生成各自的 case 结尾用例文件和 desc 结尾的描述文件，其中描述文件内容不许变动。

```sh
/usr/local/tars/cpp/tools/tars2case --json Demo.tars --dir=benchmark

cd benchmark && ls
echo.case  echo.desc  test.case  test.desc
```

##### 1.2.2. 用例文件修改

用例文件根据 tars2json 规则自动生成之后，用户可以根据场景需要修改用例文件 Json 内容，其中 vector 和 map 结构数据支持向上下增长，对于 vector<byte>格式内容支持 string 和 array 两种方式输入，采用 string 需要将原串进行 bin2hex 转换。 此外，为了规避压测过程中的热 key 效应，避免后端请求落到同一台单机上，tb 对 tars 结构中 Number 和 string 支持生成随机内容，通过如下两种随机方式(Value 必须以 string)。

1.  <范围随机值>用[1-100]表示，表示在 1-100 内随机出现, 必须是数字。
2.  <限定随机值>用[369,aaa,bbb]表示，表示在 369,aaa,bbb 中随机出现。

**用例内容**: 例如 echo 接口

```json
{
  "req": "string val",
  "bb": "[100-10000]",
  "cc": "123456A5B6"
}
```

##### 1.2.3. 压测启动

```sh
./tb -c 600 -s 6000 -D 192.168.31.1 -P 10505 -p json -S tars.DemoServer.DemoObj -M test -C test
```

tars 协议参数说明

```text
  -S                   tars服务servant名称
  -M                   tars服务接口方法
  -C(可选)             用例和描述文件的前缀，默认用函数名
```

### 1.3. <span id="chapter-3"></span>自定义协议使用说明

##### </span>1.3.1. 用例文件生成

使用 tars2case 工具自动生成 tb 工具所需的用例，工具根据各 interface 的 rpc 函数生成各自的 case，用户可以根据业务需要修改参数的值

```sh
/usr/local/tars/cpp/tools/tars2case Demo.tars --dir=benchmark

cd benchmark && ls
echo.case  test.case
```

##### </span> 1.3.2. 用例编写说明

文件分为上下部分，用"#"开头行分割，上半部分为 RPC 参数，下半部分为 RPC 调用参数的值，跟参数一一对应

- **参数帮助说明**：

1.  输入参数使用"|"符号进行分割，即 tars 参数中的","替换为"|"
2.  struct 的表示方法是:struct<tag require|optional 字段 1,字段 2,字段 3...>,如果 tag 从 0 开始，直接字段 1
3.  vector 的表示方法是:vector<类型>
4.  map 的表示方法:map<key 类型,value 类型>
5.  2、3、4 可以嵌套使用

- **参值帮助说明**：

1.  <strong>基本类型</strong>的随机值设置：
    <范围随机值>用[1-100]表示，表示在 1-100 内随机出现, 必须是数字
    <限定随机值>用[1,123,100]表示，表示在 1,123,100 中随机出现，可以是字符串
2.  输入参数每个参数一行,也就是 tars 参数列表中","的地方换成回车
3.  struct 的表示方法是:<字段值 1,字段值 2,字段值 3...>
4.  vector 的表示方法是:<值 1,值 2,值 3...>
5.  map 的表示方法:[key1=val1,key2=val2,key3=val3...]
6.  3、4、5 可以嵌套使用

- **例如**:

```text
vector<string>|struct<string, int>|map<string, string>
#######
<abc, def, tt, fbb>
<abc, 1>
[abc=def, dfd=bbb]
```

##### 1.3.3. 压测启动例子

```sh
./tb -c 600 -s 6000 -D 192.168.31.1 -P 10505 -p tars -S tars.DemoServer.DemoObj -M test -C test.txt
```

tars 协议参数说明

```text
  -S                   tars服务servant名称
  -M                   tars服务接口方法
  -C                   压测用例文件，详见[用例文件生成]和[用例编写说明]
```

### 1.4. <span id="chapter-4"></span>压测停止

主动停止： ctrl+C or killall tb，等待数秒之后输出最终统计结果。<br/>
被动停止： 压测持续时间默认为 1 小时，1 小时之后停止压测并输出统计结果，可以通过-I 参数调节压测时间。

### 1.5. <span id="chapter-5"></span>压测结果显示

![压测结果](../assets/tb_tars_result.png)
