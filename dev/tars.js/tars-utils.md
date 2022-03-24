# @tars/utils

## @tars/utils

TARS 框架辅助工具集合

### Installation

```text
$ npm install @tars/utils
```

### 01. 配置文件解析器

```javascript
var Config = require("@tars/utils").Config;
```

#### API

**parseFile\(sFilePath, \[encoding, callback\]\)**

解析指定文件

- `sFilePath`: 文件名
- `encoding`: 文件编码类型。 \(默认值: utf8\)
- `callback`: 回调函数，回调函数的格式 function callback\(ret, config\){}, 其中 ret 为对象{code: 返回码，成功为 0， 失败为-1, message: 描述, exception:如果成功为 undefined，如果失败为事件对象}， config 为解析器本身

#### parseText\(sText\)

解析字符串，并将解析的结果存于内部的\_data 属性中，可以通过 get 方法获取相应的值

- `sText`: 字符串
- `return`: true：解析成功, false: 解析失败

**get\(key, defaultValue\)**

文件被解析之后，会将结果存储到一个对象中，通过 get 方法可以获取制定的值。注：如果配置文件/字符串中有相同的 key，则 get 获取 key 对应的值时，不会获取所有的值，而是获取该 key 最后对应的那个值，也可以理解为对应相同的 key 后面的值覆盖前面的值。

- `key`: 需要取值的 key 值，格式为 x1.x2.x3，其中 x1，x2，x3 依次为深层次的 key，注：如果 key 值本身为 x1.x2 格式，取该 key 对应的值时需要写成&lt;x1.x2&gt;,具体使用参见例子。
- `defaultValue`: 取不到结果的默认值

**getDomain\(key, defaultValue\)**

获取 key 对应的值中类型为 Object 的属性数组

- `key`: key 值。
- `defaultValue`: 取不到结果的默认值

**getDomainValue\(key, defaultValue\)**

获取 key 对应的值中类型为 Object 的属性值数组

- `key`: key 值。
- `defaultValue`: 取不到结果的默认值

**getDomainLine\(key, defaultValue\)**

获取 key 对应路径下的所有非空行

- `key`: key 值。
- `defaultValue`: 取不到结果的默认值
- `return`: 数组

**data**

通过该属性，可以获取文件解析的结果

### example

```javascript
var Config = require("@tars/utils").Config;

var config = new Config();
config.parseFile("./config.conf", "utf8");

var data = config.data;
console.log("data: ", data);
console.log(
  "get: tars.application.server.local: ",
  config.get("tars.application.server.local")
);
console.log(
  "getDomain: tars.application.server: ",
  config.getDomain("tars.application.server")
);
console.log(
  "getDomainValue: tars.application.server: ",
  config.getDomainValue("tars.application.server")
);
```

具体例子参见 examples 目录下的 test-config.js 文件

### 02. Endpoint 工具

```javascript
var Endpoint = require("@tars/utils").Endpoint;
```

#### API

**Class 方法：parse\(desc\)**

从字符串中解析出 Endpoint 信息

- `desc`: 字符串，例如：'tcp -h 127.0.0.1 -p 10000 -t 60000'
- `return`: 返回 Endpoint 实例。

**toString\(\)**

Endpoint 信息转化成字符串

**copy\(\)**

拷贝 Endpoint 实例

### example

```javascript
var Endpoint = require("@tars/utils").Endpoint;

var endpoint = Endpoint.parse("tcp -h 127.0.0.1 -p 10000 -t 60000");
console.log("endpoint: " + endpoint.toString());
console.log("endpoint.copy: " + endpoint.copy().toString());
```

具体例子参见 examples 目录下的 test-endpoint.js 文件

### 03. timeProvider 工具

```javascript
var timeProvider = require("@tars/utils").timeProvider;
```

#### API

**nowTimestamp\(\)**

采用 Date.now\(\)的方式获取时间，此种方式效率最高，Date.now\(\)的方式的效率大概是 new Date\(\).getTime\(\)的 2 倍，是 process.hrtime\(\)方式的 4 倍。

- `return`: 返回对象

```json
{
    hrtime: // 数组类型，[秒, 纳秒],
    timestamp: // 单位ms
}
```

**diff\(oTime\)**

当前时间相对于 oTime 的时间间隔

- `oTime`: 相对时间，nowTimestamp 函数返回的对象类型
- `return`: 浮点类型，时间间隔，单位毫秒
- 注：nowTimestamp 和 diff 配对使用

**dateTimestamp\(\)**

获取当前的时间戳, 即机器从启动到当前的时间（process.hrtime）

- `return`: 返回对象

```json
{
    hrtime: // 数组类型，[秒, 纳秒],
    timestamp: // 单位ms
}
```

**dateTimestampDiff\(oTime\)**

当前时间相对于 oTime 的时间间隔

- `oTime`: 相对时间，dateTimestamp 函数返回的对象类型
- `return`: 浮点类型，时间间隔，单位毫秒
- 注：dateTimestamp 和 dateTimestampDiff 配对使用

### example

```javascript
var timeProvider = require("@tars/utils").timeProvider;

var i = 0,
  count = 10000000;
var tt1,
  tt2,
  interval = 0;
var t1 = new Date().getTime();
var t2 = t1;

tt1 = timeProvider.nowTimestamp();
for (i = 0; i < count; i++) {
  tt2 = timeProvider.diff(tt1);
}
t2 = new Date().getTime();
console.log("【hrTime】interval: " + (t2 - t1));

t1 = new Date().getTime();
tt1 = timeProvider.dateTimestamp();
for (i = 0; i < count; i++) {
  tt2 = timeProvider.dateTimestampDiff(tt1);
}
t2 = new Date().getTime();
console.log("【hrTime】interval: " + (t2 - t1));
```

具体例子参见 examples 目录下的 test-timer.js 文件

### 03. Promise 库

```javascript
var Promise = require("@tars/utils").Promise;
```

为 TARS 应用提供一个方便统一的 Promise 库。开发 TARS 应用时推荐大家使用此库而不是自己选择 Promise 库，当出现更好的 promise 方案时，我们可以直接替换此模块中的实现，直接对所有应用生效。

```javascript
var Promise = require("@tars/utils").Promise;
var promise = new Promise(function (resolve, reject) {
  setTimeout(function () {
    resolve(666);
  }, 3000);
});
promise.then(function (data) {
  console.log(data);
});
```

目前 TARS 中的 Promise 是基于 bluebird 库实现的，在 q、bluebird、原生 Promise 中 bluebird 性能最好。
