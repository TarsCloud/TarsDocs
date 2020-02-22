
# tars-log说明文档

## 简介
`tars-log` 是 `phptars` 日志模块，用于将日志内容打到本地日志或远程日志

## 使用说明

### 安装
使用`composer`进行安装
`composer install phptars/tars-log`

### 配置

可以逐个参数进行配置，也可以通过平台下发的配置文件统一配置
- 单独配置某个参数
```php
$config  = new \Tars\client\CommunicatorConfig();  
$config->setLocator("tars.tarsregistry.QueryObj@tcp -h 172.16.0.161 -p 17890");  
$config->setModuleName("tedtest");  
$config->setCharsetName("UTF-8");
$config->setLogLevel("INFO");	//日志级别：`INFO`、`DEBUG`、`WARN`、`ERROR` 默认INFO
$config->setSocketMode(2);		//远程日志连接方式：1：socket，2：swoole tcp client 3: swoole coroutine tcp client
```
- 配置文件初始化参数
```php
$config = new \Tars\client\CommunicatorConfig();
$sFilePath = '项目地址/src/conf'; //配置文件下发路径
$config->init($sFilePath);
```

### 输出日志
输出日志提供两种方式，一种直接调用`LogServant`的`logger`方式输出远程日志，另一种结合`monolog`输出远程日志(推荐)

- 调用`LogServant`的`logger`方式  

```php
$logServant  = new \Tars\log\LogServant($config);  
$appName = "App";	//应用名称
$serverName = "server";	//服务名称
$file = "test.log";	//文件名称
$format = "%Y%m%d";	//日志时间格式
$buffer = ["hahahahaha"];	//日志内容，数组，每个元素为一条日志
$result = $logServant->logger($appName,$serverName,$file,$format,$buffer);
```

- 结合`monolog`方式(推荐)  

```php
$logger = new \Monolog\Logger("tars_logger");
//remote log
$tarsHandler = new \Tars\log\handler\TarsHandler($config);
//local log 这里可以根据业务需要添加其他handler，比如StreamHandler、ElasticSearchHandler 等
$streamHandler = new \Monolog\Handler\StreamHandler(ENVConf::$logPath . "/" . __CLASS__  . ".log");

$logger->pushHandler($tarsHandler);
$logger->pushHandler($streamHandler);

$array = [
    "key1" => "value1",
    "key2" => "value2",
    "key3" => "value3"
];
$logger->debug("add a debug message", $array);
$logger->info("add a info message", $array);
$logger->notice("add a notice message", $array);
$logger->warning("add a warning message", $array);
$logger->error("add a error message", $array);
$logger->critical("add a critical message", $array);
$logger->emergency("add a emergency message", $array);
```

### 结合ELK
远程日志按应用与服务区分，位于`/usr/local/app/tars/remote_app_log/{App}/{ServerName}`，在实际业务中，可以通过`filebeat` 或 `logstash` 将远程日志同步到`ElasticSearch`。

除此之外`monolog` 本身提供了`ElasticSearchHandler`，可以非常方便的把日志直接输出到`ElasticSearch`。

使用之前需要在composer中引入`ruflin/elastica` 包。
> 注：截止到本文更新前，`ruflin/elastica` 的 release版只支持至 elasticsearch:6.*版本，想使用elasticsearch:7.*的同学可以使用 `elasticsearch/elasticsearch` 包自行封装 handler 

示例代码如下：

```php
$logger = new \Monolog\Logger("elk_logger");
$client = new \Elastica\Client([
	'host' => '127.0.0.1',
	'port' => 9200
]);
$elkHanlder = new Monolog\Handler\ElasticSearchHandler($client, [
	'index' => 'monolog_index_test',
	'type' => 'record'
]);

$logger->pushHandler($elkHanlder);

$logger->error("this is a test msg from monolog");
```