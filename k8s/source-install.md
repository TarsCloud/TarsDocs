# TarsK8S 安装

## 在K8S上安装TARS框架
  TarsK8S 以 helm 包的形式对外发布.每个 Helm 包包含了 完成的框架服务. 

  在安装时,以k8s命名空间为分割,每个命名空间可以且只可以部署一套框架服务,每套框架服务都有完整的功能组件,与其他命名空间的框架互相独立.

安装步骤说明:
- 编译代码

```sh
./buildBinary.sh
```

- 制作docker包并推送到仓库

**注意本机需要安装有helm3**

```sh
./buildHelm.sh <DOCKER_REPOSITORY>  <DOCKER_REGISTRY_USER> <DOCKER_REGISTRY_PASSWORD> <TAG> <DOCKER_REGISTRY>  
```

参数说明:
- DOCKER_REPOSITORY: 仓库名称, 比如tarscloud
- DOCKER_REGISTRY_USER: 访问仓库的用户名
- DOCKER_REGISTRY_PASSWORD: 访问仓库的密码
- TAG: 当前编译的版本号,  docker的tag, helm的版本号
- DOCKER_REGISTRY: 仓库url地址
>- 可以为空, 为空则为公共仓库
>- 如果是私有仓库, 则需要路径: xxx.xxx.com
>- 生成的依赖docker前缀是: $DOCKER_REGISTRY/DOCKER_REPOSITORY

- 安装控制器

```
helm install tarscontroller --set 'helm.dockerhub.registry=${DOCKER_REGISTRY}/${DOCKER_REPOSITORY},helm.build.id=v1.0.0' tarscontroller-v1.0.0.tgz
```

**注意这个v1.0.0和buildHelm中生成的tarscontroller版本相关, 具体需要看当前实际的版本号!**

- 在k8s中创建访问仓库的secret

**tars-image-secret这个名字不要改, helm包等都直接引用这个secret名称, 最好不要修改!**

```
kubectl create secret docker-registry tars-image-secret -n tars-dev --docker-server=${DOCKER_REGISTRY} --docker-username=${DOCKER_REGISTRY_USER} --docker-password=${DOCKER_REGISTRY_PASSWORD}   
```

- 创建TARS环境

```
kubectl create ns tars-dev

helm install tarsframework -n tars-dev --set 'dockerRegistry=${DOCKER_REGISTRY},dockerSecret=tars-image-secret,els.nodes=els.nodes=tars-elasticsearch:9200,helm.build.id=${TAG},helm.dockerhub.registry=${DOCKER_REGISTRY},web=${web_host}' tarsframework-v1.0.0.tgz

```


参数说明:
- tars-dev: 这个是K8S上的名字空间 , 表示Tars部署在这个名字空间内
- web_host: 访问tars web的地址, 注意集群中必须已经安装了ingress, 且web_host指向了ingress的入口!
- v1.0.0和buildHelm中生成的tarsframework版本相关, 具体需要看当前实际的版本号!


## 升级说明

如果升级TARS版本, 按照上述流程重新执行即可, 需要注意的是, 如果TARS框架的crd文件做了变更(install/tarscontroller/crds), 则需要自己手工执行以下, helm upgrade不会再执行crd文件!

示例脚本如下:

```
helm upgrade tarscontroller --set 'helm.dockerhub.registry=${DOCKER_REGISTRY}/${DOCKER_REPOSITORY},helm.build.id=${TAG}' tarscontroller-v1.0.0.tgz

helm upgrade tarsframework -n tars-dev --set 'helm.dockerhub.registry=${DOCKER_REGISTRY}/${DOCKER_REPOSITORY},,helm.build.id=${TAG},dockerRegistry=${DOCKER_REGISTRY},web=${web_host}' tarsframework-v1.0.0.tgz

```

手动执行crd!!!

```
cd install/tarscontroller/crds
kubectl apply -f ....yaml

```

## 调度

完成以上步骤以后, 实际框架并没有在K8S上调度起来, 请参考[框架调度](./framework-affinity.md)
