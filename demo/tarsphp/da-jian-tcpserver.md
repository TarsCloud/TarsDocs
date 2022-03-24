---
description: "https://github.com/TarsPHP/TarsPHP/tree/master/examples/tars-tcp-server"
---

# 搭建 TcpServer

## 目录结构说明

1. scripts

   存储业务所需要的脚本,如 tars2php.sh 负责的就是根据 tars 文件,生成服务端所需要的代码

2. src

   业务逻辑所在的目录,主要包含以下结构:

3. conf: 业务需要的配置, 这里只是给出一个 demo,如果从平台下发配置,默认也会写入到这个文件夹中;
4. impl: 业务实际的实现代码,是对 interface 的对应实现, 具体实现的文件路径需要写入到 services.php 中;
5. servant: 使用 tars2php 生成的 server 端的代码, 这个目录名称完全可以自定义,只需要在使用的时候加以区分就可以了;
6. cservant: 调用其他 client 的代码,命名完全可以自定义,参考 tars2php 的代码生成;
7. composer.json: 说明项目的依赖情况
8. index.php: 整个服务的入口文件,可以自定义,但是必须要更改平台上的私有模板,增加 entrance 这个字段在 server 下面
9. services.php: 声明 interface 的地址,声明实际实现的地址,这个两个地址会被分别用作实例化调用和注解解析
10. tars 这个 tcp 服务所依赖的就是这个文件夹下面的 example.tars 文件 而 tars.proto.php 文件,则是生成 servant 下代码所必须的,这个会在下面的 guideline 中具体说明。

## 服务部署 guideline

1. 进入运维管理 =&gt; 模板管理 平台会提供一份新的针对 php 的模板,命名为 tars.tarsphp.default, !!!!!!!必须首先修改其中 php 的执行路径!!!!!!!

   有两种方式保证 tcp-server 使用正确的模板:

   - 自己新建一个 tars.tarsphp.tcp 模板,里面添加如下内容:

     ```markup
     <tars>
     <application>
     ...
      <client>
     ...
     </client>
     <server>
       ...
        package_length_type=N
        open_length_check=1
        package_length_offset=0
        package_body_offset=0
        package_max_length=2000000
      </server>
     </application>
     </tars>
     ```

     只需要在里面添加:

     ```text
     package_length_type=N
     open_length_check=1
     package_length_offset=0
     package_body_offset=0
     package_max_length=2000000
     ```

     即可。

   - 第二种方式是在私有模板里面添加这部分内容,但是这种方式并不推荐。

2. 在平台上进入运维管理=&gt;部署服务,填写对应的应用名和服务名称,注意,这个与下文中 tars 文件夹中的 tars.proto.php 需要完全一致。
3. 选择服务类型为 tars_php
4. 选择模板为刚刚建立的 tcp 服务模板,默认不启用 set
5. 选择可用端口,填写服务器内网 ip
6. 端口类型为 TCP !!!!协议类型 TCP 服务必须选择 TARS!!!!!!
7. 线程数对应 SWOOLE 的进程数
8. 最大连接数和队列最大长度、队列超时时间,对 php 服务不生效
9. 点击添加和提交即可,接下来请进入开发 guidline

## 开发 guideline

1. 自行撰写了一份 tars 文件,姑且命名为 example.tars
2. 新建对应的目录结构,固定为 scripts、src 和 tars
3. 在 scripts 下面新建 tars2php.sh 文件, 内容为

```text
cd ../tars/

php ../src/vendor/phptars/tars2php/src/tars2php.php ./tars.proto.php
```

1. 将 example.tars 放入 tars 文件夹中,同时在 tars 文件夹下面新建 tars.proto.php 文件:

```php
<?php
return array(
    'appName' => 'PHPTest',
    'serverName' => 'PHPServer',
    'objName' => 'obj',
    'withServant' => true,//决定是服务端,还是客户端的自动生成
    'tarsFiles' => array(
        './example.tars'
    ),
    'dstPath' => '../src/servant',
    'namespacePrefix' => 'Server\servant',
);
```

APPName、serverName、objName 需要与 tars 平台上面申请的完全一致。withServant 必须为 true,同时指定 tarsFiles 的路径。 dstPath 一般是`../src/?`,这里为`../src/servant`,这样生成的代码就会到这个文件夹。 namespacePrefix 是对应代码的命名空间,这里是`Server\servant`,这个与 composer.json 中的 psr-4 的名称也是相互对应的。

1. 执行 scripts 下面的 tars2php.sh 会在 src/servant 下面生成一个三级文件夹,

   这里就是 PHPTest/PHPServer/obj

2. classes 文件夹 - 存放 tars 中的 struct 生成的文件
3. tars 文件夹 - 存放 tars 文件
4. TestTafServiceServant.php - interface 文件
5. 在 src 下新建 impl 文件夹

   在里面新建 PHPServerServantImpl.php 文件,这个文件,必须实现 TestTafServiceServant.php 其中对应的所有方法

6. 新建 composer.json 文件,内容如下:

```json
{
  "name": "tars-tcp-server-demo",
  "description": "tars tcp server",
  "require": {
    "phptars/tars-server": "~0.2",
    "phptars/tars-deploy": "~0.1",
    "phptars/tars-log": "~0.1",
    "phptars/tars2php": "~0.1",
    "ext-zip": ">=0.0.1"
  },
  "autoload": {
    "psr-4": {
      "Server\\": "./"
    }
  },
  "minimum-stability": "stable",
  "scripts": {
    "deploy": "\\Tars\\deploy\\Deploy::run"
  }
}
```

其中 name、description、autoload 中的 psr-4 可以修改为自己需要的,我们这里以此为例子。

1. 在 src 下新建 index.php,内容如下:

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

1. 新建 services.php 文件,内容如下:

```php
<?php
/**
 * Created by PhpStorm.
 * User: liangchen
 * Date: 2018/2/24
 * Time: 下午2:51
 */

// 以namespace的方式,在psr4的框架下对代码进行加载
return [
    'obj' => [
        'home-api' => '\Server\servant\PHPTest\PHPServer\obj\TestTafServiceServant',
        'home-class' => '\Server\impl\PHPServerServantImpl',
        'protocolName' => 'tars', //http, json, tars or other
        'serverType' => 'tcp', //http(no_tars default), websocket, tcp(tars default), udp
    ],
];
```

home-api 指的是 interface 文件对应的 namespace 名称和类名 home-class 指的是实现文件对应的 namespace 名称和类名

1. composer install,加载对应的依赖包
2. 在 src 下新建 conf 目录存储配置,默认为 ENVConf.php
3. 在 PHPServerServantImpl 实现业务逻辑
4. 如果需要进行其他的 tars 服务调用,请参考 tars-client 模块的文档
5. 完成代码开发后,在 src 目录下执行 composer run-script deploy 会自动进行代码打包
6. 将打包好的代码,上传到 tars 平台,并进行发布
