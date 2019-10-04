# pb2tarsgo

## 用法

```text
go get github.com/TarsCloud/TarsGo/tars
go get  github.com/golang/protobuf/{proto,protoc-gen-go}
install protoc
```

* Add tarsrpc plugin for protoc-gen-go

```text
	cd $GOPATH/src/github.com/golang/protobuf/protoc-gen-go &&  cp -r $GOPATH/src/github.com/TarsCloud/TarsGo/tars/tools/pb2tarsgo/protoc-gen-go/{link_tarsrpc.go, tarsrpc} .
	go build .
	cp protoc-gen-go $GOPATH/bin
	export PATH=$PATH:$GOPATH/bin
```

## 示例

* proto 文件

```text
syntax = "proto3";
package helloworld;

// The greeting service definition.
service Greeter {
  // Sends a greeting
  rpc SayHello (HelloRequest) returns (HelloReply) {}
}

// The request message containing the user's name.
message HelloRequest {
  string name = 1;
}

// The response message containing the greetings
message HelloReply {
  string message = 1;
}
```

* 生成代码

```text
protoc --go_out=plugins=tarsrpc:. helloworld.proto
```

* 服务端

```text
package main

import (
    "github.com/TarsCloud/TarsGo/tars"
    "helloworld" 
)

type GreeterImp  struct {
}

func (imp *GreeterImp) SayHello(input helloworld.HelloRequest)(output helloworld.HelloReply, err error) {
    output.Message = "hello" +  input.GetName() 
    return output, nil 
}

func main() { //Init servant

    imp := new(GreeterImp)                                    //New Imp
    app := new(helloworld.Greeter)                            //New init the A JCE
    cfg := tars.GetServerConfig()                              //Get Config File Object
    app.AddServant(imp, cfg.App+"."+cfg.Server+".GreeterTestObj") //Register Servant
    tars.Run()
}
	
```

* 客户端

```text
package main

import (
    "fmt"
    "github.com/TarsCloud/TarsGo/tars"
    "helloworld"
)

func main() {
    comm := tars.NewCommunicator()
    obj := fmt.Sprintf("StressTest.HelloPbServer.GreeterTestObj@tcp -h 127.0.0.1  -p 10014  -t 60000")
    app := new(helloworld.Greeter)
    comm.StringToProxy(obj, app)
    input := helloworld.HelloRequest{Name: "sandyskies"}
    output, err := app.SayHello(input)
    if err != nil {
        fmt.Println("err: ", err)
    }   
    fmt.Println("result is:", output.Message)
}
```

