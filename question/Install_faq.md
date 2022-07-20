# 安装常见问题

### 1. 部署ip不能为`127.0.0.1`，原因如下：

1. 每个服务一般都有一个或者多个对外服务的obj，obj要对外提供访问，不能用 `127.0.0.1`;
2. 每个服务都有一个管理的obj，它使用的ip是`127.0.0.1`，端口和obj的端口一样;

### 2. Tars主要包括rpc开发框架、框架基础服务、web管理系统。

其中，rpc开发框架支持c++, java, php, go, nodejs，都有自己的基础库、idl代码生成工具、客户端和服务端实现，互不依赖。

且都可以运行在linux/mac/windows

### 3. Tars依赖的软件或者工具，在部署Tars时，必须事先安装好，参见 [手动安装](https://github.com/TarsCloud/TarsDocs/blob/master/installation/source.md).

### 4. Tars部署安装步骤要按 [手动安装](https://github.com/TarsCloud/TarsDocs/blob/master/installation/source.md) 文档先后顺序进行。

### 5. 执行tars\_start.sh脚本后，需要确定一下tars核心基础服务的进程是否是活的

可以使用命令:`ps -ef|grep tars`，看看 tarsregistry、tarsAdminRegistry、tarsnode、tarsconfig、tarspatch进程是否存在

### 6. 服务部署的相关路径，如下

服务打的日志路径在 `/usr/local/app/tars/app_log/${服务的应用名}/${服务的服务名}/` 目录下，例如:

```text
/usr/local/app/tars/app_log/Test/HelloServer/
```

服务的可执行文件在 `/usr/local/app/tars/tarsnode/data/${服务的应用名}.${服务的服务名}/bin/` 下,例如:

```text
/usr/local/app/tars/tarsnode/data/Test.HelloServer/bin/
```

服务的模版配置文件在 `/usr/local/app/tars/tarsnode/data/${服务的应用名}.${服务的服务名}/conf/` 下, 例如:

```text
/usr/local/app/tars/tarsnode/data/Test.HelloServer/conf/
```

服务的缓存信息文件在 `/usr/local/app/tars/tarsnode/data/${服务的应用名}.${服务的服务名}/data/` 下,例如:

```text
/usr/local/app/tars/tarsnode/data/Test.HelloServer/data/
```

### 7. 查看日志的方法

例如：

`/usr/local/app/tars/app_log/Test/HelloServer/` 目录下会有 `Test.HelloServer.log` , 发布失败的话，可以看看里面有什么错误日志，其日志级别为ERROR。

### 8. java的服务部署时，模版配置要选择 `tars.tarsjava.default`

![](../assets/java_server_deploy.png)

### 9. 服务监控数据展示问题，注意查询条件

![](../assets/stat.png)

### 10. 老版本升级到支持IPv6版本\(v1.6.0及以上\)，数据库升级方法

执行以下数据库升级脚本，注意保证数据库密码正确性

```text
TarsFramework/sql/upgrade2IPv6.sh
```

## 11. tarsnode can not run java server: cannot execute java

安装jdk以后重启tarsnode

```
/usr/local/app/tars/tarsnode/util/start.sh
```

# TarsCPP 常见问题

### Tars C++开发环境如何搭建？

> * 参考tars\_install.md

### Tars C++如何快速上手？

> * 文档参考tars\_cpp\_quickstart.md，相关示例代码参考examples目录

### tars/tup协议是什么？

> * 具体参考tars\_tup.md

### Tars C++是否支持自定义协议（比如:http）？

> * Tars C++除了支持tars/tup协议之外，还支持业务自定义协议，具体参考tars\_cpp\_thirdprotocol.md

### Tars C++如何拉取业务配置文件？

> * Tars C++支持通过使用addConfig方法获取指定的业务配置文件，业务配置相关远离，可以参考tars\_config.md

### 在Tars框架中运行的服务是如何被监控的？

> * 具体参考tars\_server\_monitor.md

### Tars C++通讯器如何创建？

> * 如果服务基于TAF框架,请直接从Applicatin获取,不要自己创建。例如:

```text
Application::getCommunicator()->stringToProxy<NotifyPrx>(ServerConfig::Notify);
```

> * 如果服务非基于TAF框架,只是TAF客户端，可以用new Communicator\(conf\) 初始化通信器。例如:

```text
TC_Config conf(“config.conf”);
CommunicatorPtr c = new Communicator(conf);
```

### Tars C++调用的超时时间如何设置？

> * 具体参考tars\_cpp\_user\_guide.md

### 服务发布后但没有运行怎么查看原因？

> * 是否服务配置文件没有正确获取。例如：在web平台上配置的配置文件名与在程序add下载的配置文件名不一致。
> * 查找问题时，首先看web上"服务当前报告"是否提示正常，例如:配置文件有没下载成功，服务启动时下载的配置文件。
> * 再查找服务自己打印log日志。日志一般在/usr/local/app/tars/app\_log/应用名/服务名/目录下。
> * 若仍有问题请查看node日志。

### core文件如何打开，生成在什么地方？

> * core文件是在tafnode启动脚本添加ulimite -c unlimited 打开，目前core文件生成在 /usr/local/app/tars/app\_log下.

### 主控Registry故障是否会影响业务服务的正常访问？

> * 不会影响业务服务正常访问，框架底层会缓存后端服务的ip列表。

### 通讯器Communicator是如何通过ServerName获取ip:prot的？

> * 1.创建代理，并不会请求主控获取ip列表，而是调用代理的接口时才会触发请求主控ip列表
> * 2.如果obj后面有ip列表信息，相当于直连，这个是不会请求主控registry的
> * 3.如果obj后面没有ip列表信息，相当于是间接方式，会请求主控registry
> * 4.请求主控registry的策略时，如果本地缓存有请求obj的ip列表，用本地缓存的，同时去异步请求一下主控registry获取最新的ip列表
> * 5.请求主控registry的策略时，如果本地缓存没有请求obj的ip列表，业务的请求会先缓存到队列里，同时异步去请求主控获取ip列表，获取到ip列表后，再从队列里把业务的请求拿出来，发送这个请求
> * 6.6.不是每次都刷新，定时刷新的\(默认60s\)，定时刷新的触发是靠业务请求


# TarsJava 常见问题

### Tars java开发环境如何搭建？

> * 参考tars\_install.md

### 如何在本地启动和开发调试tars

> * 首先在工程目录下执行 mvn tars:build -Dapp=TestApp -Dserver=HelloJavaServer -DjvmParams="-Xms1024m -Xmx1024m -Dcom.sun.management.jmxremote.ssl=false -Dcom.sun.management.jmxremote.authenticate=false -Xdebug -Xrunjdwp:transport=dt\_socket,address=9000,server=y,suspend=n"
> * 在工程目录target/tars/bin/tars\_start 启动服务

# TarsNodejs 常见问题

### 为什么egg框架开发的nodejs应用在Tars平台上发布和重启偶尔会失败？

> * egg框架如果不是使用的single mode，会开启一个独立的worker进程来监听端口。当Tars发布或重启时，egg启动的端口监听进程并不会被杀死，导致再次启动时端口被占用，监听失败，服务启动失败。
> * Tars Nodejs的进程管理器node-agent包含了Cluster子进程、异常监控、进程拉起、监控上报等功能。普通的Nodejs程序只要发布上Tars平台自然就拥有了以上特性，无需再自己做进程管理。




# TarsPHP 常见问题

### 问题：php服务的业务日志在哪里查看？

答：/usr/local/app/tars/app\_log/$APPNAME/$SERVERNAME/$APPNAME.$SERVERNAME.log 不要忘记同时查看php\_error哈

### 问题：Class 'TARS\_Struct' not found in /usr/local/app/tars/tarsnode/data/PHPTest.PHPHttpServer/bin/src/vendor/phptars/tars-report/src/ServerInfo.php on line 5

答：请确认一下phptars扩展是否已经被安装，检查命令：php -m \| grep phptars。 安装方式请见/php/php\_extension下面的auto和auto7脚本内容以及相应文档

### 问题：搭建了http和tcp的服务，但是tcp服务无法正确回包，是什么问题？

答：请检查tcp服务状态是否正常，检查php\_error、业务日志中是否有权限导致的服务无法启动问题； 请检查tcp服务是否使用了正确的模板，比如不能指定protocolName为http，因为tcp服务一定要使用tars协议进行通信的，并且一定要指定swoole自动拼包的package\_length等配置参数； 使用tcpdump -iany -s0 port tcp服务端口 -XNNnn 进行抓包，看http服务是否正常发包给tcp服务了

### 问题：我可以不通过平台启动php的服务么？

答：显然是可以的，直接到服务目录下：/usr/local/app/tars/tarsnode/data/PHPTest.PHPHttpServer/bin 下面会有两个脚本tars\_stop.sh和tars\_start.sh，只要有权限，是可以直接启停服务的。

### 问题: 请求部分tcp的方法可以,但是部分不可以,可能什么原因?

答：一个可能的原因是你使用了format工具,破坏了server端自动生成的注释的结构\(比如examples下面的Tars-tcp-server/src/servant/PHPtest/PHPServer/obj/TestTafServiceServant.php\)。下载github上面最新的,或者用tars2php重新生成一下server的interface代码都可以解决这个问题。

### 问题: 调用Java服务, java接收不到参数,表现是报出illegalargumentException 如何解决?

答：之前java对tup的支持,没有放出,现在需要重新编译tars-core和maven-tars-plugin两个jar包, 并在项目的pom.xml中的 plugin配置里,添加true 的字样,这样生成之后就可以支持php调用默认的tup协议了。 重新生成后的接口文件会增加注解：@TarsMethodParameter,这样就能正常解析了。

### 注意点：

请务必安装phptars扩展 如果使用server，请务必安装swoole扩展

