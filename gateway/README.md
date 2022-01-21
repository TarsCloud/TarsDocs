# TarsGateway

## 简介

TarsGateway 是基于 tars 框架开发的一套通用 api 网关，请求为 http 协议，后端同时支持 tars-tup&tars-tars 协议、tars-json 协议、http 协议。 除了协议转发之外，还支持流量控制，黑白名单等功能。 详细使用文档参考[Tars 文档](https://tarscloud.github.io/TarsDocs/)

## 支持版本说明

- TarsCpp: >= v2.4.5
- TarsJava: >= v1.7.2
- TarsGo: >= v1.1.4
- TarsNode: rpc: >= v2.0.14, stream: >= v2.0.3, tars2node: >= v20200707
- TarsPHP: tars-server: >= v0.6.0

## 安装

### 支持一键安装（需要先具备 tarscpp 编译环境, 版本>=v2.4.4）：

```
    git clone https://github.com/TarsCloud/TarsGateway.git
    cd TarsGateway/install;

    ./install.sh tarsweb_base token node_ip gateway_db_ip gateway_db_port gateway_db_user gateway_db_pwd

```

### 安装参数如下：

- tarsweb_base TarsWeb 管理端的基础地址，例如：http://172.16.8.227:3000 （注意后面不要 /）
- token TarsWeb 管理端的 token，可以通过管理端获取 http://${webhost}/auth.html#/token
- node_ip GatewayServer 部署的 ip，目前这里只支持一个，如果需要更多，后面直接在平台上面扩容即可。
- gateway_db_ip gateway db 所在的数据库服务器 ip。
- gateway_db_port gateway db 端口。
- gateway_db_user gateway db 用户名（需要有建库建表权限）。
- gateway_db_pwd gateway db 密码。

注意:

- Gateway 会依赖 db, 它的 sql 放在 install/db_base.sql, 安装时会创建该 db, 注意你也需要保证 web 平台能访问到你的网关 DB
- 用脚本一键部署时, 默认只安装了一台节点, 有需要你在 web 平台上自己扩容部署即可

### 例如：

```
    ./install.sh http://172.16.8.220:3000 036105e1ebfc13843b4db0edcd000b3d9f47b13928423f0443df54d20ca65855 172.16.8.220 172.16.8.221 3306 tars tars2015
```

### 验证安装结果：

在浏览器打开 http://${server_ip}:8200/monitor/monitor.html , 如果能正常显示 hello TupMonitorxxx 就表示安装成功。

## 功能介绍

### 1. 代理类型的判断

TarsGateway 是根据请求 host+url 判断当前请求是什么类型的请求，具体 host 和 url 通过配置设定。配置及对应逻辑说明如下：

[comment]: ***配置说明***：

- 配置所在域： /main/base
- tup_host: tup 请求对应的 host，如果请求 host 在 tup_host 列表中，那么会进行后面的 tup&&json 请求的判断。如果列表配置为空，那么也会判断，这里支持前通配符。
- tup_path: tup 或 tars 请求的基础 path，默认为 /tup ;
- json_path: json 请求的基础 path，默认为 /json ;
- monitor_url: TarsGateway 的监控地址，用来远程判断服务是否存活。
- 配置举例：

```
    <main>
        <base>
            #tup_host 如果不配置，那么所有host开头的，且没有path或者path为 / ， 也判断为 tup 请求
            tup_host=prx.tup.whup.com|prx2.tup.whup.com|*.prx.upchina.com
            tup_path=/tup
            json_path=/json
            monitor_url=/monitor/monitor.html
        </base>
    </main>
```

### 2. TARS-tup && TARS-tars 协议代理

TARS-tup 协议代理，必须为 post 请求类型，路径为/tup，body 内容为 RequestPacket 包 tars 序列化的内容。TarsGateway 收到包后，去反序列化 body 的内容解析出 RequestPacket 包，然后根据其中的 sServantName 在配置中查找真是的 tars 服务的 obj。如果配置 auto_proxy=1，那么客户端调用时 sServantName 可以填真实的 obj 地址。这里建议：直接对 C 外网暴露的 TarsGateway，建议配置 auto_proxy=0，避免内网的服务都直接对外暴露。另外，proxy 的配置还可以支持 sServerName:sFuncName 的配置，会优先根据, 这种类型配置优先级高于只配置 sServerName 类型的配置。 proxy 配置如下：

```
    <proxy>
        hello = TestApp.HelloServer.HelloObj
        hello:sayhello = TestApp.Hello2Server.HelloObj
    </proxy>
```

经过 TarsGateway 调用后端服务，客户端请求的 http 头，可以通过配置采用 tars 的 context 进行 http 头的透传，默认情况下，REMOTE_IP （客户端 ip）都会透传给后端。配置为 filterheaders，可以是多个，比如：

```
    filterheaders = X-GUID|X-XUA
```

调用后端 tars 服务时，TarsGateway 默认采用 tars 自己的缺省轮训负载均衡策略（robin 轮训），也可以通过配置自定义 hash 策略，hash_type 为 1 时，根据客户端请求 id 进行 tarshash 调用; hash_type 为 2 时，根据指定 http 头(配种中的 httpheader)进行 tarshash 调用，比如 http 头中的 X-GUID，注意这里选择 httpheader 需要合理，避免过于集中某个值导致负载均衡过于不均匀的现象； hash_type 为 3 时，则根据客户端的 ip 进行 tarshash 调用。 如果 obj 后面没有配置 hash_type，那么采用 tars 默认轮训调用。配置举例如下：

```
    <proxy>
        # servant = server_full_obj [| hash_type [| http header key] ]
        # hash_type: 0, 轮训; 1: requestid, 2: http头, 3: client ip
        # Hello=TestApp.HelloServer.HelloObj | 1
        # Hello=TestApp.HelloServer.HelloObj | 2 | X-GUID
        # Hello=TestApp.HelloServer.HelloObj | 3
        hello = TestApp.HelloServer.HelloObj | 3
        hello:sayhello = TestApp.Hello2Server.HelloObj
    </proxy>

```

### 3. TARS-JSON 协议代理

TARS-JSON 协议代理，支持两种类型的接口。

- **servantName 和 funcName 在 http url 路径中指定**

路径为/json/servantName/funcName，其中/json 是固定的，后面分别是 servantName 和 funcName。

```
    请求：
    url： http://xx.xx.com/json/Test.GetSumServer.GetSumObj/getSumEx
    {"req":{"userKey":"upchina","userToken":"upchinatoken","x":1,"y":9900989}}

    响应：
    { "rsp": { "otherMsg": [ "1 + 9900989 = 9900990" ], "msg": "succ.", "sum": 9900990, "ret": 0 }, "": 0 }
```

- **相关参数都在 http body 中指定：**

必须为 post 请求类型，路径为/json，body 内容为 json 结构。其中必须有 reqid, obj, func, data 四个字段，分别表示请求 id、服务 servant、服务接口、接口参数，对应 RequestPacket 中的 reqid:iRequestId, obj:sServantName, func:sFuncName。data 内容为接口中的参数，key 为参数名，value 为参数内容。除了以上必选四个字段之外，context 为可选字段。回包内容包括 reqid 和 data， data 为接口出参内容，其中 "" 的 key 对应内容为函数返回值。 这里除了这里包格式不一样，其他后面的逻辑都和 TARS-tup 类型一样。请求参数举例如下：

```
    请求包：
    {
        "reqid": 99999,
        "obj": "getsum",
        "func": "getSumEx",
        "data": "{\"req\":{\"userKey\":\"upchina\",\"userToken\":\"upchinatoken\",\"x\":1,\"y\":9900989}}"
    }
    响应包：
    {
        "data": "{ \"rsp\": { \"otherMsg\": [ \"1 + 9900989 = 9900990\" ], \"msg\": \"succ.\", \"sum\": 9900990, \"ret\": 0 }, \"\": 0 }",
        "reqid": 99999
    }
```

### 4. 普通 HTTP 协议代理

普通 HTTP 协议代理，类似 nginx 的反向代理功能，主要功能包括根据 domain 和 url 进行请求转发，后端负载均衡，容错容灾，黑名单屏蔽，流量控制等功能。

- **路由策略**

先匹配 server_name,再匹配 path,然后根据 proxy_pass 路径进行转发,具体规则如下：

```
server_name 匹配逻辑：
{
	1、查找全匹配
	2、通配符在前匹配
	3、通配符在后面匹配
	4、正则匹配
	5、如果server_name为空， 则默认都匹配
}

path 匹配逻辑：
{
	1、= 全匹配：  /login
	2、^~ uri以某个常规字符串开头： ^~ /static/   （一旦匹配成功， 不再往后面匹配）
	3、~ 正则匹配(区分大小写)：  ~ \.(gif|jpg|png|js|css)$  （正则表达式匹配多个的情况下， 按最长的匹配）
	4、~* 正则匹配(不区分大小写)：~* \.png$
	5、!~和!~*分别为区分大小写不匹配及不区分大小写不匹配 的正则：  !~ \.xhtml$，  !~* \.xhtml$
	6、/xxx 从头开始匹配路径（匹配长度越长优先级越高）
	7、/ 任何请求都会匹配
}

proxy_pass:
{
	1、如果proxy_pass配置中没有路径（http://host/ 这个是有路径的 /），这时候 location 匹配的完整路径将直接透传给 url
	2、proxy_pass配置中包含路径（哪怕只有一个 / ， 也算）， 新路径 = proxypassPath + (访问路径-location路径)
	3、当 location 中为正则时， proxy_pass 不能带路径
}
```

- **负载均衡**

支持普通轮训和带权重轮训策略，默认权重（weight）为 1，数据越大，权重越高。权重表示在一次轮训周期内轮训的次数。

- **容错容灾**

当后端节点为多个节点时（大于等于 2 个节点），可以支持后端熔断策略，这里是否进行熔断可以配置，默认是打开的。

**失效屏蔽**：如果出现连接出错，那么就会直接临时屏蔽该节点，对应 inactive 配置，其中的值为 RequestCallback::FAILED_CODE 类型。加入到失效的节点，如果该站点配置了 monitor_url，那么会定时轮训该 url，如果出现 http 200，那么恢复该节点，轮训检测间隔递增，最大为 2 分钟。如果没有配置 monitor_url, 那么直接 connect 该节点的 ip:port，能够正常连接，那么恢复该节点。

**超时切换**：当超时次数在指定时间窗口达到一定阈值时，或者超过一定比例时，那么临时屏蔽一段时间，一定时间后会尝试超时恢复。

```
        配置如下：
        <http_retcode>
            # 定义哪些返回码做超时容错处理，哪些返回码做出错容灾处理
            inactive=2|6
            timeout=1|3
        </http_retcode>

        RequestCallback::FAILED_CODE类型定义如下：
        enum FAILED_CODE
        {
            Failed_Net     = 0x01,      //网络出错
            Failed_Connect = 0x02,      //连接服务器出错
            Failed_Timeout = 0x03,      //超时
            Failed_Interrupt = 0x04,    //中断接收数据
            Failed_Close    = 0x05,     //服务器主动关闭了链接
            Failed_ConnectTimeout = 0x06, //链接超时
        };
```

IP 黑名单和流控策略， 同时支持 TarsGateway 的三种协议，所以后面统一介绍。

## 5. 身份鉴权

支持调用后端服务时，在网关层先进行统一身份鉴权，如果鉴权失败，那么给客户端直接返回 401，客户端业务可以根据此情况跳转登陆等操作等。鉴权流程如下：
![网关身份鉴权](images/pic_verify_01.png)

### 网关鉴权配置

要使用网关统一身份鉴权，需要在网关配置文件中进行配置，配置格式如下：

```xml
<main>
    <auth>
        # 按照不同的身份类型，配置不同的 login域，比如 phone_login， wx_login
        # 如果多个域中配置的有冲突， 那么后面的配置覆盖前面的配置
        <phone_login>
            verify=Base.VerifyDemoServer.VerifyObj
            # 认证时候， 是否需要带上 body 内容给认证服务接口
            verify_body=true
            # 透传http头
            verify_headers=X-GUID|X-RemoteIP|X-XUA
            # 认证票据http头
            auth_http_header=X-Token
            # 默认不鉴权，匹配上了才鉴权，在匹配上了之后，如果在exclude部分有配置，则不鉴权
            <auth_list>
                # 支持应用级别、服务级别、接口级别
                # 应用级别
                Test1.*
                # 服务obj级别
                Test2.Test1Server.TestObj
                # 接口级别
                Test3.TestServer.TestObj:func1|func2|funcn
                Test3.HelloServer.HelloObj:test1
                TestApp.HelloServer.HelloObj
                <exclude>
                    # 以下情况不需要鉴权
                    # 支持服务级别、接口级别
                    # 服务级别
                    Test1.Test1Server.TestObj
                    # 接口级别
                    Test2.Test1Server.TestObj:func1|func2|funcn
                    TestApp.HelloServer.HelloObj:testHello
                </exclude>
            </auth_list>
        </phone_login>

        <wx_login>
            verify=WX.WXUserServer.UserObj
            auth_http_header=X-SessionID
            <auth_list>
                # 应用级别
                WXNews.*
            </auth_list>
        </wx_login>
    </auth>
</main>
```

需要进行鉴权那么就需要在配置文件中 auth_list 进行配置，鉴权配置支持应用级别，服务 Obj 级别，接口级别，不在配置中的则不进行鉴权。
在 auth_list 中的应用或者服务，如果需要排除特定服务或者特定接口不需要鉴权，那么在 exclude 中进行配置，支持服务级别或者接口级别。
比如 Test1 应用里面，除了 Test1.Test1Server.TestObj 不需要鉴权其他服务都需要鉴权，那么可以在 auth_list 中配置 Test1.\* 然后在 exclude 中配置 Test1.Test1Server.TestObj;
又比如 TestApp.HelloServer.HelloObj 除了 testHello 接口，其他接口都需要鉴权，那么可以在 auth_list 中配置 TestApp.HelloServer.HelloObj 然后在 exclude 中配置 TestApp.HelloServer.HelloObj:testHello。

### 鉴权接口实现

如果配置了网关统一鉴权，需要在配置/main/auth/xxx/中的 verify 指定的服务中，实现 verify 接口，接口定义如下：

```c++
//Verify.tars
module Base
{
    enum E_VERIFY_CODE
    {
        EVC_TOKEN_EXPIRE    = -3,           // token过期
        EVC_ERR_TOKEN = -2,                 // token格式错误等
        EVC_SYS_ERR = -1,                   // 系统异常
        EVC_SUCC = 0                        // 成功
    };

    struct VeifyReq
    {
        1 require   string                  token;          // token，在配置中指定的http头中获取，比如X-Token
        2 optional  map<string, string>     verifyHeaders;  // 请求网关时的 http 头, 具体需要透传哪些http头在配置中指定verify_headers
        3 optional  vector<byte>            body;           // 请求body，如果需要业务请求的body，那么配置中指定 verify_body=true
    };

    struct VeifyRsp
    {
        1 require   int     ret;        // 返回码， 取值为 E_VERIFY_CODE
        2 optional  string  uid;        // 认证成功后的 uid
        3 optional  string  context;    // 认证服务认证成功后可能需要的附件数据透传给业务， 后面调用服务时通过 context["X-Verify-Data"] 透传
    };

    interface Verify
    {
        // 鉴权接口
        int verify(VeifyReq req, out VeifyRsp rsp);
    };
};
```

只有该服务的 verify 接口返回 0 且 ret=EVC_SUCC 时，才算身份验证通过，会继续调业务接口，否则给客户端返回 401，不会再继续调用业务接口。

### 业务服务使用

配置了网关身份鉴权之后，那么网关请求过来的业务接口，都会通过 tars 接口的 context 附带上身份鉴权信息，包括：

1. X-Verify-UID： verify 接口返回的 uid；
2. X-Verify-Data：verify 接口返回的 context，可以为空；
3. X-Verify-Token：身份票据 token 信息，即请求 verify 接口中的 token。

### 6. 流量控制

可以支持访问 TarsGateway 访问后端进行流量控制，支持单机控制，也支持多机协同控制，也可以关闭流控。

**开关控制:** 配置 flow_control_onoff 可以对流控进行开关控制。另外如果服务 servant 没有配置 FlowControlObj，那么就不会开启流控策略。

**流控策略:** 一定时间内最多访问多少次, 通过时间滑动窗口动态控制，滑动窗口大小为 1s，超过次数则直接返回 http 403。

**多机协同:** 配置了 tup_report_obj，那么会通过该 obj 进行多机协同流量控制，否则进行单机控制。注意，如果是单机的策略，那么流控配置的一定时间内最多可以访问多少次就是单机最多可以访问该站点多少次；如果是多机协同，那么就是多机同时允许访问该站点多少次。

**配置说明:** 如果是 TARS-tup 或者 TARS-JSON 协议，那么流控的站点 ID 为服务 Obj，如果是 http 协议，那么站点 ID 为配置中的 stationId.

### 7. 黑名单策略

黑名单为 IP 黑名单，支持全局黑名单和站点黑名单两个级别。

**黑名单格式:** 客户端 IP 地址，支持通配符。如 192.168.2.130, 192.168.10.\*

**全局黑名单:** 对所有访问 TarsGateway 进行控制，包括 TARS-tup、TARS-JSON 和普通 HTTP 协议。

**站点黑名单:** 只针对指定站点控制，其他站点不首影响。

**站点白名单:** 站点一旦配置了白名单，那么就只能是指定 IP 才能访问，主要用于内部系统控制指定 ip 访问，或者开放给指定合作伙伴调用。

### 8. 配置热更新

支持常用配置热更新，包括：

1. loadProxy: 通过该 tars 命令可以实现 TARS-tup&TARS-JSON 协议的 servant 代理配置更新；
2. loadHttp: 通过该配置可以进行普通 HTTP 协议的路由策略, 后端节点配置，监控 url 配置等；
3. loadComm: 通过该命令可以进行一些公共的配置加载，主要包括黑白名单加载；
4. 流控策略自动动态加载 DB。

### 9. 环境切换

在作为 TARS-tup 或 TARS-JSON 协议代理时，可以通过 http 头中值，指定到不通的 proxy 子配置域中。
默认是直接使用 proxy 下面的配置，如果配置了 env_httpheader，且当前请求中有该 http 头，并且 http 头的 value 为配置中的内容，那么则优先选择 proxy 下面的 env 子域对应的转发规则。比如如下配置，表示 http 请求头中 X-GUID=12345678123456781234567812345678 的用户，则优先选用 test 环境中配置，即：用户请求 servant 为 hello 时，那么真实服务 obj 选择 TestApp.HelloServer.HelloObj@tcp -h 192.168.2.101 -p 10029 , 但是如果用户请求 servant 为 world 时，由于 test 环境中并没有配置 world 对应的转发规则，那么还是用 proxy 下面默认的规则，及真实 obj 为 Test.HelloworldServer.HelloworldObj，配置如下：

```
    <proxy>
        hello = TestApp.HelloServer.HelloObj
        hello:sayhello = TestApp.Hello2Server.HelloObj
        world= Test.HelloworldServer.HelloworldObj

        # test 环境转发规则
        <test>
             hello = TestApp.HelloServer.HelloObj@tcp -h 192.168.2.101 -p 10029
        </test>
    </proxy>

    #http头:值, 转到proxy某个服务
    <env_httpheader>
       # httpheader-key:httpheader-value = env
        X-GUID:12345678123456781234567812345678 = test
    </env_httpheader>
```

### 10. 返回码说明

- 200: OK 正常响应
- 400: Bad Request 1.解客户端请求包错误。
- 401: Unauthorized 1.统一鉴权失败。
- 403: Forbidden 1.客户端 ip 命中黑名单。
- 404: Not Found 1.tup 或 json 协议找不到对应 servant 代理；2. Http 找不到后端站点；
- 429: Too Many Request 1.流控超过限制策略；
- 500: Server Interval Error 1.http 后端没有配置目标地；
- 502: Bad Gateway 1.调用后端 tars 服务或者 http 服务异常；
- 504：Gateway Timeout 1.调用后端 tars 服务或者 http 服务超时;

### 11. 日志格式说明

TARS-tup & TARS-JSON 协议代理请求响应日志格式说明：

**正常回包 response 日志:**

日志时间 | 客户端 ip | 客户端 GUID | 客户端 XUA | servantName | funcName | 请求加密类型 | 请求压缩类型 | 响应是否加密 | 响应是否压缩 | 耗时(ms) | 响应包大小

**异常请求 tupcall_exception 日志：**

日志时间 | 客户端 ip |servantName | funcName | 客户端 GUID | 客户端 XUA | 请求加密类型 | 请求压缩类型 | 耗时(ms) | 后端 rpc 返回码

普通 HTTP 协议代理日志格式说明：

**Http access 日志：**

客户端 ip | 访问时间 | host | referer | 请求 url | 请求包大小 | http 方法 | 站点 ID | 后端地址 | http 返回状态码 | 响应时间 | 耗时(ms) | 响应包大小 | UA | 出错信息
