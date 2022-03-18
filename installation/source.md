# 目录

> - [依赖环境](#chapter-1)
> - [Tars C++开发环境](#chapter-2)
> - [Tars 框架安装](#chapter-3)
> - [Tars-web 说明](#chapter-4)
> - [脚本说明](#chapter-5)
> - [建议部署方案](#chapter-6)

本安装文档描述在一台或多台服务器上安装搭建整个 Tars 框架的过程，目的是为了让用户对 Tars 框架的部署搭建、运行、测试等有个整体的认识。

如要用于线上环境，部署安装的原理是一样，不过需要更多考虑分布式系统下服务的部署需要有容错、容灾等的能力。

# 1. <span id="chapter-1"></span>依赖环境

| 软件            | 软件要求                                                       |
| --------------- | -------------------------------------------------------------- |
| linux 内核版本: | 2.6.18 及以上版本（操作系统依赖）                              |
| gcc 版本:       | 4.8.2 及以上版本、glibc-devel（c++语言框架依赖）               |
| bison 工具版本: | 2.5 及以上版本（c++语言框架依赖）                              |
| flex 工具版本:  | 2.5 及以上版本（c++语言框架依赖）                              |
| cmake 版本：    | 3.2 及以上版本（c++语言框架依赖）                              |
| mysql 版本:     | 5.6 及以上版本（框架运行依赖）                                 |
| nvm 版本：      | 0.35.1 及以上版本（web 管理系统依赖, 脚本安装过程中自动安装）  |
| node 版本：     | 12.13.0 及以上版本（web 管理系统依赖, 脚本安装过程中自动安装） |

运行服务器要求：安装 linux 系统的机器 or mac 机器

## 1.1. 编译包依赖下载安装介绍

源码编译过程需要安装:gcc, glibc, bison, flex, cmake, ncurses-devel zlib-devel 等

例如，在 Centos7 下，执行：

```
yum install glibc-devel gcc gcc-c++ bison flex cmake which psmisc ncurses-devel zlib-devel yum-utils psmisc telnet net-tools wget unzip
```

在 ubuntu 下执行:

```
sudo apt-get install build-essential bison flex cmake psmisc libncurses5-dev zlib1g-dev psmisc telnet net-tools wget unzip
```

在 mac 安装, 请先安装 brew(如何在 mac 上安装 brew, 请自行搜索)

```
brew install bison flex cmake
```

## 1.2. Mysql 安装

正式部署时, 如果你的 mysql 可以安装在其他机器上.

Tars 框架安装需要在 mysql 中读写数据, 因此需要安装 mysql, 如果你已经存在 mysql, 可以忽略该步骤.

安装 mysql 请参考[mysql 安装](mysql.md)

## 1.3. Mysql client 安装

tars>=2.1.0 可以跳过这一步

**注意请保证 mysql 在 PATH 环境变量的目录下**

```
which mysql
```

如果你本机安装了 mysql, 那么会自动安装 mysql 客户端, 这一步可以跳过.

如果你的 mysql 在其他机器, 那么本机安装 Tars 框架之前, 需要先安装 mysql 客户端(安装时需要探测 mysql 的连通性)

```
rpm -ivh https://repo.mysql.com/mysql57-community-release-el7.rpm
yum install -y mysql

```

# 2. <span id="chapter-2"></span>Tars C++开发环境(源码安装框架必备)

**源码安装框架才需要做这一步, 如果只是用 c++写服务, 只需要下载 tarscpp 代码即可**

下载 TarsFramework 源码

```
cd ${source_folder}
git clone https://github.com/TarsCloud/TarsFramework.git --recursive
```

然后进入 build 源码目录

```
cd TarsFramework
git submodule update --remote --recursive
cd build
cmake ..
make -j4
```

默认情况下, 编译 Tars 会自动下载 mysql-client 源码, 并编译 libmyqlclient.a

如果需要重新编译

```
cd build
make clean
make -j4
```

切换至 root 用户，创建安装目录

```
cd /usr/local
mkdir tars
mkdir app
```

如果在非 root 下执行, 则需要先创建目录, 并赋予权限, 比如以: ubuntu(当前用户为 ubuntu)为例

```sh
sudo mkdir -p /usr/local/tars
sudo mkdir -p /usr/local/app

sudo chown -R ubuntu:ubuntu /usr/local/tars
sudo chown -R ubuntu:ubuntu /usr/local/web

```

安装

```
cd build
make install
```

**默认的安装包路径:/usr/local/tars/cpp, 即编译完成的框架&安装脚本在这个目录**
**默认的安装完成后的路径为/usr/local/app, 即安装完成后的路径**

install 以后, 依赖的库(mysql 静态库)和头文件也会安装到该目录下(/usr/local/tars/cpp/thirdparty), 如果开启了 ssl, nghttp2 同理.

开启了 ssl, nghttp2 请参见相关文章

**如果你想调整安装目录(建议不要调整, 需要修改好几个的地方, 容易出错):**

```
**需要修改tarscpp/cmake/Common.cmake文件中的安装路径。**
**需要修改tarscpp/servant/makefile/makefile.tars文件中的TARS_PATH的路径**
**需要修改tarscpp/servant/makefile/tars-tools.cmake文件中的TARS_PATH的路径**
**需要修改tarscpp/servant/script/*.sh文件中的DEMO_PATH的路径**
```

# 3 <span id="chapter-3"></span>Tars 框架安装

## 3.1. 框架安装模式

**框架有两种模式:**

- centos/ubuntu/mac 一键部署, 安装过程中需要网络从外部下载资源
- tars-framework>=2.1.0 支持 mac 部署
- 制作成 docker 镜像来完成安装, 制作 docker 过程需要网络下载资源, 但是启动 docker 镜像不需要外网

**框架安装注意事项:**

- 安装过程中, 由于 tars-web 依赖 nodejs, 所以会自动下载 nodejs, npm, pm2 以及相关的依赖, 并设置好环境变量, 保证 nodejs 生效.
- nodejs 的版本目前默认下载的 v12.13.0
- 如果你本机装了低版本 nodejs, 最好提前卸载掉

**注意:需要完成 TarsFramework 的编译和安装**
**注意:框架依赖 mysql, 如果你使用 mysql8, 注意需要关闭 ssl 以及启用 mysql_native_password**

下载 tarsweb 并 copy 到/usr/local/tars/cpp/deploy 目录下(注意目录名是 web, 不要搞错!):

```
git clone https://github.com/TarsCloud/TarsWeb.git
mv TarsWeb web
cp -rf web /usr/local/tars/cpp/deploy/
```

例如, 这是/usr/local/tars/cpp/deploy 下的文件:

```
ubuntu@VM-0-14-ubuntu:/usr/local/tars/cpp/deploy$ ls -l
total 1030h
-rw-r--r--  1 root root  443392 Apr  3 17:22 busybox.exe
-rw-r--r--  1 root root    1922 Apr  3 17:22 centos7_base.repo
-rw-r--r--  1 root root    1395 Apr  3 17:22 Dockerfile
-rwxr-xr-x  1 root root    3260 Apr  4 11:31 docker-init.sh
-rwxr-xr-x  1 root root     319 Apr  3 22:13 docker.sh
drwxr-xr-x  7 root root    4096 Apr  3 17:57 framework
-rwxr-xr-x  1 root root    4537 Apr  4 11:31 linux-install.sh
-rwxr-xr-x  1 root root 9820288 Apr  3 22:16 mysql-tool
-rwxr-xr-x  1 root root     811 Apr  4 11:31 tar-server.sh
-rwxr-xr-x  1 root root   16449 Apr  3 17:22 tars-install.sh
-rwxr-xr-x  1 root root     320 Apr  4 11:31 tars-stop.sh
drwxr-xr-x  2 root root    4096 Apr  3 17:57 tools
drwxr-xr-x 12 root root    4096 Apr  3 21:07 web
-rwxr-xr-x  1 root root    3590 Apr  3 17:22 web-install.sh
-rwxr-xr-x  1 root root    1476 Apr  3 17:22 windows-install.sh
```

## 3.2. 框架部署说明

框架可以部署在单机或者多机上, 多机是一主多从模式, 通常一主一从足够了:

- 主节点只能有一台, 从节点可以多台
- 主节点默认会安装:tarsAdminRegistry, tarspatch, tarsweb, tarslog, tarsstat, tarsproperty, 这几个服务在从节点上不会安装
- tarslog 用于收集所有服务的远程日志, 建议单节点, 否则日志会分散在多机上
- 原则上 tarspatch, tarsweb 可以是多点, 如果部署成多点, 需要把/usr/local/app/patchs 目录做成多机间共享(可以通过 NFS), 否则无法正常发布服务
- 虽然 tarsAdminRegistry 上记录了正在发布服务的状态, 但是原则上也可以可以多节点, tarsweb 调用 tarsAdminRegistry 是 hash 调用
- 后续强烈建议把 tarslog 部署到大硬盘服务器上
- 实际使用中, 即使主从节点都挂了, 也不会影响框架上服务的正常运行, 只会影响发布
- 一键部署会自动安装好 web(自动下载 nodejs, npm, pm2 等相关依赖), 同时开启 web 权限

部署完成后会创建 5 个数据库，分别是 db_tars、db_tars_web、db_user_system、 tars_stat、tars_property。

其中 db_tars 是框架运行依赖的核心数据库，里面包括了服务部署信息、服务模版信息、服务配置信息等等；

db_tars_web 是 web 管理平台用到数据库

db_user_system 是 web 管理平台用到的权限管理数据库

tars_stat 是服务监控数据存储的数据库；

tars_property 是服务属性监控数据存储的数据库；

无论哪种安装方式, 如果成功安装, 都会看到类似如下输出:

```
 2019-10-31 11:06:13 INSTALL TARS SUCC: http://xxx.xxx.xxx.xxx:3000/ to open the tars web.
 2019-10-31 11:06:13 If in Docker, please check you host ip and port.
 2019-10-31 11:06:13 You can start tars web manual: cd /usr/local/app/web; npm run prd
```

打开你的浏览器输入: http://xxx.xxx.xxx.xxx:3000/ 如果顺利, 可以看到 web 管理平台

请参考[检查 web 的问题](web.md)中的检查 web 问题章节, 如果没有问题, 请检查机器防火墙

## 3.3. (centos/ubuntu/mac)一键部署

进入/usr/local/tars/cpp/deploy, 执行:

```
chmod a+x linux-install.sh
./linux-install.sh MYSQL_HOST MYSQL_PASSWORD INET REBUILD(false[default]/true) SLAVE(false[default]/true) MYSQL_USER MYSQL_PORT
```

MYSQL_HOST: mysql 数据库的 ip 地址

MYSQL_PASSWORD: mysql 数据库的 MYSQL_USER 的密码(注意密码不要有太特殊的字符, 例如!, 否则 shell 脚本识别有问题, 因为是特殊字符)

INET: 网卡的名称(ifconfig 可以看到, 比如 eth0), 表示框架绑定的本机 IP, 注意不能是 127.0.0.1

REBUILD: 是否重建数据库,通常为 false, 如果中间装出错, 希望重置数据库, 可以设置为 true

SLAVE: 是否是从节点

MYSQL_USER: mysql 用户, 默认是 root

MYSQL_PORT: mysql 端口

举例, 安装两台节点, 一台数据库(假设: 主[192.168.7.151], 从[192.168.7.152], mysql:[192.168.7.153])

主节点上执行(192.168.7.151)

```
chmod a+x linux-install.sh
./linux-install.sh 192.168.7.153 tars2015 eth0 false false root 3306
```

主节点执行完毕后, 从节点执行:

```
chmod a+x linux-install.sh
./linux-install.sh 192.168.7.153 tars2015 eth0 false true root 3306
```

执行过程中的错误参见屏幕输出, 如果出错可以重复执行(一般是下载资源出错)

注意:

- 脚本会自动根据传入的 MYSQL_USER 和 MYSQL_PASSWORD 来登录数据库，创建 TarsAdmin 账号和授权 Tars 相关数据库供框架使用
- 如果是 ubuntu, 需要 sudo linux-install.sh ...来执行
- 注意: 执行完毕以后, 可以检查 nodejs 环境变量是否生效: node --version
- 安装完成以后, 会在/etc/profile 下写入 nodejs 相关的环境变量
- 如果没生效, 手动执行: source /etc/profile, 如果是 ubuntu 请注意权限的问题

## 3.4. 制作成 docker

目标: 将框架制作成一个 docker, 部署时启动 docker 即可.

首先将 TarsWeb clone 到 TarsFramework 源码根目录, 然后直接在 TarsFramework 源码目录下, 执行:

```
git clone https://github.com/TarsCloud/TarsWeb.git web
#x64
sudo ./deploy/docker.sh v1 amd64
#arm64
sudo ./deploy/docker.sh v1 arm64
```

docker 制作完毕: tarscloud/framework:v1

```
docker ps
```

可以将 docker 发布到你的机器, 然后执行

```
docker run -d --net=host -e MYSQL_HOST=xxxxx -e MYSQL_ROOT_PASSWORD=xxxxx \
        -e MYSQL_USER=root -e MYSQL_PORT=3306 \
        -eREBUILD=false -eINET=enp3s0 -eSLAVE=false \
        -v/data/tars:/data/tars \
        -v/etc/localtime:/etc/localtime \
        tarscloud/framework:v1
```

MYSQL_IP: mysql 数据库的 ip 地址

MYSQL_ROOT_PASSWORD: mysql 数据库的 root 密码

INET: 网卡的名称(ifconfig 可以看到, 比如 eth0), 表示框架绑定本机 IP, 注意不能是 127.0.0.1

REBUILD: 是否重建数据库,通常为 false, 如果中间装出错, 希望重置数据库, 可以设置为 true

SLAVE: 是否是从节点

MYSQL_USER: mysql 用户, 默认是 root

MYSQL_PORT: mysql 端口

映射三个目录到宿主机

- -v/data/tars:/data/tars
  > - 包含了 tars 应用日志, tarsnode/data 目录(业务服务的运行包, 保证 docker 重启, 发布到 docker 内部的服务不会丢失)
  > - 如果是主机则还包含: web 日志, 发布包目录

**如果希望多节点部署, 则在不同机器上执行 docker run ...即可, 注意参数设置!**

**这里必须使用 --net=host, 表示 docker 和宿主机在相同网络**

## 3.5. mysql 权限问题

上述安装 mysql 默认都是需要 root 权限, 但是在某些场景不具备 root 用户或者 root 用户必须交互式输入密码的情况下(比如腾讯云 cdb), 你可以这样安装:

- 首先在 mysql 中创建用户(可能管理员分配给你的), 比如:admin
- admin 用户具备以下权限(重点是创建用户的权限):

```
GRANT, SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER, CREATE TABLESPACE
```

- 执行安装脚本

```
./linux-install.sh 192.168.7.153 tars2015 eth0 false true admin 3306

```

```
docker run -d --net=host -e MYSQL_HOST=xxxxx -e MYSQL_ROOT_PASSWORD=xxxxx \
        -eMYSQL_USER=admin -eMYSQL_PORT=3306 \
        -eREBUILD=false -eINET=enp3s0 -eSLAVE=false \
        -v/data/tars:/data/tars \
        -v/etc/localtime:/etc/localtime \
        tarscloud/framework:v1
```

**实际框架安装过程中, 会再创建一个用户, 并用这个用户来连接 db,可以参见 tars-install.sh 脚本**

## 3.6. 核心模块

无论是那种安装方式, 最终 Tars Framework 都是由几个核心模块组成的, 例如:

```
[root@VM-0-7-centos deploy]# ps -ef | grep app/tars | grep -v grep
root       368     1  0 09:20 pts/0    00:00:25 /usr/local/app/tars/tarsregistry/bin/tarsregistry --config=/usr/local/app/tars/tarsregistry/conf/tars.tarsregistry.config.conf
root      9245 32687  0 09:29 ?        00:00:13 /usr/local/app/tars/tarsstat/bin/tarsstat --config=/usr/local/app/tars/tarsnode/data/tars.tarsstat/conf/tars.tarsstat.config.conf
root     32585     1  0 09:20 pts/0    00:00:10 /usr/local/app/tars/tarsAdminRegistry/bin/tarsAdminRegistry --config=/usr/local/app/tars/tarsAdminRegistry/conf/tars.tarsAdminRegistry.config.conf
root     32588     1  0 09:20 pts/0    00:00:20 /usr/local/app/tars/tarslog/bin/tarslog --config=/usr/local/app/tars/tarslog/conf/tars.tarslog.config.conf
root     32630     1  0 09:20 pts/0    00:00:07 /usr/local/app/tars/tarspatch/bin/tarspatch --config=/usr/local/app/tars/tarspatch/conf/tars.tarspatch.config.conf
root     32653     1  0 09:20 pts/0    00:00:14 /usr/local/app/tars/tarsconfig/bin/tarsconfig --config=/usr/local/app/tars/tarsconfig/conf/tars.tarsconfig.config.conf
root     32687     1  0 09:20 ?        00:00:22 /usr/local/app/tars/tarsnode/bin/tarsnode --locator=tars.tarsregistry.QueryObj@tcp -h 172.16.0.7 -p 17890 --config=/usr/local/app/tars/tarsnode/conf/tars.tarsnode.config.conf
root     32695     1  0 09:20 pts/0    00:00:09 /usr/local/app/tars/tarsnotify/bin/tarsnotify --config=/usr/local/app/tars/tarsnotify/conf/tars.tarsnotify.config.conf
root     32698     1  0 09:20 pts/0    00:00:14 /usr/local/app/tars/tarsproperty/bin/tarsproperty --config=/usr/local/app/tars/tarsproperty/conf/tars.tarsproperty.config.conf
root     32709     1  0 09:20 pts/0    00:00:12 /usr/local/app/tars/tarsqueryproperty/bin/tarsqueryproperty --config=/usr/local/app/tars/tarsqueryproperty/conf/tars.tarsqueryproperty.config.conf
root     32718     1  0 09:20 pts/0    00:00:12 /usr/local/app/tars/tarsquerystat/bin/tarsquerystat --config=/usr/local/app/tars/tarsquerystat/conf/tars.tarsquerystat.config.conf
```

- 对于主机节点 tarsnode tars-web 必须活着, 其他 tars 服务会被 tarsnode 自动拉起
- 对于从机节点 tarsnode 必须活着, 其他 tars 服务会被 tarsnode 拉起
- tars-web 是 nodejs 实现的服务, 由两个服务组成, 具体参见后面章节
- 为了保证核心服务是启动的, 可以通过 monitor.sh 来控制, 在 crontab 中配置

add to contab:

```
* * * * * /usr/local/app/tars/check.sh
```

- web 的自启需要自己添加

# 4. <span id="chapter-4"></span>Tars-web 说明

## 4.1 模块说明

Tars Framework 部署好以后, 在主机节点上会安装 tars-web(从机节点不会安装), tars-web 采用 nodejs 实现, 由两个服务组成.

### tars-web 模块说明

```
pm2 list
```

web(<v2.4.7) 输出如下:

```
[root@8a17fab70409 data]# pm2 list
┌────┬─────────────────────────┬─────────┬─────────┬──────────┬────────┬──────┬──────────┬──────────┬──────────┬──────────┬──────────┐
│ id │ name                    │ version │ mode    │ pid      │ uptime │ ↺    │ status   │ cpu      │ mem      │ user     │ watching │
├────┼─────────────────────────┼─────────┼─────────┼──────────┼────────┼──────┼──────────┼──────────┼──────────┼──────────┼──────────┤
│ 0  │ tars-node-web           │ 0.2.0   │ fork    │ 1602     │ 2m     │ 0    │ online   │ 0.1%     │ 65.1mb   │ root     │ disabled │
│ 1  │ tars-user-system        │ 0.1.0   │ fork    │ 1641     │ 2m     │ 0    │ online   │ 0.1%     │ 60.1mb   │ root     │ disabled │
└────┴─────────────────────────┴─────────┴─────────┴──────────┴────────┴──────┴──────────┴──────────┴──────────┴──────────┴──────────┘
```

web(>=v2.4.7) 输出如下:

```
[root@8a17fab70409 data]# pm2 list
┌────┬─────────────────────────┬─────────┬─────────┬──────────┬────────┬──────┬──────────┬──────────┬──────────┬──────────┬──────────┐
│ id │ name                    │ version │ mode    │ pid      │ uptime │ ↺    │ status   │ cpu      │ mem      │ user     │ watching │
├────┼─────────────────────────┼─────────┼─────────┼──────────┼────────┼──────┼──────────┼──────────┼──────────┼──────────┼──────────┤
│ 0  │ tars-node-web           │ 2.4.7   │ fork    │ 1602     │ 2m     │ 0    │ online   │ 0.1%     │ 65.1mb   │ root     │ disabled │
└────┴─────────────────────────┴─────────┴─────────┴──────────┴────────┴──────┴──────────┴──────────┴──────────┴──────────┴──────────┘
```

**如果找不到 pm2, 一般是环境变量没生效, 请先执行: source /etc/profile, 安装过程中会写这个文件**

ubuntu 下由于权限问题, 如果执行 pm2 出错(环境变量没生效):

```
sudo -s source /etc/profile
sudo chown ubuntu:ubuntu /home/ubuntu/.pm2/rpc.sock /home/ubuntu/.pm2/pub.sock
pm2 list
```

### tars-web < v2.4.7

**tars-web 由两个模块组成**

- tars-node-web: tars-web 主页面服务, 默认绑定 3000 端口, 源码对应 web 目录
- tars-user-system: 权限管理服务, 负责管理所有相关的权限, 默认绑定 3001 端口, 源码对应 web/demo 目录

tars-node-web 调用 tars-user-system 来完成相关的权限验证

**web 采用 nodejs+vue 来实现, 最终的安装运行目录如下:**

```
/usr/local/app/web
```

如果 pm2 list 中查看模块启动不了, 可以进入改目录定位问题:

```
cd /usr/local/app/web/demo; npm run start
cd /usr/local/app/web; npm run start
```

npm run start 启动服务, 可以观察控制台的输出, 如果有问题, 会有提示.

**正式运行建议: pm2 start tars-node-web; pm2 start tars-user-system**

### tars-web >= v2.4.7

**tars-web 由一个模块组成**

- tars-node-web: tars-web 主页面服务, 默认绑定 3000 端口, 源码对应 web 目录

**web 采用 nodejs+vue 来实现, 最终的安装运行目录如下:**

```
/usr/local/app/web
```

如果 pm2 list 中查看模块启动不了, 可以进入改目录定位问题:

```
cd /usr/local/app/web; npm run start
```

npm run start 启动服务, 可以观察控制台的输出, 如果有问题, 会有提示.

\*\*正式运行建议: pm2 start tars-node-web

### 自动启动 Tars

注意重启机器后, pm2 模块会丢失, 请将以下语句加入到开机启动中(比如: /etc/rc.local):

```
/usr/local/app/tars/tars-start.sh
```

如果安装完成后 web 页面打不开, 请参考[web](web.md), 检查问题章节, 定位问题

# 5. <span id="chapter-5"></span>脚本说明

框架自带了脚本, 可以控制服务的起停, 比如:

- 启动框架: /usr/local/app/tars/tars-start.sh
- 停止框架: /usr/local/app/tars/tars-stop.sh
- 控制某个组件:
  > - - /usr/local/app/tars/xxxx/util/start.sh
  > - - /usr/local/app/tars/xxxx/util/stop.sh

Note:

- 框架核心服务中, tarsnode 必须活着, 他会监控其他组件的死活, 一旦其他组件 crash, 会自动拉起
- web 组件的是有 pm2 来监控的
- 部署了框架的机器重启后, 可以执行 `/usr/local/app/tars/tars-start.sh` 来重启所有服务
- tarsnode 的监控可以在 crontab 中定时执行: `/usr/local/app/tars/check.sh`
- 只部署了 tarsnode 的节点机器, 只需要监控 tarsnode 即可

# 6. <span id="chapter-6"></span>建议部署方案

虽然本章节是介绍的源码部署 tars 框架的方案, 但是实际使用上, 不建议源码部署, 会导致升级维护比较麻烦, 下面介绍实际使用过程重点部署方案:

- 采用 docker 化部署框架(参考相关文档), 部署两台机器, 主从模式, 并启用--net=host 模式
- 节点机采用物理化部署方案, tarsnode 连接到框架即可
- 升级框架的情况下, 可以升级 docker 即可, 停止老的 docker, 启动新 docker, 注意参与不要选择 rebuild db!!!
- 在 web 上可以远程升级 tarsnode, 正常情况下, tarsnode 一般可以不用升级, 除非有重大功能优化(这里未来不排除会做成自动升级!)
- tarslog 需要大硬盘机器, 部署完框架以后, 建议将框架的 tarslog 服务扩容到其他大硬盘的节点机上
- 如果你使用 windows, 你可以考虑框架部署使用 docker, 节点机使用 windows 部署 tarsnode

如果你对 k8s 比较熟悉, 也可以将 Tars 部署在 k8s 上, [请参见 k8s 部署文档](k8s-docker-1.md)
