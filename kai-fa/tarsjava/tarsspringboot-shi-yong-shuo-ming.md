# Tars-Spring-boot Instructions

## Function Description

Tars supports writing tars services using spring boot. To use this function, you need to rely on the tars-spring-boot-starter.jar package, and spring boot 2.0 and above. You can use your servant as a Spring bean, and the annotation exposes the spring bean.

## Dependency Configuration

To use this function, you need to add dependencies. Add the following configuration in pom.xml:

```text
    <properties>
        <spring-boot.version>2.0.3.RELEASE</spring-boot.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring-boot.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <dependencies>
        <dependency>
            <groupId>com.tencent.tars</groupId>
            <artifactId>tars-spring-boot-starter</artifactId>
            <version>1.6.1</version>
        </dependency>
    </dependencies>
```

### Servant configuration

In spring boot, you need to enable the tars service related functions through annotations:

```text
@SpringBootApplication
@EnableTarsServer
public class QuickStartApplication {
    public static void main(String[] args) {
        SpringApplication.run(QuickStartApplication.class, args);
    }
}
```

Annotate @EnableTarsServer to identify this as a TARS service and enable service-related functions.

Write tars protocol files, such as:

```text
module TestApp
{
	interface Hello
	{
	    string hello(int no, string name);
	};
};
```

And generate the corresponding interface code through the maven plugin provided by TARS:

```text
@Servant
public interface HelloServant {

	public String hello(int no, String name);
}
```

The service logic is written by implementing the interface:

```text
@TarsServant("HelloObj")
public class HelloServantImpl implements HelloServant {

    @Override
    public String hello(int no, String name) {
        return String.format("hello no=%s, name=%s, time=%s", no, name, System.currentTimeMillis());
    }
}
```

The implementation class of the interface exposes the service by annotating @TarsServant. The 'HelloObj' filled in is the servant name, which can correspond to the name on the management platform.

## Write an HTTP service

In addition, if you want to use spring-boot to write an http service without using the taf interface:

```text
@SpringBootApplication
@EnableTarsServer
@TarsHttpService("HttpObj")
@RestController
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
	
	@RequestMapping(path = "/test")
    public String test() {
        return "hello world";
    }
}
```

At this time, add a @TarsHttpService annotation. In this annotation, you need to add the name of the non-tars protocol servant you want to bind. The framework will automatically bind the port of the container embedded in spring-boot to the port of the corresponding servant. It is convenient to use various annotations of spring-mvc to develop http and web services.

## Client injection

In order to simplify the construction of the client, spring-boot-starter provides the function of client automatic injection. Add the @TarsClient annotation to the properties you need to inject the client, and the framework will automatically help you construct and inject the client:

```text
@TarsServant("HelloObj")
public class HelloWorldServantImpl implements HelloWordServant {
    @TarsClient("TarsJavaTest.SpringBootServer.HelloObj")
    HelloWordPrx prx;
}
```

As in the above code, the HelloWordPrx client can be injected through @TarsClient \ ("TarsJavaTest.SpringBootServer.HelloObj" \). If you only fill in the Obj name, the default value is injected into the client. Of course, you can also customize the client configuration in the annotation. :

```text
@TarsServant("HelloObj")
public class HelloWorldServantImpl implements HelloWordServant {
    @TarsClient(name = "TarsJavaTest.SpringBootServer.HelloObj", syncTimeout = 1000)
    HelloWordPrx prx;
}
```
This sets the client synchronization timeout. This annotation provides configuration items for all common configurations:

```text
@Target({ ElementType.FIELD })
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface TarsClient {
    @AliasFor("name")
    String value() default "";

    @AliasFor("value")
    String name() default "";

    String setDivision() default "";

    boolean isgray() default false;

    int connections() default Constants.default_connections;

    int connectTimeout() default Constants.default_connect_timeout;

    int syncTimeout() default Constants.default_sync_timeout;

    int asyncTimeout() default Constants.default_async_timeout;

    boolean enableSet() default false;

    boolean tcpNoDelay() default false;

    String charsetName() default "UTF-8";
}
```

Including the number of connections, synchronous asynchronous timeout time, character set, etc. can be configured in the annotation.

## Service Release

After writing the code, you can use the spring-boot-maven-plugin plugin provided by spring boot to package, package the service into a jar package, and upload it to start.

## How to start and develop debugging tars locally

Copy the template file generated by node to the local (in the tasnode / data / service name / conf directory of the server), modify the startup ip and port text of each servant and configure the startup parameters. -Dconfig = \ (template path \) start MainClass via ide

## Version Upgrade Guide

If you need to use the new functions of tars-spring-boot, you need to upgrade tars to version 1.6.1 and above.

1. The management platform needs to be recompiled and upgraded.
2. tars-node needs to be upgraded to the new version.
3. For template selection, select tars.tarsjava.springboot template. If you are not rebuilding the environment, you can add templates by yourself. The parent template selects tars.tarsjava.default, as follows:
```text
<tars>
<application>
<server>
packageFormat=jar
mainclass=-jar ${basepath}/${app}.${server}.jar
</server>
</application>
</tars>
```

