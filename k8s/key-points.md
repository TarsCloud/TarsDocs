

# 关键特性

## tars服务

每个 tars 服务说明
- tars服务对应自定义crd: tserver, 可以通过`kubectl get tserver`查看到
- 在k8s集群上对应一个service和一个statefulset, name都为lower($ServerApp)-lower($ServerName)
- 服务的端口配置, 副本伸缩，版本变更等操作最终都是通过更新service, statefulset 对应参数来完成的
- 此外在service上和statefulset 上分别添加了 annotations 和 label

+ service annotations and label

```yml
annotations:
  tars.io/Servant: '{"notifyobj":{"Name":"NotifyObj","Port":10000,"HostPort":0,"Threads":3,"Connections":10000,"Capacity":10000,"Timeout":60000,"Istars":true,"IsTcp":true}}'
labels:
  tars.io/ServerApp: tars
  tars.io/ServerName: tarsnotify
```

+ statefulset annotations and label

```yaml
  annotations:
    tars.io/NodeSelector: '{"Kind":"AbilityPool","Value":[]}'
    tars.io/NotStacked: "false"
  creationTimestamp: "2020-07-21T01:54:22Z"
  generation: 1
  labels:
    tars.io/ServerApp: tars
    tars.io/ServerName: tarsnotify
```

annotations 的含义下文会有阐述

## 服务暴露

在某些情况下,需要将服务的地址和端口直接对集群外暴露,TarsK8S提供了两种暴露方式,如非特殊需要,建议使用 HostPort 方式暴露

- HostNetwork

   启用 HostNetwork 方式的步骤是 节点选择方式为 “指定节点”(NodeBind)->勾选 HostNetwork.
   此种方式会影响 Statefulset.Spec.Template.Spec.HostNetWork 属性 

- HostPort

   启用 HostNetwork 方式的步骤是 节点选择方式为 “指定节点”(NodeBind)->勾选 HostPort->在 Servant选项中填写 每个 ServantPort 对应 ServantHostPort

   此种方式会影响 Statefulset.Spec.Template.Spec.Containers[0].Ports 属性,示例如下:
   
   ```json
    "addresses": [
        {
            "containerPort": 232,
            "hostPort": 9090,
            "name": "testobj",
            "protocol": "TCP"
        }
    ],

   ```
 HostNetwork 与 HostPort 方式只能二选一，在同时配置的情况下,只有 HostNetwork 生效

## 服务堆叠

在默认情况下，节点可以同时运行同一个服务的多个Pod,此种情况称为堆叠.在一些高可用场景下，可能希望Pod分散在不同的Node,可以使用 不允许堆叠 选项，来强制Pod分布在不同节点. (后续会添加分散条件，比如强制Pod分散在不同的可用区)

此功能是通过定义 Statefulset.Spec.Template.Spec.Affinity.PodAntiAffinity 实现的,示例如下:

```json
{
  "affinity": {
    "podAntiAffinity": {
      "requiredDuringSchedulingIgnoredDuringExecution": [
        {
          "labelSelector":{
            "tars.io/ServerApp" :"tars",
            "tars.io/ServerName":"tars-tarsnotify"
          },
          "namespaces":"tars",
          "topologyKey": "kubernetes.io/hostname"
        }
      ]
    }
  }
}
```

## HostIpc

某些服务程序可能会使用系统 System IPC 功能(共享内存，消息队列等),如果重建Pod会导致数据丢失，因此有必要让Pod 使用宿主机命名空间IPC资源，而不是Pod自身命名空间 IPC 资源.如果启用该选项，只要 Pod不被调度到另外的节点，IPC 内保存的数据不会丢失

启用 HostIpc 方式的步骤 节点选择方式为 “指定节点”(NodeBind)->勾选 HostIpc
此种方式会影响 Statefulset.Spec.Template.Spec.HostIpc 属性

为了确保 Pod不被调度到另外的节点，可以设置 不允许服务堆叠以及设置服务Pod数量严格等于指定的节点数


## ReadinessGates
  TarsK8S 使用了 k8s 的 ReadinessGates 特性，为每一个 Statefulset 配置了  
  ```json
  {
    "readinessGates":[
      {
        "conditionsType":"tars.io/service"
      }
    ]
  }
  ```
  当 tarsregistry 程序接收到 tarsnode 发送的 state 数据时
  + 如果 state!="Active" ,会将 pod["tars.io/service"].status 设置为 false . 这会使 k8s 自动从 endpoints 种摘除此 pod
  + 如果 state=="Active",会将 pod["tars.io/service"].status 设置为 true, 这会使 k8s 自动将此 pod 加入到 endpoints
