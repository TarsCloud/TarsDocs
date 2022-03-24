# protobuf 协议支持文档

## tars 支持 protobuf service 描述文件

如果当你了解到 tars 的时候，你已经有不少已有业务采用了 protobuf 协议；如果想要把这些业务迁移到 tars，你还需要手动把 proto 文件翻译成 tars 文件,非常麻烦而且容易出错。 现在 tars 使用 protoc 的插件机制，提供了对 proto 文件的直接支持，能够生成 tars rpc 相关代码，使得迁移平滑省心。

### 使用方法

#### 1. 准备 proto 文件

proto 文件的语法是不限制的，你可以使用 proto2 或 proto3； 但注意，一定加上**option cc_generic_services=false;** 因为我们的目标就是不使用 protoc 生成的 pb rpc 接口，而是要用 tars 插件接管，生成符合 tars 框架的 rpc 接口. 一个 proto 文件的示例如下:

```
syntax = "proto2";

option cc_generic_services=false;

package TestApp;

message PbRequest {
    required int32 a = 1;
    required int32 b = 2;
}

message PbResponse {
    required int32 c = 1;
}

service Hello {
    rpc add(PbRequest) returns (PbResponse) {
    }
}
```

#### 2. 直接执行 make 即可

因为调用 tars pb 插件的语句，已经内置在框架 makefile.tars 文件中。 由于 protoc 默认生成的文件名带有.pb.h 后缀,tars 插件也遵循这个命名规则，生成的文件后缀是.tars.h
