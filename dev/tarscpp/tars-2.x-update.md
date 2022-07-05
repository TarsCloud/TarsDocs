# TarsCpp 2.x 变化

## 1. 概述

TarsCpp2.0 针对 1.x 版本做了重大的更新, 主要包括以下几个方面:

- 多平台支撑
- 删除不常用功能
- 完善协议解析器
- 各种扩展功能的 bug 修改

  2.x 的主要的修改如下:

- 多平台支撑, TarsCpp2.0 完成 linux, mac, windows(>=WIN7)三个 OS 系统的的支撑
- 依赖包自动拉取: 如果开启 mysql, ssl, http2 的支持, cmake 会自动拉取依赖包并编译(linux, mac), windows 目前还有待完善
- 去掉 promise 功能, 建议使用协程, 协程已经完成多平台, 多 cpu 的适配(如果有碰到问题, 请提交 issues)
- 去掉 TC_Atomic, 请使用 std::atomic
- 修改协议解析器的接口, 大幅度提高网络解析的包的效率, [参见第三方协议](tars-thirdparty-protocol.md), 注意:接口不再向下兼容
- 修改 TC_ThreadPool 的实现, 使用更加方便
- 完善 ssl 支持, 减少内存 copy 次数提高效率, 以及服务端和客户端可以指定不同端口采用不同的证书
- 完善 http2 的支持, 修复 bug
- 修改鉴权的加密算法为 3des, 为后续多平台支持做基础
- 完善各种 demo, 参见 cpp/examples

[TarsCpp 的编译请参见](./tars-compiler.md)
