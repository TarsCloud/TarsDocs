# 目录

> - [介绍](#chapter-1)
> - [k8s 部署](#chapter-2)

# 1 <span id="chapter-1"></span>介绍

本文介绍一种将 Tars 部署在 k8 上的方案, 主要逻辑如下:

- 将框架服务容器化, 使用 docker: tarscloud/framework
- 将 tarsnode 节点也容器化, 使用 docker: tarscloud/tars-node
- tars 框架和 tarsnode 节点都作为 pod 部署在 k8s 上, pod 运行的容器当成一台虚拟机
- 通过 tars web 发布服务到这些容器中运行

这种方式发布和容灾仍然依赖 Tars 的能力, k8s 只是作为容器的管理平台.

## 2 <span id="chapter-2"></span>k8s 部署

先安装 helm3, helm 的使用自己学习, 简单的说 helm 是一种部署服务到 k8s 的工具.

下面使用 helm 安装 tars 框架, tars-test 换成你希望的 namespace

```
helm repo add tars-stable https://tarscloud.github.io/TarsDocker/charts/stable

kubectl create namespace tars-test

#部署tars(主控两台, 节点机5台)
helm install tars-stable/tars --name tars-test --namespace tars-test \
    --set tars.namespace=tars-test,tars.replicas=2,tarsnode.replicas=5,tars.host=domain.com,tars.port=6080,tars.data=/data/shared/tars-data,mysql.data=/data/shared/mysql-data,mysql.rebuild=false


```

部署了, 2 台主控, 5 台节点的 tars 环境, web 访问的域名是: http://$namespace.$host.$port, 这里是: http://tars-test.domain.com:6080

注意:

- tars.data & mysql.data 需要在共享盘上(比如 NFS), 否则 pod 漂移数据就丢失了
- mysql.rebuild 为 false, 否则 mysql 的 pod 漂移会导致数据被清空
- mysql, tars, tars-node 的 pod 都以 StatefullSet 形式存在
