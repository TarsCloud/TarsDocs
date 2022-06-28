# TarsGo 快速入门

## 前置条件

- [安装Tars框架](../installation/README.md)
- 安装Golang，建议1.17及以上版本

## 服务编写

### 安装依赖

```shell
# < go 1.16
go get -u github.com/TarsCloud/TarsGo/tars/tools/tarsgo
go get -u github.com/TarsCloud/TarsGo/tars/tools/tars2go
# >= go 1.16
go install github.com/TarsCloud/TarsGo/tars/tools/tarsgo@latest
go install github.com/TarsCloud/TarsGo/tars/tools/tars2go@latest
```

### 创建服务

运行`tarsgo`脚手架，自动创建使用`make`构建服务必须的文件。如要使用`cmake`，可参考[这里](../dev/tarsgo/cmake.md)。

```text
tarsgo make App Server Servant GoModuleName
例如：
tarsgo make TestApp HelloGo SayHello github.com/Tars/test
```

命令执行后将生成代码当前目录中以`Server`命名目录，生成代码中也有提示具体路径。

```text
[root@1-1-1-1 ~]# tarsgo make TestApp HelloGo SayHello github.com/Tars/test
🚀 Creating server TestApp.HelloGo, layout repo is https://github.com/TarsCloud/TarsGo.git, please wait a moment.

已经是最新的。

go: creating new go.mod: module github.com/Tars/test
go: to add module requirements and sums:
	go mod tidy

CREATED HelloGo/SayHello.tars (171 bytes)
CREATED HelloGo/SayHello_imp.go (620 bytes)
CREATED HelloGo/client/client.go (444 bytes)
CREATED HelloGo/config.conf (967 bytes)
CREATED HelloGo/debugtool/dumpstack.go (412 bytes)
CREATED HelloGo/go.mod (37 bytes)
CREATED HelloGo/main.go (517 bytes)
CREATED HelloGo/makefile (193 bytes)
CREATED HelloGo/scripts/makefile.tars.gomod (4181 bytes)
CREATED HelloGo/start.sh (56 bytes)

>>> Great！Done! You can jump in HelloGo
>>> Tips: After editing the Tars file, execute the following cmd to automatically generate golang files.
>>>       /root/gocode/bin/tars2go *.tars
$ cd HelloGo
$ ./start.sh
🤝 Thanks for using TarsGo
📚 Tutorial: https://tarscloud.github.io/TarsDocs/
```

### 定义接口文件

接口文件定义请求方法以及参数字段类型等，有关接口定义文件说明参考 tars_tup.md

为了测试我们定义一个 echoHello 的接口，客户端请求参数是短字符串如 "tars"，服务响应"hello tars".

```go
# cat HelloGo/SayHello.tars
module TestApp{
    interface SayHello{
        int echoHello(string name, out string greeting);
    };
};
```

**注意**： 参数中**out**修饰关键字标识输出参数。

### 服务端开发

首先把 tars 协议文件转化为 Golang 语言形式

```bash
tars2go  -outdir=tars-protocol -module=github.com/Tars/test SayHello.tars
```

现在开始实现服务端的逻辑：客户端传来一个名字，服务端回应 hello name。

```bash
cat HelloGo/SayHello_imp.go
```

```go
package main
import "context"
type SayHelloImp struct {
}

func (imp *SayHelloImp) EchoHello(ctx context.Context, name string, greeting *string) (int32, error) {
    *greeting = "hello " + name
    return 0, nil
}
```

**注意**： 这里函数名要大写，Go 语言方法导出规定。

编译 main 函数，初始代码以及有 tars 框架实现了。

```bash
cat HelloGo/main.go
```

```go
package main

import (
	"github.com/TarsCloud/TarsGo/tars"

	"github.com/Tars/test/tars-protocol/TestApp"
)

func main() {
	// Get server config
	cfg := tars.GetServerConfig()

	// New servant imp
	imp := new(SayHelloImp)
	// New servant
	app := new(TestApp.SayHello)
	// Register Servant
	app.AddServantWithContext(imp, cfg.App+"."+cfg.Server+".SayHelloObj")

	// Run application
	tars.Run()
}
```

编译生成可执行文件，并打包发布包。

```bash
cd HelloGo && make && make tar
```

将生成可执行文件 HelloGo 和发布包 HelloGo.tgz

### 客户端开发

```go
package main

import (
	"fmt"

	"github.com/TarsCloud/TarsGo/tars"

	"github.com/Tars/test/tars-protocol/TestApp"
)

//只需初始化一次，全局的
var comm *tars.Communicator
func main() {
    comm = tars.NewCommunicator()
    obj := "TestApp.HelloGo.SayHelloObj@tcp -h 127.0.0.1 -p 10015 -t 60000"
    app := new(TestApp.SayHello)
    /*
     // if your service has been registered at tars registry
     obj := "TestApp.HelloGo.SayHelloObj"
     // tarsregistry service at 192.168.1.1:17890
     comm.SetProperty("locator", "tars.tarsregistry.QueryObj@tcp -h 192.168.1.1 -p 17890")
    */

    comm.StringToProxy(obj, app)
    reqStr := "tars"
    var resp string
    ret, err := app.EchoHello(reqStr, &resp)
    if err != nil {
        fmt.Println(err)
        return
    }
    fmt.Println("ret: ", ret, "resp: ", resp)
}
```

- TestApp 依赖是 tars2go 生成的代码。
- obj 指定服务端地址端口，如果服务端未在主控注册，则需要知道服务端的地址和端口并在 Obj 中指定，在例子中，协议为 TCP，服务端地址为本地地址，端口为 3002。如果有多个服务端，则可以这样写`TestApp.HelloGo.SayHelloObj@tcp -h 127.0.0.1 -p 9985:tcp -h 192.168.1.1 -p 9983`这样请求可以分散到多个节点。

  如果已经在主控注册了服务，则不需要写死服务端地址和端口，但在初始化通信器时需要指定主控的地址。

- com 通信器，用于与服务端通信。

编译测试

```text
# go build client/client.go
# ./client/client
ret:  0 resp:  hello tars
```

### HTTP 服务开发

tarsgo 支持 http 服务，按照上面的步骤创建好服务，tarsgo 中处理 http 请求是在 GO 原生中的封装，所以使用很简单。

```go
package main

import (
	"net/http"
	"github.com/TarsCloud/TarsGo/tars"
)

func main() {
	mux := &tars.TarsHttpMux{}
	mux.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		w.Write([]byte("Hello tars"))
	})
    cfg := tars.GetServerConfig()
	tars.AddHttpServant(mux, cfg.App+"."+cfg.Server+".HttpSayHelloObj") //Register http server
	tars.Run()
}
```

另外还可以直接调用其他 tars 服务，调用方式和“客户端开发”提到一样。
