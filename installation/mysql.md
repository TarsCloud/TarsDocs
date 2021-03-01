# Mysql安装

## 目录

> * [简介](#chapter-1)
> * [源码安装Mysql](#chapter-2)
> * [yum安装mysql](#chapter-3)
> * [docker安装mysql](#chapter-4)

## 1 简介 <span id="chapter-1"></span>

Tars框架的数据最终都存储在mysql中, 因此需要安装mysql。

mysql建议使用主从方式安装, 正常情况下, mysql即使crash了, 也不会影响Tars框架上服务的运行, 但是会影响服务的发布和部署, 以及影响管理平台的使用。

mysql安装有几种方式, 任选一种即可.

## 2 源码安装 <span id="chapter-2"></span>

源码安装可以对数据库进行自定义。

安装前，确定系统是否安装依赖库，若没有，可以执行：

```text
yum install -y ncurses-devel zlib-devel cmake make gcc gcc-c++
```

设置安装目录，切换至root用户

```text
cd /usr/local
mkdir mysql-5.6.26
chown ${普通用户}:${普通用户} ./mysql-5.6.26
ln -s /usr/local/mysql-5.6.26 /usr/local/mysql
```

用utf8的安装方式 下载mysql源码（这里使用的是mysql-5.6.26）,用utf8的安装方式mysql，解压后编译： 下面增加了mysql-5.6.26的安装方式

```text
cd ${mysql安装目录}
wget https://dev.mysql.com/get/Downloads/MySQL-5.6/mysql-5.6.26.tar.gz
tar -zxvf mysql-5.6.26.tar.gz
cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mysql-5.6.26 -DWITH_INNOBASE_STORAGE_ENGINE=1 -DMYSQL_USER=mysql -DDEFAULT_CHARSET=utf8 -DDEFAULT_COLLATION=utf8_general_ci
make
make install
```

**注意，对于用Tars的c++进行开发编译的服务，mysql建议采用静态库，源码编译，避免所有服务器都要安装mysql的动态库。**

对于在服务器用Tars的c++进行开发编译服务代码而言，经过上面步骤就可以进行编译安装Tars开发框架了。

若要是搭建Tars框架的运行环境，需要以下步骤，切换至root用户，对mysql进行配置。

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

补充了上面删除my.cnf的命令 rm -rf /etc/my.cnf

给一个my.cnf配置实例：

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

**注意将bind-address改为部署机器的IP**

启动mysql

```text
service mysql start
chkconfig mysql on
```

结束mysql

```text
service mysql stop
```

添加mysql的bin路径

```text
vim /etc/profile
PATH=$PATH:/usr/local/mysql/bin
export PATH
```

修改root密码\(采用root密码\)

```text
./bin/mysqladmin -u root password 'root@appinside'
./bin/mysqladmin -u root -h ${主机名} password 'root@appinside'
```

**注意${主机名}需要修改成自身机器的名称或者IP，可以通过查看/etc/hosts**

添加mysql库路径

```text
vim /etc/ld.so.conf
/usr/local/mysql/lib/
ldconfig
```

mysql主从配置可以参考网上教程

master赋予权限:

```text
GRANT REPLICATION SLAVE ON *.* to 'mysql-sync'@'%' identified by 'sync@appinside'
```

slave设置主备同步项

```text
change master to master_host='${备机Ip}',master_user='mysql-sync',master_password='sync@appinside' ,master_log_file='iZ94orl0ix4Z-bin.000004',master_log_pos=611;
stop slave
start slave
show master status\G;
show slave status\G;
```

**注意${备机Ip}需要修改成备机数据库的Ip**

## 3 yum安装 <span id="chapter-3"></span>

在mysql 5.7版本之后删除了源码中的mysql\_install\_db.sh安装脚本，因此上述方法不适用。 yum安装相对便捷，但是没办法实现自定义安装。如果对自定义安装有需求的请使用源码安装。

**从yum repository安装MySQL**

```text
wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
yum -y install mysql57-community-release-el7-10.noarch.rpm
yum -y install mysql-community-server
yum -y install  mysql-devel
```

这样mysql就安装好了 其中wget中的库地址可以根据需要替换版本号 如果wget无法安装，可以尝试按照下述步骤将mysql repository添加到本地服务，然后再重新执行上述命令

```text
sudo yum localinstall https://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
```

**设置MySQL**

启动mysql并查看运行状态

```text
systemctl start  mysqld.service
systemctl status mysqld.service
```

mysql开始运行并通过3306端口进行连接。

通过yum安装mysql的话root账号会默认设置一个密码，可以通过下面的方式获得。

```text
grep "password" /var/log/mysqld.log
```

使用这个密码登陆root的账户。mysql要求必须更改密码才能做数据库操作。 如果你安装的mysql版本是5.7及以上，mysql的密码设置有安全性要求\(长度、大小写、特殊字符\)，以下两种方式可以选择

1. 设置满足要求的密码
2. 通过下面的方式降低密码安全性规则

```text
mysql> set global validate_password_policy=0;
mysql> set global validate_password_length=1;
```

修改之后只有6个字符的最小长度限制 接着修改密码

```text
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY '${your password}';
```

**mysql主从支持参照上一节**

## 4 docker安装mysql <span id="chapter-4"></span>

服务器安装docker请参考[docker](docker.md)

使用docker安装来安装mysql\(目前只考虑了linux上, 时间和本机同步\)

```bash
docker pull mysql:5.6
docker run --name mysql --net=host -e MYSQL_ROOT_PASSWORD='root@appinside' -d -p 3306:3306 \
        -v/etc/localtime:/etc/localtime \
        -v /data/mysql-data:/var/lib/mysql mysql:5.6
```

**注意:--net-host表示Docker网络和本机一样**

