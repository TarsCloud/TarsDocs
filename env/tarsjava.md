# 目录
> * [环境依赖](#chapter-1)
> * [构建工程](#chapter-1)
> * [配置依赖](#chapter-1)

# <span id="chapter-1"></span>环境依赖

JDK 1.8 版本及以上

Maven 2.2.1 版本及以上

# <span id="chapter-2"></span>构建工程

通过IDE或者maven创建一个maven web项目， 这里以eclipse为例，File -&gt; New -&gt; Project -&gt; Maven Project -&gt; maven-archetype-webapp，再输入groupId、artifactId，生成完成之后可以通过eclipse进行导入，目录结构如下

```text
├── pom.xml
└── src
   ├── main
   │   ├── java
   │   │   └── tars
   │   │       └── test
   │   │          ├── HelloServant.java
   │   │          └── HelloServantImpl.java
   │   ├── resources
   │   │   └── servants.xml
   │   └── webapp
   └── test
       ├── java
       │   └── tars
       │       └── test
       │           └── TarsTest.java
       └── resources
```

# <span id="chapter-3"></span>依赖配置

在构建项目中pom.xml中添加依赖jar包

* 框架依赖配置

```text
<dependency>
	<groupId>com.tencent.tars</groupId>
     	<artifactId>tars-server</artifactId>
     	<version>1.6.1</version>
     	<type>jar</type>
</dependency>
```

* 插件依赖配置

```text
<plugin>
	<groupId>com.tencent.tars</groupId>
   	<artifactId>tars-maven-plugin</artifactId>
   	<version>1.6.1</version>
  	<configuration>
   		<tars2JavaConfig>
  			<tarsFiles>
   				<tarsFile>${basedir}/src/main/resources/hello.tars</tarsFile>
   			</tarsFiles>
  			<tarsFileCharset>UTF-8</tarsFileCharset>
   			<servant>true</servant>
  			<srcPath>${basedir}/src/main/java</srcPath>
  			<charset>UTF-8</charset>
   			<packagePrefixName>com.qq.tars.quickstart.server.</packagePrefixName>
  		</tars2JavaConfig>
   	</configuration>
</plugin>
```

