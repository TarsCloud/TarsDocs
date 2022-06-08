# 构建

**TarsCloud K8SFramework** 使用 Makefile 构建作为项目构建工具. 您可以通过执行 make [target] 完成构建目标  
一些目标可能需要您提供指定参数, 您可以以环境变量的方式指定这些参数,或者在执行 make [target] 时通过 Param=${Value} 方式指定

## 构建参数

在开始讨论构建目标前, 我们希望您能理解一些术语

+ Kubernetes Docker-Registry Secret  
  Kubernetes 总是从某个镜像仓库下载镜像然后将其启动为容器, 如果镜像仓库需要客户端提供账号，密码后才能下载镜像,  
  我们称之为私有镜像仓库, 此时,您需要在 Kubernetes 集群建立一个特定格式的,包含了仓库地址, 账号, 密码信息的secret,  
  这就是 [Kubernetes Docker-Registry Secret](https://kubernetes.io/zh/docs/tasks/configure-pod-container/pull-image-private-registry)

也希望您能理解一些必要的参数含义

+ BUILD_VERSION  
  在构建 **TarsCloud K8SFramework** 过程中, make 会生成一些 Docker 镜像并且尝试使用 BUILD_VERSION 作为 镜像的 Tag  
  **TarsCloud K8SFramework** 已经在 param.sh 中为这些参数设置了默认值  
  您可以通过环境变量或命令行重设 BUILD_VERSION 值, 您也可以通过在 param.sh 文件中设置 \_BUILD_VERSION_ 值来改变默认值

+ REGISTRY_URL , REGISTRY_USER, REGISTRY_PASSWORD  
  在构建 **TarsCloud K8SFramework** 过程中, 会生成多种 Docker 镜像 ，Makefile 会尝试将这些镜像推送到您指定的仓库地址 :REGISTRY_URL,  
  如果您的仓库地址开启了账号,密码验证, 您还需要提供账号:REGISTRY_USER, 密码:REGISTRY_PASSWORD参数  
  您可以通过环境变量, 命令行指定这些参数值,也可以通过在 param.sh 文件中设置 \_REGISTRY_URL_, \_REGISTRY_USER_,\_REGISTRY_PASSWORD_ 来指定参数值  
  我们建议您在源码构建时,使用公开仓库, 这个会降低实际部署时的难度.


+ CHART_VERSION, CHART_APPVERSION, CHART_DST
  CHART_VERSION, CHART_APPVERSION,CHART_DST 分别指生成 chart 包的 version, appversion 值以及 chart 包的存放目录  
  **TarsCloud K8SFramework** 已经在 param.sh 中为这些参数设置了默认值  
  您可以通过环境变量或命令行重设这些参数值, 您也可以通过在 param.sh 修改 \__CHART_VERSION_, \_CHART_APPVERSION_ ,\_CHART_DST_ 值来改变默认值

+ UPLOAD_REGISTRY, UPLOAD_SECRET  
  **TarsCloud K8SFramework** 内置了镜像编译服务将您的原生程序包编译成 Docker镜像, 您需要一个镜像仓库地址:UPLOAD_REGISTRY 来接收和存储这些镜像  
  如果您提供的是一个私有仓库,您还需要新建 Kubernetes Docker-Registry Secret, 并将 Secret 名字赋值给 UPLOAD_SECRET  
  您可以通过环境变量或命令行指定这些参数

## 构建目标

### base

base 是 **TarsCloud K8SFramework** 项目中基础镜像的集合名,具体包括

```
base: cppbase javabase nodejsbase php74base
```

您也可以执行 make cppbase , make javabase , make nodejsbase , make php74base 构建指定基础镜像

> 您需要提供 REGISTRY_URL, BUILD_VERSION 参数
> 如果 REGISTRY_URL 需要客户端提供账号,密码才能上传镜像，您还需要提供 REGISTRY_USER, REGISTRY_PASSWORD 参数

#### controller

controller 是 **TarsCloud K8SFramework** 项目中 Controller 包含的服务集合名,具体包括

```
controlelr: tarscontroller tarsagent
```

您也可以执行 make tarscontroller , make tarsagent 构建指定服务镜像
> 您需要提供 REGISTRY_URL, BUILD_VERSION 参数
> 如果 REGISTRY_URL 要求客户端提供账号,密码才能上传镜像, 您还需要提供 REGISTRY_USER, REGISTRY_PASSWORD 参数

### framework

framework 是 **TarsCloud K8SFramework** 项目中 Framework 包含的服务集合名,具体包括

```
framework: tarskaniko tarsimage tarsnode tarsregistry tarsconfig tarslog tarsnotify
				   tarsstat tarsproperty tarsquerystat tarsqueryproperty tarskevent tarsweb elasticsearch
```

您也可以执行 make tarskaniko , make tarsimage ... 构建指定服务镜像

> 您需要提供 REGISTRY_URL, BUILD_VERSION 参数
> 如果 REGISTRY_URL 要求客户端提供账号,密码才能上传镜像, 您还需要提供 REGISTRY_USER, REGISTRY_PASSWORD 参数

### chart

chart 是 **TarsCloud K8SFramework** 项目中 Chart 包的集合名,具体包括

```
chart: chart.controller chart.framework 为 Controller Helm Chart 包和Framework Helm Chart 包 
```

您也可以指定 make chart.controller, make chart.framework 来构建指定 Chart

> 您需要提供 REGISTRY_URL, BUILD_VERSION 参数
> 如果 REGISTRY_URL 要求客户端提供账号,密码才能上传镜像, 您还需要提供 REGISTRY_USER, REGISTRY_PASSWORD 参数
> 根据需要,您可以指定 CHART_VERSION ,CHART_APPVERSION, CHART_DST 参数

### all

all 是 base, controller, framework, chart 四个构建目标的集合名

> 您需要提供 REGISTRY_URL, BUILD_VERSION 参数
> 如果 REGISTRY_URL 需要客户端提供账号,密码才能上传镜像，您还需要提供 REGISTRY_USER, REGISTRY_PASSWORD 参数
> 根据需要,您可以指定 CHART_VERSION ,CHART_APPVERSION, CHART_DST 参数

### secret

用于构建 一个 Kubernetes docker-registry secret
> 您需要提供 REGISTRY_URL ,REGISTRY_USER, REGISTRY_PASSWORD 参数
> 您需要提供命名空间参数( NAMESPACE) 以及 Secret 的名字参数(NAME)

### install.controller , upgrade.controller

分别用于安装和升级 Controller
> 您需要提供 CHART 参数，指向 Controller Chart 包
> 如果您构建 Chart 包时使用的是私有仓库, 您需要在 tars-system 命名空间新建 docker-registry secret , 并指定 CONTROLLER_SECRET=${secret}

### install.framework ,upgrade.framework

分别用于安装和升级 framework
> 您需要提供 CHART 参数，表示 Framework Chart 的路径
> 您需要提供 NAMESPACE 参数, 表示 Framework Chart 安装的命名空间
> 您需要提供 UPLOAD_REGISTRY 参数, 如果UPLOAD_REGISTRY 指向私有仓库, 您需要在待安装的命名空间内新建 Kubernetes Docker-Registry Secret 并且指定 UPLOAD_SECRET=${secret}  
> 如果您在构建 Chart 包时使用的是私有仓库, 您需要在待安装的命名空间内新建 Kubernetes Docker-Registry Secret 并且指定 FRAMEWORK_SECRET=${secret}

### help

help 用于输出 make 帮助信息