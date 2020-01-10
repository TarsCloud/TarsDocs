
# 目录
> * [介绍](#chapter-1)
> * [k8s部署](#chapter-2)

# 1 <a id="chapter-1"></a>介绍

本文介绍一种将Tars部署在k8上的方案, 主要逻辑如下:
- 将框架服务容器化, 使用docker: tarscloud/framework 或 tarscloud/tars
- 将tarsnode节点也容器化, 使用docker: tarscloud/tars-node
- tars框架和tarsnode节点都作为pod部署在k8s上, pod运行的容器当成一台虚拟机
- 通过tars web发布服务到这些容器中运行

这种方式发布和容灾仍然依赖Tars的能力, k8s只是作为容器的管理平台.

## 2 <a id="chapter-2"></a>k8s部署

先安装helm, heml的使用自己学习, 简单的说heml一种部署服务到k8s的工具.

```
helm repo add tars-stable https://tarscloud.github.io/TarsDocker/charts/stable

kubectl create namespace tars-test

helm install tars-stable/tars --name tars-test --namespace tars-test \
    --set tars.replicas=2,tarsnode.replicas=5,tars.namespace=tars-test,tars.host=domain.com,tars.port=6080

```

部署了, 2台主控, 5台节点的tars环境, web访问的域名是: http://$namespace.$host.$port, 这里是: http://tars-test.domain.com:6080