## 完整配置参数

```xml
<tars>
    <application>
        enableset=${enableset}
        setdivision=${setdivision}
        <client>
            #主控的地址
            locator=${locator}
            #模块名称
            modulename=${modulename}
            #同步超时时间
            sync-invoke-timeout=3000
            #异步超时时间
            async-invoke-timeout=5000
            #刷新ip列表的时间间隔
            refresh-endpoint-interval=60000 
            #上报数据的时间间隔 
            report-interval=5000
            # 检测节点状态
            check-status-interval=1000
            # KeepAlive ping 时间
            keep-alive-interval=0
            stat=tars.tarsstat.StatObj
            property=tars.tarsproperty.PropertyObj
            #异步线程数
            asyncthread=${asyncthread}
            #客户端发送队列大小
            clientqueuelen=10000
            #客户端连接空闲超时时间
            clientidletimeout=600000
            #客户端连接读超时时间
            clientreadtimeout=100
            #客户端连接写超时时间
            clientwritetimeout=3000
            #客户端建立连接超时时间
            clientdialtimeout=3000
            reqdefaulttimeout=3000
            #最大积压请求
            objqueuemax=100000
            # ssl证书相关配置
            #ca
            #cert
            #key
            #verifyclient
            #ciphers
            <TestApp.HelloServer.HelloObj>
                # ssl证书相关配置
                #ca
                #cert
                #key
                #verifyclient
                #ciphers
            </TestApp.HelloServer.HelloObj>
        </client>
        <server>
            #本地node的ip:port
            node=tars.tarsnode.ServerObj@tcp -h 10.120.129.226 -p 19386 -t 60000
            #应用名称
            app=${app}
            #服务名称
            server=${server}
            # obj 协程池，默认不开启
            maxroutine=0
            # 协程池模式，队列大小
            queuecap=10000000
            # 僵尸超时时间，协程池模式有效
            zombietimeout=10000
            # 接收连接超时时间
            accepttimeout=500
            # 读超时时间
            readtimeout=0
            # 写超时时间
            writetimeout=0
            # 处理请求超时时间
            handletimeout=0
            # 空闲连接超时时间
            idletimeout=600000
            # 优雅终止超时时间
            gracedowntimeout=60000
            # 数据包最长限制
            maxPackageLength=10485760
            # 特性上报间隔 ms
            propertyreportinterval=10000
            # 统计上报间隔 ms
            statreportinterval=10000
            # 统计队列长度
            statreportchannelbuflen=100000
            # 上报健康状态时间间隔 ms
            mainloopticker=10000
            # tcp读缓存大小 128 * 1024 * 1024
            tcpreadbuffer=134217728
            # tcp写缓存大小 128 * 1024 * 1024
            tcpwritebuffer=134217728
            # 是否开启Nagle算法
            tcpnodelay=false
            #本机ip
            localip=${localip}
            #管理端口
            local=${local}
            #服务可执行文件,配置文件等
            basepath=${basepath}
            #服务数据目录
            datapath=${datapath}
            #日志路径
            logpath=${logpath}
            #滚动日志大小
            logsize=10M
            #日志等级
            lognum=10
            #日志等级
            logLevel=DEBUG
            #远程日志的地址[可选]
            log=tars.tarslog.LogObj
            #配置中心的地址
            config=tars.tarsconfig.ConfigObj
            #通知上报的地址[可选]
            notify=tars.tarsnotify.NotifyObj
            #服务停止的超时时间
            deactivating-timeout=3000
            # ssl证书相关配置
            #ca
            #cert
            #key
            #verifyclient
            #ciphers
            <TestApp.HelloServer.HelloObjAdapter>
                #允许的IP地址
                allow
                #监听IP地址
                endpoint=tcp -h 10.120.129.226 -p 20001 -t 60000
                #处理组
                handlegroup=TestApp.HelloServer.HelloObjAdapter
                #最大连接数
                maxconns=200000
                #协议
                protocol=tars
                #队列大小
                queuecap=10000
                #队列超时时间毫秒
                queuetimeout=60000
                #处理对象
                servant=TestApp.HelloServer.HelloObj
                #当前线程个数
                threads=5
                # ssl证书相关配置
                #ca
                #cert
                #key
                #verifyclient
                #ciphers
            </TestApp.HelloServer.HelloObjAdapter>
        </server>
    </application>
</tars>
```