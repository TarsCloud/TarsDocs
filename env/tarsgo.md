# TarsGo 开发环境部署

## 目录

> * [环境依赖](#环境依赖)
> * [安装](#安装)

## 环境依赖

要求 Go `1.9.x` 或以上版本(推荐 `1.13` 一样的版本)，请查看 https://golang.org/doc/install

## 安装

下面以 Go `1.13.x` 为例:

安装 go (比如安装目录: `/usr/local/go`), 设置好 `GOROOT`, `GOPATH`, 比如, Linux 下:

```sh
export GOROOT=/usr/local/go  #设置为go安装的路径
export GOPATH=/root/gocode   #GOPATH
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
```

如果在国内, 可以设置 go 代理:

```sh
go env -w GOPROXY=https://goproxy.cn   
```

另外请设置 go 模式为:

```sh
go env -w GO111MODULE=auto
```

进入 `GOPATH` 目录

```sh
go get -u github.com/TarsCloud/TarsGo/tars
```

此时 TarsGo 下载到 `$GOPATH/src/github.com/TarsCloud/TarsGo/`。

如果此目录不存在 TarsGo 源码, 请检查以上步骤.

TarsGo 源码下载完毕后, 请安装 `tars2go` 工具:

```sh
go install $GOPATH/src/github.com/TarsCloud/TarsGo/tars/tools/tars2go
```
