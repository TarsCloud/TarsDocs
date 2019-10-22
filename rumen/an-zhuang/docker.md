# Docker

## CentOS

### Docker 安装

以 CentOS 为例，

```text
sudo su
yum install -y yum-utils device-mapper-persistent-data lvm2
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum install -y docker-ce 
systemctl start docker
systemctl enable docker
```

安装后，可以用以下命令来检验是否已经安装成功：

```text
docker version
```

### 获取镜像

请把 ${your\_machine\_ip} 和 ${your\_mysql\_ip} 替换成你机器的IP地址和数据库的IP地址，然后运行以下命令：

```text
docker pull mysql:5.6
docker pull tarsdocker/tars
docker run --name mysql -e MYSQL_ROOT_PASSWORD='root@appinside' -d -p 3306:3306 -v /data/mysql-data:/var/lib/mysql mysql:5.6
docker run -d -it --name=tars --link mysql -e MOUNT_DATA=true -e DBIP=${your_mysql_ip} -e DBPort=3306 -e DBUser=root -e DBPassword=root@appinside -p 3000:3000 -v /data:/data tarsdocker/tars
```

**注意：**你可以用以下命令检测 ${your\_mysql\_ip} 是否替换成功：

```text
docker inspect --format='{{.NetworkSettings.IPAddress}}' mysql
```

然后访问 `http://${your_machine_ip}:3000` ，开始你的Tars之旅！

### 参数介绍

MYSQL\_ROOT\_PASSWORD: 数据库的 root 密码

DBIP: 数据库主机IP

DBPort: 数据库端口号

DBUser: 数据库管理员名

DBPassword: 数据库管理员密码

### 扩充 tarsnode 节点

请用你的 tarsregistry IP 替换 ${registry\_ip}，然后运行以下命令：

```text
docker pull tarsdocker/tarsnode
docker run -d -it --name tarsnode -e MASTER=${registry_ip} -v /data:/data tarsdocker/tarsnode
```

注意：你可以用以下命令检查 ${registry\_ip} ：

```text
docker inspect --format='{{.NetworkSettings.IPAddress}}' tars
```

## Windows

该部分假定你的工作环境为 **Windows**，因为Windows下的docker命令行环境会把C:盘、D:盘等盘符映射为 `/c/`、`/d/` 这样的目录形式，所以在文档中会直接使用 `/c/Users/` 这样的写法来描述C:盘的用户目录。

### MySQL <a id="mysql"></a>

本镜像是Tars的docker版本，**未安装mysql**，可以使用官方mysql镜像（5.6）：

```text
docker run --name mysql -e MYSQL_ROOT_PASSWORD=password -d -p 3306:3306 -v /c/Users/<ACCOUNT>/mysql_data:/var/lib/mysql mysql:5.6 --innodb_use_native_aio=0
```

注意上面的运行命令添加了 `--innodb_use_native_aio=0` ，因为mysql的aio对windows文件系统不支持

如果要使用 **5.7** 版本的mysql，需要再添加 `--sql_mode=NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION` 参数，因为不支持全零的date字段值（ [https://dev.mysql.com/doc/refman/5.7/en/sql-mode.html\#sqlmode\_no\_zero\_date](https://dev.mysql.com/doc/refman/5.7/en/sql-mode.html#sqlmode_no_zero_date) ）

```text
docker run --name mysql -e MYSQL_ROOT_PASSWORD=password -d -p 3306:3306 -v /c/Users/<ACCOUNT>/mysql_data:/var/lib/mysql mysql:5.7 --sql_mode=NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION --innodb_use_native_aio=0
```

如果使用 **8.0** 版本的mysql，则直接设定 `--sql_mode=''`，即禁止掉缺省的严格模式，（参考 [https://dev.mysql.com/doc/refman/8.0/en/sql-mode.html](https://dev.mysql.com/doc/refman/8.0/en/sql-mode.html) ）

```text
docker run --name mysql -e MYSQL_ROOT_PASSWORD=password -d -p 3306:3306 -v /c/Users/<ACCOUNT>/mysql_data:/var/lib/mysql mysql:8 --sql_mode='' --innodb_use_native_aio=0
```

或者你也可以挂载使用一个自定义的 my.cnf 来添加上述参数。

### 镜像 <a id="_2"></a>

docker镜像已经由docker hub自动构建：[https://hub.docker.com/r/tarscloud/tars/](https://hub.docker.com/r/tarscloud/tars/) 或 [https://hub.docker.com/r/tangramor/docker-tars/](https://hub.docker.com/r/tangramor/docker-tars/) ，使用下面命令即可获取（注意替换 `<tag>` ）：

```text
docker pull tarscloud/tars:<tag>
```

* tag 为 **latest** 的镜像支持C++服务端，包含 CentOS7 的标准C++运行环境；
* tag 为 **php** 的镜像支持PHP服务端，包含了 php 7.2 环境和 swoole、phptars 扩展；
* tag 为 **java** 的镜像支持Java服务端，包含 JDK 10.0.2 以及 maven 等支持；
* tag 为 **go** 的镜像支持Go语言服务端，包含 Golang 1.9.4；
* tag 为 **nodejs** 的镜像支持Nodejs服务端，包含 nodejs 8.11.3；
* tag 为 **dev** 的镜像包含了C++、PHP、Java、Go和Nodejs的服务端开发支持，用于开发，上述其它镜像则 **不包含** make等开发工具以减小镜像体积。

|  |  |
| :--- | :--- |
| [![](https://images.microbadger.com/badges/version/tarscloud/tars.svg)](https://microbadger.com/images/tarscloud/tars) [![](https://images.microbadger.com/badges/image/tarscloud/tars.svg)](https://microbadger.com/images/tarscloud/tars) | [![](https://images.microbadger.com/badges/version/tarscloud/tars:php.svg)](https://microbadger.com/images/tarscloud/tars:php) [![](https://images.microbadger.com/badges/image/tarscloud/tars:php.svg)](https://microbadger.com/images/tarscloud/tars:php) |
| [![](https://images.microbadger.com/badges/version/tarscloud/tars:nodejs.svg)](https://microbadger.com/images/tarscloud/tars:nodejs) [![](https://images.microbadger.com/badges/image/tarscloud/tars:nodejs.svg)](https://microbadger.com/images/tarscloud/tars:nodejs) | [![](https://images.microbadger.com/badges/version/tarscloud/tars:java.svg)](https://microbadger.com/images/tarscloud/tars:java) [![](https://images.microbadger.com/badges/image/tarscloud/tars:java.svg)](https://microbadger.com/images/tarscloud/tars:java) |
| [![](https://images.microbadger.com/badges/version/tarscloud/tars:go.svg)](https://microbadger.com/images/tarscloud/tars:go) [![](https://images.microbadger.com/badges/image/tarscloud/tars:go.svg)](https://microbadger.com/images/tarscloud/tars:go) | [![](https://images.microbadger.com/badges/version/tarscloud/tars:dev.svg)](https://microbadger.com/images/tarscloud/tars:dev) [![](https://images.microbadger.com/badges/image/tarscloud/tars:dev.svg)](https://microbadger.com/images/tarscloud/tars:dev) |

**tars-node** 之下是只部署 tarsnode 服务的节点镜像脚本，使用下面命令即可获取：

```text
docker pull tarscloud/tars-node:<tag>
```

|  |  |
| :--- | :--- |
| [![](https://images.microbadger.com/badges/version/tarscloud/tars-node.svg)](https://microbadger.com/images/tarscloud/tars-node) [![](https://images.microbadger.com/badges/image/tarscloud/tars-node.svg)](https://microbadger.com/images/tarscloud/tars-node) | [![](https://images.microbadger.com/badges/version/tarscloud/tars-node:php.svg)](https://microbadger.com/images/tarscloud/tars-node:php) [![](https://images.microbadger.com/badges/image/tarscloud/tars-node:php.svg)](https://microbadger.com/images/tarscloud/tars-node:php) |
| [![](https://images.microbadger.com/badges/version/tarscloud/tars-node:nodejs.svg)](https://microbadger.com/images/tarscloud/tars-node:nodejs) [![](https://images.microbadger.com/badges/image/tarscloud/tars-node:nodejs.svg)](https://microbadger.com/images/tarscloud/tars-node:nodejs) | [![](https://images.microbadger.com/badges/version/tarscloud/tars-node:java.svg)](https://microbadger.com/images/tarscloud/tars-node:java) [![](https://images.microbadger.com/badges/image/tarscloud/tars-node:java.svg)](https://microbadger.com/images/tarscloud/tars-node:java) |
| [![](https://images.microbadger.com/badges/version/tarscloud/tars-node:go.svg)](https://microbadger.com/images/tarscloud/tars-node:go) [![](https://images.microbadger.com/badges/image/tarscloud/tars-node:go.svg)](https://microbadger.com/images/tarscloud/tars-node:go) | [![](https://images.microbadger.com/badges/version/tarscloud/tars-node:dev.svg)](https://microbadger.com/images/tarscloud/tars-node:dev) [![](https://images.microbadger.com/badges/image/tarscloud/tars-node:dev.svg)](https://microbadger.com/images/tarscloud/tars-node:dev) |

#### 注意： <a id="_3"></a>

镜像使用的是官方Tars的源码编译构建的，容器启动后，还会有一个自动化的安装过程，因为原版的Tars代码里设置是需要修改的，容器必须根据启动后获得的IP、环境变量等信息修改设置文件，所以会花费一定的时间。可以进入容器运行 `ps -ef` 命令查看进程信息来判断系统是否已经启动完成。

### 环境变量 <a id="_4"></a>

#### TZ <a id="tz"></a>

时区设置，缺省为 `Asia/Shanghai` 。

#### DBIP, DBPort, DBUser, DBPassword <a id="dbip_dbport_dbuser_dbpassword"></a>

在运行容器时需要指定数据库的 **环境变量**，例如：

```text
DBIP mysql
DBPort 3306
DBUser root
DBPassword password
```

#### DBTarsPass <a id="dbtarspass"></a>

因为Tars的源码里面直接设置了mysql数据库里tars用户的密码，所以为了安全起见，可以通过设定此 **环境变量** `DBTarsPass` 来让安装脚本替换掉缺省的tars数据库用户密码。

#### MOUNT\_DATA <a id="mount_data"></a>

如果是在 **Linux** 或者 **Mac** 上运行，可以设定 **环境变量** `MOUNT_DATA` 为 `true` 。此选项用于将Tars的系统进程的数据目录挂载到 /data 目录之下（一般把外部存储卷挂载为 /data 目录），这样即使重新创建容器，只要环境变量一致（数据库也没变化），那么之前的部署就不会丢失。这符合容器是无状态的原则。可惜在 **Windows** 下由于[文件系统与虚拟机共享文件夹的权限问题](https://discuss.elastic.co/t/filebeat-docker-running-on-windows-not-allowing-application-to-rotate-the-log/89616/11)，我们 **不能** 使用这个选项。

#### INET\_NAME <a id="inet_name"></a>

如果想要把docker内部服务直接暴露到宿主机，可以在运行docker时使用 `--net=host` 选项（docker缺省使用的是bridge桥接模式），这时我们需要确定宿主机的网卡名称，如果不是 `eth0`，那么需要设定 **环境变量** `INET_NAME` 的值为宿主机网卡名称，例如 `--env INET_NAME=ens160`。这种方式启动docker容器后，可以在宿主机使用 `netstat -anop |grep '8080\|10000\|10001' |grep LISTEN` 来查看端口是否被成功监听。

#### MASTER <a id="master"></a>

节点服务器需要把自己注册到主节点master，这时候需要将tarsnode的配置修改为指向master节点IP或者hostname，此 **环境变量** `MASTER` 用于 **tars-node** 镜像，在运行此镜像容器前需要确定master节点IP或主机名hostname。

run\_docker\_tars.sh 里的命令如下，请自己修改：

```text
docker run -d -it --name tars --link mysql --env MOUNT_DATA=false --env DBIP=mysql --env DBPort=3306 --env DBUser=root --env DBPassword=PASS -p 8080:8080 -v /c/Users/<ACCOUNT>/tars_data:/data tarscloud/tars
```

#### 框架普通基础服务 <a id="_5"></a>

另外安装脚本把构建成功的 tarslog.tgz、tarsnotify.tgz、tarsproperty.tgz、tarsqueryproperty.tgz、tarsquerystat.tgz 和 tarsstat.tgz 都放到了 `/c/Users/<ACCOUNT>/tars_data/` 目录之下，镜像本身已经自动安装了这些服务。你也可以参考Tars官方文档的 [安装框架普通基础服务](https://github.com/TarsCloud/Tars/blob/master/Install.zh.md#44-%E5%AE%89%E8%A3%85%E6%A1%86%E6%9E%B6%E6%99%AE%E9%80%9A%E5%9F%BA%E7%A1%80%E6%9C%8D%E5%8A%A1) 来了解这些服务。

### 自己构建镜像 <a id="_6"></a>

镜像构建命令：`docker build -t tars .`

[tars-node](https://github.com/TarsDocker/tars-node) 镜像构建，请检出 tars-node 项目后执行命令：

```text
git clone https://github.com/TarsDocker/tars-node.git
cd tars-node
docker build -t tars-node -f Dockerfile .
```

### 开发方式 <a id="_7"></a>

使用docker镜像进行Tars相关的开发就方便很多了，我的做法是把项目放置在被挂载到镜像 /data 目录的本地目录下，例如 `/c/Users/<ACCOUNT>/tars_data` 。在本地使用编辑器或IDE对项目文件进行开发，然后开启命令行：`docker exec -it tars bash` 进入Tars环境进行编译或测试。  


**注意：安装后为了提供更好的安全防护，强烈建议开启[用户体系鉴权登陆模块](https://github.com/TarsCloud/TarsWeb/blob/master/docs/TARS%20%E7%94%A8%E6%88%B7%E4%BD%93%E7%B3%BB%E6%A8%A1%E5%9D%97%2B%E8%B5%84%E6%BA%90%E6%A8%A1%E5%9D%97%E4%BD%BF%E7%94%A8%E6%8C%87%E5%BC%95.md)。**

