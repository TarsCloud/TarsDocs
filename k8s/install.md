# 安装

## 准备

在开始安装前,我们建议您做一些准备工作,这样框架启动会更加顺利.

### 1. 选定 namespace

> 您可以在不同的 Kubernetes Namespace 分别部署一套 Framework, 每套 Framework 都是是完整且独立运行的的 Tars 框架.
> 在正式安装前, 明确您将要部署的 Framework 所在 Namespace 非常重要

### 2. 选定可用节点

> **TarsCloud K8SFramework** 只会将 Pod 调度到您明确指出可以被 **TarsCloud K8SFramework** 使用的集群节点
> 所以在正式部署前, 明确哪些集群节点属于 **TarsCloud K8SFramework** 可用节点非常重要.
> 如果您希望查看集群的所有节点, 您可以执行如下命令:

> ```shell
> kubeclt get nodes -o wide
> ```
>
> 作为选定节点的参考指标, 我们希望您能理解  **TarsCloud K8SFramework** 的磁盘管理策略, 具体请参考 <<[特性](property.sh)>> 文档的 "磁盘管理" 一节

### 3. 添加节点标签

添加标签是为了标识该节点属于 **TarsCloud K8SFramework** 的可用节点,标签格式为: tars.io/node.${namespace}

> 您可以使用如下指令完成完成该操作:

> ```shell
> kubeclt label nodes ${node1} ${node2} ... tars.io/node.${namespace}=
> ```
> 以选定的节点名 node-007, node-008, node-009, 选定的 namespace 名 tars-dev 为例
> 您可以执行:

> ```shell
> kubeclt label nodes node-007 node-008 node-009 tars.io/node.tars-dev=
> ```
> 如果您希望追加 **TarsCloud K8SFramework** 的可用节点, 也可以按以上命令操作
> 如果你希望撤销 **TarsCloud K8SFramework** 的可用节点, 您可以执行如下命令:
>
> ```shell
> kubeclt label nodes ${node1} ${node2} ... tars.io/node.${namespace}-
> ```
> 撤销标签后, 已经被调度运行的 pod 是否会立刻被节点驱逐由 Kubernetes 的调度器策略决定

### 4. 选定支持 TLV的节点

在 2 的基础上, 选定支持 tlv( tars local volume) 的节点, 在节点添加 tars.io/SupportLocalVolume 标签

> 关于 tlv 的介绍可以参考 [<<特性>>](property.md) ,  "TServer与Statefulset的映射. tserver.spec.k8s.mounts" 和 "磁盘管理.TLV" 章节
> 如果您仅仅是用于测试, 那么您暂时可以给任意节点添加该标签
> 您可以使用 以下命令执行该操作:

> ```shell
> kubeclt label nodes ${node1} ${node2} ... tars.io/SupportLocalVolume=
> ```

### 5. 准备镜像仓库

> 您现有的 Tars 服务程序是以原生形式存在的,需要编译成Docker 镜像后才能被 Kubernetes 集调度.
> 为此,**TarsCloud K8SFramework** 内置了 tarsimage, tarskaniko 提供镜像编译服务,
> 您可以在 **TarsCloud K8SFramework** 管理平台上传原生程序包,经由 tarsimage, tarskaniko 编译成镜像
> 在正式安装前,您需要准备一个可用的镜像仓库地址, 用于接收存储编译后的镜像.

## 执行

### 1. 获取 Helm Chart

**TarsCloud K8SFrameWork** 的正式发布版本包含 Controller 和 Framework 两种 Helm Chart

其包名分别为 tarscontroller-${Vesion}.tgz , tarsframework-${Version}.tgz

您可以选择如下任意一种方式获取 **TarsCloud K8SFramework** 正式发布的 Helm Chart:

+ 直接下载

  > 您可以在 [github](https://github.com/TarsCloud/K8SFramework/tree/master/charts) 查看并下载 **TarsCloud K8SFramework** 正式发布的 Helm Chart

+ Helm Repo

  > 您可以 添加 tars-k8s repo, 然后在需要时通过 helm 指令来获取 **TarsCloud K8SFramework** 正式发布的 Helm Chart
  >
  > ```shell
  > helm repo add tars-k8s https://tarscloud.github.io/K8SFramework/charts # 添加 tars-k8s repo
  > helm repo update tars-k8s                                              # 更新 tars-k8s repo 索引
  > helm search repo tars-k8s -l                                           # 查看 tars-k8s repo 索引
  > ```

您也可以参考 <<[构建](make.md)>>文档的 构建目标.Chart 节, 自行从源码构建 Chart 包

默认情况下构建出的 Helm Chart 包位于 charts 目录

### 2. 安装 Controller

如果您是通过  "直接下载" 或者 "源码构建" 方式获取的 Helm Chart, 执行如下命令:

```shell
# 您需要将 ${Version} 替换成实际 版本号
helm install tarscontroller tarscontroller-${Version}.tgz
```

如果您是通过 Helm Repo 方式获取 Helm Chart, 执行如下命令:

```shell 
helm update repo tars-k8s
helm install tarscontroller tars-k8s/tarscontroller
```

### 3. 等待 Controller 启动

您可以使用如下命令查看 controller pod 启动详情 :

```shell
kubectl get pods -n tars-system -o wide
```

### 4. 生成 Framework 配置文件

新建 tarsframework.yaml 文件, 按说明填充值

```yaml
# TarsCloud K8SFramework 内置了镜像编译服务,可以将您的原生程序包编译成 Docker镜像,请将您准备镜像仓库地址填充到 upload.registry
# 如果您的镜像仓库地址需要账号密码认证,那就需要新建一个 Kubernetes docker-registry secret,并将 secret 名字填充到 upload.secret
# 新建 docker-registry secret 的指令为: kubectl create secret docker-registry ${secret-name} -n ${namespace} --docker-server=${registry} --docker-username=${user} --docker-password=${pass}
upload:
  registry: ""
  secret: ""

# 如果您的 Kubernetes 集群安装了 Ingress, 可以通过此域名访问 TarsCloud K8SFramework 管理平台
web: ""
```

### 5. 安装 Framework

如果您是通过  "直接下载" 或者 "源码构建" 方式获取的 Helm Chart, 请执行如下命令:

```shell
# 您需要将 ${Namespace} 替换成实际 命名空间
# 您需要将 ${Version} 替换成实际 版本号
helm install tarsframework -n ${Namespace} --create-namespace -f tarsframework.yaml tarsframework-${Version}.tgz
```

如果您是通过 Helm Repo 方式获取 Helm Chart, 请执行如下命令:

```shell 
# 您需要将 ${Namespace} 替换成实际 命名空间
helm update repo tars-k8s
helm install tarsframework -n ${Namespace} --create-namespace -f tarsframework.yaml tars-k8s/tarsframework
```

### 6. 等待 Framework 启动

您可以执行如下命令查看 Framework 服务的 Pod 启动状态:

```shell
kubectl get pods -n ${namespace} -o wide
```

如果未遵循 "准备" 操作, 此时会发现部分 Framework 服务 pod 启动失败. 请参考 "故障排查" 处理

## 故障排查

故障现象:

1. 如果您未遵循 "准备"  给任何节点添加  "tars.io/node.${namespace}"  标签, 会导致 所有 pod 处于 "Pending" 状态
2. 如果您未遵循 "准备"  给任何节点添加  "tars.io/SupportLocalVolume" 标签, 会导致 tars-tarslog-0 处于 "Pending" 状态
3. 如果您未遵循 "准备"  给任何节点添加  "tars.io/SupportLocalVolume" 标签, 会导致 tars-elasticsearch-0 处于 "Pending" 状态

故障处理:

+ 为处理问题1, 您需要遵循 "准备" 给选定节点添加  "tars.io/node.${namespace}"  标签

  ```shell
  # 查询节点
  kubeclt get nodes
  
  # 以选定节点为 node_1, node_2, framework 安装命名空间为 tars-dev 为例
  kubeclt label nodes node_1 node_2 tars.io/node.tars-dev=
  ```

+ 为处理问题2, 您需要遵循 "准备" 给选定节点添加  "tars.io/SupportLocalVolume" 标签

  ```shell
  # 查询节点
  kubeclt get nodes
  
  # 以选定节点为 node_1, node_2为例
  kubeclt label nodes node_1 node_2 tars.io/SupportLocalVolume=
  ```

+ 为处理问题3, 您需要遵循 "准备" 给选定节点添加  "tars.io/SupportLocalVolume" 标签
  ```shell
  # 查询节点
  kubeclt get nodes
  
  # 以选定节点为 node_1, node_2为例
  kubeclt label nodes node_1 node_2 tars.io/SupportLocalVolume=
  ```

+ 问题3 解决后, 问题4 所述的 pod 会被 Kubernetes 调度器多次重启直到恢复正常, 如果重启间隔过长, 您可以删除 pod 并等待重启恢复
