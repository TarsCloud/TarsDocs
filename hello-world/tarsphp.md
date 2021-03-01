# TarsPHP 快速入门

> - [1. 框架简介](#chapter-1)
> - [2. 示例：搭建 Http 服务的 HelloWorld](#chapter-2)
> - [3. 示例：搭建 Tars 服务的 HelloWorld](#chapter-3)
> - [4. 示例：Tars HelloWorld 服务的客户端程序](#chapter-4)
> - [5. 论坛邀请](#chapter-5)

## 1. <span id="chapter-1"></span>框架简介

### tars-client

tars-client 中提供了对 tars 服务进行调用的 php 能力，包括：

- 调用远程服务的实例；
- 主调上报
- 自动寻址模块

### tars-server

tars-server 提供了底层的 server 框架,同时支持如下特性

- 基于 swoole1.x/2.x 的高性能服务
- 支持 tup 协议和 tars 流两种协议模式
- 支持 http、tcp、timer 三种 server
- 上报、监控、日志的集成
- tars 平台发布支持

### tars-config

从 tars 平台的配置服务拉取配置文件的能力模块。

### tars-deploy

进行 tars-server 业务代码打包的模块。

### tars-extension

tars 底层依赖的 php 扩展代码

### tars-log

tars 进行远程日志写入的模块

### tars-monitor

tars 进行主调上报和特性上报的功能模块

### tars-registry

tars 进行主控寻址的功能模块

### tars-report

tars 进行 keep-alive 服务保活上报的模块

### tars-utils

tars 进行配置文件解析的模块

### tars2php

代码自动生成的工具,可以自动生成 server 和 client 端的代码。

## 2. <span id="chapter-2"></span>示例：搭建 Http 服务的 HelloWorld

### 目录结构说明

1. scripts 存储业务所需要的脚本,如 tars2php.sh 负责的就是根据 tars 文件,生成客户端所需要的代码
2. src 业务逻辑所在的目录,主要包含以下结构:
3. component: 存储 Controller 的基础类,方便所有的 Controller 公用
4. conf: 业务需要的配置, 这里只是给出一个 demo,如果从平台下发配置,默认也会写入到这个文件夹中;
5. controller: MVC 模型中的 C 层;
6. servant: 使用 tars2php 生成的 client 端的代码, 这个目录名称完全可以自定义,只需要在使用的时候加以区分就可以了;
7. composer.json: 说明项目的依赖情况
8. index.php: 整个服务的入口文件,这个文件名称可以自定义,但是必须要更改平台上的私有模板,增加 entrance 这个字段在 server 下面
9. services.php: 声明整个项目的基础 namespaceName
10. tars 这个 tcp 服务所依赖的就是这个文件夹下面的\*.tars 文件 而 tars.client.proto.php 文件,则是生成 servant 下代码所必须的,这个会在下面的 guideline 中具体说明。

### 服务部署 guideline

1. 进入运维管理=&gt; 模板管理

   平台会提供一份新的针对 php 的模板,命名为 tars.tarsphp.default.

   !!!!!!!必须首先修改其中 php 的执行路径!!!!!!!

   其次有两种方式保证 http-server 使用正确的模板:

2. 在平台上进入运维管理=&gt;部署服务,填写对应的应用名和服务名称,注意,这个与下文中 tars 文件夹中的 tars.proto.php

   需要完全一致。

3. 选择服务类型为 tars_php
4. 选择模板为 tars.tarsphp.default 服务模板,默认不启用 set
5. 选择可用端口,填写服务器内网 ip. (可在运维管理=&gt;节点管理中查看)
6. 端口类型为 TCP

   !!!!协议类型 HTTP 服务必须选择非 TARS!!!!!!

7. 线程数对应 SWOOLE 的进程数
8. 最大连接数和队列最大长度、队列超时时间,对 php 服务不生效
9. 点击添加和提交即可,接下来请进入开发 guideline

![服务部署](../assets/deploy_php_http_servant.png)

### 开发 guideline

1. 新建对应的目录结构,固定为 scripts、src 和 tars
2. 新建 `src/composer.json` 文件,内容如下:

```json
{
  "name": "tars/helloworld/phphttp",
  "description": "tars php http hello world",
  "require": {
    "phptars/tars-server": "~0.2",
    "phptars/tars-deploy": "~0.1",
    "phptars/tars2php": "~0.1",
    "phptars/tars-log": "~0.1",
    "ext-zip": ">=0.0.1"
  },
  "autoload": {
    "psr-4": {
      "PHPHttp\\": "./"
    }
  },
  "minimum-stability": "stable",
  "scripts": {
    "deploy": "\\Tars\\deploy\\Deploy::run"
  },
  "repositories": {
    "packagist": {
      "type": "composer",
      "url": "https://mirrors.aliyun.com/composer/"
    }
  }
}
```

其中 name、description、autoload 中的 psr-4 可以修改为自己需要的,我们这里以此为例子。

3. 新建 `src/index.php` ,内容如下:

```php
<?php
/**
 * 框架启动的入口文件
 */
require_once __DIR__.'/vendor/autoload.php';

use \Tars\cmd\Command;

//php index.php  conf restart
$config_path = $argv[1];
$pos = strpos($config_path, '--config=');

$config_path = substr($config_path, $pos + 9);

$cmd = strtolower($argv[2]);

$class = new Command($cmd, $config_path);
$class->run();
```

这个文件负责进行启动和入口加载工作

4. 新建 `src/services.php` 文件,内容如下:

```php
<?php
// 以namespace的方式,在psr4的框架下对代码进行加载
return [
    // 请注意，这个Hello代表的就是部署服务时写的obj，
    // 如果不小心忘记了，可以在服务管理->服务管理=>HelloWorld=>PHPHttp=>管理servant中查看，
    // 管理Servant一栏写的HelloWorld.PHPHttp.Hello中的Hello就是你要的obj，这里的命名是AppName.ServerName.ObjName
    'Hello' => [
        'namespaceName' => 'PHPHttp\\', // psr4的根命名空间
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
        'registryStoreConf' => [
            'className' => Tars\registry\RouteTable::class,
            'config' => [
                'size' => 200
            ]
        ],
        'protocolName' => 'http', //http, json, tars or other
        'serverType' => 'http', //http(no_tars default), websocket, tcp(tars default), udp
    ],
];
```

namespaceName 为业务实际使用的 namespaceName,必须与 composer.json 中的配置相互对应.

monitorStoreConf 为主调上报信息的存储配置

- className 为主调上报信息的存储实现类的类名，默认为 `\Tars\monitor\cache\SwooleTableStoreCache::class` 使用*swoole_table*存储，*tars-monitor*中还提供了*redis*的存储方式，用户也可以自定义新的实现，但是必须实现 `\Tars\monitor\contract\StoreCacheInterface` 接口
- config 为主调上报信息的存储实现类的配置信息，在实现类初始化时作为参数传入，默认对应*swoole_table*的 size

5. composer install,加载对应的依赖包
6. 新建 `tars/tars.proto.php` 文件, 里面需要包含对你服务本身的说明:

```php
<?php
return [
    'appName' => 'HelloWorld', // 对应部署服务的应用名称
    'serverName' => 'PHPHttp', // 对应部署服务的服务名称
    'objName' => 'Hello', // 对应部署服务的obj
];

```

这个名称要与在 tars 平台上面的名称完全一一对应。

7. 新建 `src/component/Controller.php`

```php
<?php

namespace PHPHttp\component;

use Tars\core\Request;
use Tars\core\Response;

class Controller
{
    protected $request;
    protected $response;

    public function __construct(Request $request, Response $response)
    {
        $this->request = $request;
        $this->response = $response;
    }

    public function getResponse()
    {
        return $this->response;
    }

    public function getRequest()
    {
        return $this->request;
    }

    public function cookie($key, $value = '', $expire = 0, $path = '/', $domain = '', $secure = false, $httponly = false)
    {
        $this->response->cookie($key, $value, $expire, $path, $domain, $secure, $httponly);
    }

    // 给客户端发送数据
    public function sendRaw($result)
    {
        $this->response->send($result);
    }

    public function header($key, $value)
    {
        $this->response->header($key, $value);
    }

    public function status($http_status_code)
    {
        $this->response->status($http_status_code);
    }
}

```

- 这是所有 Controller 的基类

8. 新建 `src/controller/IndexController.php`

```php
<?php

namespace PHPHttp\controller;

use PHPHttp\component\Controller;
use Tars\client\CommunicatorConfig;
use Tars\App;

class IndexController extends Controller
{
    public function actionIndex()
    {
        $this->sendRaw('Hello Tars!');
    }
}

```

- 创建 IndexController::actionIndex 方法，对应访问地址为 `http://{machine_ip}:9000/index/index`

9. 完成代码开发后,在 src 目录下执行 composer run-script deploy 会自动进行代码打包
10. 将打包好的代码 `src/PHPHttp_xxx.tar.gz`,上传到 tars 平台,并进行发布

- 若服务启动失败，可以在 node 机器或 docker 的`/data/app/tars/app_log/HelloWorld/PHPHttp`的日志中排查原因

- 发布操作见下图：
  ![发布管理](../assets/php_http_release_1.png)
  ![选择版本](../assets/php_http_release_2.png)
  ![上传代码](../assets/php_http_release_3.png)
  ![选择发布](../assets/php_http_release_4.png)

11. 浏览器打开 `http://{machine_ip}:9000/index/index` 页面将显示 Hello Tars!

## 3. <span id="chapter-3"></span>示例：搭建 Tars 服务的 HelloWorld

### 目录结构说明

1. scripts 存储业务所需要的脚本,如 tars2php.sh 负责的就是根据 tars 文件,生成客户端所需要的代码
2. src 业务逻辑所在的目录,主要包含以下结构:
3. component: 基础类封装
4. conf: 业务需要的配置, 这里只是给出一个 demo,如果从平台下发配置,默认也会写入到这个文件夹中;
5. impl: tars 接口实现业务层;
6. servant: 使用 tars2php 生成的 client 端的代码, 这个目录名称完全可以自定义,只需要在使用的时候加以区分就可以了;
7. composer.json: 说明项目的依赖情况
8. index.php: 整个服务的入口文件,这个文件名称可以自定义,但是必须要更改平台上的私有模板,增加 entrance 这个字段在 server 下面
9. services.php: 声明整个项目的基础 namespaceName
10. tars 这个 tcp 服务所依赖的就是这个文件夹下面的\*.tars 文件 而 tarsclient.proto.php 文件,则是生成 servant 下代码所必须的,这个会在下面的 guideline 中具体说明。

### 部署服务

![部署服务](../assets/deploy_php_tars_servant.png)

### 开发 guideline

1. 创建`scripts/tars2php.sh`

```bash
#!/bin/bash

cd ../tars/

php ../src/vendor/phptars/tars2php/src/tars2php.php ./tarsclient.proto.php

```

- 这个脚本是用来根据 tarsclient.proto.php 中定义的配置生成相关代码的

2. 创建`src/composer.json`

```json
{
  "name": "tars/helloworld/phptars",
  "description": "tars php hello world",
  "require": {
    "phptars/tars-server": "~0.2",
    "phptars/tars-deploy": "~0.1",
    "phptars/tars2php": "~0.1",
    "phptars/tars-log": "~0.1",
    "ext-zip": ">=0.0.1"
  },
  "autoload": {
    "psr-4": {
      "PHPTars\\": "./"
    }
  },
  "minimum-stability": "stable",
  "scripts": {
    "deploy": "\\Tars\\deploy\\Deploy::run"
  },
  "repositories": {
    "packagist": {
      "type": "composer",
      "url": "https://mirrors.aliyun.com/composer/"
    }
  }
}
```

- 创建完之后运行 composer install

3. 创建框架入口文件`src/index.php`

```php
<?php
/**
 * 框架启动的入口文件
 */
require_once __DIR__.'/vendor/autoload.php';

use \Tars\cmd\Command;

//php index.php  conf restart
$config_path = $argv[1];
$pos = strpos($config_path, '--config=');

$config_path = substr($config_path, $pos + 9);

$cmd = strtolower($argv[2]);

$class = new Command($cmd, $config_path);
$class->run();
```

4. 创建 service 声明文件`src/services.php`

```php
<?php
// 以namespace的方式,在psr4的框架下对代码进行加载
return [
    // 请注意，这个Hello代表的就是部署服务时写的obj，
    // 如果不小心忘记了，可以在服务管理->服务管理->HelloWorld->PHPTars->管理servant中查看，
    // 管理Servant一栏写的HelloWorld.PHPTars.Hello中的Hello就是你要的obj，这里的命名是AppName.ServerName.ObjName
    'Hello' => [
        'home-api' => '\PHPTars\servant\HelloWorld\PHPTars\Hello\SayHelloTafServiceServant',
        'home-class' => '\PHPTars\impl\SayHello',
        'protocolName' => 'tars', //http, json, tars or other
        'serverType' => 'tcp', //http(no_tars default), websocket, tcp(tars default), udp
    ],
];

```

5. 创建`tars/tars.proto.php`

```php
<?php
return [
    'appName' => 'HelloWorld', // 对应部署服务的应用名称
    'serverName' => 'PHPTars', // 对应部署服务的服务名称
    'objName' => 'Hello', // 对应部署服务的obj
];

```

6. 创建`tars/tarsclient.proto.php`

```php
<?php
return array(
    'appName' => 'HelloWorld', // 对应部署服务的应用名称
    'serverName' => 'PHPTars', // 对应部署服务的服务名称
    'objName' => 'Hello', // 对应部署服务的obj
    'withServant' => true, //为true生成服务端代码, 为false则生成客户端代码
    'tarsFiles' => array(
        './SayHello.tars', // tars文件地址，目前服务端仅支持一个tars文件，基于tars目录的相对路径
    ),
    'dstPath' => '../src/servant', // 生成代码的目标路径，基于scripts目录的相对路径
    'namespacePrefix' => 'PHPTars\servant', // 生成代码的命名空间前缀
);

```

- 定义 tars 代码生成相关的属性

7. 创建 `tars/SayHello.tars`

```c++
module SayHelloTafServiceServant
{
    interface SayHelloTafService
    {
        int greeting(string name, out string greeting); // out代表输出参数，客户端可以获取到这个输出参数
    };

};
```

- 关于 tars 文件的编写方式请前往[相关文档](../../kai-fa/tars_protocol.md)查看
- 执行`cd scripts && ./tars2php.sh` 生成 tars 相关代码

8. 实现 tars 接口，创建`src/impl/SayHello.php`

```php
<?php

namespace PHPTars\impl;

use PHPTars\servant\HelloWorld\PHPTars\Hello\SayHelloTafServiceServant;

class SayHello implements SayHelloTafServiceServant
{
    public function greeting($name, &$greeting)
    {
        $greeting = "PHPTars says hello to $name";

        return 0;
    }
}

```

- 这是 tars 接口 greeting 的具体实现，在`src/services.php`的`home-class`中已有相关申明

9. 在 src 目录下执行`composer run-script deploy`打包代码
10. 发布

- 代码发布完成之后可以在 服务管理=&gt;HelloWorld=&gt;PHPTars=&gt;接口调试 中上传刚才编写的`SayHello.tars`文件 进行接口调试
- 若服务启动失败，可以在 node 机器或 docker 的`/data/app/tars/app_log/HelloWorld/PHPTars`的日志中排查原因

## 4. <span id="chapter-4"></span>示例：Tars HelloWorld 服务的客户端程序

- 接下来我们尝试在 PHPHttp 服务中调用 PHPTars 中刚完成的 Tars 接口

1. 复制刚刚的`PHPTars/tars/SayHello.tars`到`PHPHttp/tars/SayHello.tars`
2. 创建`tars/tarsclient.proto.php`

```php
<?php

return array(
    'appName' => 'HelloWorld',
    'serverName' => 'PHPTars',
    'objName' => 'Hello',
    'withServant' => false, //决定是服务端,还是客户端的自动生成
    'tarsFiles' => array(
        './SayHello.tars',
    ),
    'dstPath' => '../src/servant',
    'namespacePrefix' => 'PHPHttp\servant',
);

```

- 执行`cd scripts && ./tars2php.sh`生成客户端调用代码

3. 编辑`src/controller/IndexController.php`添加如下代码

```php
   public function actionTestGreeting()
    {
        $config = new \Tars\client\CommunicatorConfig();
        $config->setLocator(\Tars\App::$tarsConfig['tars']['application']['client']['locator']);

        $userService = new \PHPHttp\servant\HelloWorld\PHPTars\Hello\SayHelloTafServiceServant($config);
        $greeting = '';
        $return = $userService->greeting('Frank Lee', $greeting);

        $this->sendRaw(json_encode(compact('return', 'greeting')));
    }
```

- 通过刚刚生成的客户端类调用 Tars 接口
- 框架启动时将配置信息绑定到了`\Tars\App::$tarsConfig`上
- `\Tars\App::$tarsConfig['tars']['application']['client']['locator']` 即为主节点地址服务
- !!!! 注意：Tars 是个多语言框架，所以在通信过程中数据需要满足强类型相关约束，此处的\$greeting 需要预先初始化成目标类型。 !!!!

4. 打包发布

- 发布完成之后可以访问`http://{machine_ip}:{port}/index/TestGreeting`观察效果

## 5. <span id="chapter-5"></span>论坛邀请

欢迎大家加入 TarsDoc 论坛一起探讨在 Tars 使用过程中遇到的问题和新的体会，共建和谐社区

[论坛传送门](https://bbs.tarsdoc.com/forum.php)
