# 快速安装

TarsK8S 以 Helm Chart 的形式发布和部署. 您可以使用项目预先构建的 Helm Chart, 参考 "使用 Helm 仓库安装" 或 "使用 Helm Chart 安装 "  完成安装. 

您也可以参考 [<<构建 Helm Chart>>](source-deploy.md) 从源码开始 自行构建 Helm Chart, 然后参考 " 使用Helm Chart 安装" 

## 安装准备

在正式安装前,我们建议您做一些准备工作,这样框架启动会更加顺利 您也可以暂时跳过此步骤, 参考 "使用 Helm 仓库安装" 或 "使用 Helm Chart" 安装.

1. 选定 tarsk8s 的可用节点

> tarsk8s 只会将 pod 调度到您明确指出可以被 tarsk8s 使用的节点上. 在正式部署前, 您需要筛选出 tarsk8s 可用节点.
> 如果您仅仅是用于测试, 那么您可以选定任意节点
> 如果您希望查看集群节点, 您可以执行如下命令:
>
> ```shell
> kubeclt get nodes
> ```

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
> 关于 tlv 的介绍可以参考 [<<TarsK8S 特性>>](property.md)
> 如果您仅仅是用于测试, 那么您可以给任意节点添加该标签
> 您可以使用 以下命令执行该操作:
>
> ```shell
> kubeclt label nodes ${node1} ${node2} ... tars.io/SupportLocalVolume=
> ```

## 使用 Helm 仓库安装

### 添加 Helm 仓库

```shell
helm repo add tars-k8s https://tarscloud.github.io/K8SFramework/charts
```

### 安装 Controller

```shell
helm install tarscontroller tars-k8s/tarscontroller
```

### 等待 Controller 启动

您可以使用如下命令查看 controller pod 启动详情 :
```shell
kubectl get pods -n tars-system -o wide
```

### 安装 Framework

```shell
# 建立 k8s 访问 镜像仓库时使用的 secret
# docker_registry 是您的镜像仓库地址
# docker_user 是您的镜像仓库访问账号
# docker_pass 是您的镜像仓库访问密码
# 如果您没有镜像仓库, 可以暂时填充一个域名占位, 比如 example.com/tars
# 如果您的镜像仓库无需账号密码访问, 可以使用任意账号/密码占位, 比如 docker_user/docker_pass

kubectl create secret docker-registry tars-image-secret -n tars-dev --docker-server=docker_registry --docker-username=docker_user --docker-password=docker_pass

# 将 framework 安装在 tars-dev 命名空间, 您可以根据情况修改此值
kubectl create namespace tars-dev

# 执行安装
helm install tarsframework -n tars-dev --set 'helm.dockerhub.registry=tarscloud,dockerRegistry=docker_registry,web=web_host' tars-k8s/tarsframework
```

### 等待 Framework 启动

```shell
kubectl get pods -n tars-dev -o wide
```
如果未遵循 "安装准备"操作, 此时会发现部分 framework 服务 pod 启动失败.
请参考 "问题排查" 处理

## 使用 Helm Chart 安装
### 下载 Helm Chart

你可以在 [github](https://github.com/TarsCloud/K8SFramework/tree/master/charts) 查看并下载 TarsK8S 预先构建的 Helm Chart 包
预先构建包的格式为 tarscontroller-$(version).tgz 和 tarsframework-$(version).tgz
特别的,您在下载需要注意 Controller 和 Framework 和 Helm Chart 版本一致性
以使用 1.20 版本为例:

```shell
  curl -0 tarscontroller-1.2.0.tgz https://github.com/TarsCloud/K8SFramework/blob/master/charts/tarscontroller-1.2.0.tgz
  curl -0 tarsframework-1.2.0.tgz  https://github.com/TarsCloud/K8SFramework/blob/master/charts/tarsframework-1.2.0.tgz   
```

### 安装 Controller

您可以使用如下命令安装 Controller:

```shell
helm install tarscontroller tarscontroller-1.2.0.tgz
```

### 等待 Controller 启动
您可以使用如下命令查看 pod 启动详情 :

```shell
kubectl get pods -n tars-system -o wide
```

### 安装 Framework

1. 生成镜像仓库的 的访问 secret

```shell
   # 建立 k8s 访问 镜像仓库时使用的 secret
   # docker_registry 是您的镜像仓库地址
   # docker_user 是您的镜像仓库访问账号
   # docker_pass 是您的镜像仓库访问密码
   # 如果您没有镜像仓库, 可以暂时填充一个域名占位, 比如 example.com/tars
   # 如果您的镜像仓库无需账号密码访问, 可以使用任意账号/密码占位, 比如 user/password
   # tars-dev 是您将要部署 framework 的 命名空间, 您可以根据需要变更
   
   kubectl create secret docker-registry tars-image-secret -n tars-dev --docker-server=${docker_registry} --docker-username=${docker_user} --docker-password=${docker_pass} 
```

2. 新建 tarsframework.yaml 文件, 按说明填充值

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

## 指定业务服务的镜像仓库地址,请注意,此处需要和 步骤1 的镜像仓库保持一致
dockerRegistry: example.com/tars
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
helm install tarsframework -n tars-dev -f tarsframework.yaml tarsframework-1.2.0.tgz
```

### 等待 Framework 启动

您可以执行如下命令查看 Framework 服务的 Pod 启动状态:

```shell
kubectl get pods -n tars-dev -o wide
```

如果未遵循 "安装准备"操作, 此时会发现部分 framework 服务 pod 启动失败.
请参考 "问题排查" 处理

## 问题排查

1. 如果您未遵循 "安装准备"  给任何节点添加  "tars.io/node.${namespace}"  标签, 会导致 所有 pod 启动失败

2. 如果您未遵循 "安装准备"  给任何节点添加  "tars.io/SupportLocalVolume" 标签, 会导致 tars-tarslog-0 启动失败

3. 如果您未遵循 "安装准备"  给任何节点添加  "tars.io/SupportLocalVolume" 标签, 会导致 tars-elasticsearch-0 启动失败

4. 如果您未遵循 "安装准备"  给任何节点添加  "tars.io/SupportLocalVolume" 标签, 且未在 framework 安装配置中重设 elk.nodes 值

   会导致 tars-kevent-0, tars-tarsproperty-0, tars-tarsqueryproperty-0, tars-tarstat-0, tars-tarquerystat-0 启动失败

处理的方式:

+ 为处理问题1, 您需要遵循  "安装准备"  给选定节点添加  "tars.io/node.${namespace}"  标签

  ```she
  # 查询节点
  kubeclt get nodes
  
  # 以选定节点为 node_1, node_2, framework 安装命名空间为 tars-dev 为例
  kubeclt label nodes node_1 node_2 tars.io/node.tars-dev=
  ```

+ 为处理问题2, 您需要遵循  "安装准备"  给选定节点添加  "tars.io/SupportLocalVolume" 标签

  ```shell
  # 查询节点
  kubeclt get nodes
  
  # 以选定节点为 node_1, node_2为例
  kubeclt label nodes node_1 node_2 tars.io/SupportLocalVolume=
  ```

+ 为处理问题3, 您需要遵循  "安装准备"  给选定节点添加  "tars.io/SupportLocalVolume" 标签
  ```shell
  # 查询节点
  kubeclt get nodes
  
  # 以选定节点为 node_1, node_2为例
  kubeclt label nodes node_1 node_2 tars.io/SupportLocalVolume=
  ```

+ 问题3 解决后, 问题4 所述的 pod 会被 k8s 调度器多次重启直到恢复正常, 如果重启间隔过长, 您可以自行删除 pod并等待重启恢复

+ 如果您希望自行部署 elasticsearch 服务, 请暂时忽略问题 3, 4 然后参考  [<<TarsK8S 的运维>>](cloud-operator.md) 处理
