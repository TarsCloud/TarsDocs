# TarsNodejs 常见问题

### 为什么egg框架开发的nodejs应用在Tars平台上发布和重启偶尔会失败？

> * egg框架如果不是使用的single mode，会开启一个独立的worker进程来监听端口。当Tars发布或重启时，egg启动的端口监听进程并不会被杀死，导致再次启动时端口被占用，监听失败，服务启动失败。
> * Tars Nodejs的进程管理器node-agent包含了Cluster子进程、异常监控、进程拉起、监控上报等功能。普通的Nodejs程序只要发布上Tars平台自然就拥有了以上特性，无需再自己做进程管理。


