# TarsWeb管理非K8S的Tars

安装到K8S里面的tars-tarsweb除了管理K8S中的Tars环境以外, 也是可以管理非K8S的Tars环境的, 同时部署在K8S中的tars服务也能无缝调用外部的tars环境.

## 主要配置介绍
- kubectl get **configmap** tars-tarsweb -n tars-dev
  - 配置k8s环境中，访问原生tars的**框架db地址**、**主控地址**和**ES 9200端口的地址**

- kubectl get **configmap** tars-tarsregistry -n tars-dev
  - 配置k8s环境中，需要访问原生tars环境中**servant地址**(通常只需配置原生tars环境中的jceproxy地址即可)

- kubectl get **taccounts** 21232f297a57a5a743894a0e4a801fc3 -n tars-dev
  - 配置k8s环境中，web需要访问的**ldap认证地址**和**重置admin密码**

- kubectl get **ttemplates** tars.elk -n tars-dev
  - 如果在初次安装框架时指定了错误的els.nodes或es的service有变更，需要修改tars.elk的ttemplate作为**ES 9200端口的地址**

**tars-dev 是安装K8S上安装Tars的名字空间**

## tars-tarsweb

- kubectl get configmap tars-tarsweb -n tars-dev
>- [K8S外部Tars框架DB：config.json](./images/Web/configjson.png)
>- [K8S外部Tars框架主控：tars.conf](./images/Web/tarsconfig.png)

## tars-tarsregistry

- kubectl edit configmap tars-tarsregistry -n tars-dev
>- [K8S外部Tars框架服务：upchain.conf](./images/Web/upchinaconf.png)
  
配置以后, K8S中的服务可以无缝访问外部tars环境的业务服务!

## 21232f297a57a5a743894a0e4a801fc3
- kubectl edit taccounts 21232f297a57a5a743894a0e4a801fc3 -n tars-dev
  - [ldap三方认证：extra](./images/Web/ldapextra.png)
  - [admin用户密码重置：authentication](./images/Web/adminreset.png)
    
# tars.elk
- kubectl edit ttemplates tars.elk -n tars-dev
  - [elk节点](./images/Web/elknodes.png)
  - 修改后重启tars-tarsnotify的pods(prometheus版本)