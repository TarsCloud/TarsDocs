# 与thinkphp结合使用



## 简介

让thinkphp框架欢快的跑在tars里面

* 支持打包发布
* 支持服务存活上报（服务注册）

## 相关项目

* thinkphpInTars \([https://github.com/dpp2009/thinkphpInTars](https://github.com/dpp2009/thinkphpInTars)\)
* think-swoole \([https://github.com/top-think/think-swoole](https://github.com/top-think/think-swoole)\)   
* TARSPHP \([https://github.com/TarsPHP](https://github.com/TarsPHP)\)

## 使用

* Clone 项目 \([https://github.com/dpp2009/thinkphpInTars](https://github.com/dpp2009/thinkphpInTars)\)
* 安装依赖 `composer install`
* 打包 `composer run-script deploy`
* 将打包完成的文件上传tars平台

## 配置说明

* 服务名需要在 ./tars/tars.proto.php 里面正确配置
* 不使用tars平台下发的端口号,按照thinkphp的方式配置

