Installing and using an external RabbitMQ as a full-featured message broker enables new technological opportunities and the design of a production-like infrastructure.

安装和使用外部RabbitMQ作为全功能消息代理，可以提供新的技术机会和类似生产的基础设施的设计。

## Getting ready

In this recipe, we will install RabbitMQ as a standalone server and configure it so it supports STOMP messages.

We will also update our WebSocket Spring configuration to rely on this full-featured message broker, instead of the internal simple message broker.

在这个配方中，我们将安装RabbitMQ作为独立服务器并配置它，以便它支持STOMP消息。

我们还将更新我们的WebSocket Spring配置，以依赖这个全功能消息代理，而不是内部简单消息代理。

## How to do it…

1. From the Git Perspective in Eclipse, check out the v8.2.x branch this time.

2. Two new Java projects have been added, and they must be imported. From Eclipse,select the **File \| Import…** menu.

3. The **Import **wizard opens so you can select a type of project within a hierarchy. Open the **Maven **category, select **Existing Maven Projects** option, and click on **Next**.

4. The **Import Maven Project** wizard opens. As the root directory, select \(or type\) the workspace location \(which should be &lt;home-directory&gt;/workspace\).

5. As shown in the following screenshot, select the following two pom.xml files: **cloudstreetmarket-shared/pom.xml** and **cloudstreetmarket-websocket/pom.xml**.

1.从Eclipse的Git Perspective 中，查看v8.2.x分支。

2.添加两个新的Java项目，同时导入它们。 从Eclipse中，选择**File \| Import…**菜单。

3.打开**Import **向导，以便您可以在层次结构中选择项目类型。 打开**Maven**类别，选择**Existing Maven Projects**选项，然后单击**Next**。

4.打开**Import Maven Project **向导。 作为根目录，选择（或键入）工作区位置（应为&lt;home-directory&gt;/workspace）。

5.如以下屏幕截图所示，选择以下两个pom.xml文件：**cloudstreetmarket-shared/pom.xml**和**cloudstreetmarket-websocket/pom.xml**。

![](/assets/137.png)

1. The two projects cloudstreetmarket-shared and cloudstreetmarket-websocket must show up in the project hierarchy.

2. Target a runtime environment on the web module with the following instructions: In Eclipse, right-click on the cloudmarket-websocket project, select the Properties menu, in the navigation panel, select Targeted Runtimes. In the central window, check the tick of the Server Apache Tomcat v8.0.

3. In the /app directory, the cloudstreetmarket.properties file has been updated. Reflect the changes in your file located in &lt;home-directory&gt;/app/cloudstreetmarket.properties.

4. Run the Maven clean and Maven install commands on zipcloud-parent and then on cloudstreetmarket-parent, followed by a Maven \| Update Project on all the modules.

5. Running RabbitMQ in the way we want, requires us to download and install the product as a standalone product.

6. Depending upon the configuration of the local machine, different ways of proceeding apply. You will find the appropriate links and installation guides on the RabbitMQ website: [https://www.rabbitmq.com/download.html](https://www.rabbitmq.com/download.html)

7. cloudstreetmarket-sharing和cloudstreetmarket-websocket这两个项目必须显示在项目层次结构中。

7.使用以下说明在Web模块上定位运行时环境：在Eclipse中，右键单击cloudmarket-websocket项目，选择Properties 菜单，在导航面板中选择Targeted Runtimes。 在中央窗口中，检查服务器Apache Tomcat v8.0的勾号。

8.在/app目录中，cloudstreetmarket.properties文件已更新。 反映位于&lt;home-directory&gt; /app/cloudstreetmarket.properties中的文件中的更改。

9.在zipcloud-parent上，然后在cloudstreetmarket-parent上运行Maven clean和Maven install 命令，然后是Maven \| Update Project上的所有项目。

10.以我们想要的方式运行RabbitMQ，需要我们将产品下载并安装为独立产品。

11.根据本地机器的配置，应用不同的处理方式。 您可以在RabbitMQ网站上找到相应的链接和安装指南：https：//www.rabbitmq.com/download.html

> If you are using Windows OS, please note that it is a prerequisite to download and install Erlang \([http://www.erlang.org/download.html\](http://www.erlang.org/download.html\)\).
>
> 如果您使用的是Windows操作系统，请注意，它是下载并安装Erlang（[http://www.erlang.org/download.html）的先决条件。](http://www.erlang.org/download.html）的先决条件。)

1. Once RabbitMQ is installed and once its service is running, open your favourite web browser in order to check that RabbitMQ is running as a web console at the URL:[http://localhost:15672](http://localhost:15672) \(like in the following screenshot\).

12.一旦安装了RabbitMQ，并且服务运行后，打开您喜爱的Web浏览器，以便检查RabbitMQ是作为Web控制台运行在URL：[http://localhost:15672（如下面的屏幕截图）。](http://localhost:15672（如下面的屏幕截图）。)

![](/assets/138.png)

> We will come back to this later to set up the RabbitMQ configuration. For now, just remember that this console can be used to monitor messages and administrate connections, queues, topics, and exchanges.
>
> 我们将在稍后再来设置RabbitMQ配置。 现在，只要记住，这个控制台可以用来监视消息和管理连接，队列，主题和交换。

1. The RabbitMQ STOMP plugin needs to be activated. This is done from the rabbitmq\_server-x.x.x\sbin directory, by executing the command line: rabbitmq-plugins enable rabbitmq\_stomp

2. The following Maven dependencies have been added:

13.需要激活RabbitMQ STOMP插件。 这是从rabbitmq\_server-x.x.x \ sbin目录，通过执行命令行：rabbitmq-plugins enable rabbitmq\_stomp

14.添加了以下Maven依赖项：

```java
<dependency>
    <groupId>org.springframework.amqp</groupId>
    <artifactId>spring-rabbit</artifactId>
    <version>1.4.0.RELEASE</version>
</dependency>
<dependency>
    <groupId>io.projectreactor</groupId>
    <artifactId>reactor-core</artifactId>
    <version>2.0.5.RELEASE</version>
</dependency>
<dependency>
    <groupId>io.projectreactor</groupId>
    <artifactId>reactor-net</artifactId>
    <version>2.0.5.RELEASE</version>
</dependency>
<dependency>
    <groupId>io.projectreactor.spring</groupId>
    <artifactId>reactor-spring-context</artifactId>
    <version>2.0.5.RELEASE</version>
</dependency>
<dependency>
    <groupId>io.netty</groupId>
    <artifactId>netty-all</artifactId>
    <version>4.0.31.Final</version>
</dependency>
```

1. In the dispatcher-servlet.xml of the cloudstreetmarket-api module, the following beans have been added making use of the rabbit namespace:

15.在cloudstreetmarket-api模块的dispatcher-servlet.xml中，添加了以下bean，并使用了rabbit命名空间：

```java
<beans xmlns="http://www.sfw.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    ...
    xmlns:rabbit="http://www.sfw.org/schema/rabbit"
    xsi:schemaLocation="http://www.sfw.org/schema/beans
    ...
    http://www.sfw.org/schema/rabbit
    http://www.sfw.org/schema/rabbit/spring-rabbit-1.5.xsd">
    ...
    <rabbit:connection-factory id="connectionFactory" host="localhost" username="guest" password="guest" />
    <rabbit:admin connection-factory="connectionFactory" />
    <rabbit:template id="messagingTemplate" connectionfactory="connectionFactory"/>
</beans>
```

1. In the csmcore-config.xml file \(in cloudstreetmarket-core\), the following beans have been added with the task namespace:

16.在csmcore-config.xml文件（在cloudstreetmarket-core中）中，以下bean已添加任务命名空间：

```java
<beans xmlns="http://www.sfw.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    ...
    xmlns:task=http://www.sfw.org/schema/task
    http://www.sfw.org/schema/task/spring-task-4.0.xsd">
    ...
    <task:annotation-driven scheduler="wsScheduler"/>
    <task:scheduler id="wsScheduler" pool-size="1000"/>
    <task:executor id="taskExecutor"/>
</beans>
```

1. Still in the Spring configuration side of things, our AnnotationConfig bean \(the main configuration bean for cloudstreetmarket-api\) has been added the two annotations:

17.仍然在Spring配置方面，我们的AnnotationConfig bean（cloudstreetmarket-api的主配置bean）已经添加了两个注释：

```java
@EnableRabbit
@EnableAsync
public class AnnotationConfig {
    ...
}
```

1. Finally, the WebSocketConfig bean has been updated as well; especially the broker registration. We now make use of a StompBrokerRelay instead of a simples broker:

```java
@Configuration
@ComponentScan("edu.zipcloud.cloudstreetmarket.api")
@EnableWebSocketMessageBroker
@EnableScheduling
@EnableAsync
public class WebSocketConfig extends AbstractWebSocketMessageBrokerConfigurer {
    ...

    @Override
    public void configureMessageBroker(final MessageBrokerRegistry registry) {
        registry.setApplicationDestinationPrefixes(WEBAPP_PREFIX_PATH);
        registry.enableStompBrokerRelay(TOPIC_ROOT_PATH);
    }
}
```

> That's it! Everything is set to use RabbitMQ as external broker for our system. However, please note that if you try to start the server right now, the code will be expecting MySQL to be installed as well as the Redis Server. These two third-party systems are going to be detailed over the two next recipes.
>
> 而已！ 一切都设置为使用RabbitMQ作为我们系统的外部代理。 但是，请注意，如果现在尝试启动服务器，代码将期望MySQL和Redis服务器一样安装。 这两个第三方系统将详细介绍下两个下一个配方。

## How it works...

### Using a full-featured message broker

In comparison to a simple message broker, using a full-featured message broker such as RabbitMQ provides interesting benefits, which we will discuss now.

使用全功能消息代理

与简单的消息代理相比，我们现在将讨论使用诸如RabbitMQ的全功能消息代理提供的好处。

### Clusterability – RabbitMQ

A RabbitMQ broker is made of one or more Erlang nodes. Each of these nodes represent an instance of RabbitMQ in itself and can be started independently. Nodes can be linked with each other using the command line tool rabbitmqctl. For example, rabbitmqctl join\_cluster rabbit@rabbit.cloudstreetmarket.com would actually connect one node to an existing cluster network. RabbitMQ nodes use cookies to communicate with one another.To be connected on the same cluster, two nodes must have the same cookie.

集群性 - RabbitMQ

RabbitMQ代理由一个或多个Erlang节点组成。 每个节点代表RabbitMQ的实例，并可以独立启动。 节点可以相互使用命令行工具rabbitmqctl挂钩。 例如，rabbitmqctl join\_cluster rabbit@rabbit.cloudstreetmarket.com实际上是将一个节点连接到现有的群集网络。 RabbitMQ的节点使用cookies相互通信。 要在同一个群集上相连，两个节点必须有相同的cookie。

### More STOMP message types

A full-featured message message broker \(in comparison with a simple message broker\) supports additional STOMP frame commands. For example, ACK and RECEIPT are not supported by simple message brokers.

更多STOMP消息类型

全功能消息消息代理（与简单消息代理相比）支持额外的STOMP帧命令。 例如，ACK和RECEIPT不受简单消息代理支持。

### StompMessageBrokerRelay

In the previous recipe, we talked about the flow that a message passes through in the Spring WebSocket engine. As shown with the following image, this flow is not affected at all when switching to an external message broker relay.

在前面的配方中，我们讨论了一个消息在Spring WebSocket引擎中传递的流程。 如下图所示，当切换到外部消息代理中传递时，该流程不受任何影响。

![](/assets/139.png)

Only the RabbitMQ external message broker shows up as an extra piece. BrokerMessageHandler \(StompBrokerRelayMessageHandler\) acts only as a proxy targeting a RabbitMQ node behind the scenes. Only one TCP connection is maintained between the StompBrokerRelay and its message broker. The StompBrokerRelay maintains the connection by sending heartbeat messages.

只有RabbitMQ外部消息代理显示为一个额外的部分。 BrokerMessageHandler（StompBrokerRelayMessageHandler）只作为一个代理，定位到后台的RabbitMQ节点。 StompBrokerRelay与其消息代理之间仅维护一个TCP连接。 StompBrokerRelay通过发送heartbeat messages来维护连接。

