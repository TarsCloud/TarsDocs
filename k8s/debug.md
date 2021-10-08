# 如何调试

当服务部署在K8S集群中以后, 运维管理确实方便很多, 如何快速简单的调试是大的问题, 这里主要面临两个问题:
- 集群管理都是docker镜像, 如果每次都需要通过CICD流程来发布服务和调试, 也非常麻烦, 时间都消耗在等待编译发布过程中了
- 集群有自己的地址, 外部无法快速访问, 如果能在集群中开发和调试那肯定会方便很多

那么如果解决上述两个问题?

## 通过tarsweb来快速发布服务

K8S版本的也带了一个Tarsweb, 能够快速访问部署在K8S上的tars服务, 相关操作都可以通过该平台来完成.

你可以类似之前的Tars环境, 直接提交tgz包到web平台, web平台会调用tarsimage来打包生成docker镜像, 这样调试和发布方式可以和非K8S版本类似, 但是需要注意的是:
- 制作tars镜像, 是需要指定基础镜像的(这个比如tarscloud/tars.cppbase, tarscloud/tars.nodejsbase, tarscloud/tars.javabase等), [请参考](./dockerfile.md)
- tarsimage编译镜像, 默认会推送到安装Tars时的仓库上, 如果你使用的官方镜像, 你得修改这个地址, 可以参看tars-tarsimage的configmap来修改这个地址!

tars-tarsimage的configmap有两个参数:
- registry: 仓库地址
- secret: 仓库密码
默认这个地址是, 你安装tarsframework时指定的, 可以自己修改, 例如:

```
kubectl create secret docker-registry od-image-secret -n tars-dev --docker-server=docker.io --docker-username=${docker_user} --docker-password=${docker_pass}  

```

类似之前的tarsweb, 也提供了上传tgz包的api.

- 对于cpp版本, tars-tools.cmake里面, 也提供了相关的实现, 只需要:
```
cmake .. -D 指定以下三个参数:
TARS_K8S_WEB_HOST:         
TARS_K8S_BASE_IMAGE:       tarscloud/tars.cppbase
TARS_K8S_TOKEN:            
```
就可以通过:
```
make xxxx-k8s-upload
```
完成服务的发布

- 对于nodejs版本, 你可以实现一段脚本即可完成发布, 示例如下:
```sh
#!/bin/bash

TARS_K8S_WEB_HOST="http://xxx.xxx.xxx"
TARS_K8S_TOKEN=""
TARS_K8S_BASE_IMAGE="tarscloud/tars.nodejsbase"

APP=OD

TARGET=UserServer

TARGET_PATH=tmp/${TARGET}

mkdir -p tmp

echo "mkdir -p ${TARGET_PATH}"
mkdir -p ${TARGET_PATH}

echo "npm run build"
npm run build

echo "rm old build & copy new build"
rm -rf ${TARGET_PATH}/build
cp -rf build ${TARGET_PATH}/
echo "copy package.json"
cp package.json ${TARGET_PATH}/

cd ${TARGET_PATH}
echo "npm install --production"
npm install --production

cd ..

tar czf ${TARGET}.tgz ${TARGET}

echo "curl ${TARGET}.tgz to k8s"

curl ${TARS_K8S_WEB_HOST}/pages/k8s/api/upload_and_publish?ticket=${TARS_K8S_TOKEN} -Fsuse=@${TARGET}.tgz -Fapplication=${APP} -Fmodule_name=${TARGET} -Fserver_type=nodejs  -Fbase_image=${TARS_K8S_BASE_IMAGE} -Fcomment=upload

cd ..
```

说明:
- 指定tarsweb地址(通过ingress入口), 由于要上传文件, 注意ingress中对包大小的限制
- TARS_K8S_TOKEN: web的token, 在用户中心中创建一个
- TARS_K8S_BASE_IMAGE服务基础镜像, nodejs语言是: tarscloud/tars.nodejsbase, 如果你是自己源码编译的, 这个镜像可以使用你自己编译的
- 后续脚本就是把当前目录的代码, 打包成一个tgz, 最后用curl上传!

**这里和非K8S版本有一个重要区别是, tars-node-agent这里是没有打包的, 它打包在tarscloud/tars.nodejsbase镜像里面了, 而非K8S的版本, tars-node-agent会打包到源码中发布!!**

## 在集群内部构建编译机器

对于第二点, 我们可以在考虑在集群内部构建编译Pod, 相当于一台虚拟机, 这样因为它就在K8S内部, 可以畅通无阻的访问任何K8S服务, 那调试起来肯定是最简单的!

如何能做到这个效果呢? 思路如下:
- 在K8S上启动一个Pod
- 这个Pod具备编译和运行tars服务的环境
- 这个Pod挂载宿主机文件系统, 代码放在这个目录下, 这样即使Pod挂了漂移了也不影响
- Pod做到不漂移
- Pod使用K8SFramework的LocalPv来实现

具体的yaml文件如下:
```yaml
apiVersion: k8s.tars.io/v1beta1
kind: TServer
metadata:
  labels:
    tars.io/ServerApp: tars
    tars.io/ServerName: compiler
    tars.io/SubType: normal
  name: tars-compiler
  namespace: tars-dev
spec:
  app: tars
  server: compiler
  important: 3
  subType: normal
  normal:
    ports:
      - isTcp: true
        name: http
        port: 8080
  k8s:
    mounts:
      - mountPath: /data
        name: data
        readOnly: false
        source:
          tLocalVolume: {}
    replicas: 1
  release:
    source: tars-compiler
    id: v-0000001
    image:
      tarscloud/base-compiler
```

说明:
- 注意名字空间, 这里为tars-dev,  必须你和安装在K8S的tars框架相同的名字空间
- replicas可以指定副本数, 可以给每个人分配一个, 相当于开发机了
- tarscloud/base-compiler 是编译环境, 可以制作编译环境
- id: v-0000001, 每次更新编译环境, 需要更新这个id值, 不冲突即可
- tars.io/SubType: normal 以及 subType: normal, 这里值是normal, 表示非tars服务的pod, 目前在tarsweb上看不到这类服务

## 使用步骤

部署tars-compiler

```sh
kubectl apply -f debug.yaml
```

容器在K8S启动以后, 容器内部的```/data```目录已经映射到宿主机的 ```/usr/local/app/tars/host-mount/tars-dev/tars.compiler/data```


你可以进入容器开发服务了
```
kubectl exec -it tars-compiler-0 -n tars-dev -- bash
```

这时候服务可以连接集群中任何服务!主控地址为: tcp -h tars-tarsregistry -p 17890

**注意进入容器/data目录开发!**