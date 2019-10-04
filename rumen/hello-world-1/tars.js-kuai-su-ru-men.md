# Tars.js 快速入门

在 Tars 平台 -&gt; 服务管理 -&gt; 服务上线，上线一个新服务：

* 服务基本信息：
  * 服务名称：HelloWorld
  * 服务类型：NODEJS
  * 模板名称：tars.cloud.default
* OBJ 部署信息：
  * OBJ名称：HttpObj
  * 是TARS：选择否（移除复选框）
  * 端口：自动生成端口

安装 [@tars/deploy](https://www.npmjs.com/package/@tars/deploy) 打包工具。

```text
npm i -g @tars/deploy
```

根据业务需求以及您的喜好选择框架（！Tars.js 不限制您使用的框架！），编写业务代码：

```text
const http = require('http');

const hostname = process.env.IP || '127.0.0.1';
const port = process.env.PORT || 3000;

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World\n');
});

server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});
```

上述为 Node.js 官网 [HTTP Server](https://nodejs.org/en/about/) 例子，在 `Tars.js` 平台上运行只需要修改 IP 和端口即可。 这里在环境变量中获得到的 IP 和 PORT 即为您在第一步配置的 OBJ 部署信息中的 IP 与端口。

在项目根目录下执行打包工具，即可生成发布包 HelloWorld.tgz：

```text
tars-deploy HelloWorld
```

在 Tars 平台上选择您刚刚创建的服务 -&gt; 发布管理 -&gt; 手动上传发布包，然后发布此版本即可。

通过您在第一步配置的 IP 与 端口即可访问您的服务。

