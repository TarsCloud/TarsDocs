

# 目录
> * [脚手架和自动发布工具](#chapter-1)
> * [Tars rpc server开发](#chapter-2)
> * [Tars http server开发](#chapter-3)
> * [Tars client rpc调用](#chapter-4)
> * [其它](#chapter-5)

## Tars.js 快速入门
本文档提供Tars nodejs几个开发场景的step by step快速入门，更多细节与特性可参考[Tars nodejs开发文档](https://tarscloud.github.io/TarsDocs/kai-fa/tars.js/)，以及其中各个子模块的readme文档。

## 1.  <span id="chapter-1"></span> 脚手架及自动发布工具
Tars.js提供了 [脚手架和自动发布工具](https://github.com/tars-node/nodetools-cli)  

安装：`npm install -g @tars/nodetools-cli`  
初始化项目：`nodetools init`  
打包发布：`nodetools upload`

## 2. <span id="chapter-2"></span> Tars rpc server开发  
和其它nodejs项目一样初始化项目  
- 初始化 npm init
- 安装依赖 npm install @tars/rpc
### 编写tars协议文件  
首先参照[tars协议语言文档]()编写协议文件，规定主被调双方通信的数据结构和调用接口。本例实现一个简单的 Hello.tars：  
```
module Hello
{
	interface Test
	{
		int add(int a, int b, out int c);
	};
}; 
```
### 协议文件转换
协议编写完成后，通过[tars2node工具](https://github.com/tars-node/tars2node)将协议转换为rpc服务端代码。  
`tars2node --server Hello.tars`  
转换成功后代码目录如下：  
```
.
|-- Hello.js
|-- Hello.tars
|-- HelloImp.js
|-- node_modules
|-- package-lock.json
`-- package.json
```  
其中`Hello.js`和`HelloImp.js`就是自动生成的服务端代码。  
若要使用typescript开发，可以用命令`tars2node --server --ts Hello.tars`来转换。  
目前tars2node工具仅支持linux系统，无其它依赖项。若无linux开发环境，可以用docker启动一个centos或者ubuntu容器，下载工具并转换。
### rpc服务端代码实现  
协议转换完成后，只需要在`HelloImp.js`文件中补全接口逻辑代码，并编写一个服务入口函数，即可完成rpc服务端代码编写。  
HelloImp.js接口逻辑：  

```javascript
"use strict";
var Hello = require("./Hello.js").Hello;
module.exports.Hello = Hello;
Hello.HelloProxyImp.prototype.initialize = function () {
    //TODO::
};
Hello.HelloProxyImp.prototype.initialize = function () {
    //TODO::
};
Hello.HelloProxyImp.prototype.add = function (current, a, b, c) {
    c = a + b;
    current.sendResponse(0, c);
};

```  

接口实现的代码中，需要开发者自己实现的只有add函数体中的两行代码。  

然后实现一个服务入口文件 app.js:  

```javascript
const Tars   = require("@tars/rpc");
const Hello  = require("./HelloImp.js").Hello;

const APP_NAME = "Hello", SERVER_NAME = "HelloServer", OBJ_NAME = "HelloObj";
let servantName = `${APP_NAME}.${SERVER_NAME}.${OBJ_NAME}`;
let impMap = {
    [servantName] : Hello.TestImp
};
Tars.server.getServant(process.env.TARS_CONFIG || `./${APP_NAME}.${SERVER_NAME}.config.conf`).forEach(function (config){
    let svr = Tars.server.createServer(impMap[config.servant]);
    svr.start(config);
});
```  
大功告成，此时这个服务就能在Tars平台上跑起来了。  
- APP_NAME是`应用名`，SERVER_NAME是应用下的`服务名`，OBJ_NAME是服务下面的服务对象`Obj名`，记住这三个变量，服务部署的时候需要用到。  
- process.env.TARS_CONFIG是tars启动器提供的nodejs启动器设置的环境变量，指向tars框架为服务生成的服务模板文件，模板文件中指定了服务监听端口、名字服务地址等设置。  
- 入口文件的名字可以是app.js、index.js，或者自定义等，在[tars nodejs启动器文档](https://github.com/tars-node/node-agent)的入口点描述章节中查看详细说明。

### 打包、部署、发布 
**代码打包**   
tars nodejs打发布包使用的是tars-deploy模块，这个模块通过命令行运行，需要全局安装：`npm install @tars/deploy -g`  
安装完后在服务根目录下运行 `tars-deploy 服务名`，即可在服务根目录下生成 `服务名.tgz`发布包，注意这里的服务名和入口点文件中的服务名，以及平台上部署时所用的服务名，要保持一致。  

**服务部署**  
登录Tars管理平台，访问 运维管理 -&gt; 服务部署。  
```
应用名 = 入口点文件中的应用名
服务名 = 入口点文件中的服务名
服务类型 = tars_nodejs
模板 = tars.default

节点 = 要部署到tarsnode节点ip
端口 = 自动生成或手动填写
协议 = TARS
```  
提交即可  

**节点发布**  
访问 服务管理 -&gt; 服务管理 -&gt; 左侧菜单中选中刚刚部署的服务 -&gt; 发布管理 -&gt; 勾选节点 -&gt; 发布选中节点 -&gt; 上传发布包 -&gt; 发布  
发布成功后，可以在服务管理页面看到节点状态是active。至此，打包部署发布的流程完毕，一个tars nodejs的rpc server可以提供服务了。  

**使用自动发布工具**
使用自动发布工具可以省掉代码打包和节点发布的操作，直接在项目目录运行 `nodetools upload`即可。

## 3. <span id="chapter-3"></span> Tars http server开发  
第1节中介绍了如何开发一个tars协议的服务，并部署到Tars平台上运行。而tars平台对应用的通信协议是没有任何限制的，我们也可以将一个提供http协议的nodejs服务放到tars平台上运行。 除了数据编解码、组包协议与tars协议不同外，进程管理、监控上报、日志收集等特性都可以享受到。
### http服务代码实现
根据业务需求以及您的喜好选择框架（！Tars 不限制您使用的框架！），编写业务代码：

[Node.js 官网](https://nodejs.org/en/about/) 的HTTP Server例子
```javascript
const http = require('http');

const hostname = process.env.IP || '127.0.0.1';
const port = process.env.PORT || 3000;

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World\n');
});

server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});
```

[Koa 官网](https://koajs.com/) 的HTTP Server例子  
```javascript
const Koa = require('koa');
const app = new Koa();
const hostname = process.env.IP || '127.0.0.1';
const port = process.env.PORT || 3000;
app.use(async ctx => {
  ctx.body = 'Hello World';
});

app.listen(hostname, port);
```

上面的示例程序，在 Tars 平台上运行均只需要修改 IP 和端口即可，IP和端口可以从环境变量中获取。
### 打包与部署
**代码打包**  
同rpc server打包流程。  

**服务部署**  
登录Tars管理平台，访问 运维管理 -&gt; 服务部署。  
```
应用名 = 服务所属的应用名
服务名 = 服务名
服务类型 = tars_nodejs
模板 = tars.default

节点 = 要部署到tarsnode节点ip
端口 = 自动生成或手动填写
协议 = 非TARS
```  
提交即可  

这里要注意的是：
- 服务名必须和打包是设置的服务名相同
- 协议一定要选非TARS，选错了则无法从环境变量获取ip和port  

**节点发布**   
同rpc server节点发布流程。  

## 4. <span id="chapter-4"></span> Tars client rpc调用  
本节介绍如何通过Tars rpc client来调用tars rpc
 server。被调的rpc server不限语言，这里就以第1节中发布的HelloServer为例。
### 协议文件转换  
`tars2node --client Hello.tars`  
得到协议的客户端代理文件  
```
.
`-- HelloProxy.js
```

### rpc client调用代码实现
```javascript
const Tars  = require("@tars/rpc").client;
const Hello = require("./HelloProxy.js").Hello;

const proxy = Tars.stringToProxy(Hello.TestProxy, "Hello.HelloServer.HelloObj");

proxy.add(6, 7).then((data)=>{
    console.log("接口返回值：", data.response.return)
    console.log("接口返回字段：", data.response.arguments.c)
    console.log("调用耗时", data.response.costtime)
}).catch((e)=>{
    console.log("框架错误码：", e.response.error.code)
    console.log("框架错误信息：", e.response.error.message)
    console.log("调用耗时", e.response.costtime)
})
```  
这里实际开发时要注意proxy对象应该做成单例，不要每次调用都生成一个proxy对象。  

### 复杂数据结构的操作  
第1节中示例协议只包含了很简单的int类型数据结构，实际应用中数据结构会相当复杂多变，Tars nodejs使用 [@tars/stream 模块](https://github.com/tars-node/stream) 来处理数据的编解码。  

要记住一点，Tars rpc中接口调用过程中，除了基本类型数据可以直接使用js原生类型外，struct/map/list类型的数据都必须通过编解码模块封装的类型来操作，例如add接口的入参、出参变成这样：  

```
module Hello
{
	struct AddReq {
    0 require iNum1;
    1 require iNum2;
    2 optional map<string, string> mContext;
	};

	struct AddRsp {
		0 require iRet;
		1 require iResult;
		2 optional map<string, string> mContext;
	};

	interface Test
	{
		int add(AddReq stReq, out AddRsp stRsp);
	};
}; 

```  
那么我们调用接口时，入参需要从js的Object转换为AddReq结构体类，最方便的方法是通过结构体类的`readFromObject`成员函数来转换  
```javascript
let stReq = new Hello.AddReq()
stReq.readFromObject({
  iNum1: 1,
  iNum2: 2
})
proxy.add(stReq)...
```  

而当拿到结果时，得到的是AddRsp类的对象，如果要使用js原生对象，则需要将其转换一下，可以通过结构体类的`toObject`成员函数来转换  
```javascript
proxy.add(stReq).then((data)=>{
   let rsp = data.response.arguments.stRsp.toObject()
   ...
})...
```  

不光struct数据类型，vector、map也有这一对 原生 —— tars对象转换的函数。 除了这两个函数，@tars/stream模块还提供了更多细节的编解码函数，如果有必要请参阅其readme文档。

## 5. <span id="chapter-5"></span>其它
### tars服务模板配置 
tars服务启动时需要指定一个模板配置文件，我们在平台上部署服务时所设置的所有属性，以及一些系统默认的配置项，都会被写到此配置文件，可以在[tars模板配置文档](https://tarscloud.github.io/TarsDocs/kai-fa/mo-ban-pei-zhi.html) 中看到各种父模板的内容。  

我们可以在 服务管理 -&gt; 服务列表 -&gt; 编辑 中按照模板配置格式添加自己的私有模板，服务发布时私有模板也会被合并到模板配置文件中。  

服务中可以通过 [utils模块 —— 模板解析器](https://github.com/tars-node/utils) 来解析模板配置的内容，当服务在tars平台上运行时，可以通过`process.env.TARS_CONFIG`来获取模板配置的文件路径。  

### tars服务业务配置  
除了tars平台为每个服务生成的模板配置文件外，每个服务可能还有自己的配置文件，存放例如业务db配置、某开放平台key配置等。  

Tars平台提供业务配置文件管理的功能，配置文件添加方式为 服务管理 -&gt; 服务配置 -&gt; 添加配置  

配置文件可以是任意格式，Tars nodejs通过 [tars-config模块](https://github.com/tars-node/config)来读取在平台中添加的业务配置文件，这里不细讲，在模块readme中可看到详细用法。

### 一个服务启动多个Obj  
当我们在一个服务中想启动多个Obj，有的实现tars协议提供rpc调用服务，有的实现http协议，可以这样操作：  

在平台上，服务管理 -&gt; 服务列表 -&gt; 管理Servant -&gt; 添加Servant，填写要添加的Servant信息，例如在第1节的服务中添加一个非Tars协议的Servant。  

添加完，重新发布服务后，服务的模板配置就变成了这样  
```
<server>
  node=tars.tarsnode.ServerObj@tcp -h 172.17.0.4 -p 19386 -t 60000
  app=Hello
  server=HelloServer
  localip=172.17.0.4
  local=tcp -h 127.0.0.1 -p 3032 -t 3000
  basepath=/usr/local/app/tars/tarsnode/data/Hello.HelloServer/bin/
  datapath=/usr/local/app/tars/tarsnode/data/Hello.HelloServer/data/
  logpath=/usr/local/app/tars/app_log/
  logsize=10M
  config=tars.tarsconfig.ConfigObj
  notify=tars.tarsnotify.NotifyObj
  log=tars.tarslog.LogObj
  deactivating-timeout=3000
  logLevel=DEBUG
  <Hello.HelloServer.HelloObjAdapter>
    allow
    endpoint=tcp -h 172.17.0.4 -p 3031 -t 60000
    handlegroup=Hello.HelloServer.HelloObjAdapter
    maxconns=200000
    protocol=tars
    queuecap=10000
    queuetimeout=60000
    servant=Hello.HelloServer.HelloObj
    threads=5
  </Hello.HelloServer.HelloObjAdapter>
  <Hello.HelloServer.HelloHttpObjAdapter>
    allow
    endpoint=tcp -h 172.17.0.4 -p 3032 -t 60000
    handlegroup=Hello.HelloServer.HelloHttpObjAdapter
    maxconns=200000
    protocol=not_tars
    queuecap=10000
    queuetimeout=60000
    servant=Hello.HelloServer.HelloHttpObj
    threads=3
  </Hello.HelloServer.HelloHttpObjAdapter>
</server>
```
与之前相比，多出了 `<Hello.HelloServer.HelloHttpObjAdapter>`配置节，这就是在管理平台上添加Servant在模板配置中的体现。  

接下来，修改服务入口点代码  
```javascript
const Tars   = require("@tars/rpc");

Tars.server.getServant(process.env.TARS_CONFIG).forEach(function (config){
    if(config.protocol === "tars"){
      //TODO 实现tars rpc server启动逻辑
    } else {
      //TODO 实现http server启动逻辑
    }
});
```  
Tars.server.getServant解析模板配置文件后返回的config就是各个Adapter配置节中的属性，开发者可以根据其中的endpoint属性来监听端口。  
此时这个服务就启动了两个Obj，在两个端口上实现了两种不同的协议。

