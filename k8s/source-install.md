# TarsK8S 安装

## 在 K8S 上安装 TARS 框架

TarsK8S 以 helm 包的形式对外发布.每个 Helm 包包含了 对应的框架服务.

在安装时,以 k8s 命名空间为分割,每个命名空间可以且只可以部署一套框架服务,每套框架服务都有完整的功能组件,与其他命名空间的框架互相独立.

安装步骤说明:

- 编译代码

```sh
./buildBinary.sh
```

- 制作 docker 包并推送到仓库

**注意本机需要安装有 helm3**

```sh
./buildHelm.sh <DOCKER_REPOSITORY>  <DOCKER_REGISTRY_USER> <DOCKER_REGISTRY_PASSWORD> <TAG> <DOCKER_REGISTRY>
```

参数说明:

- DOCKER_REPOSITORY: 仓库名称, 比如 tarscloud
- DOCKER_REGISTRY_USER: 访问仓库的用户名
- DOCKER_REGISTRY_PASSWORD: 访问仓库的密码
- TAG: 当前编译的版本号, docker 的 tag, helm 的版本号
- DOCKER_REGISTRY: 仓库 url 地址

  > - DOCKER_REGISTRY 可以为空, 为空则为公共仓库
  > - 如果是私有仓库, 则需要路径: xxx.xxx.com
  > - 生成的依赖 docker 前缀是: $DOCKER_REGISTRY/DOCKER_REPOSITORY

- 安装控制器

```
helm install tarscontroller --set 'helm.dockerhub.registry=${DOCKER_REGISTRY}/${DOCKER_REPOSITORY},helm.build.id=v1.0.0' tarscontroller-v1.0.0.tgz
```

说明:

> - helm.build.id=v1.0.0, 这个版本号表示生成的镜像版本 Tag, 是你 buildHelm.sh 时指定的
> - tarscontroller-v1.0.0.tgz, 这是 buildHelm.sh 生成的, 是一个 helm 包, 这里的 v1.0.0 版本号是 helm 包的版本, 具体是多少要看当前的源码版本

- 在 k8s 中创建访问仓库的 secret

**tars-image-secret 这个名字不要改, helm 包等都直接引用这个 secret 名称, 最好不要修改!**

```
kubectl create secret docker-registry tars-image-secret -n tars-dev --docker-server=${DOCKER_REGISTRY} --docker-username=${DOCKER_REGISTRY_USER} --docker-password=${DOCKER_REGISTRY_PASSWORD}
```

- 创建 TARS 框架

```
kubectl create ns tars-dev

helm install tarsframework -n tars-dev --set 'dockerRegistry=${DOCKER_REGISTRY},dockerSecret=tars-image-secret,els.nodes=els.nodes=tars-elasticsearch:9200,helm.build.id=${TAG},helm.dockerhub.registry=${DOCKER_REGISTRY}/${DOCKER_REPOSITORY},web=${web_host}' tarsframework-v1.0.0.tgz

```

参数说明:

- tars-dev: 这个是 K8S 上的名字空间 , 表示 Tars 部署在这个名字空间内
- dockerRegistry: 业务服务仓库的地址
- dockerSecret: 业务服务仓库的 secret
- helm.dockerhub.registry: 编译出来的 tars 框架的镜像地址
- web_host: 访问 tars web 的地址, 注意集群中必须已经安装了 ingress, 且 web_host 指向了 ingress 的入口!
- tarsframework-v1.0.0.tgz, 这是 buildHelm.sh 生成的, 是一个 helm 包, 这里的 v1.0.0 版本号是 helm 包的版本, 具体是多少要看当前的源码版本

## 升级说明

如果升级 TARS 版本, 按照上述流程重新执行即可, 需要注意的是, 如果 TARS 框架的 crd 文件做了变更(install/tarscontroller/crds), 则需要自己手工执行以下, helm upgrade 不会再执行 crd 文件!

示例脚本如下:

```
helm upgrade tarscontroller --set 'helm.dockerhub.registry=${DOCKER_REGISTRY}/${DOCKER_REPOSITORY},helm.build.id=${TAG}' tarscontroller-v1.0.0.tgz

helm upgrade tarsframework -n tars-dev --set 'helm.dockerhub.registry=${DOCKER_REGISTRY}/${DOCKER_REPOSITORY},helm.build.id=${TAG},dockerRegistry=${DOCKER_REGISTRY},web=${web_host}' tarsframework-v1.0.0.tgz

```

手动执行 crd!!!

```
cd install/tarscontroller/crds
kubectl apply -f ....yaml

```

## 调度

完成以上步骤以后, 实际框架并没有在 K8S 上调度起来, 请参考[框架调度](./framework-affinity.md)
