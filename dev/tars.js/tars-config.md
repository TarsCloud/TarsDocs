# @tars/config

## Tars-Config

`Tars` 框架中用于获取服务配置文件。

### 安装

`npm install @tars/config`

### 实例化

使用前需先实例化 `var config = new TarsConfig(data)` 对象

其中：

**data**: 为 tars 配置文件路径 或 已配置的 `@tars/config-parser` 实例。

**如果服务通过** [**node-agent**](https://github.com/tars-node/node-agent) **（或在Tars平台）运行，则无需传入 `data` 。**

### 枚举

#### FORMAT

定义了配置文件的格式：

* **C**: C++ 服务格式
* **JSON**: JSON 格式
* **TEXT**: 普通文本（自定义格式）

#### LOCATION

定义了配置文件存放的区域：

* **APP**: 配置文件存放于业务集下
* **SERVER**: 配置文件存放于服务下

### 接口

#### loadConfig\(\[files ,\]options\)

获取配置文件内容。

`files(String|Array)` 可以为单一文件名也可是数组，如不填写则默认获取所有文件内容。

`options(Object)` 为可选项，接受如下参数：

* **format**: 文件格式， _默认为 FORMAT.C_
* **location**: 存放区域， _默认为 LOCATION.SERVER_

调用成功后返回（Promise.resolve）由如下对象组成的数组：

* **filename**: 文件名
* **content**: 文件解析后内容

**如仅获取单一文件则返回文件解析后的内容**

**例子**

获取 `a.conf` 文件内容：

```text
config.loadConfig("a.conf").then(function(data) {
    console.log("content:", data);
}, function (err) {
    console.error("loadConfig err", err);
});
```

获取 `a.conf` 文件内容并以 json 进行解析：

```text
config.loadConfig("a.conf", {format : config.FORMAT.JSON}).then(function(data) {
    console.log("content:", data);
}, function (err) {
    console.error("loadConfig err", err);
});
```

获取存在于业务集中的 `a.conf` 文件内容：

```text
config.loadConfig("a.conf", {location : config.LOCATION.APP}).then(function(data) {
    console.log("content:", data);
}, function (err) {
    console.error("loadConfig err", err);
});
```

获取 `a.conf` 与 `b.conf` 文件内容：

```text
config.loadConfig(["a.conf", "b.conf"]).then(function(data) {
    data.forEach(function(item) {
        console.log("filename:", item.filename);
        console.log("content:", item.content);
    });
}, function (err) {
    console.error("loadConfig err", err);
});
```

获取服务所有配置文件内容：

```text
config.loadConfig().then(function(data) {
    data.forEach(function(item) {
        console.log("filename:", item.filename);
        console.log("content:", item.content);
    });
}, function (err) {
    console.error("loadConfig err", err);
});
```

#### loadList\(options\)

获取配置文件列表（所有配置文件名）。

`options(Object)` 为可选项，接受如下参数

* **location**: 存放区域， _默认为 LOCATION.SERVER_

调用成功后返回（Promise.resolve）由文件名组成的数组。

**例子**

获取服务的所有配置文件名：

```text
config.loadList().then(function(filelist) {
    console.log("files:", filelist);
}, function(err) {
    console.log("loadList error", err);
});
```

#### loadServerConfig\(options\)

获取默认配置文件（文件名由 `App.Server.conf` 组成）。

`options(Object)` 为可选项，接受如下参数

* **format**: 文件格式， _默认为 FORMAT.C_

调用成功后返回（Promise.resolve）返回文件解析后的内容。

**例子**

获取服务默认配置文件：

```text
config.loadServerConfig().then(function(data) {
    console.log("content:", data);
}, function(err) {
    console.log("loadServerConfig error", err);
});
```

### 事件

#### configPushed

由Tars平台向服务Push配置文件的时将触发此事件。

回调会给出Push下发的文件名。

**例子**

监听Push事件，并获取Push文件内容：

```text
config.on("configPushed", function(filename) {
    console.log("config pushed", filename);
    config.loadConfig(filename).then(function(data) {
        console.log("content:", data);
    }, function(err) {
        console.error("loadConfig err", err);
    });
});
```

