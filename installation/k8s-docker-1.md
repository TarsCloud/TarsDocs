
# 目录
> * [介绍](#chapter-1)
> * [k8s部署](#chapter-2)

# 1 <span id="chapter-1"></span>介绍

本文介绍一种将Tars部署在k8上的方案, 主要逻辑如下:
- 将框架服务容器化, 使用docker: tarscloud/framework
- 将tarsnode节点也容器化, 使用docker: tarscloud/tars-node
- tars框架和tarsnode节点都作为pod部署在k8s上, pod运行的容器当成一台虚拟机
- 通过tars web发布服务到这些容器中运行

这种方式发布和容灾仍然依赖Tars的能力, k8s只是作为容器的管理平台.

## 2 <span id="chapter-2"></span>k8s部署

先安装helm, helm的使用自己学习, 简单的说helm是一种部署服务到k8s的工具.

下面给出helm安装的一种方式, 其他请参见helm官网
```
#下载 helm, 解开, 得到可执行程序helm (https://helm.sh/docs/using_helm/#installing-helm)
#tiller是helm的服务器端, 会部署在框架上, 它来完成其他docker部署到k8s上的工作
#创建helm的用户(tiller), 绑定到cluster-admin上, 拥有整个集群的管理权限
kubectl -n kube-system create serviceaccount tiller
kubectl create clusterrolebinding tiller   --clusterrole=cluster-admin   --serviceaccount=kube-system:tiller

#安装tiller
helm init --service-account tiller --tiller-image  sapcc/tiller:v2.14.3 --stable-repo-url https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts

#检查helm是否安装好:
helm version
kubectl -n kube-system  rollout status deploy/tiller-deploy

```

下面使用helm安装tars框架, tars-test换成你希望的namespace
```
helm repo add tars-stable https://tarscloud.github.io/TarsDocker/charts/stable

kubectl create namespace tars-test

#部署tars(主控两台, 节点机5台)
helm install tars-stable/tars --name tars-test --namespace tars-test \
    --set tars.namespace=tars-test,tars.replicas=2,tarsnode.replicas=5,tars.host=domain.com,tars.port=6080,tars.data=/data/shared/tars-data,mysql.data=/data/shared/mysql-data,mysql.rebuild=false


```

部署了, 2台主控, 5台节点的tars环境, web访问的域名是: http://$namespace.$host.$port, 这里是: http://tars-test.domain.com:6080

注意:
- tars.data & mysql.data 需要在共享盘上(比如NFS), 否则pod漂移数据就丢失了
- mysql.rebuild 为false, 否则mysql的pod漂移会导致数据被清空
- mysql, tars, tars-node的pod都以StatefullSet形式存在