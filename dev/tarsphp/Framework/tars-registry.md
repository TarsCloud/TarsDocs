# tars-registry说明文档

## 简介
tars平台的tarsregistry服务提供服务发现的功能。  
本模块为php提供主控寻址的能力(服务发现)。

## 使用说明
```php
        //从tarsregistry服务寻找服务地址
        $wrapper = new \Tars\registry\QueryFWrapper("tars.tarsregistry.QueryObj@tcp -h 172.16.0.161 -p 17890",1,60000);
        $result = $wrapper->findObjectById("PHPTest.PHPServer.obj");
        var_dump($result);

        //优先从内存寻找服务地址，其次从主控寻址
        \Tars\registry\RouteTable::getInstance();
        $result = \Tars\registry\RouteTable::getRouteInfo("PHPTest.PHPServer.obj");
        echo "result:\n";
        var_dump($result);
```

## 其他
因为其他模块已经集成了本模块， __所以一般情况下，服务脚本无需显式使用此模块。__