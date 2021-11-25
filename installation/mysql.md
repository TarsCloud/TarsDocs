# Mysql 安装

## 目录

> - [简介](#chapter-1)
> - [源码安装 Mysql](#chapter-2)
> - [yum 安装 mysql](#chapter-3)
> - [docker 安装 mysql5](#chapter-4)
> - [docker 安装 mysql8](#chapter-4)

## 1 简介 <span id="chapter-1"></span>

Tars 框架的数据最终都存储在 mysql 中, 因此需要安装 mysql。

mysql 建议使用主从方式安装, 正常情况下, mysql 即使 crash 了, 也不会影响 Tars 框架上服务的运行, 但是会影响服务的发布和部署, 以及影响管理平台的使用。

mysql 安装有几种方式, 任选一种即可.

## 2 源码安装 <span id="chapter-2"></span>

源码安装可以对数据库进行自定义。

安装前，确定系统是否安装依赖库，若没有，可以执行：

```text
yum install -y ncurses-devel zlib-devel cmake make gcc gcc-c++
```

设置安装目录，切换至 root 用户

```text
cd /usr/local
mkdir mysql-5.6.26
chown ${普通用户}:${普通用户} ./mysql-5.6.26
ln -s /usr/local/mysql-5.6.26 /usr/local/mysql
```

用 utf8 的安装方式 下载 mysql 源码（这里使用的是 mysql-5.6.26）,用 utf8 的安装方式 mysql，解压后编译： 下面增加了 mysql-5.6.26 的安装方式

```text
cd ${mysql安装目录}
wget https://dev.mysql.com/get/Downloads/MySQL-5.6/mysql-5.6.26.tar.gz
tar -zxvf mysql-5.6.26.tar.gz
cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mysql-5.6.26 -DWITH_INNOBASE_STORAGE_ENGINE=1 -DMYSQL_USER=mysql -DDEFAULT_CHARSET=utf8 -DDEFAULT_COLLATION=utf8_general_ci
make
make install
```

**注意，对于用 Tars 的 c++进行开发编译的服务，mysql 建议采用静态库，源码编译，避免所有服务器都要安装 mysql 的动态库。**

对于在服务器用 Tars 的 c++进行开发编译服务代码而言，经过上面步骤就可以进行编译安装 Tars 开发框架了。

若要是搭建 Tars 框架的运行环境，需要以下步骤，切换至 root 用户，对 mysql 进行配置。

**以下脚本有删除动作\(rm -rf /usr/local/mysql/data\), 请注意!!**

```text
yum install perl
cd /usr/local/mysql
useradd mysql
rm -rf /usr/local/mysql/data
mkdir -p /data/mysql-data
ln -s /data/mysql-data /usr/local/mysql/data
chown -R mysql:mysql /data/mysql-data /usr/local/mysql/data
cp support-files/mysql.server /etc/init.d/mysql
**如果/etc/目录下有my.cnf存在，需要把这个配置删除了**
rm -rf /etc/my.cnf
yum install -y perl-Module-Install.noarch
perl scripts/mysql_install_db --user=mysql
vim /usr/local/mysql/my.cnf
```

补充了上面删除 my.cnf 的命令 rm -rf /etc/my.cnf

给一个 my.cnf 配置实例：

```text
[mysqld]

# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
innodb_buffer_pool_size = 128M

# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
log_bin

# These are commonly set, remove the # and set as required.
basedir = /usr/local/mysql
datadir = /usr/local/mysql/data
# port = .....
# server_id = .....
socket = /tmp/mysql.sock

bind-address=${your machine ip}

# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
join_buffer_size = 128M
sort_buffer_size = 2M
read_rnd_buffer_size = 2M

sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
```

**注意将 bind-address 改为部署机器的 IP**

启动 mysql

```text
service mysql start
chkconfig mysql on
```

结束 mysql

```text
service mysql stop
```

添加 mysql 的 bin 路径

```text
vim /etc/profile
PATH=$PATH:/usr/local/mysql/bin
export PATH
```

修改 root 密码\(采用 root 密码\)

```text
./bin/mysqladmin -u root password 'root@appinside'
./bin/mysqladmin -u root -h ${主机名} password 'root@appinside'
```

**注意${主机名}需要修改成自身机器的名称或者 IP，可以通过查看/etc/hosts**

添加 mysql 库路径

```text
vim /etc/ld.so.conf
/usr/local/mysql/lib/
ldconfig
```

mysql 主从配置可以参考网上教程

master 赋予权限:

```text
GRANT REPLICATION SLAVE ON *.* to 'mysql-sync'@'%' identified by 'sync@appinside'
```

slave 设置主备同步项

```text
change master to master_host='${备机Ip}',master_user='mysql-sync',master_password='sync@appinside' ,master_log_file='iZ94orl0ix4Z-bin.000004',master_log_pos=611;
stop slave
start slave
show master status\G;
show slave status\G;
```

**注意${备机 Ip}需要修改成备机数据库的 Ip**

## 3 yum 安装 <span id="chapter-3"></span>

在 mysql 5.7 版本之后删除了源码中的 mysql_install_db.sh 安装脚本，因此上述方法不适用。 yum 安装相对便捷，但是没办法实现自定义安装。如果对自定义安装有需求的请使用源码安装。

**从 yum repository 安装 MySQL**

```text
wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
yum -y install mysql57-community-release-el7-10.noarch.rpm
yum -y install mysql-community-server
yum -y install  mysql-devel
```

这样 mysql 就安装好了 其中 wget 中的库地址可以根据需要替换版本号 如果 wget 无法安装，可以尝试按照下述步骤将 mysql repository 添加到本地服务，然后再重新执行上述命令

```text
sudo yum localinstall https://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
```

**设置 MySQL**

启动 mysql 并查看运行状态

```text
systemctl start  mysqld.service
systemctl status mysqld.service
```

mysql 开始运行并通过 3306 端口进行连接。

通过 yum 安装 mysql 的话 root 账号会默认设置一个密码，可以通过下面的方式获得。

```text
grep "password" /var/log/mysqld.log
```

使用这个密码登陆 root 的账户。mysql 要求必须更改密码才能做数据库操作。 如果你安装的 mysql 版本是 5.7 及以上，mysql 的密码设置有安全性要求\(长度、大小写、特殊字符\)，以下两种方式可以选择

1. 设置满足要求的密码
2. 通过下面的方式降低密码安全性规则

```text
mysql> set global validate_password_policy=0;
mysql> set global validate_password_length=1;
```

修改之后只有 6 个字符的最小长度限制 接着修改密码

```text
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY '${your password}';
```

**mysql 主从支持参照上一节**

## 4 docker 安装 mysql5 <span id="chapter-4"></span>

服务器安装 docker 请参考[docker](docker.md)

使用 docker 安装来安装 mysql\(目前只考虑了 linux 上, 时间和本机同步\)

```bash
docker pull mysql:5.6
docker run --name mysql --net=host -e MYSQL_ROOT_PASSWORD='tars@12345' -d -p 3306:3306 \
        -v/etc/localtime:/etc/localtime \
        -v /data/mysql-data:/var/lib/mysql mysql:5.6
```

安装 mysql5.7 方式类似, 修改 mysql:${version}的版本号即可

**注意:--net-host 表示 Docker 网络和本机一样**

## 5 docker 安装 mysql8 <span id="chapter-4"></span>

如果你使用 mysql8, 那么请注意, mysql8 默认开启了 ssl, 以及密码验证使用了 caching_sha2_password, 因此启动 mysql8 时, 需要关闭 ssl 以及默认使用 mysql_native_password 方式, 例如:

```
docker run -d --restart=always --net=host -p 3306:3306 -v /usr/local/mysql-data-8:/var/lib/mysql  -e MYSQL_ROOT_PASSWORD=tars@12345 -e TZ=Asia/Shanghai mysql:8 --skip_ssl --default-authentication-plugin=mysql_native_password
```

**源码编译 mysql8 类似, 需要关闭 ssl 以及启用 mysql_native_password**
