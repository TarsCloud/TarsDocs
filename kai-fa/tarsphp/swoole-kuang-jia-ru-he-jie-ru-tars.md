# Swoole框架如何接入Tars

## 总体思路

* 在框架启动成功的时候，上报服务存活。
* 使用框架的或者swoole的定时器，实现每隔30s上报一次存活，可以在worker里面上报，也可以在task里面上报（注意可能worker都挂了，task还在）。
* 写一个入口文件（如index.php），根据Tars平台生成的PHP服务启停脚本，已经Tars平台下发的conf配置文件，完成PHP框架的配置转换（端口号、worker数量）和启停命令控制。
* 对于HTTP的服务，实现上面3步就可以跑着Tars里面了。对于其他各种功能（查看框架简介），可以根据实际情况自行引入Tarsphp的composer扩展。
* 对于tars或者pb等RPC协议的服务，需要解决网络协议与业务协议的打包解包（可以参照tarsphp tcpserver），如果能实现定制的代码自动生成，就更好了。

## 以swoft为例

* 修改composer.json,加入phptars的包，以及打包命令。

```text
{
    "require": {
        ...
        "phptars/tars-server": "~0.1",
        "phptars/tars-deploy": "~0.1",
        "phptars/tars2php": "~0.1",
        "phptars/tars-log": "~0.1",
        "ext-zip" : ">=0.0.1"
        ...
    },
    "scripts": {
        ...
        "deploy": "\\Tars\\deploy\\Deploy::run"
        ...
    }
}
```

* 写一个用于调用Tars平台各种接口的class （src/app/Tars/Manage.php）

  ```php
  namespace App\Tars;
  use \Tars\report\ServerFSync;
  use \Tars\report\ServerFAsync;
  use \Tars\report\ServerInfo;
  use \Tars\Utils;
  class Manage
  {

    public function getNodeInfo(){
        $conf = $this->getTarsConf();
        if( !empty($conf) ){
            $node = $conf['tars']['application']['server']['node'];
            $nodeInfo = Utils::parseNodeInfo($node);
            return $nodeInfo;
        }else{
            return [];
        }
    }

    public function getTarsConf(){
        $tars_conf = dirname(BASE_PATH,2).'/conf/'.env('PNAME').'.config.conf';

        if( is_file($tars_conf) ){
            $conf = Utils::parseFile($tars_conf);
            return $conf;
        }else{
            var_dump('get tars_conf file error : '.$tars_conf);
            return [];
        }
    }

    public function keepAlive()
    {
        $pname = env('PNAME');
        $pname = explode('.',$pname);

        $adapter = env('PNAME').'.objAdapter';
        $application = $pname[0];
        $serverName = $pname[1];
        $masterPid = getmypid();

        $nodeInfo = $this->getNodeInfo();
        if( empty($nodeInfo) ){
            var_dump('keepAlive getNodeInfo fail');
            return null;
        }
        $host = $nodeInfo['host'];
        $port = $nodeInfo['port'];
        $objName = $nodeInfo['objName'];

        $serverInfo = new ServerInfo();
        $serverInfo->adapter = $adapter;
        $serverInfo->application = $application;
        $serverInfo->serverName = $serverName;
        $serverInfo->pid = $masterPid;

        $serverF = new ServerFSync($host, $port, $objName);
        $serverF->keepAlive($serverInfo);

        $adminServerInfo = new ServerInfo();
        $adminServerInfo->adapter = 'AdminAdapter';
        $adminServerInfo->application = $application;
        $adminServerInfo->serverName = $serverName;
        $adminServerInfo->pid = $masterPid;
        $serverF->keepAlive($adminServerInfo);

        var_dump(' keepalive ');
    }
  }
  ```

* 在框架启动成功的时候，上报服务存活，这使用的swoft的框架的事件监听。 （src/app/Listener/APPStart.php）

  ```php
  namespace App\Listener;
  use Swoft\Bean\Annotation\Listener;
  use Swoft\Event\EventHandlerInterface;
  use Swoft\Event\EventInterface;
  use Swoft\Task\Event\TaskEvent;
  use Swoft\Event\AppEvent;
  use App\Tars\Manage;
  use Swoft\Memory\Table;
  /**
  * Task finish handler
  *
  * @Listener(AppEvent::APPLICATION_LOADER)
  */
  class APPStart implements EventHandlerInterface
  {
    public static $num = 0;
    /**
     * @param \Swoft\Event\EventInterface $event
     */
    public function handle(EventInterface $event)
    {
        //服务启动  只上报一次 TODO
        $manage = new Manage();
        $manage->keepAlive();
    }
  }
  ```

* 每隔30s上报一次存活，这里使用swoft框架注解试的定时任务。 （src/app/Tasks/TarsKeepAliveTask.php）

  ```php
  namespace App\Tasks;
  use App\Lib\DemoInterface;
  use App\Models\Entity\User;
  use Swoft\App;
  use Swoft\Bean\Annotation\Inject;
  use Swoft\HttpClient\Client;
  use Swoft\Redis\Redis;
  use Swoft\Rpc\Client\Bean\Annotation\Reference;
  use Swoft\Task\Bean\Annotation\Scheduled;
  use Swoft\Task\Bean\Annotation\Task;
  use App\Tars\Manage;
  /**
  * TarsKeepAlive task
  *
  * @Task("tarsKeepAlive")
  */
  class TarsKeepAliveTask
  {
    /**
     *
     * @Scheduled(cron="*\/30 * * * * *")
     */
    public function cronkeepAliveTask()
    {
        $manage = new Manage();
        $manage->keepAlive();
        return 'cron';
    }
  }
  ```

* 写一个入口文件，来控制swoft框架的启停。 （src/index.php）

  ```php
  // tars 平台然后文件
  // 读取tars conf配置
  // 处理合成 env文件
  $args = $_SERVER['argv'];
  $swoft_bin = dirname(__FILE__).'/bin/swoft ';
  $arg_cmd = $args[2]=='start' ? 'start -d' : $args[2] ;
  $cmd = "/usr/bin/php " . $swoft_bin . $arg_cmd;
  exec($cmd, $output, $r);
  ```

ps：可以参考下面的提交记录 [https://github.com/dpp2009/swoftInTars/commit/97459b5012f9d7542a2a31d936c65ad8637ee1a0\#diff-efc7d6cbd3cc43b894698099b51a99ab](https://github.com/dpp2009/swoftInTars/commit/97459b5012f9d7542a2a31d936c65ad8637ee1a0#diff-efc7d6cbd3cc43b894698099b51a99ab)

