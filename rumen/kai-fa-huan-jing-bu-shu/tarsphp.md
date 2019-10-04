# TarsPHP

## 安装相关

1. 安装前请先按照此页面介绍准备好软件[https://github.com/TarsCloud/Tars/tree/master/build](https://github.com/TarsCloud/Tars/tree/master/build)
2. 建议先阅读下文件内容 [https://github.com/TarsCloud/Tars/blob/master/build/install.sh](https://github.com/TarsCloud/Tars/blob/master/build/install.sh) （因实际上有些软件依赖在某些脚本中会自动安装，避免重复劳动），熟悉大致的脚本安装过程
3. 参考前两部分内容，接下来按照安装说明进行安装[https://github.com/TarsCloud/Tars/blob/master/Install.md](https://github.com/TarsCloud/Tars/blob/master/Install.md)
4. 注意：请使用服务器内网IP，不要使用127.0.0.1
5. 安装中遇到的常见问题和注意事项，可阅读[https://github.com/TarsCloud/Tars/blob/master/Install\_faq.md](https://github.com/TarsCloud/Tars/blob/master/Install_faq.md)

### 安装环境依赖

| 软件 | 软件要求 | 备注 |
| :--- | :--- | :--- |
| linux内核版本: | 2.6.18及以上版本（操作系统依赖） | centos6.9 |
| gcc版本: | 4.8.2及以上版本、glibc-devel（c++语言框架依赖） |  |
| bison工具版本: | 2.5及以上版本（c++语言框架依赖） |  |
| flex工具版本: | 2.5及以上版本（c++语言框架依赖） |  |
| cmake版本： | 2.8.8及以上版本（c++语言框架依赖） |  |
| resin版本： | 4.0.49及以上版本（web管理系统依赖） | 下载设置软链 |
| Java JDK版本： | java语言框架（最低1.6），web管理系统（最低1.8） | 安装可能会报错，注意环境变量设置 |
| Maven版本： | 2.2.1及以上版本（web管理系统、java语言框架依赖） |  |
| mysql版本: | 4.1.17及以上版本（框架运行依赖） | 注意账号和权限设置 |
| rapidjson版本: | 1.0.2版本（c++语言框架依赖） | 可以不用单独安装，安装过程会自行下载 |

### 安装Tars核心服务

1. 错误修正：

tars\_install.sh和monitor.sh文件安装后没有的话，可手工复制文件： 从源码/cpp/build/framework/deploy/tars\_install.sh、cpp/build/framework/deploy/tarsnode/util/monitor.sh复制

2. 其他按照文档说明和顺序照着执行。

### 安装web管理界面

1. 文档中“2.2. java语言框架开发环境安装”部分，构建web工程项目可直接到源码web目录下修改app.config.properties和tars.conf配置，然后直接打包即可！
2. 运行/usr/local/resin/bin/resin.sh start启动web管理界面，正常的话会看到初始化界面，默认配置好三个服务。

### 服务启动

```text
service mysql start
/usr/local/app/tars/tars_install.sh
/usr/local/app/tars/tarspatch/util/init.sh
/usr/local/app/tars/tarsnode/bin/tarsnode --config=/usr/local/app/tars/tarsnode/conf/tarsnode.conf
/usr/local/resin/bin/resin.sh start

```

正常启动，将会看到如下进程：

```text
[root@centos data]# ps -ef|grep tars
root       5495      1  0 11:35 pts/0    00:00:03 /usr/local/app/tars/tarsregistry/bin/tarsregistry --config=/usr/local/app/tars/tarsregistry/conf/tarsregistry.conf
root       5504      1  0 11:35 pts/0    00:00:01 /usr/local/app/tars/tarsAdminRegistry/bin/tarsAdminRegistry --config=/usr/local/app/tars/tarsAdminRegistry/conf/adminregistry.conf
root       5514      1  0 11:35 pts/0    00:00:02 /usr/local/app/tars/tarsconfig/bin/tarsconfig --config=/usr/local/app/tars/tarsconfig/conf/tarsconfig.conf
root       5523      1  0 11:35 pts/0    00:00:01 /usr/local/app/tars/tarspatch/bin/tarspatch --config=/usr/local/app/tars/tarspatch/conf/tarspatch.conf
root       5610      1  0 11:35 ?        00:00:00 rsync --address=192.168.222.132 --daemon --config=/usr/local/app/tars/tarspatch/conf/rsync.conf
root       5621      1  2 11:35 ?        00:00:18 /usr/local/app/tars/tarsnode/bin/tarsnode --config=/usr/local/app/tars/tarsnode/conf/tarsnode.conf
root       6021   5621  0 11:36 ?        00:00:05 /usr/local/app/tars/tarsnode/data/tars.tarslog/bin/tarslog --config=/usr/local/app/tars/tarsnode/data/tars.tarslog/conf/tars.tarslog.config.conf
root       6022   5621  0 11:36 ?        00:00:05 /usr/local/app/tars/tarsnode/data/tars.tarsnotify/bin/tarsnotify --config=/usr/local/app/tars/tarsnode/data/tars.tarsnotify/conf/tars.tarsnotify.config.conf
root       6054   5621  0 11:36 ?        00:00:03 /usr/local/app/tars/tarsnode/data/tars.tarsqueryproperty/bin/tarsqueryproperty --config=/usr/local/app/tars/tarsnode/data/tars.tarsqueryproperty/conf/tars.tarsqueryproperty.config.conf
root       6108   5621  0 11:36 ?        00:00:03 /usr/local/app/tars/tarsnode/data/tars.tarsquerystat/bin/tarsquerystat --config=/usr/local/app/tars/tarsnode/data/tars.tarsquerystat/conf/tars.tarsquerystat.config.conf
root       6163   5621  0 11:37 ?        00:00:04 /usr/local/app/tars/tarsnode/data/tars.tarsstat/bin/tarsstat --config=/usr/local/app/tars/tarsnode/data/tars.tarsstat/conf/tars.tarsstat.config.conf
root       7097   5621  0 11:42 ?        00:00:01 /usr/local/app/tars/tarsnode/data/tars.tarsproperty/bin/tarsproperty --config=/usr/local/app/tars/tarsnode/data/tars.tarsproperty/conf/tars.tarsproperty.config.conf
```

### 安装基础服务

将安装文档中“4.1. 框架基础服务打包”相关基础包打包然后通过管理界面配置和上传发布

发布时需要在web管理的“运维管理模块”配置，具体参考“4.4. 安装框架普通基础服务”部分

### 其他

* php语言相关 建议安装php7+swoole2

#### 安装PHP

```text
项目地址 https://github.com/php/php-src 
```

#### 安装swoole

```text
项目地址 https://github.com/swoole/swoole-src
编译安装 https://wiki.swoole.com/wiki/page/6.html
PECL一键下载安装
    pecl install swoole
```

* php需要安装扩展，请在 [https://github.com/TarsPHP/tars-extension](https://github.com/TarsPHP/tars-extension) 源码目录进行编译，然后将扩展加入到php.ini中

#### 安装tarsphp扩展

项目地址 [https://github.com/TarsPHP/tars-extension](https://github.com/TarsPHP/tars-extension)

```text
git clone https://github.com/TarsPHP/tars-extension.git
cd tars-extension
sudo phpize 
sudo ./configure
sudo make 
sudo make install
```

在php.ini里面加入extension=phptars.so





