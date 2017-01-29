To summarize, so far we have seen how to broadcast STOMP messages to StockJS clients, how to stack messages in an external multi-protocol broker, and how to interact with this broker \(RabbitMQ\) in the Spring ecosystem.

总之，到目前为止，我们已经看到了如何广播STOMP消息到StockJS客户端，如何在外部多协议代理中堆栈消息，以及如何在Spring生态系统中与这个代理（RabbitMQ）进行交互。

## Getting ready

This recipe is about implementing dedicated queues, no longer topics \(broadcast\), so that users can receive real-time updates related to the specific content they are viewing. It is also a demonstration of how SockJS clients can send data to their private queues.

For private queues, we had to secure messages and queue accesses. We have broken down our stateless rule of thumb for the API to make use of Spring Session. This extends the authentication performed by cloudstreetmarket-api and reuses the Spring Security context within cloudstreetmarket-websocket.

这个配方是关于实现专用队列，不再是主题（广播），以便用户可以接收与他们正在查看的特定内容相关的实时更新。 它也是SockJS客户端如何将数据发送到其专用队列的演示。

对于私有队列，我们必须保护消息和队列访问。 我们已经分解我们的无状态经验法则的API，以利用Spring会话。 这扩展了cloudstreetmarket-api执行的认证，并在cloudstreetmarket-websocket中重用了Spring Security上下文。

## How to do it…

### Apache HTTP proxy configuration

Because the v8.2.x branch introduced the new cloudstreetmarket-websocket web app, the Apache HTTP proxy configuration needs to be updated to fully support our WebSocket implementation. Our VirtualHost definition is now:

Apache HTTP代理配置

因为v8.2.x分支引入了新的cloudstreetmarket-websocket web应用程序，所以需要更新Apache HTTP代理配置以完全支持我们的WebSocket实现。 我们的VirtualHost定义现在是：

![](/assets/142.png)

### Redis server installation

1. If you are on a Linux-based machine, download the latest stable version \(3+\) at [http://redis.io/download](http://redis.io/download). The format of the archive to download is tar.gz.Follow the instructions on the page to install it \(unpackage it, uncompress it, and build it with the make command\).

Once installed, for a quick start, run Redis with:

Redis服务器安装

1.如果您使用基于Linux的计算机，请从[http://redis.io/download  下载最新的稳定版本（3+）。](http://redis.io/download下载最新的稳定版本（3+）。) 要下载的存档格式为tar.gz.按照页面上的说明安装它（解包，解压缩，并使用make命令构建它）。

安装后，为了快速启动，请运行Redis：

```
$ src/redis-server
```

1. If you are on a Windows-based machine, we recommend this repository: [https://github.com/ServiceStack/redis-windows](https://github.com/ServiceStack/redis-windows). Follow the instructions on the README.md page. Running Microsoft's native port of Redis allows you to run Redis without any other third-party installations. To quickly start Redis server, run the following command:

2.如果您使用基于Windows的计算机，我们建议您使用以下存储库：[https://github.com/ServiceStack/redis-windows。](https://github.com/ServiceStack/redis-windows。) 按照README.md页面上的说明进行操作。 运行Microsoft的Redis本机端口允许您在没有任何其他第三方安装的情况下运行Redis。 要快速启动Redis服务器，请运行以下命令：

```
$ redis-server.exe redis.windows.conf
```

1. When Redis is running, you should be able to see the following welcome screen:

3.当Redis运行时，您应该能够看到以下欢迎屏幕：

![](/assets/143.png)

1. Update your Tomcat configuration in Eclipse to use the local Tomcat installation. To do so, double-click on your current server \(the Servers tab\):

4.在Eclipse中更新Tomcat配置以使用本地Tomcat安装。 为此，请双击当前服务器（Servers选项卡）：

![](/assets/144.png)

1. This should open the configuration panel as follows:

5.这应该打开配置面板，如下所示：

![](/assets/145.png)

Make sure the** Use Tomcat installation** radio button is checked.

确保选中**Use Tomcat installation**单选按钮。

> If the panel is greyed out, right-click on your current server again, then click** Add, Remove...** Remove the three deployed web apps from your server and right click on the server once more, then click **Publish**.
>
> 如果面板变灰，请再次右键单击当前服务器，然后单击**Add**，**Remove**...从服务器中删除三个已部署的Web应用程序，并再次右键单击服务器，然后单击**Publish**。

1. Now, download the following jars:

2. [ ] **jedis-2.5.2.jar**: A small Redis Java client library

3. [ ] **commons-pool2-2.2.jar**: The Apache common object pooling library

6.现在，下载以下jar：

* [ ] **jedis-2.5.2.jar**：一个小的Redis Java客户端库

* [ ] **commons-pool2-2.2.jar**：Apache公共对象池化库

You can download them respectively from [http://central.maven.org/maven2/redis/clients/jedis/2.5.2/jedis-2.5.2.jar](http://central.maven.org/maven2/redis/clients/jedis/2.5.2/jedis-2.5.2.jar) and [http://central.maven.org/maven2/org/apache/commons/commons-pool2/2.2/commons-pool2-2.2.jar](http://central.maven.org/maven2/org/apache/commons/commons-pool2/2.2/commons-pool2-2.2.jar)

You can also find these jars in the chapter\_8/libs directory.

您可以从[http://central.maven.org/maven2/redis/clients/jedis/2.5.2/jedis-2.5.2.jar和http://central.maven.org/maven2/org/下载](http://central.maven.org/maven2/redis/clients/jedis/2.5.2/jedis-2.5.2.jar和http://central.maven.org/maven2/org/下载) apache / commons / commons-pool2 / 2.2 / commons-pool2-2.2.jar

你也可以在chapter\_8 / libs目录中找到这些jars。

In the chapter\_8/libs directory, you will also find the tomcat-redis-sessionmanager-2.0-tomcat-8.jar archive. Copy the three jars tomcat-redis-sessionmanager-2.0-tomcat-8.jar, commons-pool2-2.2.jar, and jedis-2.5.2.jar into the lib directory of your local Tomcat installation that Eclipse is referring to. This should be C:\tomcat8\lib or /home/usr/{system.username}/tomcat8/lib if our instructions have been followed in Chapter 1, Setup Routine for an Enterprise Spring Application.

在chapter\_8/libs目录中，您还将找到**tomcat-redis-sessionmanager-2.0-tomcat-8.jar**归档文件。 将三个jars tomcat-redis-sessionmanager-2.0-tomcat-8.jar，commons-pool2-2.2.jar和jedis-2.5.2.jar复制到Eclipse所引用的本地Tomcat安装的lib目录中。 这应该是C:\tomcat8\lib或/home/usr/{system.username}/tomcat8/lib如果我们的说明已经遵循了第1章企业Spring Application的安装例程。

1. Now in your workspace, open the context.xml file of your Server project.

8.现在在您的工作区中，打开Server项目的context.xml文件。

![](/assets/146.png)

```js
<Valve asyncSupported="true" className="edu.zipcloud.catalina.session.RedisSessionHandlerValve"/>
<ManagerclassName="edu.zipcloud.catalina.session.RedisSessionManager"
    host="localhost"
    port="6379"
    database="0"
    maxInactiveInterval="60"/>
```

### MySQL server installation

While creating the new cloudstreetmarket-websocket web app, we have also changed the database engine from HSQLDB to MySQL. Doing so has allowed us to share the database between the api and websocket modules.

MySQL服务器安装

在创建新的cloudstreetmarket-websocket Web应用程序时，我们还将数据库引擎从HSQLDB更改为MySQL。 这样做允许我们在api和websocket模块之间共享数据库。

1.The first step for this section is to download and install the MySQL community server from [http://dev.mysql.com/downloads/mysql](http://dev.mysql.com/downloads/mysql).. Download the generally available release suited to your system. If you are using MS Windows, we recommend installing the installer.

2.You can follow the installation instructions provided by the MySQL team at [http://dev.mysql.com/doc/refman/5.7/en/installing.html](http://dev.mysql.com/doc/refman/5.7/en/installing.html). We are now going to define a common configuration for schema users and a database name.

1. Create a root user with the password of your choice.

2. Create a technical user \(with the administrator role\) that the application will use. This user needs to be called csm\_tech and needs to have the password csmDB1$55:

1.此部分的第一步是从[http://dev.mysql.com/downloads/mysql下载并安装MySQL社区服务器。下载适合您的系统的常用版本。](http://dev.mysql.com/downloads/mysql下载并安装MySQL社区服务器。下载适合您的系统的常用版本。) 如果您使用的是MS Windows，我们建议您安装安装程序。

2.您可以按照MySQL团队提供的安装说明，访问[http://dev.mysql.com/doc/refman/5.7/en/installing.html。](http://dev.mysql.com/doc/refman/5.7/en/installing.html。) 我们现在要为模式用户和数据库名称定义一个公共配置。

3.使用您选择的密码创建root用户。

4.创建应用程序将使用的技术用户（具有管理员角色）。 此用户需要调用csm\_tech并需要密码csmDB1 $ 55：

![](/assets/147.png)

1. Start the MySQL Client \(the command line tool\), as follows:

2. [ ] On MS Windows, start the program mysql.exe in the MySQL servers installation directory:\MySQL Server 5.6\bin\mysql.exe

3. [ ] On Linux or Mac OS, invoke the mysql command from the terminal

On both platforms, the first step is then to provide the root password chosen earlier.

5.启动MySQL客户端（命令行工具），如下所示：

* [ ] 在MS Windows上，启动MySQL服务器安装目录中的程序mysql.exe：\MySQL Server 5.6\bin\mysql.exe

* [ ] 在Linux或Mac OS上，从终端调用mysql命令

在这两个平台上，第一步是提供先前选择的root密码。

1. Create a csm database either with the MySQL workbench or with MySQL client:

6.使用MySQL工作台或MySQL客户端创建csm数据库：

```
mysql> CREATE DATABASE csm;
```

1. Select the csm database as the current database:

7.选择csm数据库作为当前数据库：

```
mysql> USE csm;
```

1. From Eclipse, start the local Tomcat server. Once it has started, you can shut it down again; this step was only to get Hibernate to generate the schema.

2. We need then to insert the data manually. To do so, execute the following import commands one after the other:

8.从Eclipse中，启动本地Tomcat服务器。 一旦它开始，你可以关闭它; 这一步只是为了让Hibernate生成模式。

9.我们需要手动插入数据。 为此，请一个接一个执行以下import命令：

```
mysql> csm < <home-directory>\cloudstreetmarket-parent\
cloudstreetmarket-core\src\main\resources\META-INF\db\currency_exchange.sql;
mysql> csm < <home-directory>\cloudstreetmarket-parent\
cloudstreetmarket-core\src\main\resources\META-INF\db\init.sql;
mysql> csm < <home-directory>\cloudstreetmarket-parent\
cloudstreetmarket-core\src\main\resources\META-INF\db\stocks.sql;
mysql> csm < <home-directory>\cloudstreetmarket-parent\
cloudstreetmarket-core\src\main\resources\META-INF\db\indices.sql;
```

### Application-level changes

1.In cloudstreetmarket-api and cloudstreetmarket-websocket, the following filter has been added to the web.xml files. This filter has to be positioned before the Spring Security chain definition:

应用程序级更改

1.在cloudstreetmarket-api和cloudstreetmarket-websocket中，以下过滤器已添加到web.xml文件中。 此过滤器必须位于Spring Security链定义之前：

```js
<filter>
    <filter-name>springSessionRepositoryFilter</filter-name>
    <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
    <async-supported>true</async-supported>
</filter>
    <filter-mapping>
    <filter-name>springSessionRepositoryFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

2.A couple of Maven dependencies have also been added to cloudstreetmarket-api:

2.还向cloudstreetmarket-api添加了一些Maven依赖项：

```java
<!-- Spring Session -->
<dependency>
    <groupId>org.springframework.session</groupId>
    <artifactId>spring-session</artifactId>
    <version>1.0.2.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
    <version>2.2</version>
</dependency>
<dependency>
    <groupId>org.springframework.session</groupId>
    <artifactId>spring-session-data-redis</artifactId>
    <version>1.0.2.RELEASE</version>
</dependency>
<!-- Spring Security -->
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-messaging</artifactId>
    <version>4.0.2.RELEASE</version>
</dependency>
<dependency>
    <groupId>commons-io</groupId>
    <artifactId>commons-io</artifactId>
    <version>2.4</version>
</dependency>
```

3.In cloudstreetmarket-api again, security-config.xml has been updated to reflect the following changes in the Spring Security filter chain:

3.再次在cloudstreetmarket-api中，security-config.xml已更新，以反映Spring Security过滤器链中的以下更改：

```js
<security:http create-session="ifRequired" authentication-manager-ref="authenticationManager"
    entry-point-ref="authenticationEntryPoint">
    <security:custom-filter ref="basicAuthenticationFilter" after="BASIC_AUTH_FILTER" />
    <security:csrf disabled="true"/>
    <security:intercept-url pattern="/oauth2/**" access="permitAll"/>
    <security:intercept-url pattern="/basic.html" access="hasRole('ROLE_BASIC')"/>
    <security:intercept-url pattern="/**" access="permitAll"/>
    <security:session-management session-authenticationstrategy-ref="sas"/>
</security:http>
<bean id="sas" class="org.springframework.security.web.authentication.session.SessionFixationProtectionStrategy" />
```

4.Also, this same security-config.xml file, as well as the security-config.xml file in cloudstreetmarket-websocket now define three extra beans:

4.另外，这个相同的security-config.xml文件以及cloudstreetmarket-websocket中的security-config.xml文件现在定义了三个额外的bean：

```js
<bean class="org.springframework.data.redis.connection.jedis.JedisConnectionFactory" p:port="6379"/>
<bean class="org.springframework.session.data.redis.config.annotation.web.http.RedisHttpSessionConfiguration"/>
<bean class="edu.zipcloud.cloudstreetmarket.core.util.RootPathCookieHttpSessionStrategy"/>
```

5.Care was taken with cloudstreetmarket-webapp not to create sessions. We wanted sessions to be created only in the cloudstreetmarket-api. We have achieved this with adding the following configuration to the web.xml file in cloudstreetmarket-webapp:

5.注意cloudstreetmarket-webapp不创建sessions。 我们希望只在cloudstreetmarket-api中创建sessions。 我们通过将以下配置添加到cloudstreetmarket-webapp中的web.xml文件来实现：

```js
<session-config>
    <session-timeout>1</session-timeout>
    <cookie-config>
        <max-age>0</max-age>
    </cookie-config>
</session-config>
```

6.Regarding Spring Security, cloudstreetmarket-websocket has the following configuration:

6.关于Spring Security，cloudstreetmarket-websocket有以下配置：

```js
<bean id="securityContextPersistenceFilter" class="org.springframework.security.web.context.SecurityContextPersistenceFilter"/>
<security:http create-session="never" authentication-manager-ref="authenticationManager" entrypoint-ref="authenticationEntryPoint">
<security:custom-filter ref="securityContextPersistenceFilter" before="FORM_LOGIN_FILTER" />
<security:csrf disabled="true"/>
<security:intercept-url pattern="/channels/private/**" access="hasRole('OAUTH2')"/>
<security:headers>
    <security:frame-options policy="SAMEORIGIN" />
</security:headers>
</security:http>
<security:global-method-security securedannotations="enabled" pre-post-annotations="enabled" 
        authentication-manager-ref="authenticationManager"/>
```

1. Two configuration-beans in cloudstreetmarket-websocket complete the XML configuration:

The WebSocketConfig bean in edu.zipcloud.cloudstreetmarket.ws.config is defined as follows:

1. cloudstreetmarket-websocket中的两个配置bean完成XML配置：

edu.zipcloud.cloudstreetmarket.ws.config中的WebSocketConfig bean定义如下：

```java
@EnableScheduling
@EnableAsync
@EnableRabbit
@Configuration
@EnableWebSocketMessageBroker
public class WebSocketConfig extends AbstractSessionWebSocketMessageBrokerConfigurer<ExpiringSession> {

    @Override
    protected void configureStompEndpoints(StompEndpointRegistry registry) {
    registry.addEndpoint("/channels/users/broadcast")
        .setAllowedOrigins(protocol.concat(realmName))
        .withSockJS()
        .setClientLibraryUrl(Constants.SOCKJS_CLIENT_LIB);
    registry.addEndpoint("/channels/private")
        .setAllowedOrigins(protocol.concat(realmName))
        .withSockJS()
        .setClientLibraryUrl(Constants.SOCKJS_CLIENT_LIB);
    }

    @Override
    public void configureMessageBroker(final MessageBrokerRegistry registry) {
        registry.enableStompBrokerRelay("/topic", "/queue");
        registry.setApplicationDestinationPrefixes("/app");
    }

    @Override
    public void configureClientInboundChannel(ChannelRegistration registration) {
        registration.taskExecutor()
            corePoolSize(Runtime.getRuntime().availableProcessors()*4);
    }

    @Override
    //Increase number of threads for slow clients
    public void configureClientOutboundChannel(ChannelRegistration registration) {
        registration.taskExecutor().corePoolSize(Runtime.getRuntime().availableProcessors() *4);
    }

    @Override
    public void configureWebSocketTransport(WebSocketTransportRegistration registration) {
        registration.setSendTimeLimit(15*1000).setSendBufferSizeLimit(512*1024);
    }
}
```

The WebSocketSecurityConfig bean in edu.zipcloud. cloudstreetmarket.ws.config is defined as follows:

edu.zipcloud中的WebSocketSecurityConfig bean。 cloudstreetmarket.ws.config的定义如下：

```java
@Configuration
public class WebSocketSecurityConfig extends AbstractSecurityWebSocketMessageBrokerConfigurer {

    @Override
    protected void configureInbound(MessageSecurityMetadataSourceRegistry messages) {
        messages.simpMessageDestMatchers("/topic/actions", "/queue/*", "/app/queue/*").permitAll();
    }

    @Override
    protected boolean sameOriginDisabled() {
        return true;
    }
}
```

1. The ActivityFeedWSController class has been copied over to cloudstreetmarket-websocket to broadcast user activities. It still doesn't require any specific role or authentication:

2. ActivityFeedWSController类已复制到cloudstreetmarket-websocket以广播用户活动。 它仍然不需要任何特定的角色或身份验证：

```java
@MessageMapping("/channels/users/broadcast")
    @SendTo("/topic/actions")
    public UserActivityDTO handle(UserActivityDTO message) throws Exception {
        return message;
    }

    @RequestMapping(value="/channels/users/broadcast/info", produces={"application/json"})
    @ResponseBody
    public String info(HttpServletRequest request) {
        return "v0";
    }
}
```

1. One extra controller sends messages \(which are up-to-date stocks values\) into private queues:

9.一个额外的控制器将消息（其是最新的stocks values）发送到private queues中：

```java
@RestController
public class StockProductWSController extends CloudstreetWebSocket WCI<StockProduct>{

    @Autowired
    private StockProductServiceOffline stockProductService;

    @MessageMapping("/queue/CSM_QUEUE_{queueId}")
    @SendTo("/queue/CSM_QUEUE_{queueId}")
    @PreAuthorize("hasRole('OAUTH2')")
    public List<StockProduct> sendContent(@Payload List<String> tickers, @DestinationVariable("queueId") String queueId) throws Exception {
        String username = extractUserFromQueueId(queueId);
        if(!getPrincipal().getUsername().equals(username)){
            throw new IllegalAccessError("/queue/CSM_QUEUE_"+queueId);
        }
        return stockProductService.gather(username, tickers.toArray(new String[tickers.size()]));
    }

    @RequestMapping(value=PRIVATE_STOCKS_ENDPOINT+"/info", produces={"application/xml", "application/json"})
    @ResponseBody
    @PreAuthorize("hasRole('OAUTH2')")
    public String info(HttpServletRequest request) {
        return "v0";
    }

    private static String extractUserFromQueueId(String token){
        Pattern p = Pattern.compile("_[0-9]+$");
        Matcher m = p.matcher(token);
        String sessionNumber = m.find() ? m.group() : "";
        return token.replaceAll(sessionNumber, "");
    }
}
```

1. On the client side, new WebSockets are initiated from the stock-search screens \(stocks result lists\). Especially in `stock_search.js` and `stock_search_by_market.js`, the following block has been added in order to regularly request data updates for the set of results that is displayed to the authenticated user:

10.在客户端，从stock-search screens（stocks result列表）启动新的WebSockets。 特别是在`stock_search.js`和`stock_search_by_market.js`中，添加了以下块，以便定期请求显示给已验证用户的一组结果的数据更新：

```js
if(httpAuth.isUserAuthenticated()){
    window.socket = new SockJS('/ws/channels/private');
    window.stompClient = Stomp.over($scope.socket);
    var queueId = httpAuth.generatedQueueId();
    window.socket.onclose = function() {
        window.stompClient.disconnect();
    };
    window.stompClient.connect({}, function(frame) {
        var intervalPromise = $interval(function() {
        window.stompClient.send('/app/queue/CSM_QUEUE_'+queueId, {}, JSON.stringify($scope.tickers));
    }, 5000);
    $scope.$on("$destroy", function( event ) {
        $interval.cancel(intervalPromise);
        window.stompClient.disconnect();
    });

    window.stompClient.subscribe('/queue/CSM_QUEUE_'+queueId, function(message){
        var freshStocks = JSON.parse(message.body);
        $scope.stocks.forEach(function(existingStock) {
            //这里我们更新当前显示的stocks
        });
    $scope.$apply();
    dynStockSearchService.fadeOutAnim(); //CSS动画
    //（绿色/红色背景...）
    });
    });
};
```

The `httpAuth.generatedQueueId()`function generates a random queue name based on the authenticated username \(see `http_authorized.js` for more details\).

`httpAuth.generatedQueueId()`函数根据验证的用户名生成一个随机队列名称（有关更多详细信息，请参阅`http_authorized.js`）。

RabbitMQ configuration

1.Open the RabbitMQ WebConsole, select the Admin tab, then select the Policy menu \(also accessible from the [http://localhost:15672/\\#/policies](http://localhost:15672/\#/policies) URL\).

1. Add the following policy:

RabbitMQ的配置

1.打开RabbitMQ Web控制台，选择Admin 选项卡，然后选择Policy菜单（也可从[http://localhost:15672/\\#/policies](http://localhost:15672/\#/policies) URL访问）。

2.添加以下policy：

![](/assets/148.png)

This policy \(named PRIVATE\) applies to all auto-generated queues matching the pattern CSM\_QUEUE\_\*, with an auto-expiration of 24 hours.

此policy （名为PRIVATE）适用于与模式CSM\_QUEUE\_ \*匹配的所有自动生成的队列，自动到期为24小时。

### The results

1.Let's have a look ... before starting the Tomcat Server, ensure that:

* [ ] MySQL is running with the loaded data

* [ ] The Redis server is running

* [ ] RabbitMQ is running

* [ ] Apache HTTP has been restarted/reloaded

1.让我们来看看...在启动Tomcat服务器之前，请确保：

* [ ] MySQL正在运行加载的数据

* [ ] Redis服务器正在运行

* [ ] RabbitMQ正在运行

* [ ] Apache HTTP已经重新启动/重新加载

* When all these signals are green, start the Tomcat servers.

* Log in to the application with your Yahoo! account, register a new user, and navigate to the screen: Prices and markets \| Search by markets. If you target a market that is potentially open at your time, you should be able to notice real-time updates on the result list:

2.当所有这些信号都为绿色时，启动Tomcat服务器。

3.使用您的Yahoo!帐户登录应用程序，注册新用户，然后导航到screen：Prices and markets \| Search by markets。 如果您的目标market 是潜在的在您的时间开放，您应该能够注意到结果列表中的实时更新：

![](/assets/149.png)

## How it works...

### The Redis server

Redis is an open source in-memory data-structure store. Day-after-day, it is becoming increasingly popular as a NoSQL database and as a key-value store.

Its ability to store keys with optional expiration times and with very high availability \(over its remarkable cluster\) makes it a very solid underlying technology for session manager implementations. This is precisely the use we make of it through Spring Session.

Redis是一个开源的内存数据结构存储。 日复一日，它作为NoSQL数据库和键值存储变得越来越流行。

它能够存储密钥，具有可选的到期时间和非常高的可用性（通过其显着的集群），使其成为会话管理器实现的一个非常坚实的基础技术。 这正是我们通过Spring Session使用它。

### Spring session

Spring Session is a relatively new Spring project, but it is meant to grow up and take a substantial space in the Spring ecosystem, especially with the recent Microservices and IoT trends. The project is managed by Rob Winch at Pivotal inc. As introduced previously, Spring Session provides an API to manage users' sessions from different Spring components.

The most interesting and notable feature of Spring Session is its ability to integrate with the container \(Apache Tomcat\) to supply a custom implementation of HttpSession.

Spring Session是一个相对较新的Spring项目，但它意味着在Spring生态系统中成长并占据相当大的空间，特别是最近的微服务和物联网趋势。 该项目由Pivotal公司的Rob Winch管理。 如前所述，Spring Session提供了一个API来管理来自不同Spring组件的用户会话。

Spring Session最有趣和最显著的特性是它与容器（Apache Tomcat）集成以提供HttpSession的自定义实现的能力。

### SessionRepositoryFilter

To make use of a custom HttpSession implementation, Spring Session  
 completely replaces the HttpServletRequest with a custom wrapper \(SessionRepositoryRequestWrapper\). This operation is performed inside SessionRepositoryFilter, which is the servlet filter that needs to be configured in the web.xml to intercept the request flow \(before Spring MVC\).

To do its job, the SessionRepositoryFilter must have an HttpSession implementation. At some point, we registered the RedisHttpSessionConfiguration bean. This bean defines a couple of other beans, and among them is a sessionRepository, which is a RedisOperationsSessionRepository.

See how the SessionRepositoryFilter is important for bridging across the application all the performed session operations to the actual engine implementation that will execute those operations.

为了使用自定义的HttpSession实现，Spring Session使用自定义包装器（SessionRepositoryRequestWrapper）完全取代了HttpServletRequest。 此操作在SessionRepositoryFilter内部执行，这是需要在web.xml中配置以拦截请求流（在Spring MVC之前）的servlet过滤器。

为了完成它的工作，SessionRepositoryFilter必须有一个HttpSession实现。 在某些时候，我们注册了RedisHttpSessionConfiguration bean。 这个bean定义了几个其他bean，其中有一个sessionRepository，它是一个RedisOperationsSessionRepository。

了解SessionRepositoryFilter对于跨应用程序桥接所有执行的会话操作到将执行这些操作的实际引擎实现的重要性。

### CookieHttpSessionStrategy

We have registered an HttpSessionStrategy implementation:  
 RootPathCookieHttpSessionStrategy. This class is a customized version \(in our codebase\) of the Spring CookieHttpSessionStrategy.

Because we wanted to pass the cookie from cloudstreetmarket-api to cloudstreetmarket-websocket, the cookie path \(which is a property of a cookie\) needed to be set to the root path \(and not the servlet context path\). Spring Session 1.1+ should offer a configurable path feature.

我们注册了一个HttpSessionStrategy实现：RootPathCookieHttpSessionStrategy。 这个类是Spring CookieHttpSessionStrategy的定制版本（在我们的代码库中）。

因为我们想要将cookie从cloudstreetmarket-api传递到cloudstreetmarket-websocket，所以需要将cookie路径（cookie的属性）设置为根路径（而不是servlet上下文路径）。 Spring会话1.1+应该提供一个可配置的路径功能。

[https://github.com/spring-projects/spring-session/issues/155](https://github.com/spring-projects/spring-session/issues/155)

For now, our RootPathCookieHttpSessionStrategy \(basically CookieHttpSessionStrategy\) produces and expects cookies with a SESSION name:

现在，我们的RootPathCookieHttpSessionStrategy（基本上是CookieHttpSessionStrategy）生成并期望具有**SESSION**名称的Cookie：

![](/assets/150.png)

Currently, only cloudstreetmarket-api produces such cookies \(the two other web apps have been restricted in their cookie generation capabilities so they don't mess up our sessions\).

目前，只有cloudstreetmarket-api生成这样的cookie（另外两个web应用程序的cookie生成功能受到限制，所以他们不会搞乱我们的sessions）。

### Spring Data Redis and Spring Session Data Redis

Do you remember our good friend Spring Data JPA? Well now, Spring Data Redis follows a similar purpose but for the Redis NoSQL key-value store:

Spring数据Redis和Spring会话数据Redis

你还记得我们的好朋友Spring Data JPA吗？ 现在，Spring Data Redis遵循类似的目的，但是对于Redis NoSQL键值存储：

"The Spring Data Redis \(framework makes it easy to write Spring applications that use the Redis key value store by eliminating the redundant tasks and boiler plate code required for interacting with the store through Spring's excellent infrastructure support."

“Spring Data Redis（框架使得使用Redis键值存储的Spring应用程序变得很容易，通过消除通过Spring的优秀基础架构支持与store 交互所需的冗余任务和锅炉板代码）。

Spring Session Data Redis is the Spring module that specifically implements Spring Data Redis for the purpose of Spring Session management.

Spring Session Data Redis是Spring模块，它专门实现了Spring Data Redis，用于Spring Session管理。

### The Redis Session manager for Tomcat

Apache Tomcat natively provides clustering and session-replication features. However, these features rely on load balancers sticky sessions. Sticky sessions have pros and cons for scalability. As cons, we can remember that sessions can be lost when servers go down. Also the stickiness of sessions can induce a slow loading time when we actually need to respond to a surge of traffic.

We have also been using an open source project from James Coleman that allows a Tomcat servers to store non-sticky sessions in Redis immediately on session creation for potential uses by other Tomcat instances. This open source project can be reached at the following address:

Tomcat的Redis会话管理器

Apache Tomcat本地提供集群和session-replication功能。 但是，这些功能依赖于负载平衡器sticky sessions。 sticky sessions具有可扩展性的优点和缺点。 作为缺点，我们可以记住，当服务器关闭时，会话可能会丢失。 此外，当我们实际需要响应大量的通信量时，会话的粘性可能导致缓慢的加载时间。

我们还使用了James Coleman的开源项目，允许Tomcat服务器在会话创建时立即在Redis中存储非粘性会话，以供其他Tomcat实例潜在使用。 可以通过以下地址访问此开源项目：

https://github.com/jcoleman/tomcat-redis-session-manager

  
However, this project doesn't officially support Tomcat 8. Thus, another fork went further in the Tomcat Release process and is closer from the Tomcat 8 requirements:

然而，这个项目没有正式支持Tomcat 8.因此，另一个分支在Tomcat发布过程中进一步发展，并且离Tomcat 8的要求更近：

https://github.com/rmohr/tomcat-redis-session-manager

  
We forked this repository and provided an adaptation for Tomcat 8 in https://github.com/alex-bretet/tomcat-redis-session-manager.

The tomcat-redis-session-manager-2.0-tomcat-8.jar copied to tomcat/lib comes from this repository.

我们分配了这个存储库，并在https://github.com/alex-bretet/tomcat-redis-session-manager中为Tomcat 8提供了一个修改。

将tomcat-redis-session-manager-2.0-tomcat-8.jar复制到tomcat / lib来自此存储库。

> Tomcat 8 is still recent, and time is required for peripheral tools to follow releases. We don't provide tomcat-redis-session-manager-2.0-tomcat-8.jar for production use.
>
> Tomcat 8仍然是最新的，外围工具需要时间来跟踪发布。 我们不提供tomcat-redis-session-manager-2.0-tomcat-8.jar供生产使用。

Viewing/flushing sessions in Redis

In the main installation directory for Redis, an executable for a command line tool \(Cli\) can be found. This executable can be launched from the command:

在Redis中查看/刷新会话

在Redis的主安装目录中，可以找到命令行工具（Cli）的可执行文件。 这个可执行文件可以从命令启动：

**`$ src/redis-cli `**或 **`$ redis-cli.exe`**

  
This executable gives access to the Redis console. From there, for example, the KEY \* command lists all the active sessions:

此可执行文件可访问Redis控制台。 从那里，例如，KEY \*命令列出所有活动会话：

```
127.0.0.1:6379> keys *
1) "spring:session:sessions:4fc39ce3-63b3-4e17-b1c4-5e1ed96fb021"
2) "spring:session:expirations:1418772300000"
```

The FLUSHALL command clears all the active sessions:

FLUSHALL命令清除所有活动会话：

```
redis 127.0.0.1:6379> FLUSHALL
OK
```

> Discover the Redis client language with their online tutorial accessible at http://try.redis.io.
>
> 通过http://try.redis.io可访问的在线教程了解Redis客户端语言。

securityContextPersistenceFilter

We make use of this filter in the cloudstreetmarket-websocket Spring Security filter chain. Its role consists of injecting an external Spring Security context into a SecurityContextHolder from the configured SecurityContextRepository:

我们在cloudstreetmarket-websocket Spring Security过滤器链中使用此过滤器。 它的作用包括从配置的SecurityContextRepository中将一个外部Spring Security上下文注入到一个SecurityContextHolder中：

```js
<bean id="securityContextPersistenceFilter" class="org.sfw.security.web.context.SecurityContextPersistenceFilter">
    <constructor-arg name="repo" ref="httpSessionSecurityContextRepo" />
</bean>
<bean id="httpSessionSecurityContextRepo" class='org.sfw.security.web.context.HttpSessionSecurityContextRepository'>
    <property name='allowSessionCreation' value='false' />
</bean>
```

This filter interacts with SecurityContextRepository to persist the context once the filter chain has been completed. Combined with Spring Session, this filter is very useful when you need to reuse an authentication that has been performed in another component \(another web app in our case\).

After this point, we have also been able to declare a `global-method-security` element \(of the `Spring Security` namespace\) that allows us to make use of `@PreAuthorize` annotations in` @MessageMapping` annotated methods \(our message handling methods\):

此过滤器与SecurityContextRepository交互，以在过滤器链完成后保持上下文。 结合Spring Session，当你需要重用在另一个组件（在我们的例子中是另一个web应用）上执行的认证时，这个过滤器是非常有用的。

在这一点之后，我们还能够声明一个`global-method-security`元素（`Spring Security`命名空间），它允许我们在`@MessageMapping`注解方法（我们的消息处理方法）中使用`@PreAuthorize`注解：

```js
<global-method-security secured-annotations="enabled" pre-postannotations="enabled" />
```

### AbstractSessionWebSocketMessageBrokerConfigurer

This is a long title. We have used this abstract class to give our WebSocketConfig the ability to:

* Ensure sessions are kept alive on incoming web socket messages

* Ensure that WebSocket sessions are destroyed when session terminate

这是一个长标题。 我们已经使用这个抽象类给我们的WebSocketConfig的技术：

* 确保会话在传入的Web套接字消息上保持活动状态

* 确保在会话终止时WebSocket会话被销毁

### AbstractSecurityWebSocketMessageBrokerConfigurer

In a similar fashion, this abstract class provides authorization capabilities to our WebSocketSecurityConfig bean. Thanks to it, the WebSocketSecurityConfig bean now controls the destinations that are allowed for incoming messages.

以类似的方式，此抽象类为我们的WebSocketSecurityConfig bean提供授权功能。 感谢它，WebSocketSecurityConfig bean现在控制允许传入消息的目的地。

## There's more…

### Spring Session

Once again, we recommend the Spring reference document on Spring Session, which is very well done. Please check it out:

再一次，我们推荐在Spring Session上使用Spring参考文档，这是非常好的。 请检查一下：

http://docs.spring.io/spring-session/docs/current/reference/html5

###  Apache HTTP proxy extra configuration

The few lines added to` httpd.conf` serve the purpose of rewriting the WebSocket scheme to ws during the WebSocket handshake. Not doing this causes SockJS to fall back to its XHR options \(one WebSocket emulation\).

Apache HTTP代理额外配置

添加到`httpd.conf`的几行用于在WebSocket握手期间将WebSocket方案重写为ws的目的。 不这样做会导致SockJS回退到其XHR选项（一个WebSocket仿真）。

### Spring Data Redis

Also, we recommend that you read more about the Spring Data Redis project \(in its reference document\):

此外，我们建议您阅读有关Spring Data Redis项目（在其参考文档中）的更多信息：

http://docs.spring.io/spring-data/data-redis/docs/current/reference/html

  


