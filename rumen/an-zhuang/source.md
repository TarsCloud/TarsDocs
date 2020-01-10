# 目录
> * [依赖环境](#chapter-1)
> * [Tars C++开发环境](#chapter-2)
> * [Tars框架安装](#chapter-3)
> * [Tars-web说明](#chapter-4)

本安装文档描述在一台或多台服务器上安装搭建整个Tars框架的过程，目的是为了让用户对Tars框架的部署搭建、运行、测试等有个整体的认识。

如要用于线上环境，部署安装的原理是一样，不过需要更多考虑分布式系统下服务的部署需要有容错、容灾等的能力。

# 1. <a id="chapter-1"></a>依赖环境

软件 |软件要求
------|--------
linux内核版本:      |	2.6.18及以上版本（操作系统依赖）
gcc版本:          	|   4.8.2及以上版本、glibc-devel（c++语言框架依赖）
bison工具版本:      |	2.5及以上版本（c++语言框架依赖）
flex工具版本:       |	2.5及以上版本（c++语言框架依赖）
cmake版本：       	|   2.8.8及以上版本（c++语言框架依赖）
mysql版本:          |   4.1.17及以上版本（框架运行依赖）
rapidjson版本:      |   1.0.2版本（c++语言框架依赖）
nvm版本：           |   0.35.1及以上版本（web管理系统依赖, 脚本安装过程中自动安装）
node版本：          |   12.13.0及以上版本（web管理系统依赖, 脚本安装过程中自动安装）

运行服务器要求：1台普通安装linux系统的机器即可。

## 1.1. 编译包依赖下载安装介绍

源码编译过程需要安装:gcc, glibc, bison, flex, cmake

例如，在Centos7下，执行：
```
yum install glibc-devel gcc gcc-c++ bison flex cmake which
```

## 1.2. Mysql依赖库安装

Tars代码编译需要依赖mysql头文件和静态库, 依赖路径如下:

- 头文件: /usr/local/mysql/include
- 库路径: /usr/local/mysql/lib

如果系统已经存在mysql头文件和静态库, 则可跳过次步骤, 否则建议: 

```
rpm -ivh https://repo.mysql.com/mysql57-community-release-el7.rpm
yum install -y mysql-devel 
mkdir -p /usr/local/mysql && ln -s /usr/lib64/mysql /usr/local/mysql/lib && ln -s /usr/include/mysql /usr/local/mysql/include && echo "/usr/local/mysql/lib/" >> /etc/ld.so.conf && ldconfig 
```

## 1.3. Mysql客户端安装

Tars环境部署需要依赖mysql客户端

```
which mysql
```

如果mysql客户端不存在, 请执行:
```
rpm -ivh https://repo.mysql.com/mysql57-community-release-el7.rpm
yum install -y mysql 
```

## 1.4. Mysql安装

Tars框架安装需要在mysql中读写数据, 因此需要安装mysql, 如果你已经存在mysql, 可以忽略该步骤.

安装mysql请参考[mysql安装](mysql.md)


# 2. <a id="chapter-2"></a>Tars C++开发环境(源码安装框架必备)

**源码安装框架才需要做这一步, 如果只是用c++写服务, 只需要下载tarscpp代码即可**

下载TarsFramework源码

```
cd ${source_folder}
git clone https://github.com/TarsCloud/TarsFramework.git --recursive
```

然后进入build源码目录
```
cd ${source_folder}/TarsFramework/build
chmod u+x build.sh
./build.sh prepare
./build.sh all
```

**编译时默认使用的mysql开发库路径：include的路径为/usr/local/mysql/include，lib的路径为/usr/local/mysql/lib/**

若mysql开发库的安装路径不在默认路径需要修改CMakeLists文件中mysql开发库的路径。CMakeLists在`${source_folder}/TarsFramework/`和`${source_folder}/TarsFramework/tarscpp/` 目录下各有一个同名文件。
修改文件中上述路径为本机mysql开发库的路径
(参考路径："/usr/include/mysql"；"/usr/lib64/mysql")。


如果需要重新编译
```
./build.sh cleanall
./build.sh all
```

切换至root用户，创建安装目录
```
cd /usr/local
mkdir tars
chown ${普通用户}:${普通用户} ./tars/
```

安装
```
cd ${source_folder}/build
./build.sh install或者make install
```
**默认的安装路径为/usr/local/tars/cpp**

**如要修改安装路径:**
```
**需要修改tarscpp目录下CMakeLists.txt文件中的安装路径。**
**需要修改tarscpp/servant/makefile/makefile.tars文件中的TARS_PATH的路径**
**需要修改tarscpp/servant/script/create_tars_server.sh文件中的DEMO_PATH的路径**
```

# 3 <a id="chapter-3"></a>Tars框架安装

## 3.1. 框架安装模式

**框架有两种模式:**

- centos or ubuntu一键部署, 安装过程中需要网络从外部下载资源
- 制作成docker镜像来完成安装, 制作docker过程需要网络下载资源, 但是启动docker镜像不需要外网

**框架安装注意事项:**

- 安装过程中, 由于tars-web依赖nodejs, 所以会自动下载nodejs, npm, pm2以及相关的依赖, 并设置好环境变量, 保证nodejs生效.
- nodejs的版本目前默认下载的v12.13.0
- 如果你本机以及安装了nodejs, 最好卸载掉

**注意:需要完成TarsFramework的编译和安装**

下载tarsweb并copy到/usr/local/tars/cpp/deploy目录下(注意目录名是web, 不要搞错!):

```
git clone https://github.com/TarsCloud/TarsWeb.git
mv TarsWeb web
cp -rf web /usr/local/tars/cpp/deploy/
```

例如, 这是/usr/local/tars/cpp/deploy下的文件:
```
[root@cb7ea6560124 deploy]# ls -l
total 52
-rw-rw-r-- 1 tars tars 1923 Nov  2 17:31 centos7_base.repo
-rwxrwxr-x 1 tars tars 1515 Nov  5 18:21 Dockerfile
-rwxrwxr-x 1 tars tars 2844 Nov  5 18:21 docker-init.sh
-rwxrwxr-x 1 tars tars  215 Nov  5 18:21 docker.sh
-rw-rw-r-- 1 tars tars  664 Nov  2 17:31 epel-7.repo
drwxrwxr-x 4 tars tars   30 Nov  2 17:31 framework
-rwxrwxr-x 1 tars tars 4599 Nov  8 09:41 linux-install.sh
-rw-rw-r-- 1 tars tars  191 Nov  2 17:31 MariaDB.repo
-rwxrwxr-x 1 tars tars  565 Nov  8 09:23 README.md
-rwxrwxr-x 1 tars tars  539 Nov  8 09:23 README.zh.md
-rwxrwxr-x 1 tars tars 9713 Nov  7 09:42 tars-install.sh
drwxrwxr-x 2 tars tars   44 Nov  7 10:04 tools
drwxr-xr-x 11 tars tars  4096 Oct 31 11:01 web
```

## 3.2. 框架部署说明

框架可以部署在单机或者多机上, 多机是一主多从模式, 通常一主一从足够了:

- 主节点只能有一台, 从节点可以多台
- 主节点默认会安装:tarsAdminRegistry, tarspatch, tarsweb, tarslog, 这几个服务在从节点上不会安装
- tarsAdminRegistry只能是单点(带有发布状态)
- tarslog也只能是单点, 否则日志会分散在多机上
- 原则上tarspatch, tarsweb可以是多点, 如果部署成多点, 需要把/usr/local/app/patchs目录做成多机间共享(可以通过NFS), 否则无法正常发布服务
- 可以后续把tarslog部署到大硬盘服务器上
- 实际使用中, 即使主从节点都挂了, 也不会影响框架上服务的正常运行, 只会影响发布
- 一键部署会自动安装好web(自动下载nodejs, npm, pm2等相关依赖), 同时开启web权限

部署完成后会创建5个数据库，分别是db_tars、db_tars_web、db_user_system、 tars_stat、tars_property。 

其中db_tars是框架运行依赖的核心数据库，里面包括了服务部署信息、服务模版信息、服务配置信息等等；

db_tars_web是web管理平台用到数据库

db_user_system是web管理平台用到的权限管理数据库

tars_stat是服务监控数据存储的数据库；

tars_property是服务属性监控数据存储的数据库；

无论哪种安装方式, 如果成功安装, 都会看到类似如下输出:

```
 2019-10-31 11:06:13 INSTALL TARS SUCC: http://xxx.xxx.xxx.xxx:3000/ to open the tars web. 
 2019-10-31 11:06:13 If in Docker, please check you host ip and port. 
 2019-10-31 11:06:13 You can start tars web manual: cd /usr/local/app/web; npm run prd 
```
打开你的浏览器输入: http://xxx.xxx.xxx.xxx:3000/ 如果顺利, 可以看到web管理平台

**注意: 执行完毕以后, 可以检查nodejs环境变量是否生效: node --version, 如果输出不是v12.13.0, 则表示nodejs环境变量没生效**
**如果没生效, 手动执行:  centos: source ~/.bashrc or ubuntu: source ~/.profile**

## 3.3. (centos or ubuntu)一键部署

进入/usr/local/tars/cpp/deploy, 执行:
```
chmod a+x linux-install.sh
./linux-install.sh MYSQL_HOST MYSQL_ROOT_PASSWORD INET REBUILD(false[default]/true) SLAVE(false[default]/true)
```

MYSQL_HOST: mysql数据库的ip地址

MYSQL_ROOT_PASSWORD: mysql数据库的root密码(注意root不要有太特殊的字符, 例如!, 否则shell脚本识别有问题, 因为是特殊字符)

INET: 网卡的名称(ifconfig可以看到, 比如eth0), 表示框架绑定的本机IP, 注意不能是127.0.0.1

REBUILD: 是否重建数据库,通常为false, 如果中间装出错, 希望重置数据库, 可以设置为true

SLAVE: 是否是从节点

举例, 安装两台节点, 一台数据库(假设: 主[192.168.7.151], 从[192.168.7.152], mysql:[192.168.7.153])

主节点上执行(192.168.7.151)
```
chmod a+x linux-install.sh
./linux-install.sh 192.168.7.153 tars2015 eth0 false false
```
主节点执行完毕后, 从节点执行:
```
chmod a+x linux-install.sh
./linux-install.sh 192.168.7.153 tars2015 eth0 false true
```

执行过程中的错误参见屏幕输出, 如果出错可以重复执行(一般是下载资源出错)

## 3.4. 制作成docker

目标: 将框架制作成一个docker, 部署时启动docker即可.

进入该目录, 执行生成docker:
```
chmod a+x docker.sh
./docker.sh v1
```
docker制作完毕: tar-docker:v1
```
docker ps
```

可以将docker发布到你的机器, 然后执行

```
docker run -d --net=host -e MYSQL_HOST=xxxxx -e MYSQL_ROOT_PASSWORD=xxxxx \
        -eREBUILD=false -eINET=enp3s0 -eSLAVE=false \
        -v/data/tars:/data/tars \
        -v/etc/localtime:/etc/localtime \
        tars-docker:v1
```

MYSQL_IP: mysql数据库的ip地址

MYSQL_ROOT_PASSWORD: mysql数据库的root密码

INET: 网卡的名称(ifconfig可以看到, 比如eth0), 表示框架绑定本机IP, 注意不能是127.0.0.1

REBUILD: 是否重建数据库,通常为false, 如果中间装出错, 希望重置数据库, 可以设置为true

SLAVE: 是否是从节点

映射三个目录到宿主机
- -v/data/tars:/data/tars, 包含了 tars应用日志, web日志, 发布包目录

**如果希望多节点部署, 则在不同机器上执行docker run ...即可, 注意参数设置!**

**这里必须使用 --net=host, 表示docker和宿主机在相同网络** 

## 3.5. 核心模块

无论是那种安装方式, 最终Tars Framework都是由几个核心模块组成的, 例如:

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

- 对于主机节点 tarsAdminRegistry  tarsnode  tarsregistry tars-web 必须活着, 其他tars服务会被tarsnode自动拉起
- 对于从机节点 tarsnode  tarsregistry 必须活着, 其他tars服务会被tarsnode拉起
- tars-web是nodejs实现的服务, 由两个服务组成, 具体参见后面章节
- 为了保证核心服务是启动的, 可以通过check.sh来控制, 在crontab 中配置

主机(add to contab):

```
* * * * * /usr/local/app/tars/check.sh master
```

从机(add to contab):
```
* * * * * /usr/local/app/tars/check.sh 
```

如果配置了check.sh, 就不需要配置后面章节中的tarsnode的监控了

# 4. <a id="chapter-4"></a>Tars-web说明

## 4.1 模块说明

Tars Framework部署好以后, 在主机节点上会安装tars-web(从机节点不会安装), tars-web采用nodejs实现, 由两个服务组成.

查看tars-web的模块:
```
pm2 list
```

输出如下:
```
[root@8a17fab70409 data]# pm2 list
┌────┬─────────────────────────┬─────────┬─────────┬──────────┬────────┬──────┬──────────┬──────────┬──────────┬──────────┬──────────┐
│ id │ name                    │ version │ mode    │ pid      │ uptime │ ↺    │ status   │ cpu      │ mem      │ user     │ watching │
├────┼─────────────────────────┼─────────┼─────────┼──────────┼────────┼──────┼──────────┼──────────┼──────────┼──────────┼──────────┤
│ 0  │ tars-node-web           │ 0.2.0   │ fork    │ 1602     │ 2m     │ 0    │ online   │ 0.1%     │ 65.1mb   │ root     │ disabled │
│ 1  │ tars-user-system        │ 0.1.0   │ fork    │ 1641     │ 2m     │ 0    │ online   │ 0.1%     │ 60.1mb   │ root     │ disabled │
└────┴─────────────────────────┴─────────┴─────────┴──────────┴────────┴──────┴──────────┴──────────┴──────────┴──────────┴──────────┘
```

**如果找不到pm2, 一般是环境变量没生效, 请先执行: centos: source ~/.bashrc or ubuntu: source ~/.profile, 安装过程中会写这个文件**

**tars-web由两个模块组成**
- tars-node-web: tars-web主页面服务, 默认绑定3000端口, 源码对应web目录
- tars-user-system: 权限管理服务, 负责管理所有相关的权限, 默认绑定3001端口, 源码对应web/demo目录

tars-node-web调用tars-user-system来完成相关的权限验证

**web采用nodejs+vue来实现, 最终的安装运行目录如下:**

```
/usr/local/app/web
```

如果pm2 list中查看模块启动不了, 可以进入改目录定位问题:

```
cd /usr/local/app/web/demo; npm run start
cd /usr/local/app/web; npm run start
```

npm run start 启动服务, 可以观察控制台的输出, 如果有问题, 会有提示.

**正式运行建议: pm2 start tars-node-web; pm2 start tars-user-system**


## 4.2 权限说明

tar-web默认起来后, 默认有一个admin账号, 第一次登录需要修改admin用户的密码

admin用户可以创建其他用户, 并给其他用户授权(三种权限admin, operator, developer)

三种权限的能力不同, admin权限拥有超级管理权限, operator运维权限(包含developer权限, 能发布), developer(查看)

权限可以精确到应用或者服务级别

## 4.3 部署说明

tars-node-web & tars-user-system默认是部署在同一台机器上, 并且都绑定了0.0.0.0 (即也绑定了127.0.0.1)

tars-node-web通过localhost(127.0.0.1)来访问tars-user-system, 如果未绑定127.0.0.1, 则无权限, 此时需要修改tars-user-system模块的配置(demo/config/loginConf.js), 开放白名单:ignoreIps

如果demo未绑定127.0.0.1, 则默认web过来的调用无权限, 此时需要修改demo/config/loginConf.js, 开放白名单:ignoreIps

web & demo 的登录态通过cookie传递, 因此需要部署在同一个域名下面

如果web & demo前面挂了nginx代理, 通过不同域名来访问, 则需要指定两个环境变量, 来方便web&demo互通

比如: web的nginx的入口域名是: http://user.tars.com, demo的nginx域名是: http://auth.tars.com, 那么需要在服务器上设定环境变量:

```
export USER_CENTER_HOST=http://auth.tars.com
export COOKIE_DOMAIN=.tars.com
```

**注意COOKIE_DOMAIN不要少了.**

设定环境变量后, 即可正常访问demo


最后，在安装环境过程中，如果系统仍有问题，请到以下的目录查找日志文件分析问题所在：
(1) ${TarsWeb}/log  
(2) /usr/local/app/tars/app_log/tars


