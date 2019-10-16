---
description: 'https://github.com/TarsPHP/TarsPHP/tree/master/examples/tars-websocket-server'
---

# 搭建WebSocketServer

## 目录结构说明

1. clients 存放了简单的聊天室前端页面,包含了两个页面, 分别代表两个聊天室。 聊天室可以相互发送留言,也可以进行全局广播。
2. scripts 存储业务所需要的脚本,如tars2php.sh 负责的就是根据tars文件,生成客户端所需要的代码
3. src 

   业务逻辑所在的目录,主要包含以下结构: 

4. component: 存储Controller的基础类,方便所有的Controller公用,存放用来存储websocket的fd的接口和一种实现;
5. conf: 业务需要的配置, 这里只是给出一个demo,如果从平台下发配置,默认也会写入到这个文件夹中;
6. controller: MVC模型中的C层;
7. **message**: 存放被swoole的onMessage方法触发之后, 调用的文件;
8. servant: 使用tars2php生成的client端的代码, 这个目录名称完全可以自定义,只需要在使用的时候加以区分就可以了; 
9. composer.json: 说明项目的依赖情况
10. index.php: 整个服务的入口文件,这个文件名称可以自定义,但是必须要更改平台上的私有模板,增加entrance这个字段在server下面
11. services.php: 声明整个项目的基础namespaceName
12. tars 这个websocket服务所依赖的就是这个文件夹下面的example.tars文件; tars.proto.php是进行服务打包和发布的必须文件, 其中的APPName和ServerName需要和tars平台上面完全一致;

而tars.client.proto.php文件,则是生成servant下代码所必须的,这个会在下面的guideline中具体说明。

## Demo说明

这个demo是为了演示websocket与tarsphp相互结合的过程。请求链路如下:

* client1.html发起websocket请求, 要求加入房间1
* tars-websocket接收到对应请求, 解析后将client1的fd放入房间1为key的任意存储中\(本例中是文件\);
* client2有类似过程;
* 通过server-&gt;connections可以获取所有fd, 并执行广播操作;

场景说明:

* 一个可能的场景是, websocket服务本身监听http端口, 外部触发接口, 进而推送消息给浏览器
* 聊天室;
* 聊天软件;

## 服务部署guideline

1. 进入运维管理=&gt; 模板管理

   平台会提供一份新的针对php的模板,命名为tars.tarsphp.default. 

!!!!!!!必须首先修改其中php的执行路径!!!!!!!

其次有两种方式保证websocketserver使用正确的模板:

* 自己新建一个tars.tarsphp.websocket,里面添加如下内容:

```text
<tars>
 <application>
...
    <client>
...
   </client>
   <server>
     ...
      protocolName=http
      servType=websocket
    </server>
 </application>
</tars>
```

只需要在里面添加:

```text
  protocolName=http
```

即可。

* 第二种方式是在私有模板里面添加这部分内容:

```text
<tars>
 <application>
    <client>
   </client>
   <server>
      servType=websocket 
      protocolName=http
    </server>
 </application>
</tars>
```

1. 在平台上进入运维管理=&gt;部署服务,填写对应的应用名和服务名称,注意,这个与下文中tars文件夹中的tars.proto.php 需要完全一致。
2. 选择服务类型为tars\_php
3. 选择模板为刚刚建立的websocket服务模板,默认不启用set
4. 选择可用端口,填写服务器内网ip
5. 端口类型为TCP !!!!协议类型HTTP服务必须选择非TARS!!!!!!
6. 线程数对应SWOOLE的进程数
7. 最大连接数和队列最大长度、队列超时时间,对php服务不生效
8. 点击添加和提交即可,接下来请进入开发guidline

## 开发guideline

1. 新建对应的目录结构,固定为scripts、src和tars
2. 在src下新建目录,拷贝example中的component和controller两个文件夹
3. 新建composer.json文件,内容如下:

```text
{
   "name" : "tars-http-server-demo",
   "description": "tars http server",
   "require": {
       "phptars/tars-server": "~0.2",
       "phptars/tars-deploy": "~0.1",
       "phptars/tars2php": "~0.1",
       "phptars/tars-log": "~0.1",
       "ext-zip" : ">=0.0.1"
   },
   "autoload": {
       "psr-4": {
           "HttpServer\\" : "./"
       }
   },
   "minimum-stability": "stable",
   "scripts" : {
       "deploy" : "\\Tars\\deploy\\Deploy::run"
   }
}
```

其中name、description、autoload中的psr-4可以修改为自己需要的,我们这里以此为例子。

1. 在src下新建index.php,内容如下:

```php
<?php
require_once(__DIR__."/vendor/autoload.php");

use \Tars\cmd\Command;

//php tarsCmd.php  conf restart
$config_path = $argv[1];
$pos = strpos($config_path,"--config=");

$config_path = substr($config_path,$pos+9);

$cmd = strtolower($argv[2]);

$class = new Command($cmd,$config_path);
$class->run();
```

这个文件负责进行启动和入口加载工作

1. 新建services.php文件,内容如下:

```php
<?php  
// 以namespace的方式,在psr4的框架下对代码进行加载  
return [
    'obj' => [
        'namespaceName' => 'WebsocketServer\\',
        'home-class' => '\WebsocketServer\message\ExampleWebsocket',
        'saveTarsConfigFileDir' => 'src/conf/', //从tarsconfig拉下来的文件保存目录 默认src目录下的conf
        'saveTarsConfigFileName' => ['',], //需要从tarsconfig拉下来的文件名 在web上配置
        'monitorStoreConf' => [
            //使用redis缓存主调上报信息
            //'className' => Tars\monitor\cache\RedisStoreCache::class,
            //'config' => [
            // 'host' => '127.0.0.1',
            // 'port' => 6379,
            // 'password' => ':'
            //],
            //使用swoole_table缓存主调上报信息（默认）
            'className' => Tars\monitor\cache\SwooleTableStoreCache::class,
            'config' => [
                'size' => 40960
            ]
        ],
        'protocolName' => 'http', //http, json, tars or other
        'serverType' => 'websocket', //http(no_tars default), websocket, tcp(tars default), udp
    ]
];
```

namespaceName为业务实际使用的namespaceName,必须与composer.json中的配置相互对应  
monitorStoreConf为主调上报信息的存储配置

* _className_ 为主调上报信息的存储实现类的类名，默认为 _\Tars\monitor\cache\SwooleTableStoreCache::class_  使用_swoole\_table_存储，_tars-monitor_中还提供了_redis_的存储方式，用户也可以自定义新的实现，但是必须实现 _\Tars\monitor\contract\StoreCacheInterface_ 接口 
* _config_ 为主调上报信息的存储实现类的配置信息，在实现类初始化时作为参数传入，默认对应_swoole\_table_的size

1. composer install,加载对应的依赖包
2. 在src下新建conf目录存储配置,默认为ENVConf.php
3. tars文件夹下面新建tars.proto.php文件, 里面需要包含对你服务本身的说明:

```php
<?php
return array(
    'appName' => 'PHPTest',
    'serverName' => 'PHPWebsocketServer',
    'objName' => 'obj',
);
```

这个名称要与在tars平台上面的名称完全一一对应。

1. 如果你只是试一试,那么首先在可以直接跳到第14步 如果你需要调用tars服务,请继续
2. 将隔壁tcp-server的hello.tars放入tars文件夹中,同时在tars文件夹下面新建tarsclient.proto.php文件:

```php
<?php
return array(
    'appName' => 'PHPTest',
    'serverName' => 'PHPServer',
    'objName' => 'obj',
    'withServant' => false,//决定是服务端,还是客户端的自动生成
    'tarsFiles' => array(
        './example.tars'
    ),
    'dstPath' => '../src/servant',
    'namespacePrefix' => 'HttpServer\servant',
);
```

APPName、serverName、objName 需要与tars平台上面申请的完全一致。withServant必须为false,同时指定tarsFiles的路径。 dstPath一般是`../src/?`,这里为`../src/servant`,这样生成的代码就会到这个文件夹。 namespacePrefix是对应代码的命名空间,这里是`HttpServer\servant`,这个与composer.json中的psr-4的名称也是相互对应的。

1. 执行scripts下面的tars2php.sh, 会在src/servant下面生成一个三级文件夹,

```text
cd ../tars/

php ../src/vendor/phptars/tars2php/src/tars2php.php ./tarsclient.proto.php
```

这里就是PHPTest/PHPServer/obj

* classes文件夹 - 存放tars中的struct生成的文件
* tars文件夹 - 存放tars文件
* TestTafServiceServant.php - 实际的远程rpc调用文件

1. 在controller中增加对应的rpc调用代码,具体可参考代码中demo,或tars-client的用法说明
2. 完成代码开发后,在src目录下执行 composer run-script deploy 会自动进行代码打包
3. 将打包好的代码,上传到tars平台,并进行发布
4. 如果你想要试用自己的fd存储和删除, 请实现component文件夹下的FdStore接口。

