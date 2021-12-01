# 制作业务服务镜像

## 镜像制作

为了方便开发者制作 tars 服务的镜像, 特提供了一个`exec-build.sh`来完成, 该脚本已经被内置到编译镜像`tarscloud/compiler`中了, 你可以在编译镜像中直接使用!

该脚本的使用如下:

```
exec-build.sh BaseImage SERVERTYPE(cpp/nodejs/java-war/java-jar/go/php) Files YamlFile Tag Push(true/false) Dockerfile
```

参数说明:

- BaseImage: 依赖的基础镜像(官方镜像为: tarscloud/tars.cppbase:$tag)
- SERVERTYPE: 语言, 目前支持: cpp/nodejs/java-war/java-jar/go/php
- Files: 需要打包进 docker 中的文件或者目录
- YamlFile: yaml 文件描述服务用, 可以参考[helm 包](./helm.md)
- Tag: docker 版本号, 例如 v1.0.2
- Push: 制作好的 docker 是否 push 到仓库中($Registry/$APP/$SERVER:$TAG)
- Dockerfile: 制作镜像的 dockerfile 路径, 正常情况不需要提供, 你如果希望自己改写 Dockerfile, 则需要提供, 请参考[Dockerfile](../Dockerfile.md)
  例如:

```
exec-build.sh tarscloud/tars.cppbase cpp build/StorageServer yaml/value.yaml v1.0.0
```

执行完脚本后会生成:

- 服务的镜像是 YamlFile 中: repo.image:$TAG, 可以通过`docker images`查看到, 你需要自己推送到 docker 仓库

后续流程:

- 为了方便你部署, 提供了`exec-helm`脚本, 快速制作 helm 包, 具体请参考[exec-helm](./exec-helm.md)
- 为了方便你部署, 提供了`exec-deploy`脚本, 方便你部署到 K8S 集群, 具体请参考[exec-deploy](./exec-deploy.md)

## 镜像说明

- 实际服务的镜像, 在 K8S 上运行起来以后, 是有两个进程的, 一个是 tarsnode 进程, 一个业务进程.
- 但是制作的业务服务镜像中, 只有业务进程, 无 tarsnode 进程, tarsnode 进程是独立的 init 镜像, 可以参考具体发布到 k8s 上的每个 tars 服务
- 业务服务制作镜像时, 会把自己可执行程序 copy 到容器/usr/local/server/bin 目录下(Dockerfile)
- tars 服务(TServer)展开成 Statefullset 时, 会有两个 container, 一个 tarsnode, 一个业务容器, 他们通过一个目录共享(/usr/local/app/tars/tarsnode)
- tarsnode 镜像(tarscloud/tars.tarsnode)主要是包含 tarsnode 程序和配置, 在镜像的/tarsnode 目录下
- 启动时 init container 先初始化, 即 tarsnode 的脚本 entrypoint.sh 先动, 它里面执行环境初始化, 将 tarsnode 相关的程序和脚本从/tarsnode copy 到 /usr/local/app/tarsnode 目录下
- 业务容器启动会调用 /usr/local/app/tarsnode/util/start.sh 启动 tarsnode (上一步 copy 的)
- start.sh 中会创建 链接目录 /usr/local/app/tarsnode/data/xxxx/bin 到 /usr/local/server/bin, 并执行根据服务类型(ServerType)启动实际服务
- 如果业务进程挂掉, pod 是不会退出的(因为 tarsnode 还存在), 这个时候会出现类似这样的错误: The status of pod readiness gate "tars.io/active" is not "True", but False
