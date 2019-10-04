# TarsJava 常见问题

### Tars java开发环境如何搭建？

> * 参考tars\_install.md

### 如何在本地启动和开发调试tars

> * 首先在工程目录下执行 mvn tars:build -Dapp=TestApp -Dserver=HelloJavaServer -DjvmParams="-Xms1024m -Xmx1024m -Dcom.sun.management.jmxremote.ssl=false -Dcom.sun.management.jmxremote.authenticate=false -Xdebug -Xrunjdwp:transport=dt\_socket,address=9000,server=y,suspend=n"
> * 在工程目录target/tars/bin/tars\_start 启动服务

