# TarsK8S 升级

tarsk8s 的版本升级是通过 Helm Char 来完成的, 您可以使用项目已构建好的 Helm Chart, 也可以自行构建

## 升级评估

tarsk8s 在发展过程中不可避免的会遇到新版本对旧版本兼容的问题

每次的版本发布我们会根据兼容性政策对旧版本兼容,同时附带说明

 您在升级前需要根据兼容性说明评估升级对您的现有集群带来的影响 特别要注意的是 crd 和 controller
升级的影响范围是整个集群, framework 的升级的影响范围限于您指定的 namespace

## 升级步骤

### 升级 Controller

如果在版本说明中指出修改过 crd 字段, 您需要先解压 controller 的 helm char 包, 然后执行:

```shell
kubectl apply -f ${dir}/crds
```

您可以使用如下命令升级 controller:

```shell
helm3 upgrade tarscontroller tarscontroller-${version}.tgz
```

如果需要定制参数, 您可以新建 tarscontroller.yaml 文件, 并根据说明填充参数

```yaml
helm:
  build:
    #id 是在构建 helm chart 时指定的 tag 值
    # 如果你迁移或 retag 过镜像,需要重设此地址
    id: v20220301
  dockerhub:
    # registry 是在构建 helm chart 时指定的 docker-registry
    # 如果你迁移过镜像,需要重设此地址
    registry: tarscloud
```

然后使用如下命令安装:

```shell
helm3 upgrade tarscontroller tarscontroller-${version}.tgz -f tarscontroller.yaml
```

您也可以直接通过命令行指定参数,命令格式为:

```shell
helm3 upgrade tarscontroller --set 'helm.dockerhub.registry=${DOCKER_REGISTRY}' tarscontroller-${version}.tgz
```

### 等待 Controller 启动

helm3 upgrade 执行完毕后, 您可以执行如下命令等待 Controller 的所有 pod 启动 :

```shell
kubectl wait --for=condition=Ready nodes -n tars-system --timeout=180s
```

您可以使用如下命令查看 pod 启动详情 :

```shell
kubectl get pods -n tars-system -o wide
```

### 升级 Framework

在升级 framework前, 您需要新建 tarsframework.yaml 文件, 并根据说明填充参数

```yaml
helm:
  build:
    #id 是在构建 helm chart 时指定的 tag 值
    #如果你迁移或 retag 过镜像,可能需要重设此地址
    id: v20220301
  dockerhub:
    # registry 是在构建 helm chart 时指定的 docker-registry
    # 如果你迁移过镜像,可能需要重设此地址
    registry: tarscloud
```

您需要执行如下命令升级Framework:

```shell
helm3 upgread tarsframework -n ${namespace} -f  tarsframework.yaml tarsframework-${version}.tgz
```

### 等待 Framework 启动

您可以执行如下命令查看 Framework 服务的 Pod 启动状态:

```shell
kubectl get pods -n ${namespace} -o wide
```