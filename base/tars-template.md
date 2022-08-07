# 基础概念

> - [模板说明](#main-chapter-1)
> - [配置格式](#main-chapter-2)
> - [服务启动](#main-chapter-3)


## 1. <span id="main-chapter-1"></span> 模板说明

- TARS服务启动的时候, 都需要一个配置文件, 这个配置文件决定了服务绑定哪些端口, 设置TARS RPC调用的相关参数等. 
- 在正式环境中, 这个配置文件是由tarsnode基于框架上的模板文件生成的, 因此你可以通过tarsweb修改模板文件.
- 服务在部署时会选择自己的模板, 当然你可以通过tarsweb自定义自己的模板, 变更它的参数等.
- tarsnode在启动业务服务时, 会根据模板生成实际服务启动的配置文件, 这个配置文件在目录:  `/usr/local/app/tars/tarsnode/data/${app}.${server}/conf/`
- 框架初始化时, 几个重要的框架都有自己的自定义模板,通过你不需要变更它.
- 模板文件可以继承, 这样方便管理
- 业务服务可以有私有模板, 私有模板中定义的配置项, 会覆盖模板中的相同的配置项

## 2. <span id="main-chapter-2"></span> 配置格式

通过tarsweb, 我们可以看到`tars.default`内容大概如下:
```xml
<tars>
	<application>
		enableset=${enableset}
		setdivision=${setdivision}
		<client>
			locator=${locator}
			sync-invoke-timeout=3000
			async-invoke-timeout=5000
			refresh-endpoint-interval=60000
			stat=tars.tarsstat.StatObj
			property=tars.tarsproperty.PropertyObj
			report-interval=60000
			sample-rate=100000
			max-sample-count=50
			asyncthread=${asyncthread}
			modulename=${modulename}
		</client>
		<server>
			app=${app}
			server=${server}
			localip=${localip}
			local=${local}
			basepath=${basepath}
			datapath=${datapath}
			logpath=${logpath}
			logsize=10M
			config=tars.tarsconfig.ConfigObj
			notify=tars.tarsnotify.NotifyObj
			log=tars.tarslog.LogObj
			deactivating-timeout=3000
			logLevel=DEBUG
		</server>
	</application>
</tars>
```
你可以变更这里面的配置值.


当tarsnode启动业务服务时, 根据模板生成的配置文件格式大概如下, 以`tars.tarsconfig`举例:
```xml
<tars>
  <application>
    # 是否启动用了set
    enableset=n
    # set分组信息
    setdivision=NULL
    <server>
      #服务器上tarsnode的端口, 方便业务服务和tarsnode通信
      node=tars.tarsnode.ServerObj@tcp -h 172.168.1.3 -p 19386 -t 60000
      #服务的app名称
      app=tars
      #服务的server名称
      server=tarsconfig
      #本机地址
      localip=172.168.1.3
      #业务服务开启的管理端口, 给tarsnode使用
      local=tcp -h 172.168.1.3 -p 18197 -t 10000
      #业务服务的基础路径, 框架中有api可以获取到这个路径
      basepath=/usr/local/app/tars/tarsconfig/bin/
      #业务服务的数据目录, 框架中有api可以获取到这个路径, 通常业务服务可以把自己的业务数据写在这个目录下, 
      datapath=/usr/local/app/tars/tarsnode/data/tars.tarsconfig/data/
      #日志路径
      logpath=/usr/local/app/tars/app_log/
      #滚动日志大小
      logsize=10M
      #滚动日志最大个数
      lognum=10
      #滚动日志级别
      logLevel=DEBUG
      #tarsconfig obj
      config=tars.tarsconfig.ConfigObj
      #tarsnotify obj
      notify=tars.tarsnotify.NotifyObj
      #服务停止时, tarsnode最大等待时间
      deactivating-timeout=3000
      #服务启动时, tarsnode最大等待时间
      activating-timeout=10000
      #tarslog obj
      log=tars.tarslog.LogObj
      #以下adapter是tarsnode根据服务在tarsweb上配置的servant来自动生成的
      <tars.tarsconfig.ConfigObjAdapter>
        allow
        endpoint=tcp -h 172.168.1.3 -p 18193 -t 60000
        maxconns=200000
        protocol=tars
        queuecap=100000
        queuetimeout=60000
        servant=tars.tarsconfig.ConfigObj
        threads=10
      </tars.tarsconfig.ConfigObjAdapter>
    </server>
    #rpc客户端通信器的配置项
    <client>
        #tarsregistry地址, 可以有多个
      locator=tars.tarsregistry.QueryObj@tcp -h 172.168.1.3 -p 17890
      #同步调用时间
      sync-invoke-timeout=3000
      #异步调用时间
      async-invoke-timeout=5000
      #刷新主控时间
      refresh-endpoint-interval=60000
      #tarstat obj
      stat=tars.tarsstat.StatObj
      #tarsproperty obj
      property=tars.tarsproperty.PropertyObj
      #上报监控数据间隔
      report-interval=60000
      #异步回调线程个数
      asyncthread=3
      #当前模块名称
      modulename=tars.tarsconfig
    </client>
  </application>
  #其他配置属于业务自定义的配置, 一般没有, 因为业务服务通过业务配置来实现配置, 而不是通过模板来扩展配置
  <db>
    dbhost=172.168.1.3
    dbname=db_tars
    dbuser=tarsAdmin
    dbpass=Tars@2019
    dbport=3306
    charset=utf8
    dbflag=CLIENT_MULTI_STATEMENTS
  </db>
  <propertydb>
    <db1>
      dbhost=172.168.1.3
      dbname=tars_property
      tbname=tars_property_
      dbuser=tarsAdmin
      dbpass=Tars@2019
      dbport=3306
      charset=utf8
    </db1>
  </propertydb>
  <statdb>
    <db1>
      dbhost=172.168.1.3
      dbname=tars_stat
      tbname=tars_stat_
      dbuser=tarsAdmin
      dbpass=Tars@2019
      dbport=3306
      charset=utf8
    </db1>
  </statdb>
</tars>
```

## 2. <span id="main-chapter-3"></span> 服务启动

TARS服务启动时, 需要指定以上生成的配置, 例如: 
```
./HelloServer --config=hello.conf
```
