
# 目录
> * [介绍](#chapter-1)
> * [如何安装Docker](#chapter-2)

# 1 <a id="chapter-1"></a>介绍

由于后续章节中会涉及到docker的使用, 本节简要介绍如何安装docker环境.

docker的详细资料, 请上docker官网了解.

# 2 <a id="chapter-2"></a>如何安装Docker

**本文只介绍在centos中安装docker环境**

Centos上安装Docker如下:
```sh
sudo su
yum install -y yum-utils device-mapper-persistent-data lvm2
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum install -y docker-ce 
systemctl start docker
systemctl enable docker
```

安装完毕以后, 查看Docker版本:
```sh
docker version
``