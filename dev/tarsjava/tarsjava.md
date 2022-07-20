# TarsJava 快速入门

## 服务开发

### 接口文件定义

接口文件定义是通过Tars接口描述语言来定义，在src/main/resources目录下建立hello.tars文件，内容如下

```java
module TestApp 
{
	interface Hello
	{
	    string hello(int no, string name);
	};
};
```

### 接口文件编译

提供插件编译生成java代码，在tars-maven-plugin添加生成java文件配置

```xml
<plugin>
	<groupId>com.tencent.tars</groupId>
	<artifactId>tars-maven-plugin</artifactId>
	<version>1.6.1</version>
	<configuration>
		<tars2JavaConfig>
			<!-- tars文件位置 -->
			<tarsFiles>
				<tarsFile>${basedir}/src/main/resources/hello.tars</tarsFile>
			</tarsFiles>
			<!-- 源文件编码 -->
			<tarsFileCharset>UTF-8</tarsFileCharset>
			<!-- 生成服务端代码 -->
			<servant>true</servant>
			<!-- 生成源代码编码 -->
			<charset>UTF-8</charset>
			<!-- 生成的源代码目录 -->
			<srcPath>${basedir}/src/main/java</srcPath>
			<!-- 生成源代码包前缀 -->
			<packagePrefixName>com.qq.tars.quickstart.server.</packagePrefixName>
		</tars2JavaConfig>
	</configuration>
</plugin>
```

在工程根目录下执行mvn tars:tars2java后，会自动创建 HelloServant.java 文件，该文件内包含一下代码

```java
@Servant
public interface HelloServant {

	public String hello(int no, String name);
}	
```

### 服务接口实现

新创建一个HelloServantImpl.java文件，实现HelloServant.java接口

```java
public class HelloServantImpl implements HelloServant {

    @Override
    public String hello(int no, String name) {
        return String.format("hello no=%s, name=%s, time=%s", no, name, System.currentTimeMillis());
    }
}
```

### 服务暴露配置

在resources下创建一个servants.xml的配置文件，服务编写后需要进程启动时加载配置暴露服务，配置如下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<servants>
	<servant name="HelloObj">
		<home-api>com.qq.tars.quickstart.server.testapp.HelloServant</home-api>
		<home-class>com.qq.tars.quickstart.server.testapp.impl.HelloServantImpl</home-class>
	</servant>
</servants>
```

说明：除了此方法之外，还可以采用spring模式来配置服务，详情见tars\_java\_spring.md。

### 服务编译打包

在工程根目录下执行 mvn package生成war包，后续可以管理系统进行发布。

### 客户端同步/异步调用服务

* 构建客户端工程项目
* 添加依赖

```xml
<dependency>
	<groupId>com.tencent.tars</groupId>
   	<artifactId>tars-client</artifactId>
   	<version>1.7.2</version>
   	<type>jar</type>
</dependency>    
```

* 添加插件

```xml
  <plugin>
   	<groupId>com.tencent.tars</groupId>
   	<artifactId>tars-maven-plugin</artifactId>
   	<version>1.6.1</version>
   	<configuration>
   		<tars2JavaConfig>
   			<!-- tars文件位置 -->
   			<tarsFiles>
   				<tarsFile>${basedir}/src/main/resources/hello.tars</tarsFile>
   			</tarsFiles>
   			<!-- 源文件编码 -->
   			<tarsFileCharset>UTF-8</tarsFileCharset>
   			<!-- 生成代码，PS：客户端调用，这里需要设置为false -->
   			<servant>false</servant>
   			<!-- 生成源代码编码 -->
   			<charset>UTF-8</charset>
   			<!-- 生成的源代码目录 -->
   			<srcPath>${basedir}/src/main/java</srcPath>
   			<!-- 生成源代码包前缀 -->
   			<packagePrefixName>com.qq.tars.quickstart.client.</packagePrefixName>
   		</tars2JavaConfig>
   	</configuration>
  </plugin>

```


- 根据服务tars接口文件生成代码


```java

@Servant
public interface HelloPrx {
      
	public String hello(int no, String name);
      
      	public String hello(int no, String name, @TarsContext java.util.Map<String, String> ctx);
      
      	public void async_hello(@TarsCallback HelloPrxCallback callback, int no, String name);
      
      	public void async_hello(@TarsCallback HelloPrxCallback callback, int no, String name, @TarsContext java.util.Map<String, String> ctx);
}
```


* 同步调用

```java

public static void main(String[] args) {
		CommunicatorConfig cfg = new CommunicatorConfig();
		//这里的主控要换成实际的主控地址才可以你
	    cfg.setLocator("tars.tarsregistry.QueryObj@tcp -h 172.25.0.1 -t 60000 -p 17890");
        //构建通信器
        Communicator communicator = CommunicatorFactory.getInstance().getCommunicator(cfg);
        //通过通信器，生成代理对象，线程安全，不需要多次实例化
        HelloPrx proxy = communicator.stringToProxy(HelloPrx.class, "TestApp.HelloServer.HelloObj");
        String ret = proxy.hello(1000, "HelloWorld");
        System.out.println(ret);
}

```

* 异步调用

```java
public static void main(String[] args) {
		CommunicatorConfig cfg = new CommunicatorConfig();
		cfg.setLocator("tars.tarsregistry.QueryObj@tcp -h 172.25.0.1 -t 60000 -p 17890");
        //构建通信器
        Communicator communicator = CommunicatorFactory.getInstance().getCommunicator(cfg);
        //通过通信器，生成代理对象，线程安全，不需要多次实例化
        HelloPrx proxy = communicator.stringToProxy(HelloPrx.class, "TestApp.HelloServer.HelloObj");
        proxy.async_hello(new HelloPrxCallback() {
        		
        	@Override
        	public void callback_expired() {
        	}
        		
        	@Override
        	public void callback_exception(Throwable ex) {
        	}
        		
        	@Override
        	public void callback_hello(String ret) {
        		System.out.println(ret);
        	}
        }, 1000, "HelloWorld");
}
```



