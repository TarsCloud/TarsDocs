# 源码部署

TarsK8S 以 Helm Chart 的形式发布和部署. 通常情况下, 您可以使用项目预先构建的 Helm Chart, 并参照 "安装 Helm Char"  安装.

您也可以参照 "构建 Helm Chart" 自行生成 Helm Chart, 然后参照 "安装 Helm Chart" 安装

## 构建 Helm Chart

### 编译依赖

在 构建前,请安装好依赖程序或依赖库

+ helm3

+ docker ( >=19.03)
+ flex
+ bison
+ make
+ cmake
+ libzlib

### 编译源码

 ```shell
 git submodule update --init --recursive
 ./buildBinary
 ```

上述命令执行成功后,会在 build/files/binary 目录生成可执行程序文件

### 生成 Helm Chart

```
./buildHelm.sh <DOCKER_REPOSITORY> <DOCKER_REGISTRY_USER> <DOCKER_REGISTRY_PASSWORD> <TAG> <DOCKER_REGISTRY>
```

参数说明:

- DOCKER_REPOSITORY: 仓库名称, 比如 tarscloud

- DOCKER_REGISTRY_USER: 访问仓库的用户名

- DOCKER_REGISTRY_PASSWORD: 访问仓库的密码

- TAG: 当前编译的版本号, herm chart 的版本号

- DOCKER_REGISTRY: 仓库 url 地址

在命令执行完毕后,会在 install 目录生成 tarscontroller_$(TAG).tgz, tarsframewro_$(TAG).tgz  两个 Helm Char

您可以参考 [<<快速安装>>](quick-deploy.md) 文档安装 Helm Chart
