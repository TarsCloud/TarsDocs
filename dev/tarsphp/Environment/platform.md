# 安装说明

* 本文说明了如何在单机服务器部署TARS环境，如需安装TARS-PHP请参考[搭建php环境](./php.md)，如需进行服务开发请参考[快速起步](../QuickStart)。
* 本文使用操作系统版本: centOs7.2
* 本文使用的数据库版本: mysql5.7
* 本文的的操作命令运行需要root权限，需要确保服务器联网。

### 1.安装各种工具

C++语言框架依赖。

```
yum update -y
sudo yum install -y glibc-devel cmake ncurses-devel zlib-devel perl flex bison net-tools vim git yum-config-manager yum-utils gcc gcc-c++
```

### 2.安装mysql5.7

框架运行依赖，如果下载较慢，可以尝试将centOs默认yum源修改为国内yum的镜像。

```
yum install https://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm -y
yum-config-manager --disable mysql56-community
yum-config-manager --enable mysql57-community-dmr
yum install mysql mysql-devel mysql-server mysql-utilities -y
```

### 3.安装pm2

Web管理系统依赖。

```
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
source ~/.bashrc
nvm install v8.11.3
npm install -g pm2
```

### 4.安装TARS

##### 4.1 下载TarsFramework源码

```
git clone https://github.com/TarsCloud/TarsFramework.git
```

##### 4.2 检查MYSQL路径

**查看源码目录下的CMakeLists.txt文件，确保MYSQL的相关路径配置与本机一致。**

比如，在centos7.2使用yum安装mysql，mysql的lib文件默认放在`/usr/lib64/mysql`，include文件夹默认在`/usr/include/mysql/`，可将CMakeLists.txt文件中的MYSQL_DIR_LIB改为"/usr/lib64/mysql"；也可使用`cp -r /usr/include/mysql/* /usr/local/mysql/include`将文件copy到指定路径`/usr/local/mysql/include`，如下：

```
set(MYSQL_DIR_INC "/usr/local/mysql/include")
set(MYSQL_DIR_LIB "/usr/lib64/mysql")
```

##### 4.3 编译安装TARS

```
cd {$source_folder}/build
chmod u+x build.sh
./build.sh prepare
./build.sh all
```

如需重新编译请使用

```
./build.sh cleanall
./build.sh all
```

### 5.数据库环境初始化

##### 5.1修改mysql初始密码为root@appinside

不同版本的mysql初始密码的修改方式略有不同，可根据实际情况自行修改，以下为centOs7.2的mysql5.7初始密码修改方式。

```
service mysqld start
cat /var/log/mysqld.log | grep password  // 复制mysql初始密码7zi;e3lNXV,
mysql -uroot -p 
Enter password: // 输入初始密码，进入mysql
mysql>update user set authentication_string = password("root@appinside") where user="root";
mysql>\q
service mysqld restart
```

##### 5.2 添加用户

${your machine ip}需要修改成自身机器内网ip，可以通过`ifconfig`查看。

```
grant all on *.* to 'tars'@'%' identified by 'tars2015' with grant option;
grant all on *.* to 'tars'@'localhost' identified by 'tars2015' with grant option;
grant all on *.* to 'tars'@'${your machine ip}' identified by 'tars2015' with grant option;
flush privileges;
```

##### 5.3 创建数据库

进入源码的sql目录

```
cd {$source_folder}/sql 
```

修改部署的ip为本机内网ip

```
sed -i "s/192.168.2.131/${your machine ip}/g" `grep 192.168.2.131 -rl ./*`
sed -i "s/db.tars.com/${your machine ip}/g" `grep db.tars.com -rl ./*`
sed -i "s/10.120.129.226/${your machine ip}/g" `grep 10.120.129.226 -rl ./*`
```

执行exec-sql.sh脚本

```
chmod u+x exec-sql.sh
./exec-sql.sh
```

脚本执行后，会创建3个数据库，分别是db_tars、tars_stat、tars_property。
* db_tars：框架运行依赖的核心数据库，里面包括了服务部署信息、服务模版信息、服务配置信息等等；
* tars_stat：服务监控数据存储的数据库；
* tars_property：服务属性监控数据存储的数据库；

### 6.安装框架基础服务

框架服务的安装分两种：

* 核心基础服务：tarsAdminRegistry, tarsregistry, tarsnode, tarsconfig, tarspatch 
* 普通基础服务：tarsstat, tarsproperty,tarsnotify, tarslog，tarsquerystat，tarsqueryproperty

核心基础服务必须手工部署，普通基础服务可以通过管理平台发布。

##### 6.1 准备核心基础服务包

```
cd {$source_folder}/build
make framework-tar
```

命令执行后，会在当前目录生成framework.tgz，这个包包含了tarsAdminRegistry, tarsregistry, tarsnode, tarsconfig, tarspatch 部署相关的文件。

##### 6.2 准备普通基础服务安装包

```
make tarsstat-tar
make tarsnotify-tar
make tarsproperty-tar
make tarslog-tar
make tarsquerystat-tar
make tarsqueryproperty-tar
```

命令执行后，会在当前目录生成基础服务发布包，可以在管理平台部署完成后，通过管理平台进行部署发布。

##### 6.3 安装核心基础服务

将核心基础服务包copy到/usr/local/app/tars目录下，并解压。

```
mkdir -p /usr/local/app/tars
cp {$source_folder}/build/framework.tgz /usr/local/app/tars
cd /usr/local/app/tars
tar zxvf framework.tgz
```

修改各个服务对应conf目录下的配置文件，注意将配置文件中的ip地址修改为本机内网ip地址。

```$xslt
cd /usr/local/app/tars
sed -i "s/192.168.2.131/${your_machine_ip}/g" `grep 192.168.2.131 -rl ./*`
sed -i "s/db.tars.com/${your_machine_ip}/g" `grep db.tars.com -rl ./*`
sed -i "s/registry.tars.com/${your_machine_ip}/g" `grep registry.tars.com -rl ./*`
sed -i "s/web.tars.com/${your_machine_ip}/g" `grep web.tars.com -rl ./*`
```

然后在`/usr/local/app/tars/`目录下，执行脚本，启动tars框架服务。

```$xslt
chmod u+x tars_install.sh
./tars_install.sh
```

部署管理平台并启动web管理平台

```$xslt
tarspatch/util/init.sh
```

##### 6.4.安装web管理平台

下载web源码

```$xslt
git clone https://github.com/TarsCloud/TarsWeb
```

修改源码中的配置文件，将配置文件中的ip地址改成本机内网ip地址。

```$xslt
cd {$web_source_folder}
sed -i 's/db.tars.com/${your_machine_ip}/g' config/webConf.js
sed -i 's/registry.tars.com/${your_machine_ip}/g' config/tars.conf
```

安装web管理页面依赖，启动web。

```$xslt
cd {$web_source_folder}
npm install --registry=https://registry.npm.taobao.org
npm run prd
```

创建日志目录。

```$xslt
mkdir -p /data/log/tars
```

访问站点 浏览器输入${your machine ip}:3000，即可看到web管理界面，如访问不了，可尝试关闭防火墙。

![alt text](../../../assets/tars_web_system_index.png)

##### 6.5 安装普通基础服务

**平台部署的端口号仅供参考，保证端口无冲突即可**

在安装核心基础服务时，普通基础服务的部署信息已经初始化了，因此此处只要进行发布即可，以下以tarsstat为例进行操作说明。

点击`运维管理` => `服务部署`

![alt_text](../../../assets/tars_tarsstat_bushu.png)

点击`服务管理` => 选中一个服务 => `发布管理`，上传发布包，即6.2中生成的基础发布包，点击发布即可。

![alt_text](../../../assets/tars_tarsnotify_patch.png)

按照此方式逐个部署tarsnotify、tarsproperty、tarslog、tarsquerystat、tarsqueryproperty。

**注意在服务部署页面选择服务相应的模板，如tarsstat模板为tars.tarsstat, tarsproperty模板为tars.tarsproperty。**

**注意tarsquerystat、tarsqueryproperty的协议选择非TARS，其他选择为TARS。**

## 附录
* 安装前请先按照此页面介绍准备好软件https://github.com/TarsCloud/Tars/tree/master/build
* 建议先阅读下文内容 https://github.com/TarsCloud/Tars/blob/master/build/install.sh 
    （因实际上有些软件依赖在某些脚本中会自动安装，避免重复劳动，熟悉大致的脚本安装过程）
* 参考前两部分内容，接下来按照安装说明进行安装 https://github.com/TarsCloud/Tars/blob/master/Install.zh.md
* 安装中遇到的常见问题和注意事项，可阅读 https://github.com/TarsCloud/Tars/blob/master/Install_faq.zh.md
(试了下，上面4个页面都404了)
* 安装教学视频 https://www.bilibili.com/video/av47290510/ 

# 快速安装

* [使用docker](./docker.md)


