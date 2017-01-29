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

8. Now in your workspace, open the context.xml file of your Server project.

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

1.The first step for this section is to download and install the MySQL community server from http://dev.mysql.com/downloads/mysql.. Download the generally available release suited to your system. If you are using MS Windows, we recommend installing the installer.

2.You can follow the installation instructions provided by the MySQL team at http://dev.mysql.com/doc/refman/5.7/en/installing.html. We are now going to define a common configuration for schema users and a database name.

3. Create a root user with the password of your choice.

4. Create a technical user \(with the administrator role\) that the application will use. This user needs to be called csm\_tech and needs to have the password csmDB1$55:

1.此部分的第一步是从http://dev.mysql.com/downloads/mysql下载并安装MySQL社区服务器。下载适合您的系统的常用版本。 如果您使用的是MS Windows，我们建议您安装安装程序。

2.您可以按照MySQL团队提供的安装说明，访问http://dev.mysql.com/doc/refman/5.7/en/installing.html。 我们现在要为模式用户和数据库名称定义一个公共配置。

3.使用您选择的密码创建root用户。

4.创建应用程序将使用的技术用户（具有管理员角色）。 此用户需要调用csm\_tech并需要密码csmDB1 $ 55：

![](/assets/147.png)

5. Start the MySQL Client \(the command line tool\), as follows:

* [ ] On MS Windows, start the program mysql.exe in the MySQL servers installation directory:\MySQL Server 5.6\bin\mysql.exe

* [ ] On Linux or Mac OS, invoke the mysql command from the terminal

On both platforms, the first step is then to provide the root password chosen earlier.

5.启动MySQL客户端（命令行工具），如下所示：

* [ ] 在MS Windows上，启动MySQL服务器安装目录中的程序mysql.exe：\MySQL Server 5.6\bin\mysql.exe

* [ ] 在Linux或Mac OS上，从终端调用mysql命令

在这两个平台上，第一步是提供先前选择的root密码。

6. Create a csm database either with the MySQL workbench or with MySQL client:

6.使用MySQL工作台或MySQL客户端创建csm数据库：

```
mysql> CREATE DATABASE csm;
```

7. Select the csm database as the current database:

7.选择csm数据库作为当前数据库：

```
mysql> USE csm;
```

8. From Eclipse, start the local Tomcat server. Once it has started, you can shut it down again; this step was only to get Hibernate to generate the schema.

9. We need then to insert the data manually. To do so, execute the following import commands one after the other:

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

7. Two configuration-beans in cloudstreetmarket-websocket complete the XML configuration:

The WebSocketConfig bean in edu.zipcloud.cloudstreetmarket.ws.config is defined as follows:

7. cloudstreetmarket-websocket中的两个配置bean完成XML配置：

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

8. The ActivityFeedWSController class has been copied over to cloudstreetmarket-websocket to broadcast user activities. It still doesn't require any specific role or authentication:

8. ActivityFeedWSController类已复制到cloudstreetmarket-websocket以广播用户活动。 它仍然不需要任何特定的角色或身份验证：

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

9. One extra controller sends messages \(which are up-to-date stocks values\) into private queues:

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

10. On the client side, new WebSockets are initiated from the stock-search screens \(stocks result lists\). Especially in `stock_search.js` and `stock_search_by_market.js`, the following block has been added in order to regularly request data updates for the set of results that is displayed to the authenticated user:

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

1.Open the RabbitMQ WebConsole, select the Admin tab, then select the Policy menu \(also accessible from the http://localhost:15672/\#/policies URL\).

2. Add the following policy:

RabbitMQ的配置

1.打开RabbitMQ Web控制台，选择Admin 选项卡，然后选择Policy菜单（也可从http://localhost:15672/\#/policies URL访问）。

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

2. When all these signals are green, start the Tomcat servers.

3. Log in to the application with your Yahoo! account, register a new user, and navigate to the screen: Prices and markets \| Search by markets. If you target a market that is potentially open at your time, you should be able to notice real-time updates on the result list:

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

To make use of a custom HttpSession implementation, Spring Session completely replaces the HttpServletRequest with a custom wrapper \(SessionRepositoryRequestWrapper\). This operation is performed inside SessionRepositoryFilter, which is the servlet filter that needs to be configured in the web.xml to intercept the request flow \(before Spring MVC\).

To do its job, the SessionRepositoryFilter must have an HttpSession implementation. At some point, we registered the RedisHttpSessionConfiguration bean. This bean defines a couple of other beans, and among them is a sessionRepository, which is a RedisOperationsSessionRepository.

See how the SessionRepositoryFilter is important for bridging across the application all the performed session operations to the actual engine implementation that will execute those operations.

为了使用自定义的HttpSession实现，Spring Session使用自定义包装器（SessionRepositoryRequestWrapper）完全取代了HttpServletRequest。 此操作在SessionRepositoryFilter内部执行，这是需要在web.xml中配置以拦截请求流（在Spring MVC之前）的servlet过滤器。

为了完成它的工作，SessionRepositoryFilter必须有一个HttpSession实现。 在某些时候，我们注册了RedisHttpSessionConfiguration bean。 这个bean定义了几个其他bean，其中有一个sessionRepository，它是一个RedisOperationsSessionRepository。

了解SessionRepositoryFilter对于跨应用程序桥接所有执行的会话操作到将执行这些操作的实际引擎实现的重要性。

### CookieHttpSessionStrategy

We have registered an HttpSessionStrategy implementation: RootPathCookieHttpSessionStrategy. This class is a customized version \(in our codebase\) of the Spring CookieHttpSessionStrategy.

Because we wanted to pass the cookie from cloudstreetmarket-api to cloudstreetmarket-websocket, the cookie path \(which is a property of a cookie\) needed to be set to the root path \(and not the servlet context path\). Spring Session 1.1+ should offer a configurable path feature.

我们注册了一个HttpSessionStrategy实现：RootPathCookieHttpSessionStrategy。 这个类是Spring CookieHttpSessionStrategy的定制版本（在我们的代码库中）。

因为我们想要将cookie从cloudstreetmarket-api传递到cloudstreetmarket-websocket，所以需要将cookie路径（cookie的属性）设置为根路径（而不是servlet上下文路径）。 Spring会话1.1+应该提供一个可配置的路径功能。

https://github.com/spring-projects/spring-session/issues/155

  
For now, our RootPathCookieHttpSessionStrategy \(basically CookieHttpSessionStrategy\) produces and expects cookies with a SESSION name:

现在，我们的RootPathCookieHttpSessionStrategy（基本上是CookieHttpSessionStrategy）生成并期望具有**SESSION**名称的Cookie：

![](/assets/150.png)



