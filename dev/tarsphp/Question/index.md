# 常见问题

## 这个框架必须和swoole绑定吗
tars-server必须和swoole绑定。但是如果仅使用tars-client，就不需要了。

## client和server有什么分别
两者承担的职责不同，项目结构也不同的。client只是一个调用方，server是一个服务提供者。

## 只用tars-php作为客户端，不用swoole可以吗
可以的。只有tars-server是必需swoole的，tars-client没有限制。

## 主调上报一定要存储swoole_table吗
用户可以自己实现`Tars\monitor\contract\StoreCacheInterface`接口实现其他的存储方式,可以参考[示例代码](!https://github.com/TarsPHP/TarsPHP/tree/master/examples/tars-http-server)。

## 自动生成代码时报错
有用户在执行`php ../src/vendor/phptars/tars2php/src/rc/tars2php.php ./t ./ ./tars.proto.php` 时报错：
>mkdir: cannot create directory ‘../src/servant/Common’: No such file or directory
  
这个是目录权限问题。可以手动创建*../src/servant*，或者修改目录权限。

## 手动启动脚本在哪个位置
在服务根目录下的*./bin*目录下

## 手动脚本重启时，设置状态不会变更为active
请尽量在平台上操作重启服务，避免手动脚本重启。对于希望做发布流水自动化的，可以通过直接调用重启服务的http请求。

## 如何部署多个注册中心的
部署两个register，重启业务服务后，conf文件会自动更新。

## 多机器部署TarsPHP时，第二台机器的安装跟第一台完全一样吗，还是说第二台机器只需要安装部分服务？
在第二台机器上只需要安装核心基础服务和tarsnode，其他的比如mysql不用安装

## composer下载包失败
```
composer require phptars/tars-server
```
用用户碰到过上述命令执行失败。这种问题首先请确认本地composer没有问题。然后根据失败的msg判断具体原因。  
曾经出现过的问题是国内源没有同步，导致版本不一致，切换源之后问题解决。

## 配置中心给服务发送tcp请求，http服务器怎么办
配置中心的推送对于http服务器是不能使用的，对于http服务器，可以指定配置文件，启停的时候会拉取。

## tarslog能和elk结合吗
tarslog组件可以通过增加ElasticSearchHandler来实现到elk的日志输出，预留了口子。

## win跑tars，发布服务失败
如果是类似`pid is not digit`的问题，则是因为windows获取pid的方式和linux不同，启动脚本需要用powershell修改才能兼容，类似:    
`ps -eopid,cmd | grep "$bin"| grep "tarsnode" |  grep -v "grep"|grep -v "sh" |awk '{print $1}'`

## 配置文件中的dispatch_mode一定要选2吗
2表示一个链接发来的数据只会被一个worker处理。这个配置可以按需调整，具体参考[swoole文档](!https://wiki.swoole.com/wiki/page/277.html)

## 缺少tars扩展
类似下面这种问题，是由于没有安装tars扩展导致的。安装请参考php环境搭建
>PHP Fatal error:  Class 'TARS_Struct' not found in /usr/local/app/tars/tarsnode/data/PHPTest.PHPServer/bin/src/vendor/phptars/tars-report/src/ServerInfo.php on line 5

>Fatal error: Uncaught Error: Class 'TUPAPI' not found in D:\www\tars-client\tests\vendor\phptars\tars-registry\src\client\TUPAPIWrapperRegistry.php:369
