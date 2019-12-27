# 目录
> * [环境准备](#chapter-1)

# 1 <a id="chapter-1"></a>环境准备

Golang环境准备，tarsgo要求golang版本在1.9.x及以上。

安装tars： `go get github.com/TarsCloud/TarsGo/tars`

编译tars协议转Golang工具：

```text
cd $GOPATH/src/github.com/TarsCloud/TarsGo/tars/tools/tars2go && go build . 

cp tars2go $GOPATH/bin
```

检查下GOPATH路径下tars是否安装成功。

