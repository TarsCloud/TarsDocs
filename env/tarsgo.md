# TarsGo 开发环境部署

## 目录

> * [环境依赖](#环境依赖)
> * [安装](#安装)

## 环境依赖

要求 Go `1.9.x` 或以上版本(推荐 `1.14` 以上的版本)，请查看 https://go.dev/doc/install

## 安装

下面以 Go `1.14.x` 为例:

安装 go (比如安装目录: `/usr/local/go`), 设置好 `GOROOT`, `GOPATH`, 比如, Linux 下:

```sh
export GOROOT=/usr/local/go  #设置为go安装的路径
export GOPATH=/root/gocode   #GOPATH
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
```

如果在国内, 可以设置 go 代理:

```sh
go env -w GOPROXY=https://goproxy.cn,direct
```

另外请设置 go 模式为:

```sh
go env -w GO111MODULE=auto
```

安装 `tarsgo` 创建项目脚手架:

```sh
# < go 1.17 
go get -u github.com/TarsCloud/TarsGo/tars/tools/tarsgo
# >= go 1.17
go install github.com/TarsCloud/TarsGo/tars/tools/tarsgo@latest
```

安装 `tars2go` 工具:

```sh
# < go 1.17 
go get -u github.com/TarsCloud/TarsGo/tars/tools/tars2go
# >= go 1.17
go install github.com/TarsCloud/TarsGo/tars/tools/tars2go@latest
```
