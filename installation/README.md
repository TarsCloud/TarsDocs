# 目录

> - [部署简介](#chapter-1)
> - [源码编译方式部署](#chapter-2)
> - [框架 Docker 化部署](#chapter-3)
> - [K8s Docker 部署](#chapter-4)
> - [K8s 融合部署](#chapter-5)
> - [推荐的部署方案](#chapter-6)

在了解部署之前, 请务必整体了解 Tars 的[设计](https://github.com/TarsCloud/TarsDocs/blob/master/base/tars-intro.md)

# 1 <span id="chapter-1"></span>部署简介

需要了解 Tars 部署基础知识:

Tars 由 mysql+框架服务+业务服务组成:

- mysql, 用于存储各种服务信息
- 框架服务是由多个 Tars 实现好的服务(c++实现), web 管理平台(nodejs)构成
- 业务服务是由开发团队根据产品需求自己实现的服务, 这些服务可以由 c++, java, go, nodejs, php 等语言实现

Tars 完成部署后, 从服务器角度, 它由以下几部分组成:

- mysql 集群(主从配置或者集群配置): 通常主从配置即可, 正常情况, 即使 mysql 挂了, 也不会影响业务服务的运行(但是会影响部署和发布)
- 框架服务: 是由多个 c++实现的 tars 服务 + web 管理平台组成, 通常部署在两台机器上, 其中一台会多部署 web 管理平台, tarspatch, tarsAdminRegistry, 运行过程中框架服务器都挂了, 也不会影响业务服务的运行(影响部署和发布)
- 多台节点服务器: 从 1 台至上万台, 每台节点服务器上都必须部署一个 tarsnode 进程, tarsnode 要连接到框架服务上(连接 tarsregistry)
- 业务自己实现的服务通过 web 管理平台发布到这些节点服务器上

Tars 的部署工作包括:

- mysql 的安装
- 框架服务部署(一般是两台服务器)
- 节点服务器部署(部署 tarsnode), 一般可以通过 web 管理平台远程部署节点服务器

**注意:如果节点服务器上运行了不同语言实现的业务服务, 那么节点服务器的运行环境需要自己安装, 比如安装 jdk, node 等**

Tars 部署方式有以下几种:

- 源码编译部署
- Docker 部署
- K8s Docker 部署
- K8s 融合部署

根据你的需要选择一种即可.

**建议部署都在 linux 上完成, 即使是 docker 安装, 推荐使用 centos7**

# 2 <span id="chapter-2"></span>源码编译方式部署

源码部署方式是了解 Tars 非常好的途径, 主要步骤如下:

- 安装 mysql
- 下载源码(TarsFramework)
- 编译源码
- 下载管理平台(TarsWeb)
- 通过一键部署脚本完成部署

源码部署细节方式请参见 [Install](source.md) 中的源码部署部分, 强烈建议阅读.

源码部署方式 Tars 框架服务都以独立的进程模式运行在服务器上, 可以手工启停, 每个服务都可以独立更新.

**注意:该方式建议对 Tars 比较熟悉的团队使用**

# 3 <span id="chapter-3"></spanS 框架 Docker 化部署

源码部署虽然可以独立更新, 但是也带来了不便, 毕竟每个模块更新还是比较麻烦的, 同时模块版本可能还有依赖, 更新维护就更麻烦了.

在这种情况下, 可以选择框架 Docker 化部署: 简单的说, 将框架服务以及 web 都 docker 容器化, 启动容器则框架服务都自动启动, 更新时也整体更新.

Tars 框架的 Docker 制作分三种模式:

- 源码制作, 参见 [Install](source.md) docker 制作部分.
- 自动制作(不包含运行环境), 参见 [docker](docker.md) tarscloud/framework 部分
- 自动制作(包含运行环境), 参见 [docker](docker.md) tarscloud/tars 部分

两者区别如下:

- 源码制作

  > - 需要手工下载源码, 一步一步制作 docker
  > - 镜像都采用了国内镜像, 比较容易制作成功
  > - 制作的镜像, 不包含运行环境, 即 java, php 等需要运行时的环境的业务服务, 无法发布到 docker 镜像内部

- 自动制作
  > - 一键运行命令, 既可以完成镜像的制作
  > - 采用的系统默认的源, 可能连接海外源, 较慢(但是在 github 上 action 制作的时候比较快)

这种模式下, 几点注意:

- 框架 framework 模式是运行在 docker 容器中的, 因此更新起来非常方便, 整体更新即可.
- 节点服务既可以使用 tarsnode 容器(服务发布后运行在容器里面), 也可以在虚拟机(物理上)安装 tarsnode 进程, 这样服务发布后直接运行在虚拟机(物理机上).
- 从 tarscloud/framework:v3.0.5 开始, 也支持业务服务运行在自己独立的容器中, 这种情况下:
  > - 需要在 web 平台配置服务以容器运行
  > - 节点服务需要安装 node, 如果你的节点服务也是一个 tarsnode 容器运行, 则启动时映射宿主机的 docker socket 即可.

# 4 <span id="chapter-4"></span>K8S Docker 部署

框架 Docker 部署, 虽然极大方便了框架的部署, 但是对于使用 k8s 来管理容器的团队而言, 仍然有很多工作要做.

因此这里, 提供一种 k8s 上部署 Tars 的建议:

- 将框架服务容器化, 使用 docker: tarscloud/framework 或 tarscloud/tars
- 将 tarsnode 节点也容器化, 使用 docker: tarscloud/tars-node
- tars 框架和 tarsnode 节点都作为 pod 部署在 k8s 上, pod 运行的容器当成一台虚拟机
- 通过 tars web 发布服务到这些容器中运行

**注意:docker 内部提供了各种语言的运行环境, 可以把业务服务发布到 docker 内部运行(相当于把 docker 当成虚拟机)**

虽然这种方式并没有把每个服务都做一个 pod, 独立运行在 k8s 上, 但是也基本解决了 tars 和 k8s 结合的问题, 虽然不优雅, 当整体可用.

这种模式下需要解决的最核心问题是: 框架服务和 tarsnode 都作为 pod, 可能会死掉并产生漂移, 导致 ip 会变化, 如何解决?

建议解决方式如下:

- 所有 pod 可以采用 stateful headless 模式部署, 每个都有独立的域名, 比如 tars-0, tars-1, tarsnode-1, tarsnode-2 等...
- 业务服务部署时, 业务节点都采用域名(不要采用 ip)

Tars 团队已经制作了一个[一键部署 k8s 的方案](k8s-docker-1.md)

# 5 <span id="chapter-5"></span>K8S 融合部署

上面 K8s 的 Docker 部署, 虽然把 Tars 部署在 k8s 上运行起来, 但是实际发布, 扩容并没有使用 K8s, 只是把 k8s 当成了一个容器管理平台了.

https://github.com/TarsCloud/K8SFramework 提供了一种 Tars 和 K8S 深度融合的方案, 使用 k8s 部署的情况强烈推荐这种模式!

[具体请参看](../k8s/README.md)

# 6 <span id="chapter-6"></span>推荐部署方案

建议可以把集中方式都用一用, 以便你数据框架的基本部署, 当然如果你对 K8S 不熟悉, 那么可以不使用 K8S 方式部署, 总体而言 K8S 的学习成本和曲线还是比较高, 对中小公司未必是最佳选择.

tars 的部署方式非常灵活和多样化, 可以根据自己公司的实际状况来选择, 我们对于部署方案的推荐如下:

- 如果你的公司规模较大, 且熟悉 K8S 那么推荐使用 [K8S 融合部署](k8s-docker-3.md)
- 如果你的公司规模较小, 或者不熟悉 K8S, 那么推荐一般的[Docker 部署方式](docker.md):
  > - 框架使用 docker 方式部署: tarscloud/framework
  > - 节点也使用 docker 部署: tarscloud/tars-node
  > - 这种方式下, 可以根据需要启用容器化运行(这样每个业务服务也运行在独立的容器中)
