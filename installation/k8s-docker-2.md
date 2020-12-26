# k8stars
k8stars是便于将tars服务运行在 Kubernetes 中的方案。

## 特性
- 保持tars原生的开发框架能力
- 支持tars的名字服务自动注册和配置删除
- 支持原有tars服务平滑迁移到k8s等容器平台
- 无侵入性设计，与运行环境无偶合关系
 
## 实现原理
1. 在tarsregistry增加了3个接口，用于tars名字的自动注册/心跳上报和节点下线。详情见[接口定义](https://github.com/TarsCloud/K8STARS/blob/master/tarsregistry/protocol/tarsregistry.tars)。

2. 提供一个`tarscli`命令行工具，用于分配端口/生成配置/上报心跳以及节点下线。

## 部署示例
1. 安装tars基础服务
```
curl https://raw.githubusercontent.com/TarsCloud/K8STARS/master/baseserver/install_all.sh | sh
```

2. 部署服务示例
   - 部署示例simpleserver

     ```cd examples/simple && kubectl apply -f simpleserver.yaml```

     示例说明：
     - 镜像由 `examples/simple/Dockerfile` 文件制作，基础镜像由 `cmd/tarscli/Dockerfile` 制作
     - start.sh中的`tarscli genconf`用于生成tars服务启动配置
     - _server_meta.yaml文件用于配置服务的元数据，字段信息可以参考 `app/genconf/config.go` 中的 `ServerConf` 结构体。endpoint默认为`tcp -h ${local_ip} -p ${random_port}`，支持自动填入IP和随机端口。

    - 基于Go语言的 HelloWorld 程序 TestApp.HelloGo
    
      详细查看 [examples/README.md](https://github.com/TarsCloud/K8STARS/blob/master/examples)


3. 验证部署
   登录`db_tars`，然后执行`select * from t_server_conf\G`可以看到simpleserver的节点信息已自动注册。

## tars部署目录结构
`tarscli`基于环境变量`TARS_PATH`（默认/tars）来管理服务，各目录功能：
   - `${TARS_PATH}/bin`：启动脚本和二进制文件
   - `${TARS_PATH}/conf`：配置文件
   - `${TARS_PATH}/log`：log文件
   - `${TARS_PATH}/data`：运行状态/缓存文件

## 关于tarscli
   `tarscli`提供了一组命令工具，方便tars服务的容器化部署，可通过环境变量来指定参数，详细说明见`tarscli help`。
   以下是tarscli支持的子命令
   - `genconf` 用于生成tars服务的启动配置文件。支持的环境变量有：
     - `TARS_APPLICATION` 指定的应用名，默认从`_server_meta.yaml`中读取
     - `TARS_SERVER` 指定的服务名，默认从`_server_meta.yaml`中读取
     - `TARS_BUILD_SERVER`编译时的服务名，编译的服务名与运行的服务名不同时会用到
     - `TARS_LOCATOR`可以指定registry的地址，默认是`tars.tarsregistry.QueryObj@tcp -h tars-registry.tars-system.svc.cluster.local -p 17890`（service的地址）
     - `TARS_SET_ID`可以指定服务set
     - `TARS_MERGE_CONF`可指定配置模板文件，并将配置合并到服务的启动配置文件中
  
   - `supervisor` 默认执行先`genconf`命令，再启动并监控服务。支持的环境变量有：
     - `TARS_START_PATH`服务的启动脚本，默认是`$TARS_PATH/bin/start.sh`
     - `TARS_STOP_PATH`服务的停止脚本，默认是kill在`$TARS_PATH`路径下的所有服务进程
     - `TARS_REPORT_INTERVAL`上报心跳到registry的时间间隔
     - `TARS_DISABLE_FLOW`注册到registry时是否开启流量，非空表示关闭，默认开启
     - `TARS_CHECK_INTERVAL`检查服务状态的时间间隔，如果状态有变化会实时同步到regitry
     - `TARS_BEFORE_CHECK_SCRIPT`每次检查前都运行的shell命令
     - `TARS_CHECK_SCRIPT_TIMEOUT`每次检查前运行shell命令的超时时间
     - `TARS_PRESTOP_WAITTIME`关流量-停止服务前的等待时间，用于无损变更，默认80秒
  
   - `hzcheck` 用于同步服务状态和k8s的pod状态，需要将pod的`readiness probe`设为tarscli `hzcheck`命令
   - `prestop` 用于在服务退出前删除在registry对应的配置
     - `TARS_PRESTOP_WAITTIME`关流量-停止服务前的等待时间，用于无损变更，默认80秒
   - `notify` 用于发送管理命令，常用命令有：tars.setloglevel/tars.pprof等

## 基础服务
   tars相关基础服务提供了丰富的服务治理功能功能。可参考[baseserver](https://github.com/TarsCloud/K8STARS/blob/master/baseserver)的说明进行部署。
   
