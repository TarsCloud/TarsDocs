# Tars 资源对接能力使用指引

TARS管理平台提供了由平台自动安装TARS Node的能力。当用户上线服务，或扩容服务之时，若相应IP未安装TARS Node，管理平台可通过ssh登录相应机器并安装TARS Node。 系统将对外提供“install\_tars\_node”和 “uninstall\_tars\_node”两个接口用于安装和卸载tars\_node，具体可参见接口API文档.

## **Tars Node安装包准备**

TARS Node 二进制包准备，用户需要从开发框架代码中，拷贝TARS Node的二进制包，并生成相应的tarsnode.tar.gz包，放在管理平台的files目录下。注意：一定要放在files目录下，且名称为tarsnode.tar.gz。

其中：需修改conf下的配置文件tarsnode.conf。需将localip，endpoint中的ip地址换成 {{machine\_ip}} 这个特殊标志，管理平台执行安装时，会将 {{machine\_ip}} 置换成待安装tarsnode的机器ip。

## **设置配置文件** 

资源管理相关的有两个对接配置文件，/config/resourceConf.js 和 /config/sshConf.json 其中，resouceConf文件，可配置两个字段 1）、enableAutoInstall： 若此值为true，则系统将在上线和扩容的时候，自动登录机器并安装Tars Node。 2）、getMachineConf： 由用户提供的获取机器信息的url，入参为ip，出参必须为：​{ “data”: { “ip”: “127.0.0.1”, “port”: “3306”, “username”: “user”, “password”: “123456” } } 其中，data对象包含机器的ip，ssh端口，用户名和登录密码。

若不配置此接口，或此接口查询不到相应IP的机器信息，Tars管理平台允许配置sshConf.json文件，由用户提前准备好机器信息并填入，管理平台会到此配置文件下读取相应的机器信息。此文件也可直接修改，无需重启服务。

