# 使用实践

## Tars 使用相关

### tars协议

tars协议采用接口描述语言（Interface description language，缩写IDL）来实现，它是一种二进制、可扩展、代码自动生成、支持多平台的协议，使得在不同平台上运行的对象和用不同语言编写的程序可以用PRC远程调用的方式相互通信交流， 主要应用在后台服务之间的网络传输协议，以及对象的序列化和反序列化等方面。

协议支持的类型分两种，基本类型和复杂类型。

基本类型包括：void、bool、byte、short、int、long、float、double、string、unsigned byte、unsigned short、unsigned int；

复杂类型包括：enum、const、struct、vector、map，以及struct、vector、map的嵌套。

### 开发和打包

不同语言在开发上存在差异，但基本思路一致：

1. 编写tars文件xx.tars； 比如

```text
module MTT
{
	enum MYE1
	{
		EM_CAT,
		EM_DOG,
	};
	struct A
	{
		0 require int b;
	};
    struct HelloWorld
    {
        0 require int nId;
        1 require string sMsg;
        2 require vector<string> vNews;
		///4 optional string sNew="client new";
		5 require map<int,int> mAddr;
		6 require A a;
    };

	struct HelloPerson
	{
		0 require int nId;
		1 require string sName;
		2 optional vector<string> vFavor;
		3 optional MYE1 ePet;
		///4 optional byte aPetAge[5];
		5 optional map<int,string> mAddr;
		6 optional string sPhone;
		7 optional bool bMan;
		9 optional HelloWorld shello;
	};

    interface Hello
    {
        int testHello(string s,vector<short> vsh,out string r);
		int testPerson(HelloPerson stPersonIn,out HelloWorld stPersonOut);
    };
};
```

1. 利用tars官方转换工具根据tars文件内容生成客户端或服务端代码\(在配置文件中指定生成客户端还是服务端，注意命名规范\)；
2. 完善代码逻辑（完形填空）；
3. 打包代码；
4. 通过web管理界面添加应用信息（与tars定义保持一致）并上传以上步骤最终的打包代码并发布。
5. 代码打包文件上传位置/data/tars/patchs/tars.upload/

### 发布

代码打包同时会执行发布过程，还可以在服务管理列表中重启或关闭处理。

打包程序上传后，会放置在应用服务器的/usr/local/app/tars/tarsnode/data目录下，——应用名+服务名，服务名PHPTest，服务名PHPHttpServer则会将代码解压放置在PHPTest.PHPHttpServer目录下

每个完整的目录下会放置bin、conf、data文件夹，基本作用为：

* bin目录——放置解压后的程序文件，tars同时自动生成对应的启动命令文件和关闭命令文件 tars\_start.sh、tars\_stop.sh （也可以跳过web管理界面直接使用此文件开启/关闭服务）
* conf目录下，则存放此应用相关的服务模版配置文件——结合应用发布填写的配置项生成的最终配置文件；
* data目录下放置服务的缓存信息文件，以及进程id文件等

### 日志查看

日志目录位置 /usr/local/app/tars/app\_log/，在此目录下的应用名称目录下找到对应的日志文件，如 /usr/local/app/tars/app\_log/PHPTest/PHPHttpServer/PHPTest.PHPHttpServer.log

服务打的日志路径在/usr/local/app/tars/app\_log/服务的应用名/服务的服务名/目录下，例如:/usr/local/app/tars/app\_log/Test/HelloServer/

服务的可执行文件在/usr/local/app/tars/tarsnode/data/服务的应用名.服务的服务名/bin/下,例如:/usr/local/app/tars/tarsnode/data/Test.HelloServer/bin/

服务的模版配置文件在/usr/local/app/tars/tarsnode/data/服务的应用名.服务的服务名/conf/下,例如:/usr/local/app/tars/tarsnode/data/Test.HelloServer/conf/

服务的缓存信息文件在/usr/local/app/tars/tarsnode/data/服务的应用名.服务的服务名/data/下,例如:/usr/local/app/tars/tarsnode/data/Test.HelloServer/data/

## Tars PHP 开发相关

### PHP服务模版配置

每个Tars服务启动运行时，必须指定一个模版配置文件，在Tars web管理系统中部署的服务的模版配置由node进行组织生成，若不是在web管理系统上，则需要自己创建一个模版文件。具体[https://github.com/Tencent/Tars/blob/master/docs/tars\_template.md](https://github.com/Tencent/Tars/blob/master/docs/tars_template.md)

对php开发，首先在web管理界面中-&gt;运维管理-&gt;模板管理，找到tars.tarsphp.default模板，根据实际php安装位置修改，如:

```text
php=/usr/bin/php/bin/php
```

同时，将tars.tarsphp.default内容复制，新建tcp、http、timer版本的模板 相比较将tars.tarsphp.default,http模板，差异为：

http模板在server节点增加：

```text
protocolName=http
type=http
```

TCP模板在server节点增加：

```text
package_length_type=N
open_length_check=1
package_length_offset=0
package_body_offset=0
package_max_length=2000000
protocolName=tars
type=tcp
```

timer模板在server节点增加：

```text
protocolName=http
type=http
isTimer=1
```

注：

* 父模板名均选择tars.default即可
* 文档中protocolName、type的说明缺失，实际使用中发现会报错，保险起见按照以上说明配置



