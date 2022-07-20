## [PHP 8.0 现在不支持吗？](https://github.com//TarsPHP/TarsPHP/issues/51)



No description provided.



## [Test Phalcon integration with TARS](https://github.com//TarsPHP/TarsPHP/issues/46)



No description provided.




已经做的修改：

fork了一份代码，https://github.com/luoxiaojun1992/tars-php-phalcon
修复了servers.php配置，因为支持了多servant，原作者的配置文件会报错
删除了原作者的一些测试代码(格式比较乱)，增加了http、tars的示例接口
对目录结构适当做了一些调整
更新文档
用tarsphp的docker镜像搭建的平台，在node容器安装了phalcon扩展，测试了http和tars可以调用

遇到的问题：

第一次部署phalcon服务，平台弹窗返回成功，之后都是返回失败，实际上服务已经更新重启，平台上显示的服务状态也是active。Laravel和Lumen的服务部署也是同样的问题，可能是docker镜像平台的问题。




子任务ID
应用
服务
节点
命令
开始时间
结束时间
状态
执行信息




d128c8f5d1bc4610b6ba5b256d3a852e
PHPTest
LaravelTars
172.18.0.4
patch_tars
2020-04-22 22:55:59
2020-04-22 22:56:07
EM_I_FAILED
[ServantProxy::invoke timeout:3000,servant:tars.tarsAdminRegistry.AdminRegObj,func:restartServer,adaptertcp -h 172.18.0.3 -p 12000,reqid:22]






子任务ID
应用
服务
节点
命令
开始时间
结束时间
状态
执行信息




216605b3a2df4c1385e023627071d747
PHPTest
TarsPhalcon
172.18.0.4
patch_tars
2020-04-22 22:57:10
2020-04-22 22:57:14
EM_I_FAILED
[ServantProxy::invoke timeout:3000,servant:tars.tarsAdminRegistry.AdminRegObj,func:restartServer,adaptertcp -h 172.18.0.3 -p 12000,reqid:30]


7645a1714f80421d8510b65f1f79942e
PHPTest
TarsPhalcon
172.18.0.3
patch_tars
2020-04-22 22:57:14
2020-04-22 22:57:14
EM_I_CANCEL




还需要研究完善的：

支持phalcon的路由(如果有的话)，目前是采用的Tars\Command的默认路由，将tars的请求响应上下文转换为phalcon的请求响应上下文 (Done)
拉取tars的平台配置合并到phalcon的项目配置中
phalcon的log适配集成tarsLog
参考其他的phalcon集成swoole的项目做一些优化(比如防止内存泄露等)



## [Modify PHP-Zipkin library to use swoole async report](https://github.com//TarsPHP/TarsPHP/issues/45)



No description provided.



## [Demo for distributed tracing](https://github.com//TarsPHP/TarsPHP/issues/44)



No description provided.



## [tarsphp+swoft project start failed on Tars web. I manually start the service, but it can't be reached in browser](https://github.com//TarsPHP/TarsPHP/issues/43)


I did a telnet on the node server. Port 18306 is connectable
Here's the config content of my service.
<tars>
        <application>
                enableset=n
                setdivision=NULL
                <server>
                        node=tars.tarsnode.ServerObj@tcp -h 172.25.0.5 -p 19386 -t 60000
                        deactivating-timeout=3000
                        app=PHPTest
                        server=swoft
                        localip=172.25.0.5
                        local=tcp -h 127.0.0.1 -p 9100 -t 3000
                        basepath=/usr/local/app/tars/tarsnode/data/PHPTest.swoft/bin/
                        datapath=/usr/local/app/tars/tarsnode/data/PHPTest.swoft/data/
                        logpath=/usr/local/app/tars/app_log/
                        loglevel=DEBUG
                        logsize=15M
                        log=tars.tarslog.LogObj
                        config=tars.tarsconfig.ConfigObj
                        notify=tars.tarsnotify.NotifyObj
                        logLevel=DEBUG
                        php=/usr/bin/php
                        buffer_output_size=12582912
                        open_tcp_nodelay=1
                        open_eof_check=0
                        open_eof_split=0
                        task_worker_num=1
                        dispatch_mode=2
                        daemonize=1
                        <PHPTest.swoft.objAdapter>
                                allow
                                endpoint=tcp -h 172.25.0.5 -p 9100 -t 60000
                                handlegroup=PHPTest.swoft.objAdapter
                                maxconns=200000
                                protocol=not_tars
                                queuecap=10000
                                queuetimeout=60000
                                servant=PHPTest.swoft.obj
                                threads=5
                        </PHPTest.swoft.objAdapter>
                </server>
                <client>
                        asyncthread=12
                        locator=tars.tarsregistry.QueryObj@tcp -h 172.25.0.3 -p 17890
                        sync-invoke-timeout=20000
                        async-invoke-timeout=20000
                        refresh-endpoint-interval=60000
                        stat=tars.tarsstat.StatObj
                        property=tars.tarsproperty.PropertyObj
                        report-interval=60000
                        modulename=PHPTest.swoft
                        sample-rate=100000
                        max-sample-count=50
                </client>
        </application>
</tars>



Please check the node tars.application.server.local in your configure file. Your service is on 9100 port.


## [Investigate Github actions](https://github.com//TarsPHP/TarsPHP/issues/42)



No description provided.



## [怎么没有phalcon 整合的文档呢？](https://github.com//TarsPHP/TarsPHP/issues/35)


名字也是错的！！！



Phalcon我们还在接入，敬请期待。



您可以先参考这位开发者的代码，文档我们近期会完善好，感谢您的支持。
https://github.com/iamloso/tars-php-phalcon


## [tars监控swoole进程导致服务器内部502](https://github.com//TarsPHP/TarsPHP/issues/22)


测试重现步骤：
max_request设置为5   (实际生产环境配置为1000000）
worker接收到http最大请求次数超过5，首先swoole机制原因worker进程会自动重启，
推断：tars监听到的woker进程有变化，并将其kill掉再启动一次？
附swoole官方文档解释 https://wiki.swoole.com/wiki/page/p-max_request.html
附swoole.log中的记录：
[2019-04-18 09:33:32 #2357.4]   NOTICE  Server is shutdown now.
[2019-04-18 09:33:32 $2358.0]   ERROR   swProcessPool_shutdown(:339): waitpid(2369) failed. Error: No child processes[10].
[2019-04-18 09:33:32 $2358.0]   ERROR   swProcessPool_shutdown(:339): waitpid(2370) failed. Error: No child processes[10].
[2019-04-18 09:33:32 $2358.0]   ERROR   swProcessPool_shutdown(:339): waitpid(2371) failed. Error: No child processes[10].
[2019-04-18 09:33:32 $2358.0]   ERROR   swProcessPool_shutdown(:339): waitpid(2372) failed. Error: No child processes[10].
[2019-04-18 09:33:32 $2358.0]   ERROR   swProcessPool_shutdown(:339): waitpid(2373) failed. Error: No child processes[10].
[2019-04-18 09:33:32 $2358.0]   ERROR   swProcessPool_shutdown(:339): waitpid(2374) failed. Error: No child processes[10].
[2019-04-18 09:33:32 $2358.0]   ERROR   swProcessPool_shutdown(:339): waitpid(2375) failed. Error: No child processes[10].
[2019-04-18 09:33:32 $2358.0]   ERROR   swProcessPool_shutdown(:339): waitpid(2376) failed. Error: No child processes[10].
[2019-04-18 09:33:32 $2358.0]   ERROR   swProcessPool_shutdown(:339): waitpid(2377) failed. Error: No child processes[10].
[2019-04-18 09:33:32 $2358.0]   ERROR   swProcessPool_shutdown(:339): waitpid(2378) failed. Error: No child processes[10].
[2019-04-18 09:33:32 $2358.0]   ERROR   swManager_loop(:474): waitpid(2379) failed. Error: No child processes[10].
[2019-04-18 09:33:32 $2358.0]   ERROR   swManager_loop(:474): waitpid(2380) failed. Error: No child processes[10].
[2019-04-18 09:33:32 $2358.0]   ERROR   swManager_loop(:474): waitpid(2381) failed. Error: No child processes[10].
[2019-04-18 09:33:32 $2358.0]   ERROR   swManager_loop(:474): waitpid(2382) failed. Error: No child processes[10].
[2019-04-18 09:33:32 $2358.0]   ERROR   swManager_kill_user_worker(:609): waitpid(2383) failed. Error: No child processes[10].
[2019-04-18 09:33:32 $2358.0]   ERROR   swManager_kill_user_worker(:609): waitpid(2384) failed. Error: No child processes[10].
[2019-04-18 09:33:32 #2357.4]   ERROR   swFactoryProcess_shutdown(:62): waitpid(2358) failed. Error: No child processes[10].
Server has been started. (master PID: 2415, manager PID: 2416)



tars的确会对swoole进程进行监控，使用max_request参数的原因是？



这个可能呢是监听事件的问题， tars 拉起来的 swoole ，worker 退出后，master 不能获得信号量，导致master 不能拉起退出的worker



问下啊 这个问题解决了么，用max request NUM主要是为了内存泄漏

tars的确会对swoole进程进行监控，使用max_request参数的原因是？



