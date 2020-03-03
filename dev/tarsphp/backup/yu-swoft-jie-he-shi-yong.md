# 与Swoft结合使用



## 简介

让swoft框架欢快的跑在tars里面

* 支持打包发布
* 支持服务存活上报（服务注册）

## 相关项目

* SwoftInTars \([https://github.com/dpp2009/swoftInTars](https://github.com/dpp2009/swoftInTars)\)
* SWOFT \([https://github.com/swoft-cloud/swoft](https://github.com/swoft-cloud/swoft)\)   
* TARSPHP \([https://github.com/TarsPHP](https://github.com/TarsPHP)\)
* TARSPHP DOCKER \([https://github.com/tangramor/docker-tars](https://github.com/tangramor/docker-tars)\)

## 使用

* Clone 项目 \([https://github.com/dpp2009/swoftInTars](https://github.com/dpp2009/swoftInTars)\)
* 安装依赖 `composer install`
* 打包 `composer run-script deploy`
* 将打包完成的文件上传tars平台

## 配置说明

* 服务名需要在 ./tars/tars.proto.php （appName&serverName） 和 ./src/.env （PNAME） 里面正确配置
* swoft的端口号不使用tars平台下发的端口号（因为swoft可以支持多端口多协议）

## 重启问题

* 使用docker-tars，pcre可能没开启。运行php --ri swoole，看是否存在pcre =&gt; enabled。不存在运行yum install pcre-devel，再重新编译swoole！！

