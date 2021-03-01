# 基础概念

> - [APP](#main-chapter-1)
> - [Server](#main-chapter-2)
> - [Servant](#main-chapter-3)
> - [module](#main-chapter-4)
> - [Tars 文件目录规范](#main-chapter-5)
> - [服务端开发方式](#main-chapter-6)
> - [客户端开发方式](#main-chapter-7)
> - [模板配置](#main-chapter-8)
> - [业务配置](#main-chapter-9)
> - [日志系统](#main-chapter-10)
> - [开发调试发布](#main-chapter-11)

本文主要介绍 TARS 平台中一些常见的基础概念, 弄清楚这些基础概念有助于对平台的使用, 这篇文档可以反复阅读, 如果第一次看不懂没有关系, 写完 helloworld, 再回来阅读文档会有不同的感受.

## 1. <span id="main-chapter-1"></span> APP

APP 即应用名，标识一组服务的一个小集合, 开发者可以根据需要自己定义, 通常表示实现某个业务系统名称.

- 在 Tars 系统中，应用名必须唯一, 例如：TestApp
- 通常应用名对应代码中的某个名字空间
- `tars` 这个应用名是框架使用的, 业务服务请不要使用

## 2. <span id="main-chapter-2"></span> Server

Server 即服务名，提供服务的进程名称

- Server 名字根据业务服务功能命名, 它会在 TARS web 平台上左边服务树上展示
- 一个 Server 必须属于某个 App, App 下的 Server 名称都具备唯一性
- 一般命名为：XXServer，例如 LogServer，TimerServer 等
- 一个 Server 代表一个独立的程序, 绑定至少一个 ip, 实现一组相关的接口

## 3. <span id="main-chapter-3"></span> Servant

Servant 即服务提供者, 提供了一个多个具体的接口(interface), 提供给客户端调用

- Servant 对应服务代码中一个类, 继承于 tars 协议文件中的 interface(内涵多个具体的函数), 由业务开发者实现
- 一个 Servant 必须属于某个 Server, Server 下的 Servant 名称都具备唯一性
- Servant 需要一个名称, 比如: HelloObj, 当提供给客户端使用的, 全称是: App.Server.Servant, 比如: Test.HelloServer.HelloObj
- 客户端调用 Server 时, 只需要指定 Servant 的名称即可完成远程通信(如何实现后续会介绍)

**Tars 采取这种三层结构, 尽可能的避免不同业务开发者开发的服务名称和 Servant 名称冲突**

## 4. <span id="main-chapter-4"></span> module

module 是 tars 协议文件中的关键字, 定义了协议的空间, 也对应了各语言名字空间(c++)或者包名(java, go)或模块(nodejs,php)

## 5. <span id="main-chapter-5"></span> Tars 文件目录规范

Tars 文件是 TARS 服务的协议通信接口，尤其某 Tars Server 的客户端调用 Server 时都需要依赖该 Server 的 tars protocol 文件, 因此非常重要，在管理上我们推荐按照如下方式管理(当然你可以不采取该模式, 构建你自己合适的开发方式):

- tars 文件原则上和相应的 server 放在一起；
- 每个 server 在开发机上建立/home/tarsproto/\[namespace\]/\[server\]子目录；
- 所有 tars 文件需要更新到/home/tarsproto 下相应 server 的目录；
- 使用其他 server 的 tars 文件时，需要到/home/tarsproto 中使用，不能 copy 到本目录下；
- 如此, 在相同服务器上开发服务时, 你只需要将 tars 文件 release 到该目录下, 就能方便其他调用方使用
- tars 的接口原则上只能增加，不能减少或修改；
- 各语言提供的 Tars 服务框架, 都提供了快速 release tars 文件到/home/tarsproto/\[namespace\]/\[server\]下的工具

## 6. <span id="main-chapter-6"></span> 服务端开发方式

任何 Tars 服务端和客户端的开发方式都基本一样:

- 确定 APP, Server, Servant 名称
- 编写 tars 文件, 定义服务对外提供的 interface 以及 interface 下面的函数, 主要 interface 必须有一个, interface 下面的函数可以有多个, [tars 的文件的语法请参考](tars-protocol.md)
- 使用 tars2xxx 工具(不同语言的工具不同), 将 tars 文件生成不同的语言的代码
- 实现 Tars 服务(请参考不同语言的文档), 继承生成的文件中的 Servant 类, 实现 Servant 的 interface
- 编译服务, 发布在管理平台上(在管理平台需要配置 App, Server, Servant Obj 名称等), 可以参考后续章节
- 当然你的服务也可以本地运行, 可以在平台上启动服务后, `ps -ef`看到服务的启动方式后, 放在本地执行即可(注意可能有配置文件, 需要修改端口等信息)

正常情况下, 服务最终都通过 tarsweb 发布运行在 tars 平台上的各个节点服务器上, 但是在调试过程中希望在本机运行, 该如何处理?

服务启动实际上无非是一条命令行, 比如 c++服务是:

```
HelloServer --config=xxxxx.conf
```

这里配置 config 表示服务启动的配置文件, 在 tars 平台上是由 tarsnode 通过拉取模板配置生成的, 并拉起 HelloServer, 如果你想本地运行服务, 就必须本地具备这个配置文件.

注意:

- 建议弄清楚 config 的主要配置项含义, [参考开发指南](../dev/tarscpp/tars-guide.md)
- config 中的 ip 要注意提供成本机的
- `node=tars.tarsnode.ServerObj@xxxx`, 表示连接的 tarsnode 的地址, 如果本地没有 tarsnode, 这项配置可以去掉
- `local=...`, 表示开放的本机给 tarsnode 连接的端口, 如果没有 tarsnode, 可以掉这项配置
- `locator=...`, 表示主控中心的地址(框架地址), 用于根据服务名字获取 ip list 的
- 如果你是独立的客户端, 有这项配置, 就可以不用指定其他服务地址, 进行访问

**注意这个配置文件不是业务配置, 而是服务框架的配置, 对应 tars 平台上的模板!**

如何获取这个配置文件呢?

你可以先将服务发布到平台的某个节点上, 然后登陆节点服务器, 运行:

```
ps -efww | grep ${your server name}
```

你可以看到服务启动的命令行, 将对应的配置文件 copy 到本地, 并且打开配置文件, 修改配置文件里面对应 ip port 以及相关路径, 然后使用相同的命令行本地运行即可!

其他语言方式类似!

## 7. <span id="main-chapter-7"></span> 客户端开发方式

完成服务端编写和启动后, 即可编写客户端, 通过引用 tars 文件生成的客户端代码, 并构建通信器, 使用通信器并根据 Servant 名称获取到对应的服务的代理对象, 使用代理对象完成通信.

注意:

- 通信器(Communicator)是客户端管理线程和网络的一个类, 它在各大语言中都有对应的类, 通常服务框架中会提供一个初始化过的给你使用(通常你使用这个对象即可), 如果是存客户端, 那么你就需要自己创建通信器
- 如果你的 client 是另外一个服务, 并部署在框架上, 那么你的通信器可以使用框架提供好的通信器(参考各语言文档), 此时调用 Server 时, 不需要指定 ip port, 只需要 Servant 的 Obj 名称即可, 框架会自动寻址并完成调用
- 如果你的 client 是独立的客户端程序, 并不部署在框架上, 那么你可以自己创建通信器, 此时调用服务有两种方式:
  > - 直接指定服务端的 ip 端口的方式(你可以指定多个, 框架会自动容灾切换), 各语言基本相同, 比如 c++语言:

```
Communicator *communicator = new Communicator();
HelloPrx helloPrx = communicator->stringToProxy<HelloPrx>("Test.HelloServer.HelloObj@tcp -h xxx -p yyy:tcp -h www -p zzz");
helloPrx->call();
```

> - 也可以通信器指定到对应的框架的主控中, 这样就不需要指定对应的 ip port 了, 各语言基本相同, 比如 c++语言:

```
Communicator *communicator = new Communicator();
communicator->setProperty("locator", "tars.tarsregistry.QueryObj@tcp -h xxxx -p 17890");
HelloPrx helloPrx = communicator->stringToProxy<HelloPrx>("Test.HelloServer.HelloObj");
helloPrx->call();
```

这种客户端调用方式, 虽然服务可以寻址和容灾, 但是没有上报信息, 如果需要上报信息还需要指定其他相关属性, 比如:

```
communicator->setProperty("stat", "tars.tarsstat.StatObj");
communicator->setProperty("property", "tars.tarspropery.PropertyObj");
```

当然你可以直接用配置文件来初始化通信器, 参考 web 平台模板配置中的 client 部分. 另外上报服务这里类同, 如果没有 locator 指定框架的主控地址, 你就需要自己指定上报的 ip port.

## 8 <span id="main-chapter-8"></span> 模板配置

web 平台上, 运维管理中有模板配置, 模板配置对于框架非常重要, 需要去理解模板配置的作用.

每一个部署在 TARS 框架上的服务, 最终其实被框架发布到对应的节点了(tarsnode), 那么 tarsnode 在拉起这个服务时如何知道服务绑定的端口, 启动的线程数等信息呢? 答案就是通过: 模板配置

tarsnode 会去平台拉取服务对应的模板(服务部署时配置好的), 然后根据模板生成服务对应的配置, 并用户这个配置启动服务.

注意不同语言使用的模板配置文件不同, 可以参考后续各语言的文档.

**强烈建议你不需要修改框架自带的模板, 因为后续框架升级可能会修改这些模板内容, 如果你需要修改, 你可以继承该模板, 让你的服务使用继承的模板**

## 9 <span id="main-chapter-9"></span> 业务配置

上一节讲到模板配置, 模板配置实际是 rpc server 用到的公用配置, 它是 tarsnode 生成的. 但是对于业务服务而言, 一般都有自己的配置信息, 这种配置我们称之为业务配置.

业务配置一般都在 web 平台上配置中心来管理, 使用方式如下:

- 在 web 平台上, 对应服务的配置中心下, 添加业务配置文件
- 在服务代码中, 加载配置文件(各语言的 rpc 库都提供了加载配置文件的 api)
- 如果有需要, 也可以在 web 平台上 push 配置文件到具体的 server(php 的 swoole 不支持)

业务配置具体使用可以参见[业务配置](../dev/tars-config.md)

## 10 <span id="main-chapter-10"></span> 日志系统

业务服务中通常也需要打印日志, 框架也提供了集中的日志中心.

通常日志分两类:

- 一类是 debug 日志(循环日志/单个文件控制大小/多个日志文件循环),
- 一类是按天/小时等的日志, 这类日志通常记录重要的日志信息, 这类日志可以输出到远程日志中心上.

通过日志 api, 你可以将日志直接输出到远程的日志中心上, 即 tarslog 所在机器上(日志路径为: /usr/local/app/tars/remote_app_log)

所有语言的框架都提供了远程日志的 api.

另外在 web 平台上, 点击服务详情界面, 点击服务名称, 也可以快速查看到服务的本机日志.

## 11 <span id="main-chapter-11"></span> 开发调试发布

如果开发过程中, 每次都需要手工发布到 web 平台调试, 调试效率是非常低, 因此 Tars 平台提供了一个方式, 能够一键发布服务到 Tars 框架上.

使用方式如下:

- 这需要 web >= 2.0.0, tarscpp>=2.1.0 的版本才能支持.
- 完成框架安装后, 登录用户中心, 创建一个 token
- linux 上使用 curl 命令即可完成服务的上传和发布,以 Test/HelloServer 为例, [参考 cmake 管理规范](../dev/tarscpp/tars-spec.md)

```
curl http://${your-web-host}/api/upload_and_publish?ticket=${token} -Fsuse=@HelloServer.tgz -Fapplication=Test -Fmodule_name=HelloServer -Fcomment=dev
```

**注意替换你的 token**

c++版本的 cmake 已经内嵌了命令行在服务的 CMakeLists.txt 中, 比如用 cmake_tars_server.sh 创建服务之后, 只需要:

```
cd build
cmake .. -DTARS_WEB_HOST=${WEB_HOST} -DTARS_TOKEN=${TOKEN}
make HelloServer-tar
make HelloServer-upload
```

即可完成服务的上传和发布(提前需要在 web 平台配置好)

注意:

- 替换 WEB_HOST 和 token
- HelloServer.tgz 是 c++的发布包, java 对应是 war 包, 其他语言类似, 对应你上传到 web 平台的发布包
