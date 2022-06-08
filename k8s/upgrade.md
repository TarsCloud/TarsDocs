# 目录

- [升级](#升级)
    * [版本与兼容性计划](#版本与兼容性计划)
        + [版本格式](#版本格式)
        + [版本规则](#版本规则)
        + [兼容性规则](#兼容性规则)
        + [兼容性检测](#兼容性检测)
    * [获取 Helm Chart](#获取HelmChart)
    * [升级 Controller](#升级Controller)
        + [确认Framework兼容性](#确认Framework兼容性)
        + [升级 CRD](#升级CRD)
        + [安装 Controller](#安装Controller)
        + [等待 Controller 启动](#等待Controller启动)
    + [升级 Framework](#升级Framework)
        + [确认Controller兼容性](#确认Controller兼容性)
        + [生成Framework配置文件](#生成Framework配置文件)
        + [安装Framework](#安装Framework)
        + [等待Framework启动](#等待Framework启动)

# 升级

## 版本与兼容性计划
从版本 1.3.2 开始, 我们制定了全新的版本与兼容性规则  
不兼容的升级可能对给您的业务带来损失, 我们强烈建议您在执行升级操作前阅读和理解版本与兼容性规则

### 版本格式

**TarsCloud K8SFramework** 在发布 Helm Chart 时,采用与 Kubernetes 相同格式的版本.  
具体为: "主版本号.次版本号.修订号-附注说明", 其中附注说明为可选项.

### 版本规则

1. 根据最高 CRD Version 生成 项目主版本号与次版本号, 比如:  
   最高 CRD Version 为 v1beta3 时, 主版本号为 "1" , 次版本号为 "3"  
   最高 CRD Version 为 v2beta1 时,主版本号为 "2" , 次版本号为 "1"
2. 修订号与附注在发布时酌情定义
3. 主版本号,次版本号,修订号按整数升序发布

### 兼容性规则

**TarsCloud K8SFramework** 提供最多三个"主版本.次版本"的兼容性保证.具体规则为
1. 相同的 "主版本.次版本", 提供兼容性保证
2. 当 "次版本" == 0 时, 提供前一主版本的最新两个次版本的兼容性保证
3. 当 "次版本" == 1 时, 提供前一主版本的最新一个次版本,同主版本的最新一个次版本的兼容性保证
4. 当 "次版本" >=2 时, 提供同主版本的最新两个次版本的兼容性保证

### 兼容性检测
1. 升级 Controller 时, 请确保待升级的 Controller 版本能兼容已安装的 Framework 版本
2. 升级 Framework 时, 请确保已安装的 Controller 版本能兼容待升级的 Framework 版本
3. 降级 Framework 时, 请确保已安装的 Controller 版本能兼容待降级的 Framework 版本
4. 降级 Controller 可能导致不可预知的问题, 我们不建议您对 Controller 执行降级操作
   不满足兼容性规则的 升/降级操作 可能导致 framework 程序运行错误, 甚至 crd 对象丢失

## 获取HelmChart

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
默认情况下构建出的 Helm Chart 包位于 charts 目录, 版本格式为  "主版本.次版本.0-master"  
如果您在源码构建时 修改了CHAR_VERSION , CRD 定义等, 则需要您自行保证兼容性

## 升级Controller

### 确认Framework兼容性

升级 Controller 前, 请您确保待安装的 Controller 能兼容当前已安装的 Framework  
不兼容的升级可能导致 Framework 启动失败或者 CRD 对象丢失  
您可以执行如下命令查看当前安装了哪些 Framework 版本

```shell
helm list -f tarsframework -A
```

您可以看到如下输入内容:
```shell
NAME           NAMESPACE REVISION  UPDATED                       STATUS   CHART                APP VERSION
tarsframework  tars      1        2022-06-01 11:09:09.034451845 deployed tarsframework-1.3.2  v1beta3  
```
CHAT 参数即代表了您已安装的 Framework 版本

### 升级CRD

如果您意图跨越 "主版本.次版本" 升级 Controller, 或者您使用的是 1.3.2 以前的版本 Controller, 则需要先升级 CRD 定义  
您可以执行如下命令查看当前安装的 Controller 版本

```shel
helm list -f tarscontroller -A
```

您可以看到如下输入内容:

```shell
NAME           NAMESPACE   REVISION  UPDATED                       STATUS   CHART                 APP VERSION
tarscontroller tars-system 1         2022-06-01 11:09:09.034451845 deployed tarscontroller-1.3.2  v1beta3  
```

CHAT 参数即代表了您已安装的 Controller 版本
您可以按如下步骤 升级 CRD 定义:
1. 如果您通过 Helm Repo 获取 Helm Chart ,则需要先将 Chart 拉取到本地
   ```shell
   helm repo update tars-k8s                                              # 更新 tars-k8s repo 索引
   helm search repo tars-k8s -l                                           # 查看 tars-k8s repo 索引
   helm pull tars-k8s/tarscontroller --version ${Version}                 # 下载指定版本的 helm chart 
   ```
   如果您是通过源码构建的的 Helm Chart ,默认情况下,包位于 charts 目录下
2. 解压 Helm Chart 包
   ```shell
   # 您需要将 ${Version} 替换成实际 版本号
   tar zxvf tarscontroller-${Version}.tgz
   ```
3. 安装 CRD
   ```shell
   kubectl apply -f tarscontroller/crds
   ```

### 安装Controller
如果您是通过  "直接下载" 或者 "源码构建" 方式获取的 Helm Chart, 执行如下命令:
```shell
# 您需要将 ${Version} 替换成实际 版本号
helm upgrade tarscontroller tarscontroller-${Version}.tgz
```
如果您是通过 Helm Repo 方式获取 Helm Chart, 执行如下命令:

```shell
# 您需要将 ${Version} 替换成实际 版本号
helm repo update tars-k8s                                              # 更新 tars-k8s repo 索引
helm search repo tars-k8s -l                                           # 查看 tars-k8s repo 索引
helm upgrade tarscontroller tars-k8s/tarscontroller --version=${Version}
```

### 等待Controller启动

您可以使用如下命令查看 controller pod 启动详情 :

```shell
kubectl get pods -n tars-system -o wide
```

## 升级Framework

### 确认Controller兼容性

升级 Controller 前, 请您确保已安装的 Controller 能兼容当前待安装的 Framework  
不兼容的升级可能导致 Framework 启动失败或者 CRD 对象丢失  
您可以执行如下命令查看当前安装的 Controller 版本

```shel
helm list -f tarscontroller -A
```

您可以看到如下输入内容:

```shell
NAME           NAMESPACE   REVISION  UPDATED                       STATUS   CHART                 APP VERSION
tarscontroller tars-system 1         2022-06-01 11:09:09.034451845 deployed tarscontroller-1.3.2  v1beta3  
```

CHART 值 即为当前已安装的 Controller 版本

### 生成Framework配置文件

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

### 安装Framework

如果您是通过  "直接下载" 或者 "源码构建" 方式获取的 Helm Chart, 请执行如下命令:

```shell
# 您需要将 ${Namespace} 替换成实际 命名空间
# 您需要将 ${Version} 替换成实际 版本号
helm upgrade tarsframework -n ${Namespace} --create-namespace -f tarsframework.yaml tarsframework-${Version}.tgz
```

如果您是通过 Helm Repo 方式获取 Helm Chart, 请执行如下命令:

```shell 
# 您需要将 ${Namespace} 替换成实际 命名空间
# 您需要将 ${Version} 替换成实际 版本号
helm repo update tars-k8s                                              # 更新 tars-k8s repo 索引
helm search repo tars-k8s -l                                           # 查看 tars-k8s repo 索引
helm upgrade tarsframework -n ${Namespace} --create-namespace -f tarsframework.yaml tars-k8s/tarsframework --version=${Version}
```

### 等待Framework启动

您可以执行如下命令查看 Framework 服务的 Pod 启动状态:

```shell
kubectl get pods -n ${namespace} -o wide
```
