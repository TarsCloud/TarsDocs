# TarsPHP

TARSPHP作为TARS在PHP语言的解决方案, 设计的时候主要考虑如下三个方面:

* 功能完善: 对标现有C++、JAVA、NodeJS体系功能
* 灵活: 论灵活, 谁与PHP争锋?
* 轻量: 用最轻量的设计, 点到即止, 即插即用
* 高效: 插上SWOOLE协程的翅膀, 不得不飞

## 功能完善

TARSPHP主要实现了如下功能模块:

* TARS-CLIENT
* TARS-SERVER
* TARS-EXT扩展
* TARS2PHP工具

从宏观上来讲, 使用TARSPHP你可以做这些事:

* 启动一个HTTP服务, QPS达到万级
* 启动一个二进制协议的TCP服务, QPS达到十几万级
* 启动一个定时服务, 其中可以统一管理一定数量的定时服务\(与worker数量相等\)
* 启动一个Websocket服务, 与浏览器进行ws协议的交互
* 调用其他支持TARS协议的服务\(不限语言\)

从细分模块上来讲, 你可以:

* 向TARS的本地和远程目录写日志
* 向TARS监控上报调用监控以获得图表展示
* 将TARS服务打包发布
* 拉取TARS平台的配置
* 拉取其他服务的地址
* 解析TARS平台的配置文件

## 灵活

从灵活性上来讲, TARSPHP做了很多优化。 以TARS-CLIENT为例, 它的使用者既可以是后台系统, 比如Laravel。 也可以是SWOOLE同步的客户端, 更可以是SWOOLE协程的客户端。 这三种情况, 兼顾了不同的应用场景, 使用者是需要按需配置即可。

除此之外, TARS-LOG模块, 既支持本地的日志, 也支持远程的日志, 使用者也可以灵活的选择。

## 轻量

为了最大程度的提高性能, 并支持其他现有成熟框架的扩展, 我们并没有实现非常繁重的框架逻辑。而只是给出了实现的guideline, 让开发者可以自行根据自己的情况进行定制。

比如现有社区中, 基于TARSPHP, 已有如下扩展的案例:

* [与Kong结合](https://tangramor.gitlab.io/tars-docker-guide/3.TARS-PHP-HTTP服务端与客户端开发/)
* [与ThinkPHP结合](yu-thinkphp-jie-he-shi-yong.md)
* [与SWOFT结合](yu-swoft-jie-he-shi-yong.md)
* [与Laravel结合](yu-laravel-jie-he-shi-yong.md)
* [与Yii2结合](yu-yii2-jie-he-shi-yong.md)

后续我们也会尝试接入更多的框架, 从而更好的服务开发者。

## 高效

在性能方面, TARSPHP也一直在追求极致:

* 使用SWOOLE作为服务容器
* 使用PHP扩展进行二进制流的打包和解包
* 对服务端的路由配置进行预热, 减少路由时间
* 对寻址信息进行本地缓存, 降低耗时
* 对日志写入、监控上报、服务保活上报进行异步化处理,绝不阻塞

