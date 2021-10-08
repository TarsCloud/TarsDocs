

# helm包管理

由于tars服务使用自定义CRD来描述(tserver), 通常情况你需要熟悉tserver等资源描述的写法(../install/tarscontroller/crds), 根据tars服务的应用和名称, 实现yaml文件, 才能发布到K8S平台上.

但是这样操作起来比较麻烦, 为了方便tars服务的发布, 提供了一套使用helm发布tars服务机制, 方便使用者快速发布tars服务. 你可以把这套方案和内网gitlab结合完成自动的CI/CD. 

helm的使用和说明请自己查询相关的文档.

基本说明:
- 每个TARS服务最终在正式环境以及对外输出时都是生成一个对应代码tag的helm包来完成部署. 
- 框架提供了一个基础的[helm libary](./helm/README.md) 以及 tars服务的helm模板(helm-template目录), 使用它们你可以快速生成一个tars服务的helm包.
- 具体每个tars业务服务的helm包是需要你自己生成的, 官方提供了多个脚本帮你完成 镜像制作/helm包制作/发布K8S 的过程
>- 自动镜像制作的脚本[exec-build.sh](./exec-build.md), 它完成了镜像的制作和推送
>- Helm包制作脚本[exec-helm.sh](./exec-helm.md), 来帮助你快速生成helm
>- 使用Helm发布到K8S[exec-deploy.sh](./exec-deploy.md), 发布镜像到K8S中
- 服务制作镜像以及发布到K8S中, 每个服务都需要有一个values.yaml来描述服务基本信息, 参见后续文档
# values.yaml

每个TARS服务都需要对应一个values.yaml文件, 这个文件在helm包模板中(helm-template目录)是不存在的, 这个文件通常跟随服务的源码, 在服务自动构建过程中, 传递给exec-deploy.sh脚本, 完成helm包的制作.

这个文件是一个固定格式, 实例如下:

```yaml
# 应用名称
app: Base 
# 服务名称
server: HelloServer
# subtype(tars/normal)
subtype: tars
# 使用的模板(tars.nodejs/tars.cpp/tars.java/tars.go/tars.php)
template: tars.nodejs
# 私有模板(通常为空)
profile: ""
# servant列表
servants:
  - name: AuthObj
    # 容器内端口
    port: 10000
    # 是否是tars服务, 如果是http服务, 这里为false
    isTars: true
    #是否是tcp服务
    isTcp: true
    #业务线程个数
    thread: 4
    capacity: 100000
    connection: 100000
    timeout: 60000
# 服务部署个数    
replicas: 2
# 环境变量, 默认为空
envVars:
  - name: name1 
    value: value1
# 是否启用宿主机网络
hostNetwork: false
# 是否启用hostIpc, 一旦启用, 则共享内存等, 都和宿主机打通, 这个时候就需要注意共享内存key不用搞冲突了!!!
hostIPC: false
# 是否启用hostPorts 默认[]即可
hostPorts: []
# 是否开启hostPorts, 如果使用, 则表示这个Obj, 会开启nodePort(即宿主机的port)
#hostPorts:
#  - nameRef: HelloObj
#    port: 22394
# 发布地址, id必填, image可以自己加tag, 这个通常是CI/CD时自动填写的
repo:
  id:
  image: tarscloud/base.helloserver
  secret: tars-image-secret
#应用级配置, 通常为空
appConfig:
  - name: app.conf
    content: |
      app config content
#节点配置, podSeq表示节点号(statefullset部署的)
nodeConfig:
  - name: config.json
    podSeq: 0
    content: |
      node config
# 服务配置文件, 可以有多个!  
config:
  - name: config.json
    content: |
      {
          "webConf": {
              "port": 6080,
              "loggerPath": "./log",
              "logFileKeepDays": "1",
              "defaultLanguage": "cn",
          }
      }
    
```

说明:
- tars-image-secret这个私有仓库的secret, 是你在K8S上部署TARS框架上之前, 创建的仓库secret, 以便k8s从你的仓库获取镜像
- repo.id是自动构建时```exec-helm.sh```填充的
- repo.image不能为空, 指向镜像地址且不带TAG, 自动构建时会在后面添加TAG
- subtype: 表示服务的类型(tars/normal), tars:表示是tars服务生成的镜像, normal:表示普通的镜像, 比如mysql等
# 如何映射宿主机HostPath

部分服务由于需要涉及到本地存储, 需要将宿主机目录映射到, values.yaml采用如下方式(注意参考mounts这段):
```yaml

# 应用名称
app: Base 
# 服务名称
server: HelloServer
# subtype(tars/normal)
subtype: tars
# 使用的模板(tars.nodejs/tars.cpp/tars.java/tars.go)
template: tars.nodejs
# 私有模板(通常为空)
profile: ""
# servant列表
servants:
  - name: AuthObj
    port: 10000
    isTars: true
    isTcp: true
    thread: 2
    capacity: 100000
    connection: 100000
    timeout: 60000
# 服务部署个数    
replicas: 2
# 发布地址, id必填, image可以自己加tag, 这个通常是CI/CD时自动填写的
repo:
  id:
  image: base/base.helloserver
  secret: tars-image-secret
# 环境变量, 默认为空
envVars:
  - name: name1 
    value: value1
mounts:
  - name: test-host-path
    source:
      hostPath:
        path: /data/test-host-path
        type: DirectoryOrCreate
    mountPath: /data/mount-path
    subPathExpr: $(Namespace)/$(PodName)
  - name: test-pvc
    source:
      persistentVolumeClaim:
        claimName: remote_log_pvc
        readOnly: false
  - name: remote-log-dir
    mountPath: /usr/local/app/tars/remote_app_log
    source:
      tLocalVolume: {}
  - name: auth-remote-log-dir
    mountPath: /usr/local/app/tars/remote_app_log
    source:
      tLocalVolume:
        gid: "0"
        mode: "755"
        uid: "1000
...

```

以上示例中, 给出三种mounts的方式:
- 使用HostPath去mount宿主机的目录, 这种情况下如果漂移了就比较麻烦
- 通过pvc申请空间(这种情况下, 需要提前配置好pv)
- 使用LocalPV来mount宿主机目录, 这种情况下pod不再会漂移到其他节点[请参考LocalPV的说明文档](./LocalPV.md)
- 使用LocalPV时, 如果pod内部权限不是root的, 你可以指定git/mode/uid来控制权限

# 如何配置节点绑定

在web平台,可以将应用绑定到某些节点上, 但是如果想指定某个服务绑定, 就需要需要用LabelMatch的能力, 可以直接在values.yaml中添加LabelMatch来实现, 参考y以下示例(注意LabelMatch段):


```yaml
# 应用名称
app: Base 
# 服务名称
server: HelloServer
# subtype(tars/normal)
subtype: tars
# 使用的模板(tars.nodejs/tars.cpp/tars.java/tars.go)
template: tars.nodejs
# 私有模板(通常为空)
profile: ""
# servant列表
servants:
  - name: AuthObj
    port: 10000
    isTars: true
    isTcp: true
    thread: 2
    capacity: 100000
    connection: 100000
    timeout: 60000
# 发布地址, id必填, image可以自己加tag, 这个通常是CI/CD时自动填写的
repo:
  id:
  image: base/base.helloserver
  secret: tars-image-secret
# 服务部署个数    
replicas: 2
# 环境变量, 默认为空
envVars:
  - name: name1 
    value: value1
labelMatch:
  - key: xxxx
    operator: In
    values: ["yyyy"]
  - key: kubernetes.io/hostname
    operator: In
    values: ["node87","node88"] 
...
    
```

LabelMatch的匹配说明如下:
- key: 节点的标签, 可以提前自己给node加标签 
- operator: In, NotIn, Exists, DoesNotExist, Gt, Lt
- values: 匹配值
- 如上labelMatch的匹配条件是个数组, 是与的关系, 即需要都满足才匹配