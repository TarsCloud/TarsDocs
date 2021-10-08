

# 制作业务服务镜像

## 镜像制作
为了方便开发者制作tars服务的镜像, 特提供了一个```exec-build.sh```来完成, 该脚本已经被内置到编译镜像```tarscloud/compiler```中了, 你可以在编译镜像中直接使用!

该脚本的使用如下:
```
exec-build.sh BaseImage SERVERTYPE(cpp/nodejs/java-war/java-jar/go/php) Files YamlFile Tag Push(true/false) Dockerfile
```

参数说明:
- BaseImage: 依赖的基础镜像(官方镜像为: tarscloud/tars.cppbase:$tag)
- SERVERTYPE: 语言, 目前支持: cpp/nodejs/java-war/java-jar/go/php
- Files: 需要打包进docker中的文件或者目录
- YamlFile: yaml文件描述服务用, 可以参考[helm包](./helm.md)
- Tag: docker版本号, 例如v1.0.2
- Push: 制作好的docker是否push到仓库中($Registry/$APP/$SERVER:$TAG)
- Dockerfile: 制作镜像的dockerfile路径, 正常情况不需要提供, 你如果希望自己改写Dockerfile, 则需要提供, 请参考[Dockerfile](../Dockerfile.md)
例如:
```
exec-build.sh tarscloud/tars.cppbase cpp build/StorageServer yaml/value.yaml v1.0.0
```

执行完脚本后会生成:
- 服务的镜像是 YamlFile中: repo.image:$TAG, 可以通过```docker images```查看到, 你需要自己推送到docker仓库

后续流程:
- 为了方便你部署, 提供了```exec-helm```脚本, 快速制作helm包, 具体请参考[exec-helm](./exec-helm.md)
- 为了方便你部署, 提供了```exec-deploy```脚本, 方便你部署到K8S集群, 具体请参考[exec-deploy](./exec-deploy.md)

## 镜像说明

- 实际服务的镜像, 在K8S上运行起来以后, 是有两个进程的, 一个是tarsnode进程, 一个业务进程.
- 但是制作的业务服务镜像中, 只有业务进程, 无tarsnode进程, tarsnode进程是镜像启动时, 
- 业务服务制作镜像时, 会把自己可执行程序copy到容器/usr/local/server/bin目录下(Dockerfile)
- tars服务(TServer)展开成Statefullset时, 会有两个容器, 一个tarsnode, 一个业务容器, 他们通过一个目录共享(/usr/local/app/tars/tarsnode)
- 然后这两个容器各自把自己容器的数据copy到对应的目录下, tarsnode容器copy tarsnode相关目录, 业务容器copy业务可执行程序, 然后再启动tarsnode(同时拉起业务进程)
- 如果业务进程挂掉, pod是不会退出的(因为tarsnode还存在), 这个时候会出现类似这样的错误: The status of pod readiness gate "tars.io/active" is not "True", but False


