
## 说明

为了方便发布tars服务到K8S集群中, 特制作了发布镜像, 内置了相关工具和脚本, 方便发布tars服务对应的helm包到K8S集群.

其中, tars服务的docker镜像和helm包是编译镜像中完成的, 具体请参考[编译镜像](./base-compiler.md)

制作发布镜像的docker如下:

Dockerfile:
- base-deploy.Dockerfile

```
docker build . -f base-deploy.Dockerfile -t tarscloud/base-deploy
docker push tarscloud/base-deploy
```

发布镜像内容其实已经包含编译镜像中了, 之所以要单独再制作发布镜像, 是因为有时候在CI/CD流程过程中, 编译和发布是两个流程, 甚至运行在不同的机器上了, 为了减少镜像大小, 所以特别制作了发布镜像!

## 工具

镜像内部说明:
- 内置了发布/部署工具, ```kubectl``` 以及 ```helm``` 工具, 以便操作K8S完成部署.
- 内置了exec-deploy.sh脚本, 一般是CICD流程中, 使用exec-deploy.sh来发布exec-build.sh生成的helm包.
- 注意编译镜像中, 调用exec-build.sh已经生成并tars服务的镜像到仓库, 并生成helm包, 发布镜像只需要调用exec-deploy.sh来发布即可!

[exec-deploy.sh的说明请参考](./exec-deploy.sh)

## 发布镜像的使用

发布镜像是需要K8S权限, 你可以将发布镜像部署在K8S上, 并给与K8S管理员权限, 也可以部署在K8S外部, 挂载K8S的配置启动
