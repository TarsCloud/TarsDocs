# tars-utils说明文档

## 简介
`tars-utils` 是 phptars的辅助类库，主要提供如下几个功能：
* 配置文件解析并缓存(swoole table)
* 主控地址解析
* node上报配置解析

## 使用说明
### 配置文件解析

以[QDPHP.TARSServer.config.conf]() 为例，将`conf` 文件解析成 数组形式

### 地址解析
```php
$locatorString = "tars.tarsregistry.QueryObj@tcp -h 127.0.0.1 -p 17890:tcp -h 127.0.0.1 -p 17890";

$locatorInfo = \Tars\Utils::getLocatorInfo($locatorString);
```
得到结果如下：
```php
[
	'locatorName' => 'tars.tarsregistry.QueryObj',
	'routeInfo' => [
		[
			'sHost' => '127.0.0.1',
			'sProtocol' => 'tcp',
			'iPort' => 17890,
			'iTimeout' => '',
			'bIp' => '',
			'sIp' => ''
		],
		[
			'sHost' => '127.0.0.1',
			'sProtocol' => 'tcp',
			'iPort' => 17890,
			'iTimeout' => '',
			'bIp' => '',
			'sIp' => ''
		]
	]
]
```
### node上报配置解析
```php
$locatorString = "tars.tarsnode.ServerObj@tcp -h 127.0.0.1 -p 2345 -t 10000";

$nodeInfo = \Tars\Utils::parseNodeInfo($locatorString);
```

得到结果如下：
```php
[
	'objName' => 'tars.tarsnode.ServerObj',
	'mode' => 'tcp',
	'host' => '127.0.0.1',
	'port' => 2345,
	'timeout' => 1,
	'sIp' => '',
	'iPort' => 2345
]
```
