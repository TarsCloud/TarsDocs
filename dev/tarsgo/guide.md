# 使用指南
### 服务端

#### **tars协议**

有关 tars 协议的更多详细信息, 请查看 [Tars协议](../../base/tars-protocol.md)

```go
module TestApp
{
    interface Hello
    {
        int test();
        int testHello(string sReq, out string sRsp);
    };
};
```

### **编译接口定义文件**

#### **构建 tars2go**

编译并安装 tars2go 工具(如果已经执行, 则忽略)

```text
# < go 1.16
go get -u github.com/TarsCloud/TarsGo/tars/tools/tars2go
# >= go 1.16
go install github.com/TarsCloud/TarsGo/tars/tools/tars2go@latest
```

#### **编译 tars 文件并转成 go 文**

```text
tars2go --outdir=./autogen hello.tars
```

### **接口实现**

```go
package main

import (
	"github.com/TarsCloud/TarsGo/tars"

	"TestApp"
)

type HelloImp struct {
}

// Test implement the Test interface
func (imp *HelloImp) Test() (int32, error) {
	return 0, nil
}

// TestHello implement the testHello interface
func (imp *HelloImp) TestHello(in string, out *string) (int32, error) {
	*out = in
	return 0, nil
}

func main() { //Init servant
	imp := new(HelloImp)                                    //New Imp
	app := new(TestApp.Hello)                               //New init the A Tars
	cfg := tars.GetServerConfig()                           //Get Config File Object
	app.AddServant(imp, cfg.App+"."+cfg.Server+".HelloObj") //Register Servant
	tars.Run()
}

```

说明:

- HelloImp 是结构体，你在里面实现 Hello 和 Test 接口, 注意 Test 和 Hello 必须以大写字母开头才能被导出,这是唯一与 tars 文件定义有所不同的地方。
- TestApp.Hello 是由 tar2go 工具生成的，它可以在./vendor/TestApp/Hello_IF.go 中找到，其中包含一个名为 TestApp 的软件包，它与 tars 文件的 TestApp 模块一样。
- tars.GetServerConfig\(\)用于获得服务端配置。
- cfg.App+"."+cfg.Server+".HelloObj" 是绑定到 Servant 的对象名，客户端将使用此名称访问服务端.

### **服务端配置**

tars.GetServerConfig\(\)返回服务端配置，其定义如下:

```go
type serverConfig struct {
	Node      string
	App       string
	Server    string
	LogPath   string
	LogSize   string
	LogLevel  string
	Version   string
	LocalIP   string
	BasePath  string
	DataPath  string
	config    string
	notify    string
	log       string
	netThread int
	Adapters  map[string]adapterConfig

	Container   string
	Isdocker    bool
	Enableset   bool
	Setdivision string
}

```

- Node: 本地 tarsnode 地址，只有你使用 tars 平台部署才会使用这个参数.
- APP: 应用名.
- Server: 服务名.
- LogPath: 保存日志的目录.
- LogSize: 轮换日志的大小.
- LogLevel: 轮换日志的级别.
- Version: Tarsg 的版本.
- LocalIP: 本地 ip 地址.
- BasePath: 二进制文件的基本路径.
- DataPath: 一些缓存文件存储路径.
- config: 获取配置的配置中心，如 tars.tarsconfig.ConfigObj
- notify： 上报通知报告的通知中心，如 tars.tarsnotify.NotifyObj
- log： 远程日志中心，如 tars.tarslog.LogObj
- netThread: 保留用于控制接收和发送包的 go 线程.
- Adapters: 每个 adapter 适配器的指定配置.
- Contianer: 保留供以后使用，用于存储容器名称.
- Isdocker: 保留供以后使用，用于指定服务是否在容器内运行.
- Enableset: 如果使用了 set，则为 True.
- Setdivision: 指定哪个 set，如 gray.sz.\*

如下是一个服务端配置的例子:

```text
<tars>
  <application>
      enableset=Y
      setdivision=gray.sz.*
    <server>
       node=tars.tarsnode.ServerObj@tcp -h 10.120.129.226 -p 19386 -t 60000
       app=TestApp
       server=HelloServer
       localip=10.120.129.226
       local=tcp -h 127.0.0.1 -p 20001 -t 3000
       basepath=/usr/local/app/tars/tarsnode/data/TestApp.HelloServer/bin/
       datapath=/usr/local/app/tars/tarsnode/data/TestApp.HelloServer/data/
       logpath=/usr/local/app/tars/app_log/
       logsize=10M
       config=tars.tarsconfig.ConfigObj
       notify=tars.tarsnotify.NotifyObj
       log=tars.tarslog.LogObj
       #timeout for deactiving , ms.
       deactivating-timeout=2000
       logLevel=DEBUG
    </server>
  </application>
</tars>
```

### **适配器**

适配器为每个对象绑定 ip 和端口.在服务端代码实现的例子中， app.AddServant\(imp, cfg.App+"."+cfg.Server+".HelloObj"\)完成 HelloObj 的适配器配置和实现的绑定。适配器的完整例子如下：

```text
<tars>
  <application>
    <server>
       #each adapter configuration
       <TestApp.HelloServer.HelloObjAdapter>
            #allow Ip for white list.
            allow
            # ip and port to listen on
            endpoint=tcp -h 10.120.129.226 -p 20001 -t 60000
            #handlegroup
            handlegroup=TestApp.HelloServer.HelloObjAdapter
            #max connection
            maxconns=200000
            #portocol, only tars for now.
            protocol=tars
            #max capbility in handle queue.
            queuecap=10000
            #timeout in ms for the request in the queue.
            queuetimeout=60000
            #servant
            servant=TestApp.HelloServer.HelloObj
            #threads in handle server side implement code. goroutine for golang.
            threads=5
       </TestApp.HelloServer.HelloObjAdapter>
    </server>
  </application>
</tars>
```

### **服务端启动**

如下命令用于启动服务端：

```text
./HelloServer --config=config.conf
```

请参阅下面的 config.conf 的完整示例，稍后我们将解释客户端配置。

```text
<tars>
  <application>
    enableset=n
    setdivision=NULL
    <server>
       node=tars.tarsnode.ServerObj@tcp -h 10.120.129.226 -p 19386 -t 60000
       app=TestApp
       server=HelloServer
       localip=10.120.129.226
       local=tcp -h 127.0.0.1 -p 20001 -t 3000
       basepath=/usr/local/app/tars/tarsnode/data/TestApp.HelloServer/bin/
       datapath=/usr/local/app/tars/tarsnode/data/TestApp.HelloServer/data/
       logpath=/usr/local/app/tars/app_log/
       logsize=10M
       config=tars.tarsconfig.ConfigObj
       notify=tars.tarsnotify.NotifyObj
       log=tars.tarslog.LogObj
       deactivating-timeout=2000
       logLevel=DEBUG
       <TestApp.HelloServer.HelloObjAdapter>
            allow
            endpoint=tcp -h 10.120.129.226 -p 20001 -t 60000
            handlegroup=TestApp.HelloServer.HelloObjAdapter
            maxconns=200000
            protocol=tars
            queuecap=10000
            queuetimeout=60000
            servant=TestApp.HelloServer.HelloObj
            threads=5
       </TestApp.HelloServer.HelloObjAdapter>
    </server>
    <client>
       locator=tars.tarsregistry.QueryObj@tcp -h 10.120.129.226 -p 17890
       sync-invoke-timeout=3000
       async-invoke-timeout=5000
       refresh-endpoint-interval=60000
       report-interval=60000
       sample-rate=100000
       max-sample-count=50
       asyncthread=3
       modulename=TestApp.HelloServer
    </client>
  </application>
</tars>
```

## 客户端

用户可以轻松编写客户端代码，而无需编写任何指定协议的通信代码.

### **客户端例子**

请参阅下面的一个客户端例子:

```go
package main

import (
    "fmt"
    "github.com/TarsCloud/TarsGo/tars"
    "TestApp"
)
//tars.Communicator should only init once and be global
var comm *tars.Communicator

func main() {
    comm = tars.NewCommunicator()
    obj := "TestApp.TestServer.HelloObj@tcp -h 127.0.0.1 -p 10015 -t 60000"
    app := new(TestApp.Hello)
    comm.StringToProxy(obj, app)
	var req string="Hello Wold"
    var res string
    ret, err := app.TestHello(req, &out)
    if err != nil {
        fmt.Println(err)
        return
    }
    fmt.Println(ret, out)
```

说明:

- TestApp 包是由 tars2go 工具使用 tars 协议文件生成的.
- comm: Communicator 用于与服务端进行通信，它应该只初始化一次并且是全局的.
- obj: 对象名称，用于指定服务端的 ip 和端口。通常在"@"符号之前我们只需要对象名称.
- app: 与 tars 文件中的接口关联的应用程序。 在本例中它是 TestApp.Hello.
- StringToProxy: StringToProxy 方法用于绑定对象名称和应用程序，如果不这样做，通信器将不知道谁与应用程序通信 .
- req, res: 在 tars 文件中定义的输入和输出参数,用于在 TestHello 方法中.
- app.TestHello 用于调用 tars 文件中定义的方法，并返回 ret 和 err.

### **通信器**

通信器是为客户端发送和接收包的一组资源，其最终管理每个对象的 socket 通信。在一个程序中你只需要一个通信器。

```go
var comm *tars.Communicato
comm = tars.NewCommunicator()
comm.SetProperty("property", "tars.tarsproperty.PropertyObj")
comm.SetProperty("locator", "tars.tarsregistry.QueryObj@tcp -h ... -p ...")
```

描述:

> - 通信器配置文件的格式将在后面描述.
> - 可以在没有配置文件的情况下配置通信器，并且所有参数都具有默认值.
> - 通信器也可以通过“SetProperty”方法直接初始化.
> - 如果您不需要配置文件，则必须自己设置 locator 参数.

通信器属性描述:

> - locator:主控服务的地址必须采用“ip port”格式。 如果你不需要主控来查找服务，则无需配置此项.
> - important async-invoke-timeout:客户端调用的最大超时时间（以毫秒为单位），此配置的默认值为 3000.
> - sync-invoke-timeout:现在没用于 tarsgo.
> - refresh-endpoint-interval:定期访问主控以获取信息的时间间隔（以毫秒为单位），此配置的默认值为一分钟.
> - stat:在模块之间调用的服务的地址。 如果未配置此项，则表示将直接丢弃上报的数据.
> - property:服务上报其属性的地址。 如果未配置，则表示将直接丢弃上报的数据.
> - report-interval:现在没用于 tarsgo.
> - asyncthread: 已被 tarsgo 舍弃.
> - modulename: 模块名称，默认值是可执行程序的名称。

通信器配置文件的格式如下：

```text
<tars>
  <application>
    #The configuration required by the proxy
    <client>
        #address
        locator                     = tars.tarsregistry.QueryObj@tcp -h 127.0.0.1 -p 17890
        #The maximum timeout (in milliseconds) for synchronous calls.
        sync-invoke-timeout         = 3000
        #The maximum timeout (in milliseconds) for asynchronous calls.
        async-invoke-timeout        = 5000
        #The maximum timeout (in milliseconds) for synchronous calls.
        refresh-endpoint-interval   = 60000
        #Used for inter-module calls
        stat                        = tars.tarsstat.StatObj
        #Address used for attribute reporting
        property                    = tars.tarsproperty.PropertyObj
        #report time interval
        report-interval             = 60000
        #The number of threads that process asynchronous responses
        asyncthread                 = 3
        #The module name
        modulename                  = Test.HelloServer
    </client>
  </application>
</tars>
```

### **超时控制**

如果你想在客户端使用超时控制，请使用以 ms 为单位的 TarsSetTimeout。

```go
    app := new(TestApp.Hello)
    comm.StringToProxy(obj, app)
    app.TarsSetTimeout(3000)
```

### **接口调用**

本节详细介绍了 Tars 客户端如何远程调用服务端。

首先，简要描述 Tars 客户端的寻址模式。 其次，它将介绍客户端的调用方法，包括但不限于单向调用，同步调用，异步调用，hash 调用等。

#### **寻址模式简介**

Tars 服务的寻址模式通常可以分为两种方式：服务名称在 master 上注册了，服务名称未在 master 上注册。 master 是专用于注册服务节点信息的名字服务（路由服务）。

把服务名添加到名字服务中是通过操作管理平台实现。

对于未在 master 中注册的服务，可以将其分类为直接寻址，即在调用服务之前需要指定服务提供者的 IP 地址。 客户端需要在调用服务时指定 HelloObj 对象的特定地址，即 Test.HelloServer.HelloObj@tcp -h 127.0.0.1 -p 9985

Test.HelloServer.HelloObj: 对象名

tcp:Tcp 协议

-h:指定主机地址,这里是 127.0.0.1

-p:端口,这里是 9985

如果 HelloServer 在两台服务器上运行，则应用程序初始化如下:

```go
    obj:= "Test.HelloServer.HelloObj@tcp -h 127.0.0.1 -p 9985:tcp -h 192.168.1.1 -p 9983"
    app := new(TestApp.Hello)
    comm.StringToProxy(obj, app)
```

HelloObj 的地址设置为两个服务器的地址。 此时，请求将被分发到两个服务器（可以指定分发方法，这里不再介绍）。 如果一台服务器关闭，请求将自动分配给另一台服务器，服务器将定期重新启动。

对于在 master 中注册的服务，将根据服务名称对服务进行寻址。 当客户端请求服务时，它不需要指定 HelloServer 的特定地址，但是在生成通信器或初始化通信器时需要指定`registry`的地址。

以下通过设置通信器的参数显示主控的地址：

```go
var *tars.Communicator
comm = tars.NewCommunicator()
comm.SetProperty("locator", "tars.tarsregistry.QueryObj@tcp -h ... -p ...")
```

由于客户端需要依赖主控的地址，因此主控还必须具有容错能力。 主控的容错方法与上面相同，即指定了两个主控的地址。

#### **单向调用**

TODO. tarsgo 暂未支持.

#### **同步调用**

```go
package main

import (
    "fmt"
    "github.com/TarsCloud/TarsGo/tars"
    "TestApp"
)

var *tars.Communicator
func main() {
    comm = tars.NewCommunicator()
    obj := "TestApp.TestServer.HelloObj@tcp -h 127.0.0.1 -p 10015 -t 60000"
    app := new(TestApp.Hello)
    comm.StringToProxy(obj, app)
	var req string="Hello Wold"
    var res string
    ret, err := app.TestHello(req, &out)
    if err != nil {
        fmt.Println(err)
        return
    }
    fmt.Println(ret, out)
```

#### **异步调用**

tarsgo 可以使用 goroutine 轻松使用异步调用。 与 cpp 不同，我们不需要实现回调函数。

```go
package main

import (
    "fmt"
    "github.com/TarsCloud/TarsGo/tars"
    "time"
    "TestApp"
)
var *tars.Communicator
func main() {
    comm = tars.NewCommunicator()
    obj := "TestApp.TestServer.HelloObj@tcp -h 127.0.0.1 -p 10015 -t 60000"
    app := new(TestApp.Hello)
    comm.StringToProxy(obj, app)
	go func(){
		var req string="Hello Wold"
    	var res string
    	ret, err := app.TestHello(req, &out)
    	if err != nil {
        	fmt.Println(err)
        	return
    	}
		fmt.Println(ret, out)
	}()
    time.Sleep(1)
```

#### **通过 set 调用**

客户端可以通过 set 来调用服务端，只需要配置上文提到的配置文件，其中 enableset 置为 y，setdivision 比如设置为 gray.sz. \*。 有关更多详细信息，请参阅[https://github.com/TarsCloud/Tars/blob/master/docs-en/tars_idc_set.md。](https://github.com/TarsCloud/Tars/blob/master/docs-en/tars_idc_set.md%E3%80%82) 如果您想手动通过 set 调用，tarsgo 将很快支持此功能。

#### **Hash 调用**

由于可以部署多个服务端，因此客户端的请求会随机分发到服务端上，但在某些情况下，希望始终将某些请求发送到特定的服务端。 在这种情况下，Tars 提供了一种简单的实现方法，称为 hash 调用。 Tarsgo 很快将支持此功能。

## tars 定义的返回码

```cpp
//Define the return code given by the TARS service
const int TARSSERVERSUCCESS       = 0;    //Server-side processing succeeded
const int TARSSERVERDECODEERR     = -1;   //Server-side decoding exception
const int TARSSERVERENCODEERR     = -2;   //Server-side encoding exception
const int TARSSERVERNOFUNCERR     = -3;   //There is no such function on the server side
const int TARSSERVERNOSERVANTERR  = -4;   //The server does not have the Servant object
const int TARSSERVERRESETGRID     = -5;   // server grayscale state is inconsistent
const int TARSSERVERQUEUETIMEOUT  = -6;   //server queue exceeds limit
const int TARSASYNCCALLTIMEOUT    = -7;   // Asynchronous call timeout
const int TARSINVOKETIMEOUT       = -7;   //call timeout
const int TARSPROXYCONNECTERR     = -8;   //proxy link exception
const int TARSSERVEROVERLOAD      = -9;   //Server overload, exceeding queue length
const int TARSADAPTERNULL         = -10;  //The client routing is empty, the service does not exist or all services are down.
const int TARSINVOKEBYINVALIDESET = -11;  //The client calls the set rule illegally
const int TARSCLIENTDECODEERR     = -12;  //Client decoding exception
const int TARSSERVERUNKNOWNERR    = -99;  //The server is in an abnormal position
```

## 日志

使用 tarsgo 轮换日志的快速示例：

```go
TLOG := tars.GetLogger("TLOG")
TLOG.Debug("Debug logging")
```

这将创建一个在 tars/util/rogger 中定义的\*Rogger.Logger，并在调用 GetLogger 之后，会在 config.conf 中定义的 Logpath 下创建一个日志文件，其名称为 cfg.App + "." + cfg.Server + "\_" +名称，该日志文件将在 100MB（默认）后轮换，最大轮换文件数为 10（默认）。

如果你不想按文件大小轮换日志。 例如，你想要按天轮换，使用：

```go
TLOG := tars.GetDayLogger("TLOG",1)
TLOG.Debug("Debug logging")
```

使用 GetHourLogger\("TLOG",1\)按小时轮换日志。如果你想打日志到 config.conf 中定义的名为 tars.tarslog.LogObj 的远程服务器上，你不得不先配置一个日志服务器。可以在 tars/protocol/res/LogF.tars 中找到完整的 tars 文件定义，可以在 Tencent/Tars/cpp/framework/LogServer 中查找日志服务器。快速示例如下：

```go
TLOG := GetRemoteLogger("TLOG")
TLOG.Debug("Debug logging")
```

如果你想设置日志等级，你可以在 Tencent/Tars/web 下的 tars 项目提供的 OSS 平台上设置它。 如果你想自定义你的日志，请在 tars/util/logger，tars/logger.go 和 tars/remotelogger.go 中的查看更多细节。

## 服务管理

Tars 服务框架支持动态接收命令来处理相关的业务逻辑，例如动态更新配置

tarsgo 目前有 tars.viewversion / tars.setloglevel 管理命令。 用户可以从 oss 发送管理命令来查看版本或设置日志等级。

如果你想定义你自己的管理命令，请看下面的例子：

```go
func helloAdmin(who string ) (string, error) {
	return who, nil
}
tars.RegisterAdmin("tars.helloAdmin",  helloAdmin)
```

然后你可以发送自定义的管理命令“tars.helloAdmin tarsgo”，tarsgo 将在浏览器中显示。

举例:

```go
// A function  should be in this format
type adminFn func(string) (string, error)

//then u should registry this function using

func RegisterAdmin(name string, fn adminFn)
```

## 统计上报

上报统计信息是向 Tars 框架内的 tarsstat 上报耗时信息和其他信息。 无需用户开发，只需在程序初始化期间正确设置相关信息后，就可以在框架内自动报告（包括客户端和服务端）。

客户端调用上报接口后，会暂时将信息存储在内存中，当到达某个时间点时，会向 tarsstat 服务上报（默认为 1 分钟上报一次）。 我们将两个上报时间点之间的时间间隔称为统计间隔，在统计间隔中会执行诸如聚合和比较相同 key 的一些操作。 示例代码如下：

```go
//for error
ReportStat(msg, 0, 1, 0)

//for success
ReportStat(msg, 1, 0, 0)


//func ReportStat(msg *Message, succ int32, timeout int32, exec int32)
//see more detail in tars/statf.go
```

描述:

> - 通常，我们不必关心统计上报，每次客户端调用服务端之后，无论成功与否，tarsgo 框架都将会上报。 如果你设置正确，将在 Web 管理系统中显示成功率，失败率，平均耗时等。
> - 如果主服务部署在 Web 管理系统上，则无需定义 Communicator、设置 tarsregistry，tarsstat 等的配置，该服务将会自动上报这些信息。
> - 如果未在 Web 管理系统上部署主服务或程序，则需要定义 Communicator，设置 tarsregistry，tarsstat 等，以便您可以在 Web 管理系统上查看被调服务的服务监控。
> - 数据定期上报是可以在通信器的配置中设置。

## 异常上报

为了更好地监控，TARS 框架支持直接向程序中的 tarsnotify 上报异常情况，并可在 WEB 管理页面上查看。

该框架提供了三个宏来上报不同类型的异常：

```go
tars.ReportNotifyInfo(notifyf.NOTIFYLEVEL_NOTIFYERROR, "Get data from mysql error!")
```

Info 是一个字符串，可以直接将字符串上报给 tarsnotify。 上报的字符串可以在页面上看到，随后，我们可以根据上报的信息进行报警。

如果结合: http://alarm.k.tarsyun.com/alarms/index.html#/document, 能快速将异常通知到您!
## 特性监控

为了便于业务统计，TARS 框架还支持在 Web 管理平台上显示信息。

目前支持的统计类型包括：

> - Sum\(sum\) //计算每个上报值的总和
> - Average\(avg\) //计算每个上报值的均值
> - Distribution\(distr\) //计算每个上报的分布，其参数是一个列表，来计算每个区间的概率分布
> - Maximum\(max\) //计算每个上报值的最大值
> - Minimum\(min\) // 计算每个上报值的最小值
> - Count\(count\) //计算上报次数

示例代码如下：

```go
    sum := tars.NewSum()
    count := tars.NewCount()
    max := tars.NewMax()
    min := tars.NewMin()
    d := []int{10, 20, 30, 50}
    distr := tars.NewDistr(d)
    p := tars.CreatePropertyReport("testproperty", sum, count, max, min, distr)
    for i := 0; i < 5; i++ {
        v := rand.Intn(100)
        p.Report(v)

    }
```

描述:

> - 定期上报数据，可以在通信器的配置中设置，目前是每分钟上报一次;
> - 创建一个 PropertyReportPtr 函数：参数 createPropertyReport 可以是任何统计方法的集合，示例中使用六种统计方法，通常只需要使用一个或两个;
> - 注意，当你在调用 createPropertyReport 时，必须在启用服务后创建并保存所创建的对象，然后只需将对象上报，不要在你每次使用时都创建它。

## 远程配置

用户可以从 OSS 设置远程配置。详情请查看[https://github.com/TarsCloud/TarsFramework/blob/master/docs-en/tars_config.md](https://github.com/TarsCloud/TarsFramework/blob/master/docs-en/tars_config.md) . 如下示例用于说明如何使用此 api 从远程获取配置文件。

```go
import "github.com/TarsCloud/TarsGo/tars"
...
cfg := tars.GetServerConfig()
remoteConf := tars.NewRConf(cfg.App, cfg.Server, cfg.BasePath)
config, _ := remoteConf.GetConfig("test.conf")

...
```

## setting.go

tars 包中的 setting.go 用于控制 tarsgo 性能和特性。有些选项应该从 Getserverconfig\(\)中更新。

```go
//number of woker routine to handle client request
//zero means  no contorl ,just one goroutine for a client request.
//runtime.NumCpu() usually best performance in the benchmark.
var MaxInvoke int = 0

const (
	//for now ,some option shuold update from remote config

	//version
	TarsVsersion string = "1.0.0"

	//server

	AcceptTimeout time.Duration = 500 * time.Millisecond
	//zero for not set read deadline for Conn (better  performance)
	ReadTimeout time.Duration = 0 * time.Millisecond
	//zero for not set write deadline for Conn (better performance)
	WriteTimeout time.Duration = 0 * time.Millisecond
	//zero for not set deadline for invoke user interface (better performance)
	HandleTimeout  time.Duration = 0 * time.Millisecond
	IdleTimeout    time.Duration = 600000 * time.Millisecond
	ZombileTimeout time.Duration = time.Second * 10
	QueueCap       int           = 10000000

	//client
	ClientQueueLen     int           = 10000
	ClientIdleTimeout  time.Duration = time.Second * 600
	ClientReadTimeout  time.Duration = time.Millisecond * 100
	ClientWriteTimeout time.Duration = time.Millisecond * 3000
	ReqDefaultTimeout  int32         = 3000
	ObjQueueMax        int32         = 10000

	//report
	PropertyReportInterval time.Duration = 10 * time.Second
	StatReportInterval     time.Duration = 10 * time.Second

	//mainloop
	MainLoopTicker time.Duration = 10 * time.Second

	//adapter
	AdapterProxyTicker     time.Duration = 10 * time.Second
	AdapterProxyResetCount int           = 5

	//communicator default ,update from remote config
	refreshEndpointInterval int = 60000
	reportInterval          int = 10000
	AsyncInvokeTimeout      int = 3000

	//tcp network config
	TCPReadBuffer  = 128 * 1024 * 1024
	TCPWriteBuffer = 128 * 1024 * 1024
	TCPNoDelay     = false
)

```

## HTTP 支持

目前的 tar.TarsHttpMux 和 golang 内置 http.ServeMux 使用方式是一致的，其中 pattern 参数做为监控数据的接口名，后续会参考`github.com/gorilla/mux`实现功能更强大的路由功能。

具体实现可参考下面的例子：

```go
package main

import (
	"net/http"
	"github.com/TarsCloud/TarsGo/tars"
)

func main() {
	mux := &tars.TarsHttpMux{}
	mux.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		w.Write([]byte("Hello tafgo"))
	})

    cfg := tars.GetServerConfig()
	tars.AddHttpServant(mux, cfg.App+"."+cfg.Server+".HttpObj") //Register http server
	tars.Run()
}

```

## Context 支持

TarsGo 之前在生成的客户端代码，或者用户传入的实现代码里面，都没有使用 context。 这使得我们想传递一些框架的信息，比如客户端 ip，端口等，或者用户传递一些调用链的信息给框架，都很难于实现。 通过接口的一次重构，支持了 context，这些上下文的信息，将都通过 context 来实现。 这次重构为了兼容老的用户行为，采用了完全兼容的设计。

服务端使用 context

```go
type ContextTestImp struct {
}
//只需在接口上添加 ctx context.Context参数
func (imp *ContextTestImp) Add(ctx context.Context, a int32, b int32, c *int32) (int32, error) {
	//我们可以通过context 获取框架传递的信息，比如下面的获取ip， 甚至返回一些信息给框架，详见tars/util/current下面的接口
	ip, ok := current.GetClientIPFromContext(ctx)
    if !ok {
        logger.Error("Error getting ip from context")
    }
	return 0, nil
}
//以前使用AddServant ，现在只需改成AddServantWithContext
app.AddServantWithContext(imp, cfg.App+"."+cfg.Server+".ContextTestObj")
```

客户端使用 context

```go
    ctx := context.Background()
    c := make(map[string]string)
    c["a"] = "b"
//以前使用app.Add 进行客户端调用，这里只要变成app.AddWithContext ，就可以传递context给框架，如果要设置给tars请求的context
//可以多传入参数，比如c，参数c是可选的，格式是 ...[string]string
    ret, err := app.AddWithContext(ctx, i, i*2, &out, c)
```

服务端和客户端的完整例子，详见 TarGo/examples

## filter 机制（插件） 和 zipkin opentracing

为了支持用户编写插件，我们支持了 filter 机制，分为服务端的过滤器和客户端过滤器

```go
//服务端过滤器， 传入dispatch，和f， 用于调用用户代码， req， 和resp为传入的用户请求和服务端相应包体
type ServerFilter func(ctx context.Context, d Dispatch, f interface{}, req *requestf.RequestPacket, resp *requestf.ResponsePacket, withContext bool) (err error)
//客户端过滤器， 传入msg（包含obj信息，adapter信息，req和resp包体）， 还有用户设定的调用超时
type ClientFilter func(ctx context.Context, msg *Message, invoke Invoke, timeout time.Duration) (err error)
//注册服务端过滤器
//func RegisterServerFilter(f ServerFilter)
//注册客户端过滤器
//func RegisterClientFilter(f ClientFilter)
```

有了过滤器，我们就能对服务端和客户端的请求做一些过滤，比如使用 hook 用于分布式追踪的 opentracing 的 span。 我们来看下客户端 filter 的例子：

```go
//生成客户端tars filter，通过注册这个filter来实现span的注入
func ZipkinClientFilter() tars.ClientFilter {
	return func(ctx context.Context, msg *tars.Message, invoke tars.Invoke, timeout time.Duration) (err error) {
		var pCtx opentracing.SpanContext
		req := msg.Req
		//先从客户端调用的context 里面看下有没有传递来调用链的信息，
		//如果有，则以这个做为父span，如果没有，则起一个新的span，span名字是RPC请求的函数名
		if parent := opentracing.SpanFromContext(ctx); parent != nil {
			pCtx = parent.Context()
		}
		cSpan := opentracing.GlobalTracer().StartSpan(
			req.SFuncName,
			opentracing.ChildOf(pCtx),
			ext.SpanKindRPCClient,
		)
		defer cSpan.Finish()
		cfg := tars.GetServerConfig()

		//设置span的信息，比如我们调用的客户端的ip地址，请求的接口，方法，协议，客户端版本等信息
		cSpan.SetTag("client.ipv4", cfg.LocalIP)
		cSpan.SetTag("tars.interface", req.SServantName)
		cSpan.SetTag("tars.method", req.SFuncName)
		cSpan.SetTag("tars.protocol", "tars")
		cSpan.SetTag("tars.client.version", tars.TarsVersion)

		//将span注入到 请求包体的  Status里面，status 是一个map[strint]string 的结构体
		if req.Status != nil {
			err = opentracing.GlobalTracer().Inject(cSpan.Context(), opentracing.TextMap, opentracing.TextMapCarrier(req.Status))
			if err != nil {
				logger.Error("inject span to status error:", err)
			}
		} else {
			s := make(map[string]string)
			err = opentracing.GlobalTracer().Inject(cSpan.Context(), opentracing.TextMap, opentracing.TextMapCarrier(s))
			if err != nil {
				logger.Error("inject span to status error:", err)
			} else {
				req.Status = s
			}
		}
		//没什么其他需要修改的，就进行客户端调用
		err = invoke(ctx, msg, timeout)
		if err != nil {
			//调用错误，则记录span的错误信息
			ext.Error.Set(cSpan, true)
			cSpan.LogFields(oplog.String("event", "error"), oplog.String("message", err.Error()))
		}

		return err
	}
```

服务端也会注册一个 filter，主要功能就是从 request 包体的 status 提取调用链的上下文，以这个作为父 span，进行调用信息的记录。

详细代码参见 TarsGo/tars/plugin/zipkintracing 完整的 zipkin tracing 的客户端和服务端例子，详见 TarsGo/examples 下面的 ZipkinTraceClient 和 ZipkinTraceServer
