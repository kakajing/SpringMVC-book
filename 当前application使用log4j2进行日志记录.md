After 20 years of evolution in the Java ecosystem, the ways of Logging have seen different strategies, trends, and architectures. Nowadays, several Logging frameworks can be found among the used third-party dependencies. We must support them all to debug an application or to trace runtime events.

在Java生态系统中经历了20年的发展之后，Logging的方式看到了不同的策略，趋势和架构。 现在，可以在使用的第三方依赖项中找到几个日志记录框架。 我们必须支持他们调试应用程序或跟踪运行时事件。

# Getting ready

This recipe provides a future-proof implementation of Log4j2 for the CloudStreet Market application. It requires several Maven dependencies to be added to our modules. As a solution, it can appear quite complicated, but in reality the amount of Logging frameworks to support is limited, and the logic behind a Log4j2 migration is fairly straightforward.

该配方为CloudStreet Market应用程序提供了面向未来的Log4j2实现。 它需要几个Maven依赖项添加到我们的模块。 作为一个解决方案，它可能显得相当复杂，但在现实中支持的日志框架的数量是有限的，Log4j2迁移背后的逻辑是相当简单。

# How to do it…

1.The following Maven dependencies have been added to the dependency-management section of the parent-module \(cloudstreetmarket-parent\):

1.以下Maven依赖项已添加到父模块（cloudstreetmarket-parent）的dependency-management部分：

```java
<!-- Logging dependencies -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api</artifactId>
    <version>2.4.1</version>
</dependency>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.4.1</version>
</dependency>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j-impl</artifactId>
    <version>2.4.1</version>
</dependency>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-1.2-api</artifactId>
    <version>2.4.1</version>
</dependency>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-jcl</artifactId>
    <version>2.4.1</version>
</dependency>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-web</artifactId>
    <scope>runtime</scope>
    <version>2.4.1</version>
</dependency>
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>${slf4j.version}</version>
</dependency>
```

> The last dependency-management, `org.slf4j`, allows us to make sure one single version of slf4j will be used everywhere.
>
> 最后一个依赖管理，`org.slf4j`，允许我们确保slf4j的一个单一版本将无处不在被使用。

2.In the api, ws, and core modules, the following dependencies have then been added: log4j-api, log4j-core, log4j-slf4j-impl, log4j-1.2-api, and log4j-jcl..

3.In the web modules \(api, ws, and webapp\), log4j-web has been added..

4.Notice that slf4j-api has only been added for dependency management.

5.Start the Tomcat Server with the **extra JVM argument**:

2.在api，ws和core 模块中，添加了以下依赖关系：log4j-api，log4j-core，log4j-slf4j-impl，log4j-1.2-api和log4j-jcl。

3.在Web模块（api，ws和webapp）中，添加了log4j-web。

4.注意slf4j-api只是为依赖管理添加的。

5.使用**额外的JVM参数**启动Tomcat服务器：



> Replace `<home-directory>` with the path you actually use on your machine.
>
> 将`<home-directory>`替换为您计算机上实际使用的路径。



6.The app directory in the user home now contains the log4j2 configuration file:

6.用户home中的应用程序目录现在包含log4j2配置文件：

```java
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="OFF" monitorInterval="30">
    <Appenders> 
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout pattern"="%d{HH:mm:ss.SSS} %-5level%logger{36} - %msg%n""/>
        </Console>
        <RollingFile name="FileAppender" fileName="${sys:user.home}/app/logs/cloudstreetmarket.log"
                filePattern="${sys:user.home}/app/logs/${date:yyyyMM}/cloudstreetmarket-%d{MM-dd-yyyy}-%i.log.gz">
            <PatternLayout>
                <Pattern>%d %p %C{1} %m%n</Pattern>
            </PatternLayout>
            <Policies>
                <TimeBasedTriggeringPolicy />
                <SizeBasedTriggeringPolicy size="250 MB"/>
            </Policies>
        </RollingFile>
    </Appenders>
    <Loggers>
        <Logger name="edu.zipcloud" level="INFO"/>
        <Logger name="org.apache.catalina" level="ERROR"/>
        <Logger name="org.springframework.amqp" level="ERROR"/>
        <Logger name="org.springframework.security" level="ERROR"/>
        <Root level="WARN">
            <AppenderRef ref="Console"/>
            <AppenderRef ref="FileAppender"/>
        </Root>
    </Loggers>
</Configuration>
```

7.As a fall back option, a log4j2.xml file is also present in the classpath \(src/main/resources\) of every single module.

7.作为后退选项，log4j2.xml文件也出现在每个模块的类路径（src/main/resources）中。

8.A couple of log instructions have been placed in different classes to trace the user journey.

Log instructions in SignInAdapterImpl:

8.一对日志指令已被放置在不同的类中以跟踪用户旅程。

在SignInAdapterImpl中记录指令：

```java
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

@Transactional
public class SignInAdapterImpl implements SignInAdapter{

    private static final Logger logger = LogManager.getLogger(SignInAdapterImpl.class);
    ...
    public String signIn(String userId, Connection<?> connection, NativeWebRequest request) {
        ...
        communityService.signInUser(user);
        logger.info("User {} logs-in with OAUth2 account", user.getId());
        return view;
    }
}
```

Log instructions in UsersController:

在UsersController中记录指令：

```java
@RestController
@RequestMapping(value=USERS_PATH, produces={"application/xml", "application/json"})
public class UsersController extends CloudstreetApiWCI{

    private static final Logger logger = LogManager.getLogger(UsersController.class);
    ...
    @RequestMapping(method=POST)
    @ResponseStatus(HttpStatus.CREATED)
    public void create(@Valid @RequestBody User user, 
                @RequestHeader(value="Spi", required=false) String guid, 
                @RequestHeader(value="OAuthProvider", required=false) String provider, 
                HttpServletResponse response) throws IllegalAccessException{
                
        if(isNotBlank(guid)){
            ...
            communityService.save(user);
            logger.info("User {} registers an OAuth2 account:"{}", user.getId(), guid);
        }else{
            user = communityService.createUser(user, ROLE_BASIC);
            ...
            logger.info("User registers a BASIC account", user.getId());
        }
        ...
    }
    ...
}
```

9. Start your local Tomcat Server and navigate briefly through the application. As with the following example, you should be able to observe a trace of the customer activity in the aggregated file: `<home-directory>/apps/logs/cloudstreetmarket.log`:

9.启动本地Tomcat服务器并简单导航应用程序。 与以下示例一样，您应该能够在聚合文件中观察客户活动的跟踪：`<home-directory> /apps/logs/cloudstreetmarket.log`：

![](/assets/162.png)

> With the log4j2.xml configuration we have made, the cloudstreetmarket.log files will automatically be zipped and categorized in directories as they reach 250 MB.
>
> 使用我们所做的log4j2.xml配置，cloudstreetmarket.log文件将自动压缩并在目录中分类，因为它们达到250 MB。

# How it works...

We are mainly going to review in this section how Log4j2 has been set up to work along with the other Logging frameworks. The other parts of the configuration \(not covered here\) have been considered more intuitive.

我们主要将在本节中回顾如何将Log4j2设置为与其他日志框架一起工作。 配置的其他部分（这里未讨论）被认为更直观。

## Apache Log4j2 among other logging frameworks

Log4j1+ is dying as a project since it is not compatible any longer with Java 5+.

Log4j 2 has been built as a fork of the log4j codebase. In this perspective, it competes with the Logback project. Logback was initially the legitimate continuation of Log4j.

Log4j 2 actually implements many of the Logback's improvements but also fixes problems that are inherent to the Logback's architecture.

Logback provides great performance improvements, especially with multithreading. Comparatively, Log4j 2 offers similar performance.

Apache Log4j2和其他日志框架

Log4j1 +正在作为一个项目消失，因为它不再兼容Java 5+。

Log4j 2已经被构建为log4j代码库的一个分支。 在这个角度来看，它与Logback项目竞争。 Logback最初是Log4j的合法延续。

Log4j 2实际上实现了许多Logback的改进，但也修复了Logback架构固有的问题。

Logback提供了巨大的性能改进，特别是对于多线程。 相比之下，Log4j 2提供了类似的性能。

## The case of SLF4j

SLF4j is not a Logging framework as such; it is an abstraction layer that allows the user to plug in any logging system at deployment time.

SLF4j requires a SLF4j binding in the classpath. Examples of bindings are the following:

SLF4j不是这样的日志框架; 它是一个抽象层，允许用户在部署时插入任何日志系统。

SLF4j需要在类路径中使用SLF4j绑定。 绑定的示例如下：

* slf4j-log4j12-xxx.jar: \(log4j version 1.2\),

* slf4j-jdk14-xxx.jar: \(java.util.logging from the jdk 1.4\),

* slf4j-jcl-xxx.jar: \(Jakarta Commons Logging\)

* logback-classic-xxx.jar.

It also often requires core libraries of the targeted Logging framework.

它还经常需要目标Logging框架的核心库。

## Migrating to log4j 2

Log4j2 does not offer backward compatibility for Log4j1+. It may sound like a problem because applications \(like CloudStreetMarket\) often use third-party libraries that embed their own logging framework. Spring core, for example, has a transitive dependency to Jakarta Commons Logging.

To solve this situation, Log4j 2 provides adapters guaranteeing that internal logs won't be lost and will be bridged to join the log4j 2 flow of logs. There are adapters pretty much for all the systems that may produce logs.

迁移到log4j 2

Log4j2不提供Log4j1 +的向后兼容性。 它可能听起来像一个问题，因为应用程序（如CloudStreetMarket）经常使用嵌入自己的日志框架的第三方库。 例如，Spring核心对Jakarta Commons Logging具有传递依赖性。

为了解决这种情况，Log4j 2提供了适配器，保证内部日志不会丢失，并且将被桥接以加入log4j 2日志流。 对于所有可能产生日志的系统，都有适配器。

## Log4j 2 API and Core

Log4j 2 comes with an API and an implementation. Both are required and come with the following dependencies:

Log4j 2附带了一个API和一个实现。 两者都是必需的，并且具有以下依赖关系：

```java
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api</artifactId>
    <version>2.4.1</version>
</dependency>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.4.1</version>
</dependency>
```

## Log4j 2 Adapters

As introduced earlier, a set of **Adapters **and **Bridges **are available to provide backward compatibility to our applications.

如前所述，一组**适配器**和**桥接器**可用于向后兼容我们的应用。

##  Log4j 1.x API Bridge

When transitive dependencies to Log4j 1+ are noticed in specific modules, the following bridge should be added:

当在特定模块中传递到Log4j 1+的传递依赖性时，应添加以下桥：

```
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-1.2-api</artifactId>
    <version>2.4.1</version>
</dependency>
```

## Apache Commons Logging Bridge

When transitive dependencies to Apache \(Jakarta\) Commons Logging are noticed in specific modules, the following bridge should be added:

Apache Commons日志桥

当在特定模块中传递到Apache（Jakarta）Commons Logging的传递依赖性时，应添加以下bridge ：

```java
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-jcl</artifactId>
    <version>2.4.1</version>
</dependency>
```

## SLF4J Bridge

The same logic is applied to cover slf4j uses; the following bridge should be added:

相同的逻辑适用于覆盖slf4j的用途; 应添加以下bridge ：

```
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j-impl</artifactId>
    <version>2.4.1</version>
</dependency>
```

Java Util Logging Adapters

No transitive dependencies to java.util.logging have been noticed in our application, but if it would have been the case, we would have used the following bridge:

Java Util日志适配器

在我们的应用程序中没有通知到java.util.logging的传递依赖，但是如果是这种情况，我们将使用以下bridge：

```java
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-jul</artifactId>
    <version>2.4.1</version>
</dependency>
```

## Web Servlet Support

The Apache Tomcat container has its own set of libraries that also produce logs. Adding the following dependency on web modules is a way to ensure that container logs are routed to the main Log4j2 pipeline.

Apache Tomcat容器有自己的一组库，它们也产生日志。 向Web模块添加以下依赖关系是确保容器日志路由到主Log4j2管道的一种方法。

```java
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-web</artifactId>
    <version>2.4.1</version>
    <scope>runtime</scope>
</dependency>
```

## Configuration files

The sixth step of this recipe details our log4j2 configuration. It is made of different and configurable Appenders \(output channels basically\). We are using the console and a filebased Appender, but Log4j 2 has a plugin based architecture about Appenders that allows the use of external output channels if needed \(SMTP, Printer, Database, and so on\)

这个谱方的第六步详细说明了我们的log4j2配置。 它是由不同的和可配置的Appenders（主要的输出通道）。 我们使用控制台和基于文件的Appender，但Log4j 2有一个关于Appenders的基于插件的架构，允许在需要时使用外部输出通道（SMTP，打印机，数据库等）

# There is more…

As external sources of information, we point out the interesting Log4j2 auto-configuration which is made of a cascading lookup for configuration files, the official documentation, and an Appender for logging directly into Redis.

作为外部信息源，我们指出有意思的Log4j2自动配置，它由级联查找配置文件，官方文档和用于直接记录到Redis的Appender。

utomatic configuration

Log4j2 implements a cascading lookup in order to locate log4j2 configuration files. Starting from looking for a provided log4j.configurationFile system property, to `log4j2-test.xml` and `log4j2.xml` files in the classpath, the official documentation details all the followed waterfall steps. This documentation is available at:

自动配置

Log4j2实现级联查找以定位log4j2配置文件。 从查找提供的log4j.configurationFile系统属性开始，到类路径中的`log4j2-test.xml`和`log4j2.xml`文件，官方文档详细介绍了所有跟随的步骤。 本文档位于：

https://logging.apache.org/log4j/2.x/manual/configuration.html

## Official documentation

The official documentation is very well made and complete, and is available at

官方文档非常完善，完整，可在https://logging.apache.org/log4j/2.x.

Interesting Redis Appender implementation

The following address introduces an Apache licensed project that provides a Log4j2 **Appender **to log straight into Redis:

以下地址介绍了一个Apache许可项目，它提供了一个Log4j2 **Appender**来直接登录到Redis：

https://github.com/pavlobaron/log4j2redis

  
  
  
  






