# 目录
> * [依赖环境](#chapter-1)
> * [Tars C++开发环境](#chapter-2)
> * [Tars框架安装](#chapter-3)
> * [Tars-web说明](#chapter-4)

本安装文档描述在一台或多台服务器上安装搭建整个Tars框架的过程，目的是为了让用户对Tars框架的部署搭建、运行、测试等有个整体的认识。

如要用于线上环境，部署安装的原理是一样，不过需要更多考虑分布式系统下服务的部署需要有容错、容灾等的能力。

# 1. <span id="chapter-1"></span>依赖环境

软件 |软件要求
------|--------
windows版本:      |	win7以及以上
cmake版本：        | 3.2及以上版本（c++语言框架依赖）
mysql版本:         |   5.6及以上版本（框架运行依赖）
nvm版本：          |   0.35.1及以上版本
node版本：         |   12.13.0及以上版本

运行服务器要求：Windows机器

## 1.1. 编译包依赖下载安装介绍

- 安装nodejs, nodejs官网: https://nodejs.org/en/

- 安装vs(建议vs2019), vs2019自带cmake, 比如我的机器cmake路径为:
C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\Common7\IDE\CommonExtensions\Microsoft\CMake\CMake\bin

- 如果你的vs低于vs2019, 请安装cmake, cmake官网: https://cmake.org/

- 安装git

**请保证你的nodejs和cmake处于系统环境变量中, 即能命令行运行: cmake, node, git**

## 1.2. Mysql安装

正式部署时, 如果你的mysql可以安装在其他机器上.

Tars框架安装需要在mysql中读写数据, 因此需要安装mysql, 如果你已经存在mysql, 可以忽略该步骤.

linux上安装mysql请参考[mysql安装](mysql.md)

windows上安装mysql请自行搜索教程

# 2. <span id="chapter-2"></span>Tars C++开发环境(源码安装框架必备)

**源码安装框架才需要做这一步, 如果只是用c++写服务, 只需要下载tarscpp代码即可**

**注意我们的操作都在命令行下进行, 请保证cmake git nodejs vs等环境变量的生效**

下载TarsFramework源码

```
cd ${source_folder}
git clone https://github.com/TarsCloud/TarsFramework.git --recursive
```

打开windows console 控制台(可能需要以管理身份运行), 然后进入build源码目录
```
cd TarsFramework
git submodule update --remote --recursive
cd build
cmake ..
cmake --build . --config RelWithDebInfo
cmake --build . --config RelWithDebInfo  --target install
```

默认情况下, 编译Tars会自动下载mysql client源码, 并编译libmyql.dll

安装编译完的包
```
cmake --build . --config RelWithDebInfo  --target install
```

**默认的安装包路径:c:/tars/cpp, 即编译完成的框架&安装脚本在这个目录**
**默认的安装完成后的路径为c:/tars-install, 即安装完成后的路径**

install以后, 依赖的库(mysql静态库)和头文件也会安装到该目录下(c:/tars/cpp/thirdparty), 如果开启了ssl, nghttp2同理.

**如要修改安装路径:**
```
**需要修改tarscpp/cmake/Common.cmake文件中的安装路径。**
**需要修改tarscpp/servant/makefile/makefile.tars文件中的TARS_PATH的路径**
**需要修改tarscpp/servant/makefile/tars-tools.cmake文件中的TARS_PATH的路径**
**需要修改tarscpp/servant/script/*.bat文件中的DEMO_PATH的路径**
```

# 3 <span id="chapter-3"></span>Tars框架安装

## 3.1. 框架安装模式

**注意:需要完成TarsFramework的编译和安装**

在c:\tars\cpp\deploy下载tarsweb(注意目录名是web, 不要搞错!):

```
cd c:\tars\cpp\deploy
git clone https://github.com/TarsCloud/TarsWeb.git web
```

例如, 这是c:\tars\cpp\deploy下的文件:
```
 C:\tars\cpp\deploy 的目录

2020/03/31  17:20    <DIR>          .
2020/03/31  17:20    <DIR>          ..
2020/03/24  11:38           443,392 busybox.exe
2020/03/24  11:38             1,981 centos7_base.repo
2020/03/28  15:43             3,273 docker-init.sh
2020/03/28  15:43               319 docker.sh
2020/03/28  15:43             2,365 Dockerfile
2020/03/28  14:33    <DIR>          framework
2020/03/30  12:45         4,099,584 libmysql.dll
2020/03/28  15:43             4,829 linux-install.sh
2020/03/30  12:51           110,592 mysql-tool.exe
2020/03/28  15:43             1,349 tar-server.sh
2020/03/30  12:52           635,904 tars-client.exe
2020/03/30  11:28            16,885 tars-install.sh
2020/03/24  11:38               322 tars-stop.sh
2020/03/30  14:23    <DIR>          tools
2020/03/30  14:14    <DIR>          web
2020/03/28  15:43             3,732 web-install.sh
2020/03/28  15:43             1,543 windows-install.sh
              14 个文件      5,326,070 字节
               5 个目录 196,844,564,480 可用字节
```

## 3.2. 框架部署说明

框架可以部署在单机或者多机上, 多机是一主多从模式, 通常一主一从足够了:

- 主节点只能有一台, 从节点可以多台
- 主节点默认会安装:tarsAdminRegistry, tarspatch, tarsweb, tarslog, tarsstat, tarsproperty, 这几个服务在从节点上不会安装
- tarslog用于收集所有服务的远程日志, 建议单节点, 否则日志会分散在多机上
- 原则上tarspatch, tarsweb可以是多点, 如果部署成多点, 需要把c:\tars-install\patchs目录做成多机间共享, 否则无法正常发布服务
- 虽然tarsAdminRegistry上记录了正在发布服务的状态, 但是原则上也可以可以多节点, tarsweb调用tarsAdminRegistry是hash调用
- 后续强烈建议把tarslog部署到大硬盘服务器上
- 实际使用中, 即使主从节点都挂了, 也不会影响框架上服务的正常运行, 只会影响发布
- 一键部署会自动安装好web, 同时开启web权限

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

请参考[检查web的问题](web.md)中的检查web问题章节, 如果没有问题, 请检查机器防火墙
 
## 3.3. 一键部署

进入c:\tars\cpp\deploy, 执行:
```
busybox.exe sh ./windows-install.sh MYSQL_HOST MYSQL_PASSWORD HOSTIP REBUILD(false[default]/true) SLAVE(false[default]/true) MYSQL_USER MYSQL_PORT
```

**注意: busybox.exe是一个通用工具, 继承了大量linux工具**

MYSQL_HOST: mysql数据库的ip地址

MYSQL_PASSWORD: mysql数据库的MYSQL_USER的密码(注意密码不要有太特殊的字符, 例如!, 否则shell脚本识别有问题, 因为是特殊字符)

HOSTIP:  本机IP, 注意不能是127.0.0.1

REBUILD: 是否重建数据库,通常为false, 如果中间装出错, 希望重置数据库, 可以设置为true

SLAVE: 是否是从节点

MYSQL_USER: mysql用户, 默认是root

MYSQL_PORT: mysql端口

举例, 安装两台节点, 一台数据库(假设: 主[192.168.7.151], 从[192.168.7.152], mysql:[192.168.7.153])

主节点上执行(192.168.7.151)
```
busybox.exe sh ./windows-install.sh 192.168.7.153 tars2015 192.168.7.151 false false root 3306
```
主节点执行完毕后, 从节点执行:
```
busybox.exe sh ./windows-install.sh 192.168.7.153 tars2015 192.168.7.152 false true root 3306
```

执行过程中的错误参见屏幕输出, 如果出错可以重复执行(一般是下载资源出错)

## 3.4. mysql权限问题

上述安装mysql默认都是需要root权限, 但是在某些场景不具备root用户或者root用户必须交互式输入密码的情况下(比如腾讯云cdb), 你可以这样安装:

- 首先在mysql中创建用户(可能管理员分配给你的), 比如:admin
- admin用户具备以下权限(重点是创建用户的权限):
```
GRANT, SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER, CREATE TABLESPACE
```
- 执行安装脚本

```
busybox.exe sh ./windows-install.sh 192.168.7.153 tars2015 192.168.7.151 false true admin 3306

```
- 脚本会自动根据传入的MYSQL_USER和MYSQL_PASSWORD来登录数据库，创建TarsAdmin账号和授权Tars相关数据库供框架使用

**实际框架安装过程中, 会再创建一个用户, 并用这个用户来连接db,可以参见tars-install.sh脚本**

## 3.5. 核心模块

无论是那种安装方式, 最终Tars Framework都是由几个核心模块组成的, 例如:

```
C:\tars\cpp\deploy>busybox.exe ps | busybox.exe grep tars
14592 14400  0:00  5:05   tarsAdminRegist
 8892 14520  0:00  5:02   tarsregistry.ex
 8524  7916  0:00  5:01   tarsconfig.exe
 9400  9624  0:01  4:59   tarsnode.exe
10488  5348  0:00  4:57   tarsnotify.exe
10460  8356  0:01  4:56   tarsproperty.ex
 8536  4384  0:00  4:48   tarsqueryproper
 1268 13708  0:00  4:45   tarsquerystat.e
 7308 10560  0:00  4:37   tarsstat.exe
11760 14236  0:00  4:34   tarslog.exe
 4180 14428  0:00  4:32   tarspatch.exe
```

- 对于主机节点  tarsnode tars-web 必须活着, 其他tars服务会被tarsnode自动拉起
- 对于从机节点 tarsnode 必须活着, 其他tars服务会被tarsnode拉起
- tars-web是nodejs实现的服务, 由两个服务组成, 具体参见后面章节
- 为了保证核心服务是启动的, 可以通过c:\tars-install\tars\tarsnode\util\monitor.bat来控制, 在windows计划任务中配置(3秒执行一次)

# 4. <span id="chapter-4"></span>Tars-web说明

## 4.1 模块说明

Tars Framework部署好以后, 在主机节点上会安装tars-web(从机节点不会安装), tars-web采用nodejs实现, 由两个服务组成.

查看tars-web的模块:
```
pm2 list
```

### web < v2.4.7

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

**如果找不到pm2, 一般是环境变量没生效**

**tars-web由两个模块组成**
- tars-node-web: tars-web主页面服务, 默认绑定3000端口, 源码对应web目录
- tars-user-system: 权限管理服务, 负责管理所有相关的权限, 默认绑定3001端口, 源码对应web/demo目录

tars-node-web调用tars-user-system来完成相关的权限验证

**web采用nodejs+vue来实现, 最终的安装运行目录如下:**

```
c:\tars-install\web
```

如果pm2 list中查看模块启动不了, 可以进入改目录定位问题:

```
cd c:\tars-install\web\demo; npm run start
cd c:\tars-install\web; npm run start
```

npm run start 启动服务, 可以观察控制台的输出, 如果有问题, 会有提示.

**正式运行建议: pm2 start tars-node-web; pm2 start tars-user-system**

如果安装完成后web页面打不开, 请参考[web](web.md), 检查问题章节, 定位问题


### web >= v2.4.7

输出如下:
```
[root@8a17fab70409 data]# pm2 list
┌────┬─────────────────────────┬─────────┬─────────┬──────────┬────────┬──────┬──────────┬──────────┬──────────┬──────────┬──────────┐
│ id │ name                    │ version │ mode    │ pid      │ uptime │ ↺    │ status   │ cpu      │ mem      │ user     │ watching │
├────┼─────────────────────────┼─────────┼─────────┼──────────┼────────┼──────┼──────────┼──────────┼──────────┼──────────┼──────────┤
│ 0  │ tars-node-web           │ 0.2.0   │ fork    │ 1602     │ 2m     │ 0    │ online   │ 0.1%     │ 65.1mb   │ root     │ disabled │
└────┴─────────────────────────┴─────────┴─────────┴──────────┴────────┴──────┴──────────┴──────────┴──────────┴──────────┴──────────┘
```

**如果找不到pm2, 一般是环境变量没生效**

- tars-node-web: tars-web主页面服务, 默认绑定3000端口, 源码对应web目录

**web采用nodejs+vue来实现, 最终的安装运行目录如下:**

```
c:\tars-install\web
```

如果pm2 list中查看模块启动不了, 可以进入改目录定位问题:

```
cd c:\tars-install\web; npm run start
```

npm run start 启动服务, 可以观察控制台的输出, 如果有问题, 会有提示.

**正式运行建议: pm2 start tars-node-web**

如果安装完成后web页面打不开, 请参考[web](web.md), 检查问题章节, 定位问题

