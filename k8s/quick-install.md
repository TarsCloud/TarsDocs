# TarsK8S 快速安装

再安装 TARS 到 K8S 之前, 需要理解一下, 这里有两个镜像仓库:

- tars 框架服务的镜像仓库: 如果你不采用源码编译, 通常使用官方镜像仓库即可
- 业务服务的镜像仓库: 通常都是使用者自己构建镜像仓库

## 在 K8S 上安装控制器(tarscontroller)

直接使用官方提供的镜像完成安装! helm 请使用 helm3 版本.

- 添加 helm 仓库

```
helm repo add tars-k8s https://tarscloud.github.io/K8SFramework/charts

```

- 安装控制器

```
helm install tarscontroller tars-k8s/tarscontroller
```

注意:

> - 一个 K8S 集群只需要安装一个 TARS 控制器即可
> - 控制器是用来在 K8S 上安装 TARS 框架的
> - 执行完这一步以后, 可以在 K8S 集群中看到新建了一个名字空间: tars-system, 下面有 tarscontroller

- 在 k8s 中创建访问仓库的 secret

**tars-image-secret 这个名字不要改, helm 包等都直接引用这个 secret 名称, 最好不要修改!**

```
kubectl create secret docker-registry tars-image-secret -n tars-dev --docker-server=${docker_registry} --docker-username=${docker_user} --docker-password=${docker_pass}
```

这个 secret 是 k8s 从 业务仓库 下载/上传镜像时使用的 secret

## 在 K8S 上安装 Tars 框架

- 创建 TARS 环境

```
kubectl create ns tars-dev

helm install tarsframework -n tars-dev --set 'helm.dockerhub.registry=tarscloud,dockerRegistry=${docker_registry},web=${web_host}' tars-k8s/tarsframework

```

参数说明:

- tars-dev: 这个是 K8S 上的名字空间 , 表示 Tars 部署在这个名字空间内
- tarscloud: 官方 tars 镜像的仓库名称(如果你源码编译, 使用你自己的仓库名称, 这里就需要修改)
- docker_registry: 业务服务的仓库, 这个用于存放业务服务的镜像仓库
- tars-image-secret: 这个名字不要修改, 如果修改, 则需要指定 helm 安装时增加指定参数: `dockerSecret={your-tars-image-secret}`
- web_host: 访问 tars web 的地址, 注意集群中必须已经按了 ingress, 且 web_host 指向了 ingress 的入口!

## 升级说明

如果是升级, 方式类似, 使用 helm upgrade 命令即可.

- 首先添加和升级 repo 仓库:

```
helm repo add tars-k8s https://tarscloud.github.io/K8SFramework/charts

helm repo update
```

- 升级框架

```
helm upgrade tarscontroller --set 'helm.dockerhub.registry=tarscloud,helm.build.id=v1.0.0-nightly' tars-k8s/tarscontroller
helm upgrade tarsframework -n tars-dev --set 'helm.dockerhub.registry=tarscloud,dockerRegistry=${docker_registry},web=${web_host},helm.build.id=v1.0.0-nightly' tars-k8s/tarsframework

```

注意:

- 最好只升级版本, 不要降级版本, 同时注意升级版本需要自己执行 CRD(CRD 如果不变可以不管)
- v1.0.0-nightly 代表的镜像版本, 匹配 K8SFramework 的 tag, 最好不要用 latest, 指定版本更安全!
- 升级最好手动执行 CRD!!!如果大版本升级, CRD 通常会升级.

```
cd install/tarscontroller/crds

kubectl apply -f ...

```

说明:

- helm.build.id: 对应了 K8SFramework 的 tag 版本号!

## 调度

完成以上步骤以后, 实际框架并没有在 K8S 上调度起来, 请参考[框架调度](./framework-affinity.md)
