# TarsK8S 部署

TarsK8S 以 Helm Chart 的形式发布和部署. 通常情况下, 您可以使用项目预先构建的 Helm Chart, 并参照 "安装 Helm Char"  安装.

您也可以参照 "构建 Helm Chart" 自行生成 Helm Chart, 然后参照 "安装 Helm Chart" 安装

## 构建 Helm Chart

### 编译依赖

在 构建前,请安装好依赖程序或依赖库

+ helm3

+ docker ( >=19.03)
+ flex
+ bison
+ make
+ cmake
+ libzlib

### 编译源码

 ```shell
 git submodule update --init --recursive
 ./buildBinary
 ```

上述命令执行成功后,会在 build/files/binary 目录生成可执行程序文件

### 生成 Helm Chart

```
./buildHelm.sh <DOCKER_REPOSITORY> <DOCKER_REGISTRY_USER> <DOCKER_REGISTRY_PASSWORD> <TAG> <DOCKER_REGISTRY>
```

参数说明:

- DOCKER_REPOSITORY: 仓库名称, 比如 tarscloud

- DOCKER_REGISTRY_USER: 访问仓库的用户名

- DOCKER_REGISTRY_PASSWORD: 访问仓库的密码

- TAG: 当前编译的版本号, herm chart 的版本号

- DOCKER_REGISTRY: 仓库 url 地址

## 安装 Helm Chart

在正式安装前, 建议您阅读 [<<TarsK8S 特性>>](property.md) 以便于更好的理解以下操作流程的原因

### 安装准备

1. 选定 tarsk8s 的可用节点
   > tarsk8s 只会将 pod 调度到您明确指出可以被 tarsk8s 使用的节点上. 在正式部署前, 您需要筛选出 tarsk8s 可用节点.
   > 如果您仅仅是用于测试, 那么您可以选定任意节点

2. 选定您将要部署 framework 的 namespace
   > k8s 集群可以在不同的 namespace 中分别部署一套 framrwork , 每套 framework 都是是完整的 tars 框架.
   > 在正式部署前, 规划你将要使用的 namespace 非常重要
   > 如果您仅仅是用于测试, 那么您可以使用任意合法的的 namespace

3. 为所有选定的节点添加标签, 标识该节点是 tarsk8s 的可用节点, 标签格式为: tars.io/node.${namespace}
   > 您可以使用完成完成该操作:
   > ```shell
    > kubeclt label nodes ${node1} ${node2} ... tars.io/node.${namespace}=
    > ```
   > 以选定的节点 node-007, node-008, node-009 , 选定的 namespace 为 tars-dev 为例子
   > 您可以执行:
   > ```shell
    > kubeclt label nodes node-007 node-008 node-009 tars.io/node.tars-dev=
    > ```
   > 如果您希望追加 tarsk8s 可用节点, 也可以按以上命令操作
   > 如果你希望撤销 tarsk8s 可用节点, 您可以执行如下命令:
   > ```shell
    > kubeclt label nodes ${node1} ${node2} ... tars.io/node.${namespace}-
    > ```
   > 撤销标签后, tars 服务关联 pod 是否会立刻被驱逐由 k8s 的调度器决定

4. 在 1 的基础上, 选定支持 tlv( tars local volume) 的节点, 在节点添加 tars.io/SupportLocalVolume 标签
   > 关于 tlv 的介绍可以参考
   > 如果您仅仅是用于测试, 那么您可以给任意节点添加该标签
   > 您可以使用 以下命令执行该操作:
   >
   > ```shell
   > kubeclt label nodes ${node1} ${node2} ... tars.io/SupportLocalVolume=
   > ```

### 安装步骤

#### 安装 Controller

您可以使用如下命令安装 Controller:

```shell
helm3 install tarscontroller tarscontroller-${version}.tgz
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
helm3 install tarscontroller tarscontroller-${version}.tgz -f tarscontroller.yaml
```

您也可以直接通过命令行指定参数,命令格式为:

```shell
helm3 install tarscontroller --set 'helm.dockerhub.registry=${DOCKER_REGISTRY}' tarscontroller-${version}.tgz
```

#### 等待 Controller 启动

helm3 install 执行完毕后, 您可以执行如下命令等待 Controller 的所有 pod 启动 :

```shell
kubectl wait --for=condition=Ready nodes -n tars-system --timeout=180s
```

您可以使用如下命令查看 pod 启动详情 :

```shell
kubectl get pods -n tars-system -o wide
```

#### 安装 Framework

在安装 framework前, 您需要新建 tarsframework.yaml 文件, 并根据说明填充参数

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

## 指定业务服务的镜像仓库地址, tarsimage 服务会将编译后的镜像提交到此仓库
dockerRegistry:

# 指定 k8s 从 dockerRegistry 下载,上传镜像时使用的 image secret
# 安装完成后请不要修改该字段值,否则可能引起镜像拉取失败.
# 如果您需要更新仓库地址,账号,密码, 请不要修改此字段.而是修改 k8s secret 对象.
# 创建命令为 kubectl create secret docker-registry ${name} --docker-server=DOCKER_REGISTRY --docker-username=DOCKER_USER --docker-password=DOCKER_PASSWORD
dockerSecret: tars-image-secret

elk:
  nodes:
    # 此字段的默认值指向 framework 自带的 tarselasticsearch 服务.
    # 如果您希望自行部署 elasticsearch, 可以将此字段值重设为您部署的 elasticsearch 节点值
    # 如果您填充的节点不可用, 会导致部分 framework 程序无法运行
    - tars-elasticsearch:9200
## web打开的地址
## 如果您的 k8s 安装了 Ingress, 可以通过此域名访问 tarsweb
web: web.k8s.com
```

您需要执行如下命令安装 Framework:

```shell
helm3 install tarsframework -n ${namespace} -f  tarsframework.yaml tarsframework-${version}.tgz
```

#### 等待 Framework 启动

您可以执行如下命令查看 Framework 服务的 Pod 启动状态:

```shell
kubectl get pods -n ${namespace} -o wide
```

可能的问题 :

1. 如果您未遵循 "安装准备"  给任何节点添加  "tars.io/node.${namespace}"  标签, 会导致 所有 pod 启动失败

2. 如果您未遵循 "安装准备"  给任何节点添加  "tars.io/SupportLocalVolume" 标签, 会导致 tars-tarslog-0 启动失败

3. 如果您未遵循 "安装准备"  给任何节点添加  "tars.io/SupportLocalVolume" 标签, 会导致 tars-elasticsearch-0 启动失败

4. 如果您未遵循 "安装准备"  给任何节点添加  "tars.io/SupportLocalVolume" 标签, 且未在 framework 安装配置中重设 elk.nodes 值

   会导致 tars-kevent-0, tars-tarsproperty-0, tars-tarsqueryproperty-0, tars-tarstat-0, tars-tarquerystat-0 启动失败

处理的方式:

+ 为处理问题1, 您需要遵循  "安装准备"  给选定节点添加  "tars.io/node.${namespace}"  标签
+ 为处理问题2, 您需要遵循  "安装准备"  给选定节点添加  "tars.io/SupportLocalVolume" 标签
+ 为处理问题3, 您需要遵循  "安装准备"  给选定节点添加  "tars.io/SupportLocalVolume" 标签
+ 问题3 解决后, 问题4 所述的 pod 会被 k8s 调度器多次重启直到恢复正常, 如果重启间隔过长, 您可以自行删除 pod并等待重启恢复
+ 如果您希望自行部署 elasticsearch 服务, 请暂时忽略问题2, 3, 然后参考  "TarsK8S 的运维" 处理

## 升级 Helm Chart

### 升级准备

tarsk8s 发展过成中不可避免的会遇到新版本对旧版本兼容的问题 每次的版本发布我们会根据兼容性政策对旧版本兼容,同时附带说明 您在升级前需要根据兼容性说明评估升级对您的现有集群带来的影响 特别要注意的是 crd 和 controller
升级的影响范围是整个集群, framework 的升级的影响范围限于您指定的 namespace

### 升级步骤

#### 升级 Controller

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

#### 等待 Controller 启动

helm3 upgrade 执行完毕后, 您可以执行如下命令等待 Controller 的所有 pod 启动 :

```shell
kubectl wait --for=condition=Ready nodes -n tars-system --timeout=180s
```

您可以使用如下命令查看 pod 启动详情 :

```shell
kubectl get pods -n tars-system -o wide
```

#### 升级 Framework

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

#### 等待 Framework 启动

您可以执行如下命令查看 Framework 服务的 Pod 启动状态:

```shell
kubectl get pods -n ${namespace} -o wide
```