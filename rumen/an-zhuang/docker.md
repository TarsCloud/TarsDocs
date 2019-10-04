# Docker

## Docker 安装

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

## 获取镜像

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

## 参数介绍

MYSQL\_ROOT\_PASSWORD: 数据库的 root 密码

DBIP: 数据库主机IP

DBPort: 数据库端口号

DBUser: 数据库管理员名

DBPassword: 数据库管理员密码

## 扩充 tarsnode 节点

请用你的 tarsregistry IP 替换 ${registry\_ip}，然后运行以下命令：

```text
docker pull tarsdocker/tarsnode
docker run -d -it --name tarsnode -e MASTER=${registry_ip} -v /data:/data tarsdocker/tarsnode
```

注意：你可以用以下命令检查 ${registry\_ip} ：

```text
docker inspect --format='{{.NetworkSettings.IPAddress}}' tars
```

