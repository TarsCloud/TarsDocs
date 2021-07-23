# TarsGateway简介
---
# 简介
TarsGateway是基于tars框架开发的一套通用api网关，请求为http协议，后端同时支持tars-tup&tars-tars协议、tars-json协议、http协议。除了协议转发之外，还支持流量控制，黑白名单等功能。

# 支持版本说明
* TarsCpp:    >= v2.4.5
* TarsJava:   >= v1.7.2
* TarsGo:     >= v1.1.4
* TarsNode:   （rpc: >= v2.0.14, stream: >= v2.0.3,  tars2node: >= v20200707）
* TarsPHP:    tars-server: >= v0.6.0

# 功能介绍
## 1. 代理类型的判断
TarsGateway 是根据请求host+url 判断当前请求是什么类型的请求，具体host和url通过配置设定。配置及对应逻辑说明如下：

[comment]: <***配置说明***：>
* 配置所在域： /main/base
* tup_host: tup请求对应的host，如果请求host在tup_host列表中，那么会进行后面的tup&&json请求的判断。如果列表配置为空，那么也会判断，这里支持前通配符。
* tup_path: tup或tars请求的基础path，默认为 /tup ;
* json_path: json 请求的基础path，默认为 /json ;
* monitor_url: TarsGateway 的监控地址，用来远程判断服务是否存活。
* 配置举例：
```
    <main>
        <base>
            #tup_host 如果不配置，那么所有host开头的，且没有path或者path为 / ， 也判断为 tup 请求
            tup_host=prx.tup.whup.com|prx2.tup.whup.com|*.prx.upchina.com
            tup_path=/tup
            json_path=/json
            monitor_url=/monitor/monitor.jsp
        </base>
    </main>
```

## 2. TARS-tup && TARS-tars 协议代理
TARS-tup协议代理，必须为post请求类型，路径为/tup，body内容为BasePacket包tars序列化的内容。TarsGateway收到包后，去反序列化body的内容解析出BasePacket包，然后根据其中的sServantName在配置中查找真是的tars服务的obj。如果配置auto_proxy=1，那么客户端调用时 sServantName 可以填真实的obj地址。这里建议：直接对C外网暴露的TarsGateway，建议配置auto_proxy=0，避免内网的服务都直接对外暴露。另外，proxy的配置还可以支持 sServerName:sFuncName 的配置，会优先根据, 这种类型配置优先级高于只配置sServerName 类型的配置。 proxy配置如下：
```
    <proxy>
        hello = TestApp.HelloServer.HelloObj
        hello:sayhello = TestApp.Hello2Server.HelloObj
    </proxy>
```
经过TarsGateway调用后端服务，客户端请求的http头，可以通过配置采用tars的context进行http头的透传，默认情况下，REMOTE_IP （客户端ip）都会透传给后端。配置为 filterheaders，可以是多个，比如：
```
    filterheaders = X-GUID|X-XUA
```
调用后端tars服务时，TarsGateway默认采用tars自己的缺省轮训负载均衡策略（robin轮训），也可以通过配置自定义hash策略，hash_type为1时，根据客户端请求id进行tarshash调用; hash_type为2时，根据指定http头(配种中的httpheader)进行tarshash调用，比如http头中的 X-GUID，注意这里选择httpheader需要合理，避免过于集中某个值导致负载均衡过于不均匀的现象； hash_type为3时，则根据客户端的ip进行tarshash调用。 如果obj后面没有配置hash_type，那么采用tars默认轮训调用。配置举例如下：
```
    <proxy>
        hello = TestApp.HelloServer.HelloObj | 3
        hello:sayhello = TestApp.Hello2Server.HelloObj
    </proxy>
    
```

## 3. TARS-JSON 协议代理
TARS-JSON协议代理，支持两种类型的接口。
* **servantName和funcName在http url路径中指定**
  
路径为/json/servantName/funcName，其中/json是固定的，后面分别是servantName 和 funcName。

```
    tars 协议文件如下：
    module Test
    {
        struct GetSumReq
        {
            1 require int 	x;
            2 require int 	y;
        };

        struct GetSumRsp
        {
            1 optional int z;
            2 optional string msg;
        };

        interface GetSum
        {
            int getSumEx(GetSumReq req, out GetSumRsp rsp);
        };
    }; 


    请求(推荐 postman工具)：
    url： http://gateway_ip:port/json/Test.GetSumServer.GetSumObj/getSumEx
    {"req":{"userKey":"upchina","userToken":"upchinatoken","x":1,"y":9900989}}

    响应：
    { "rsp": { "otherMsg": [ "1 + 9900989 = 9900990" ], "msg": "succ.", "sum": 9900990, "ret": 0 }, "tars_ret": 0 }
```

* **相关参数都在http body中指定：**

必须为post请求类型，路径为/json，body内容为json结构。其中必须有reqid, obj, func, data 四个字段，分别表示请求id、服务servant、服务接口、接口参数，对应BasePacket中的reqid:iRequestId, obj:sServantName, func:sFuncName。data内容为接口中的参数，key为参数名，value为参数内容。除了以上必选四个字段之外，context为可选字段。回包内容包括 reqid 和 data， data为接口出参内容，其中 "" 的key对应内容为函数返回值。 这里除了这里包格式不一样，其他后面的逻辑都和TARS-tup类型一样。请求参数举例如下：


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
        "data": "{ \"rsp\": { \"otherMsg\": [ \"1 + 9900989 = 9900990\" ], \"msg\": \"succ.\", \"sum\": 9900990, \"ret\": 0 }, \"tars_ret\": 0 }", 
        "reqid": 99999 
    }

```

 
## 4. 普通HTTP协议代理

普通HTTP协议代理，类似nginx的反向代理功能，主要功能包括根据domain和url进行请求转发，后端负载均衡，容错容灾，黑名单屏蔽，流量控制等功能。

* **路由策略**

先匹配server_name,再匹配path,然后根据 proxy_pass 路径进行转发,具体规则如下：

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

* **负载均衡**

支持普通轮训和带权重轮训策略，默认权重（weight）为1，数据越大，权重越高。权重表示在一次轮训周期内轮训的次数。

* **容错容灾**

当后端节点为多个节点时（大于等于2个节点），可以支持后端熔断策略，这里是否进行熔断可以配置，默认是打开的。

**失效屏蔽**：如果出现连接出错，那么就会直接临时屏蔽该节点，对应inactive配置，其中的值为RequestCallback::FAILED_CODE类型。加入到失效的节点，如果该站点配置了monitor_url，那么会定时轮训该url，如果出现http 200，那么恢复该节点，轮训检测间隔递增，最大为2分钟。如果没有配置 monitor_url, 那么直接connect该节点的ip:port，能够正常连接，那么恢复该节点。

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

IP黑名单和流控策略， 同时支持TarsGateway的三种协议，所以后面统一介绍。


## 5. 流量控制

可以支持访问TarsGateway 访问后端进行流量控制，支持单机控制，也支持多机协同控制，也可以关闭流控。

**开关控制:**  配置flow_control_onoff可以对流控进行开关控制。另外如果服务servant没有配置FlowControlObj，那么就不会开启流控策略。

**流控策略:**  一定时间内最多访问多少次, 通过时间滑动窗口动态控制，滑动窗口大小为1s，超过次数则直接返回http 403。 

**多机协同:**  配置了tup_report_obj，那么会通过该obj进行多机协同流量控制，否则进行单机控制。注意，如果是单机的策略，那么流控配置的一定时间内最多可以访问多少次就是单机最多可以访问该站点多少次；如果是多机协同，那么就是多机同时允许访问该站点多少次。

**配置说明:**  如果是TARS-tup或者TARS-JSON协议，那么流控的站点ID为服务Obj，如果是http协议，那么站点ID为配置中的stationId.

## 6. 黑名单策略

黑名单为IP黑名单，支持全局黑名单和站点黑名单两个级别。

**黑名单格式:** 客户端IP地址，支持通配符。如 192.168.2.130, 192.168.10.*

**全局黑名单:** 对所有访问TarsGateway进行控制，包括TARS-tup、TARS-JSON和普通HTTP协议。

**站点黑名单:** 只针对指定站点控制，其他站点不首影响。

**站点白名单:** 站点一旦配置了白名单，那么就只能是指定IP才能访问，主要用于内部系统控制指定ip访问，或者开放给指定合作伙伴调用。


## 7. 配置热更新

支持常用配置热更新，包括：
1. loadProxy: 通过该tars命令可以实现TARS-tup&TARS-JSON协议的servant代理配置更新；
2. loadHttp:  通过该配置可以进行普通HTTP协议的路由策略, 后端节点配置，监控url配置等；
3. loadComm:  通过该命令可以进行一些公共的配置加载，主要包括黑白名单加载；
4. 流控策略自动动态加载DB。

## 8. 环境切换
在作为TARS-tup或TARS-JSON协议代理时，可以通过http头中值，指定到不通的proxy子配置域中。
默认是直接使用proxy下面的配置，如果配置了env_httpheader，且当前请求中有该http头，并且http头的value为配置中的内容，那么则优先选择proxy 下面的 env 子域对应的转发规则。比如如下配置，表示http请求头中X-GUID=12345678123456781234567812345678 的用户，则优先选用test环境中配置，即：用户请求servant为hello时，那么真实服务obj选择TestApp.HelloServer.HelloObj@tcp -h 192.168.2.101 -p 10029 , 但是如果用户请求servant为world时，由于test环境中并没有配置 world 对应的转发规则，那么还是用 proxy 下面默认的规则，及真实obj为Test.HelloworldServer.HelloworldObj，配置如下：

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

## 9. 返回码说明
* 200: OK 正常响应
* 400: Bad Request  1.解客户端请求包错误。
* 403: Forbidden  1.客户端ip命中黑名单。
* 404: Not Found  1.tup或json协议找不到对应servant代理；2. Http找不到后端站点；
* 429: Too Many Request  1.流控超过限制策略；
* 500: Server Interval Error 1.http后端没有配置目标地；
* 502: Bad Gateway  1.调用后端tars服务或者http服务异常；
* 504：Gateway Timeout  1.调用后端tars服务或者http服务超时;

## 10. 日志格式说明
TARS-tup & TARS-JSON 协议代理请求响应日志格式说明：

**正常回包 response日志:**

日志时间 | 客户端ip | 客户端GUID | 客户端XUA | servantName | funcName | 请求加密类型 | 请求压缩类型 | 响应是否加密 | 响应是否压缩 | 耗时(ms) | 响应包大小

**异常请求 tupcall_exception日志：**

日志时间 | 客户端ip |servantName | funcName | 客户端GUID | 客户端XUA |  请求加密类型 | 请求压缩类型 | 耗时(ms) | 后端rpc返回码

普通HTTP协议代理日志格式说明：

**Http access 日志：**

客户端ip | 访问时间 | host | referer | 请求url | 请求包大小 | http方法 | 站点ID | 后端地址 | http返回状态码 | 响应时间 | 耗时(ms) | 响应包大小 | UA | 出错信息
