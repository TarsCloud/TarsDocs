
## 说明

为了方便业务制作tars服务的docker镜像, 特制作了基础编译镜像, 在编译镜像内部, 可以完成tars服务的编译以及制作docker.

制作编译镜像的docker如下:

Dockerfile:
- base-compiler.Dockerfile

```
docker build . -f base-compiler.Dockerfile -t tarscloud/base-compiler:master --build-arg master
docker push tarscloud/base-compiler:master
```

**注意base-compiler镜像内部的tarscpp/php/go等各语言版本都是最新版本, 使用的时候请注意!**

## 工具

镜像内部说明:
- 内置了java, nodejs, php, cpp, go的编译环境
- 内置了发布/部署工具, ```kubectl``` 以及 ```helm``` 工具, 以便操作K8S完成部署.
- 内置了官方的helm包模板
- 内置了exec-build.sh脚本, 方便你部署生成tars服务的docker

**编译镜像内部其实涵盖了部署镜像, 但是有时候编译和部署在两个镜像中完成, 这样单独制作了部署镜像, 方便部署**

[exec-deploy.sh的说明请参考](./exec-build.sh)
