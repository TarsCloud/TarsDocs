# tars-report说明文档

## 简介
`tars-report` 是 `phptars` 心跳上报（保活）模块
tarsphp框架在master进程启动的时候会上报服务存活状态(主进程存活&管理进程存活)。  
在第一个worker进程启动的时候，会开启一个timer来定时上报存活状态。  

## 使用说明
```php
        $serverInfo = new ServerInfo();
        $serverInfo->adapter = 'PHPTest.test.objAdapter';
        $serverInfo->application = 'PHPTest';
        $serverInfo->serverName = 'test';
        $serverInfo->pid = $masterPid;

        $serverF = new ServerFSync($host, $port, $objName);
        $serverF->keepAlive($serverInfo); //主进程存活

        $adminServerInfo = new ServerInfo();
        $adminServerInfo->adapter = 'AdminAdapter';
        $adminServerInfo->application = 'PHPTest';
        $adminServerInfo->serverName = 'test';
        $adminServerInfo->pid = $masterPid;
        $serverF->keepAlive($adminServerInfo);//管理进程存活
```

## 其他
因为其他模块已经集成了本模块， __所以一般情况下，服务脚本无需显式使用此模块。__