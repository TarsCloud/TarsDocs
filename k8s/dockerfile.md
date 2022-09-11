#  Dockerfile说明

## 目的

部署在K8S上每个业务最终都要编译成Docker, 才能发布到K8S+TARS上.

每个业务制作成docker, 都有确定的规范, 必须遵循这个规范构建docker, 因为:
- tars服务制作成docker后, 跑在k8s上pod内部都有两个进程, 一个业务进程, 一个tarsnode进程;
- tarsnode进程是pod启动时框架注入进去的, 制作docker时候并不具备
- 业务进程目录结构有一定的规范, 必须在: ```/usr/local/server/bin``` 下
- 业务进程都有类型: cpp/nodejs/java-war/java-jar/go/php, 并通过环境变量来设定
- 业务进程其实是tarsnode进程拉起的, 根据环境变量的不同启动方式也不同
- tars服务的镜像制作时, 有一个基础镜像, 这个基础镜像包含了服务的运行环境, 比如jdk, node等, 官方针对不同语言的服务会提供对应的基础镜像, 当然你可以制作自己的基础镜像
- 基础镜像和编译镜像是保持一致的, 这样保证编译的服务能在基础镜像中运行

**注意Dockerfile通常不需要你自己实现, 统一制作了一个docker容器, 里面存在了各种语言制作的Dockerfile, 从容器中copy出来即可**

## 格式示例

Dockerfile格式大致介绍下, 以cpp语言的为例

cpp格式如下, 其他格式, 更换ServerType即可, 具体可以参考Dockerfile目录:

```dockerfile
ARG BaseImage
ARG ServerType
ARG BIN

FROM ${BaseImage}

ENV ServerType=${ServerType}

RUN mkdir -p /usr/local/server/bin/
COPY $BIN /usr/local/server/bin/
```

说明:
- BaseImage: 基础镜像`(tarscloud/tars.cppbase, tarscloud/tars.javabase, tarscloud/tars.nodejsbase, tarscloud/tars.php74base)`, 注意go的服务也使用tarscloud/tars.cppbase
- ServerType: 服务的类型`(cpp/go/java-war/java-jar/nodejs/php)`
- BIN: 哪些文件/文件夹, 需要copy到镜像中
- 如果你是自己编译的源码, 其中`tarscloud/tars.cppbase`等镜像可以使用你自己的编译出来镜像

- 制作docker

正常情况docker的制作, 是在自动构建脚本中构建, 并推送到harbor仓库中的同时发布, 当然你也可以手工制作docker, 比如:
```sh
docker build . -t xxx/notifyproxyserver --build-arg BIN=build/bin/NotifyProxyServer --build-arg BaseImage=tarscloud/tars.cppbase --build-arg ServerType=cpp
docker push xxx/notifyproxyserver
```

这个语句其实被```exec-build.sh```中执行, 在CI/CD中自动构建业务服务的镜像, 后续会介绍.

