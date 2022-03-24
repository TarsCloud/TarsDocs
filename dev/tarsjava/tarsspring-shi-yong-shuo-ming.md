# Tars-Spring 使用说明

## 功能说明

Tars 支持使用 Spring 配置 servant，使用此功能需要依赖 tars-spring.jar 包，以及 spring 4 及以上版本。你可以将你的 servant 作为一个 Spring bean，你可以自由的使用 Spring 的功能最后只需要通过 Tars 提供的标签告知 Tars 哪些些 bean 是 servant 即可。

## 依赖配置

使用此功能需要添加依赖 jar 包，在 pom.xml 中添加如下配置：

```xml
<dependency>
      <groupId>com.tencent.tars</groupId>
      <artifactId>tars-spring</artifactId>
      <version>1.6.1</version>
</dependency>
```

## 服务暴露配置

使用 spring 配置模式需要在 resources 目录下将 tars 原版配置文件 servants.xml 改为 servants-spring.xml，如果两种配置文件均存在会优先读取 servant.xml 则不会启用 spring 模式。

### Servant 配置

在 Spring 模式下，servants-spring.xml 为 spring 配置文件，在配置文件中需要先引入 Tars 的 xsd 文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:tars="http://tars.tencent.com/schema/tars"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
       http://tars.tencent.com/schema/tars http://tars.tencent.com/schema/tars/tars.xsd">
</beans>
```

Tars 中自定义了多个 spring 标签，配置 servant 时需要先将 servant 定义为一个 bean，然后通过 Tars 标签定义为 servant：

```text
<tars:servant name="HelloObj" interface="com.qq.test.GreeterServant" ref="greeterServantImp"/>
<bean id="greeterServantImp" class="com.qq.test.impl.GreeterServantImp" />
```

servant 标签通过 name 指定 servant 名称，ref 指定对应的 bean 名，通过 interface 指定对应的接口名。当然你也可以通过添加@Component 标签然后通过 spring 自动扫描获取到 bean，同样只需要配置对应 bean 名即可：

```text
<context:component-scan base-package="com.qq.tars.test"/>
<tars:servant name="HelloObj" interface="com.qq.test.GreeterServant" ref="greeterServantImp"/>
```

### Listener 配置

listener 的配置与 servant 配置相同，也需要将你的 Listener 定义为 bean，然后通过 tars 标签将对应的 bean 指定为 Listener：

```text
<tars:listener ref="startListener"/>
<bean id="startListener" class="com.qq.test.ServiceStartListener" />
```

## 1.3.0 版本升级指南

如需使用 tars-spring 等新功能需要将 tars 升级到 1.3.0 版本及以上版本，本次改动相对较大，附上版本升级指南：

1. 管理平台需要重新编译升级。
2. tars-node 需要升级到新版本。
3. 在模版管理中修改 tars.tarsjava.default 模版的 classpath 配置项，修改为

> classpath=${basepath}/conf:${basepath}/WEB-INF/classes:${basepath}/WEB-INF/lib

1. 在构建 tars-java 项目时 servants.xml 需要放在 resources 目录下
