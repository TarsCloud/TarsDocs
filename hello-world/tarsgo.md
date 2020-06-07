# TarsGo 快速入门

## 服务编写

### 创建服务

运行create\_tars\_server.sh脚本，自动创建服务必须的文件, 执行过程中如果出现语法错误尝试使用`dos2unix create_tars_server.sh`进行转码。

```text
sh $GOPATH/src/github.com/TarsCloud/TarsGo/tars/tools/create_tars_server.sh [App] [Server] [Servant]
例如： 
sh $GOPATH/src/github.com/TarsCloud/TarsGo/tars/tools/create_tars_server.sh TestApp HelloGo SayHello
```

命令执行后将生成代码至GOPATH中，并以`APP/Server`命名目录，生成代码中也有提示具体路径。

```text
[root@1-1-1-1 ~]# sh $GOPATH/src/github.com/TarsCloud/TarsGo/tars/tools/create_tars_server.sh TestApp HelloGo SayHello
[create server: TestApp.HelloGo ...]
[mkdir: $GOPATH/src/TestApp/HelloGo/]
>>>Now doing:./start.sh >>>>
>>>Now doing:./Server.go >>>>
>>>Now doing:./Server.conf >>>>
>>>Now doing:./ServantImp.go >>>>
>>>Now doing:./makefile >>>>
>>>Now doing:./Servant.tars >>>>
>>>Now doing:client/client.go >>>>
>>>Now doing:vendor/vendor.json >>>>
# runtime/internal/sys
>>> Great！Done! You can jump in $GOPATH/src/TestApp/HelloGo
>>> 当编辑完成Tars文件后，使用如下自动生成go文件
>>>       $GOPATH/bin/tars2go *.tars
```

### 定义接口文件

接口文件定义请求方法以及参数字段类型等，有关接口定义文件说明参考tars\_tup.md

为了测试我们定义一个echoHello的接口，客户端请求参数是短字符串如 "tars"，服务响应"hello tars".

```text
# cat $GOPATH/src/TestApp/HelloGo/SayHello.tars 
module TestApp{
interface SayHello{
     int echoHello(string name, out string greeting); 
   };
};
```

**注意**： 参数中**out**修饰关键字标识输出参数。

### 服务端开发

首先把tars协议文件转化为Golang语言形式

```text
$GOPATH/bin/tars2go SayHello.tars
```

现在开始实现服务端的逻辑：客户端传来一个名字，服务端回应hello name。

```text
cat $GOPATH/src/TestApp/HelloGo/SayHelloImp.go
```

```text
package main

type SayHelloImp struct {
}

func (imp *SayHelloImp) EchoHello(name string, greeting *string) (int32, error) {
     *greeting = "hello " + name
     return 0, nil
}
```

**注意**： 这里函数名要大写，Go语言方法导出规定。

编译main函数，初始代码以及有tars框架实现了。

cat $GOPATH/src/TestApp/HelloGo/HelloGo.go

```text
package main

import (
	"github.com/TarsCloud/TarsGo/tars"

	"TestApp"
)

func main() { //Init servant
	imp := new(SayHelloImp)                                    //New Imp
	app := new(TestApp.SayHello)                                 //New init the A JCE
	cfg := tars.GetServerConfig()                               //Get Config File Object
	app.AddServant(imp, cfg.App+"."+cfg.Server+".SayHelloObj") //Register Servant
	tars.Run()
}
```

编译生成可执行文件，并打包发布包。

```text
cd $GOPATH/src/TestApp/HelloGo/ && make && make tar
```

将生成可执行文件HelloGo和发布包HelloGo.tgz

### 客户端开发

```text
package main

import (
        "fmt"
        "github.com/TarsCloud/TarsGo/tars"

        "TestApp"
)

//只需初始化一次，全局的
var comm *tars.Communicator
func main() {
        comm = tars.NewCommunicator()
        obj := "TestApp.HelloGo.SayHelloObj@tcp -h 127.0.0.1 -p 3002 -t 60000"
        app := new(TestApp.SayHello)
        /*
         // if your service has been registered at tars registry
         comm = tars.NewCommunicator()
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

* TestApp依赖是tars2go生成的代码。
* obj指定服务端地址端口，如果服务端未在主控注册，则需要知道服务端的地址和端口并在Obj中指定，在例子中，协议为TCP，服务端地址为本地地址，端口为3002。如果有多个服务端，则可以这样写`TestApp.HelloGo.SayHelloObj@tcp -h 127.0.0.1 -p 9985:tcp -h 192.168.1.1 -p 9983`这样请求可以分散到多个节点。

  如果已经在主控注册了服务，则不需要写死服务端地址和端口，但在初始化通信器时需要指定主控的地址。

* com通信器，用于与服务端通信。

编译测试

```text
# go build client.go
# ./client
ret:  0 resp:  hello tars 
```

### HTTP 服务开发

tarsgo支持http服务，按照上面的步骤创建好服务，tarsgo中处理http请求是在GO原生中的封装，所以使用很简单。

```text
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

另外还可以直接调用其他tars服务，调用方式和“客户端开发”提到一样。



