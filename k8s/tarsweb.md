# TarsWeb 管理平台

## TarsWeb 管理平台的访问

当您成功安装 framework , 且观察到 tars-tarsweb-0 pod 成功启动, 即代表 tarweb 部署成功.

但此时, 因为k8s 本身的网络模型限制, 你需要选择一种方式访问 tarsweb:

### 使用 HostNetwork 方式访问
1. 修改 tserver/tarsweb.spec.k8s.hostnetwork 值为 true,具体命令为
```shell
kubectel edit tserver -n ${namespace} tars-tarsweb
```
2. 等待 tars-tarsweb-0 pod 成功启动并获取其 ip 值, 具体命令为
```shell
kubectl get pods -n ${namespace} tars-tarsweb-0 -o wide
```
3. 使用 http://$(ip):3000 访问管理平台

### 使用 HostPort 方式访问
1. 修改 tserver/tarsweb.spec.k8s.hostPorts 为如下值
```yaml
   spec:
     k8s:
      hostPorts:
       - nameRef: http
         port: 3000
```
 具体命令为: 

```shell
kubectel edit tserver -n ${namespace} tars-tarsweb
```
2. 等待 tars-tarsweb-0 pod 成功启动并获取其 ip值, 具体命令为
```shell
kubectl get pods -n ${namespace} tars-tarsweb-0 -o wide
```
3. 使用 http://$(ip):3000 访问管理平台

### 使用 Ingress 方式访问

如果您的集群已经安装了 Ingress 控制器 , 可以配置ingress 规则指向 tars-tarsweb:3000

然后可以就可以使用 ingress 规则中的路由地址访问了

## TarsWeb 管理平台的使用

TarsWeb 主要特性包括:

- 服务的发布, 扩容, yaml 文件变更
- pod 漂移以后, 记录历史 pod/节点 ip, 能看到服务的漂移前的日志, 方便定位问题
- 记录了 pod 历史镜像, 访问回滚
- 服务流量和耗时统计, 属性上报的展示
- 配置文件修改, 变更以及回滚
- shell 方式进入 pod, 方便查看日志
- 支持服务以 tgz 包形式上传打包, web 会调用 tarsimage 制作镜像并发布

### TarsWeb 管理平台与原生集群的融合

 当前版本的 TarsWeb 可以同时管理 K8S 集群和原生集群.具体开启方式为. 登陆 TarsWeb 管理平台后, 进入 ""运维管理"->"集群配置" 界面

在 nativeDBConfig 中填入原生集群的 MySql 配置信息, 内容格式如下:

```
{
  "show": true,
  "enable": false,
  "dbConf": {
    "host": "db.tars.com",
    "port": "3306",      
    "user": "tars",      
    "password": "tars2015", 
    "charset": "utf8", 
    "pool": {
        "max": 10,
        "min": 0, 
        "idle": 10000 
      }
  }
}
```



在 nativeFrameworkConfig 中填入原生集群的 TafRegistry 服务信息, 内容格式如下:

```
<tars>
  <application>
    #proxy需要的配置
    <client>
      #地址
      locator = tars.tarsregistry.QueryObj@tcp -h registry.tars.com -t 60000 -p 17890 -t 3000
      sync-invoke-timeout = 20000
      #最大超时时间(毫秒)
      max-invoke-timeout = 60000
      #刷新端口时间间隔(毫秒)
      refresh-endpoint-interval = 300000
      #模块间调用[可选]
      stat = tars.tarsstat.StatObj
      #网络异步回调线程个数
      asyncthread = 3
      modulename = tars.system
    </client>
  </application>
</tars>
```



upchain 中填入需要访问原生集群中 Obj 名和地址, 这样 tarsk8s 集群中未寻址到的 Obj 地址,就会按照  upChain 中的配置寻址.

内容格式如下:

```
upChain:
  default:
  - host: 172.16.8.67
    port: 8888
  - host: 172.16.8.68
    port: 8888
  tars.tarsnotify.NotifyObj:
  - host: 172.16.8.123
    port: 10017
  Test.TestServer.GetsumObj:
  - host: 172.16.8.123
    port: 10018
```



配置完成并重建 tars-tarsweb-0 , 再次访问 tarsweb 管理平台,即可同时看到 tars 信息和 tarsk8s信息.

