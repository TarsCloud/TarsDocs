# TarsGo 开发环境部署

## 目录

> * [环境依赖](#环境依赖)
> * [安装](#安装)

## 环境依赖

要求 Go `1.9.x` 或以上版本(推荐 `1.14` 以上的版本)，请查看 [https://go.dev/doc/install](https://go.dev/doc/install)

## 安装

### Go安装

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

### tarsgo、tars2go安装

#### 查看go版本

```bash
go version
```

#### go [get, install] 命令

- 如果 go 版本在 1.16 以前，则使用如下命令安装：
    ```sh
    GO111MODULE=on go get -u github.com/TarsCloud/TarsGo/tars/tools/tarsgo@latest
    GO111MODULE=on go get -u github.com/TarsCloud/TarsGo/tars/tools/tars2go@latest
    ```

- 如果 go 版本在 1.16 及以后，则使用如下命令安装：

    ```sh
    go install github.com/TarsCloud/TarsGo/tars/tools/tarsgo@latest
    go install github.com/TarsCloud/TarsGo/tars/tools/tars2go@latest
    ```

#### 验证

```sh
tarsgo -v
tars2go
```
