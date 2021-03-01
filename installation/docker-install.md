# Docker环境安装

## 目录

> * [介绍](#chapter-1)
> * [如何安装Docker](#chapter-2)

## 1 介绍 <span id="chapter-1"></span>

由于后续章节中会涉及到docker的使用, 本节简要介绍如何安装docker环境.

docker的详细资料, 请上docker官网了解.

如果不采用docker部署Tars环境, 请忽略该步骤.


## 2 如何安装Docker <span id="chapter-2"></span>

**本文只介绍在centos中安装docker环境**

Centos上安装Docker如下:

```bash
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
```

