# @tars/winston-tars

`@tars/winston-tars` 提供基于 [winston](https://github.com/flatiron/winston) 的 TARS 扩展，以提供符合 TARS 框架的日志格式与输出。

在 `@tars/winston-tars` 中提供 4 种 `transport` 对象：

- **TarsBase:** 提供符合 `TARS日志` 的基础类
- **TarsRotate:** 提供按大小输出的滚动日志
- **TarsDate:** 提供按日期输出的日志
- **TafRemote:** 输出至远程日志（tars.tarslog）

并提供一种自定义日志级别：

- **TarsConfig:** 提供符合 TARS 框架标准的日志级别与颜色值

以及相关的辅助方法：

- **Formatter:** 提供了符合 TARS 日志格式标准的内容格式化方法
- **DateFormat:** 定义了与时间相关日志滚动的处理方法

**请注意：如果您的服务在 `TARS平台` 上运行，应直接使用** [**@tars/logs**](https://github.com/tars-node/logs) **模块，更为便捷**

### 安装

`npm install @tars/winston-tars`

### 使用

```javascript
var winston = require("winston");

// Requiring `@tars/winston-tars` will expose

// transports
// `winston.transport.TarsRotate`
// `winston.transport.TarsDate`

// config
// `winston.config.tars.levels`
// `winston.config.tars.colors`
require("@tars/winston-tars");
```

### 日志格式

对于支持 [formatter](https://github.com/winstonjs/winston#custom-log-format) 的 `transport` 对象，`@tars/winston-tars` 提供 2 种方法格式化日志内容：

- **详细日志:** Formatter.Detail\(\[options\]\)
- **精简日志:** Formatter.Simple\(\[options\]\)

**options**:

- **separ**: 日志内容项与项之间的分隔符， _默认值为 \|_

#### 详细日志：

```javascript
var winston = require("winston");
var winstonTars = require("@tars/winston-tars");

var logger = new winston.Logger({
  transports: [
    new winston.transports.Console({
      formatter: winstonTars.Formatter.Detail(),
    }),
  ],
});
```

输出日志的格式为：`日期 时间|PID|日志级别|文件名与行号|内容`

其中 `文件名与行号` 部分可选（详见 [Metadata](https://github.com/tars-node/winston-tars#metadata) 节）

#### 精简日志

```javascript
var winston = require("winston");
var winstonTars = require("@tars/winston-tars");

var logger = new winston.Logger({
  transports: [
    new winston.transports.Console({
      formatter: winstonTars.Formatter.Simple(),
    }),
  ],
});
```

输出日志的格式为：`日期 时间|内容`

### TarsConfig

`winston.config.tars` 提供了符合 TARS 框架标准的日志级别（`levels`）与颜色（`colors`）

TARS 框架的日志级别从低到高（及其对应的颜色）为：

- info : white
- debug : cyan
- warn : yellow
- error : red
- none : grey

在使用时需要主动引入：

```javascript
logger.setLevels(winston.config.tars.levels);

winston.addColors(winston.config.tars.colors);
```

### TarsBase

此模块可单独使用，也可作为其他日志模块的基类。

模块实现了与现有 `TARS` 日志类似的管理方式：

- 定时重新打开日志文件，以便获取 `fd` 的变更。（当用户删除/移动当前文件后，模块会自动创建新文件）
- 在文件打开的过程中，产生的日志将会写入临时内存队列，等待文件打开完成后一次性写入。（只要不超过队列最大长度，所有日志均会写入文件）
- 此模块使用文件名 `options.filename` 作缓存，所以使用相同的 `options.filename` 多次实例化此模块仅会产生一个单例（共享一个实例）

#### 独立使用

```javascript
winston.add(winston.transports.TarsBase, options);
```

**options**:

- **filename**: 输出的文件名
- **interval**: 重新打开日志文件的间隔， _默认值为 5000ms_
- **bufferSize**: 临时队列的最大长度（单位为 条）， _默认值为 10000 条_
- **prefix**: 每条日志内容前缀， _默认值为 空_
- **formatter**: 定义日志内容格式化方法， _默认值为 Formatter.Detail\(\)_

#### 作为其他类的基类

需要重写如下 2 个对象：

- TarsBase.prototype.name：Transport 模块名
- TarsBase.prototype.\_checkfile\(callback\)：当重新打开日志文件时会调用此函数，函数处理完成之后，应显式调用 `callback([err])`

例如：

```javascript
var TarsFile = function (options) {
  var instance = TarsBase.call(this, options);

  //由于父类存在缓存，所以这里需判断父类是否有返回值，如存在（命中缓存）则直接返回无需继续初始化

  if (instance) {
    return instance;
  }

  //业务代码
};
util.inherits(TarsFile, TarsBase);

TarsFile.prototype.name = "tarsFile";

TarsFile.prototype._checkfile = function (cb) {
  //业务代码
  cb();
};

winston.transports.TarsFile = TarsFile;
```

### TarsRotate

此模块继承于 `TarsBase`

提供按文件大小输出的滚动日志

当到达设定的最大大小时，会自动向下滚动，并创建一个新的日志文件。

例如：app.log 文件写到最大大小，则会将会执行如下过程：

> delete app_n.log  
> app_n-1.log ===&gt; app_n.log  
> ... ...  
> app_1.log ===&gt; app_2.log  
> app.log ===&gt; app_1.log  
> create app.log

```text
  winston.add(winston.transports.TarsRotate, options)
```

**options**:

- **filename**: 输出的文件名
- **maxFiles**: 最大的文件总数（也就是例子中的 n）， _默认值为 10_
- **maxSize**: 单文件最大大小（单位为 bytes）， _默认值为 10M_
- **concatStr**: 日志文件名中字符间的连接符， \_默认值为 \_\_
- **formatter**: 定义日志内容格式化方法， _默认值为 Formatter.Detail\(\)_

#### TarsRotate.Master

如果业务脚本通过 [Cluster](http://www.nodejs.org/api/cluster.html)（多进程方式启动的）： Worker 则只负写入文件、而移动文件由 Master 完成，以解决多进程资源竞争问题。

当服务进程（Worker）打开日志文件准备写入时会向主进程发送消息，如下：

```json
{
	cmd : 'log:rotate',
	msg : {
		filename : String, //文件名
		interval : Number, //多久打开一回文件
		maxFiles : Number, //最大文件数
		maxSize : Number, //最大单个文件大小
		concatStr ： String //日志文件名中字符间的连接符
	}
}
```

主进程（Master）收到消息后，需透传给 `TarsRotate.Master.start` 方法，完整的例子如下：

```javascript
worker.on("message", function (msg) {
  if (msg && typeof msg === "object" && msg.cmd === "log:rotate") {
    var data = msg.msg;
    TarsRotate.Master.start(
      data.filename,
      data.interval,
      data.maxFiles,
      data.maxSize,
      data.concatStr
    );
  }
});

process.on("exit", function () {
  TarsRotate.Master.close();
});
```

**如果服务通过** [**node-agent**](https://github.com/tars-node/node-agent) **（或在 TARS 平台）运行，则无需配置平台）运行，无需显式调用此模块。只需按照平时的写法 `console.[log|info|warn|error]` 即可正确的输出滚动日志**

### DateFormat

定义了与时间相关的日志（`TarsDate`）滚动的处理方法：

- 按 1 天日志：LogByDay\(\[interval, pattern\]\)
  - **interval**: 1 _每隔一天滚动一份日志_
  - **pattern**: %Y%m%d _年月日_ （如：20150101）
- 按 1 小时日志：LogByHour\(\[interval, pattern\]\)
  - **interval**: 1 _每隔一小时滚动一份日志_
  - **pattern**: %Y%m%d%H _年月日小时_ （如：2015010110）
- 按 10 分钟日志：LogByMinute\(\[interval, pattern\]\)
  - **interval**: 10 _每隔十分钟滚动一份日志_
  - **pattern**: %Y%m%d%H%M _年月日小时分钟_ （如：201501011020）
- 自定义格式日志：LogByCustom\(pattern\)
  - **pattern**: 需要用户自定义

其中 `pattern` 为日志文件名中的时间格式，可参见 `linux strftime`

#### 例子

每隔 1 天 滚动一份日志

> DateFormat.LogByDay  
> 或者  
> new DateFormat.LogByDay\(\)

每隔 20 分钟 滚动一份日志

> new DateFormat.LogByMinute\(20\)

每隔 20 分钟 滚动一份日志，文件名中时间格式为 %Y-%m-%d\_%H:%M

> new DateFormat.LogByMinute\(20, '%Y-%m-%d\_%H:%M'\)

### TarsDate

此模块继承于 `TarsBase`

提供按日期（年、月、日、小时、分）输出的日志

当到达设定的时间间隔时，会自动创建一个新的日志文件

输出的文件名的格式为：filename\_\[%Y\|%m\|%d\|%H\|%M\].log，如：app_20141015.log

```text
  winston.add(winston.transports.TarsDate, options)
```

**options**:

- **filename**: 输出的文件名
- **concatStr**: 日志文件名中字符间的连接符， \_默认值为 \_\_
- **format**: 创建新文件的间隔，为 DateFormat 对象， _默认值为 FORMAT.LogByDay_
- **formatter**: 定义日志内容格式化方法， _默认值为 Formatter.Simple\(\)_

为了方便使用 TarsDate.FORMAT = DateFormat

### TarsRemote

提供远程日志的功能，会将日志输出至 `tars.tarslog.LogObj` 服务。

请注意：这里并不是一收到日志就会发送，而是将日志整合在一起定时发送。

```javascript
var logger = new winston.Logger({
  transports: [new winston.transports.TarsRemote(options)],
});
```

**options**:

- **filename**: 远端日志文件名（不需要包含日期、路径等附加信息）
- **tarsConfig**: tars 配置文件路径 或 已配置的 `@tars/utils.Config` 实例
- **tarsLogServant**: 远程日志服务 Servant Obj，_默认读取配置文件 `tars.application.server.log` 节_
- **interval**: 发送日志的间隔， _默认值为 500ms_
- **format**: 创建新文件的间隔，为 DateFormat 对象， _默认值为 FORMAT.LogByDay_
- **hasSufix**: 日志文件名是否带.log 后缀， _默认值为 true_
- **hasAppNamePrefix**: 是否允许框架在日志文件名上增加业务相关的标识， _默认值为 true_
- **concatStr**: 日志文件名中用户自定义字符与日期字符间的连接符， \_默认值为 \_\_
- **separ**: 日志内容项之间的分隔符， _默认值为 \|_
- **formatter**: 定义日志内容格式化方法， _默认值为 Formatter.Detail\(\)_

_请注意：在 `TarsRemote` 中 `options.format` 不能为 `FORMAT.LogByCustom`_

为了方便使用 TarsRemote.FORMAT = DateFormat

**如果服务通过** [**node-agent**](https://github.com/tars-node/node-agent) **（或在 TARS 平台）运行，则无需配置 `options.tarsConfig` 项**

### Metadata

通过指定 [Metadata](https://github.com/winstonjs/winston#logging-with-metadata) 可输出 2 种附加数据。

#### pid

通过 `pid` 属性，可以指定日志输出条目中 _进程 id_ 部分，默认情况下为 `process.pid`

如：

```javascript
logger.info("data", {
  pid: 123456,
});
```

则输出：

> 2015-01-01 00:00:01\| **123456** \|INFO\|data

#### lineno

通过 `lineno` 属性，可以指定日志输出条目中 _文件名与行号_ 部分，默认值为空（也就是不输出这一节）。

如：

```javascript
logger.info("data", {
  lineno: "app.js:6",
});
```

则输出：

> 2015-01-01 00:00:01\|123456\|INFO\| **app.js:6** \|data
