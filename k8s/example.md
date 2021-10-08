# 服务发布示例

本文介绍tars服务如何快速制作成docker镜像, 并发布到K8S集群上.

## 镜像制作以及helm包

tars服务制作镜像可以通过官方提供的```tarscloud/base-compiler```来制作. 方式如下:

### 拉取编译镜像
```
docker pull tarscloud/base-compiler
```

### 进入镜像
```
docker run -it -v/var/run/docker.sock:/var/run/docker.sock -v`pwd`:/data/src tarscloud/base-compiler bash
```
### tars服务的yaml文件

每个服务都需要一个(或者多个)values.yaml文件, 来描述服务是如何部署在TarsK8S上面的, 请参考[yaml文件格式](./helm.md)

- 制作tars服务的镜像

在base-compiler容器内部, 使用exec-build.sh制作tars服务的镜像, 示例如下:
```
exec-build.sh BaseImage SERVERTYPE(cpp/nodejs/java-war/java-jar/go/php) Files YamlFile Tag Push Dockerfile(可选)
```

例如:
```
exec-build.sh tarscloud/tars.cppbase:v1.0.0 cpp build/bin/StorageServer yaml/values.yaml v1.0.0 true

```

yaml/values.yaml示例如下:
```yaml

# 应用名称
app: OD
# 服务名称
server: StorageServer
# 使用的模板(tars.nodejs/tars.cpp/tars.java/tars.go/tars.php)
template: tars.cpp
# 私有模板(通常为空)
profile: ""
# servant列表
servants:
  - name: StorageObj
    port: 10000
    isTars: true 
    isTcp: true
    thread: 2
    capacity: 100000
    connection: 100000
    timeout: 60000
# 服务部署个数    
replicas: 3
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
# 发布地址, id/image必填, 这个通常是CI/CD时自动填写的
repo:
  id:
  image: yourregistry/od.storageserver
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
```
执行完脚本后会生成:
* 服务的镜像(yourrepository/od.storageserver:v1.0.0)

### 制作Helm包


执行以下命令, 完成部署:
```
exec-helm.sh YamlFile Tag 
```

例如:
```
exec-helm.sh yaml/values.yaml v1.0.0
```

说明:
- 这里yaml/values.yaml, TAG和上一步exec-build.sh中的tag要一致
- 会生成一个helm包: od-storageserver.tgz
- 使用这个od-storageserver.tgz可以完成服务的发布
### 执行部署

执行以下命令, 完成部署:
```
exec-deploy.sh Namespace HelmPackage
```

例如:
```
exec-deploy.sh tars-dev od-storageserver-1.0.0.tgz
```

注意, 此时你需要有连接到K8S集群的能力, 即当前环境中有K8S集群的config

