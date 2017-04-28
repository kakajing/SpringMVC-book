In this recipe, we broadcast user activities \(events\) with STOMP over SockJS. SockJS provides a custom implementation of WebSocket.

在这个谱方中，我们通过SockJS使用STOMP广播user activities\(events\)。 SockJS提供了WebSocket的自定义实现。

## Getting ready

There is some configuration work to be done beforehand, especially on the Apache HTTP proxy. After that, we will see how to initiate a WebSocket with SockJS and with AngularJS on the client side.

Our WebSocket will subscribe to a topic \(for broadcasting\) published via Spring from the cloudstreetmarket-api module.

有一些配置工作要事先做，特别是在Apache HTTP代理。 之后，我们将看到如何在客户端使用SockJS和AngularJS启动WebSocket。

我们的WebSocket将订阅一个通过Spring从cloudstreetmarket-api模块发布的主题（用于广播）。

## How to do it…

1. From the **Git Perspective **in Eclipse, checkout the latest version of the branch v8.1.x.
2. Run the**Maven clean**and**Maven install**commands on the zipcloud-parent project \(right-click on the**project**, select**Run as… \| Maven Clean**, then select**Run as… \| Maven Install**\). After this, operate a**Maven \| Update Project**to synchronize Eclipse with the Maven configuration \(right-click on the project and then click**Maven \| Update Project…**\)..

3. Similarly, run the Maven clean and Maven install commands on cloudstreetmarket-parent followed by a Maven \| Update Project… \(in order to update all cloudstreetmarket-parent modules\).

1.从Eclipse中的**Git Perspective**中，检出最新版本的分支v8.1.x.

2.在zipcloud-parent项目上运行**Maven clean**和**Maven Install**命令（右键单击项目，选择**Run as ... \| Maven Clean**，然后选择**Run as ... \| Maven Install**）。 之后，操作一个**Maven \| Update Project**以使Eclipse与Maven配置同步（右键单击project，然后单击**Maven \| Update Project..**.）

3.同样，在cloudstreetmarket-parent上运行**Maven clean**和**MavenInstall**命令，然后运行**Maven \| Update Project**...（为了更新所有cloudstreetmarket父模块）。

### Apache HTTP Proxy configuration

1. In the Apache httpd.conf file, change the VirtualHost definition to:

1.在Apache httpd.conf文件中，将VirtualHost定义更改为：

```js
<VirtualHost cloudstreetmarket.com:80>
    ProxyPass /portal http://localhost:8080/portal
    ProxyPassReverse /portal http://localhost:8080/portal
    ProxyPass /api http://localhost:8080/api
    ProxyPassReverse /api http://localhost:8080/api
    RewriteEngine on
    RewriteCond %{HTTP:UPGRADE} ^WebSocket$ [NC]
    RewriteCond %{HTTP:CONNECTION} ^Upgrade$ [NC]
    RewriteRule .* ws://localhost:8080%{REQUEST_URI} [P]
    RedirectMatch ^/$ /portal/index
</VirtualHost>
```

2.Still in httpd.conf, uncomment the line:

2.仍然在httpd.conf中，取消注释行：

```java
LoadModule proxy_wstunnel_module
modules/mod_proxy_wstunnel.so
```

### Frontend

1.In the index.jsp file \(in the cloudstreetmarket-webapp module\), two extra JavaScript files are imported:

1.在index.jsp文件（在cloudstreetmarket-webapp模块中）中，导入两个额外的JavaScript文件：

```js
<script src="js/util/sockjs-1.0.2.min.js"></script>
<script src="js/util/stomp-2.3.3.js"></script>
```

> These two files have been copied locally, but originally, both were found online at:
>
> 这两个文件已在本地复制，但最初，这两个文件都在网上找到：
>
> [https://cdnjs.cloudflare.com/ajax/libs/sockjsclient/1.0.2/sockjs.min.js](https://cdnjs.cloudflare.com/ajax/libs/sockjsclient/1.0.2/sockjs.min.js)
>
> [https://cdnjs.cloudflare.com/ajax/libs/stomp](https://cdnjs.cloudflare.com/ajax/libs/stomp).js/2.3.3/stomp.js

2.For this recipe, all the changes on the client side, are related to the file src/main/webapp/js/home/home\_community\_activity.js \(which drives the feed of**User Activities**on the landing page\). This file is associated with the template /src/main/webapp/html/home.html..

3.As part of the`init()`function of homeCommunityActivityController, the following section was added:

2.对于此配方，客户端上的所有更改都与文件src/main/webapp/js/home/home\_community\_activity.js （它驱动着陆页上的\*\_User Activities \*\_Feed）相关。 此文件与模板/src/main/webapp/html/home.html相关联..

3.作为homeCommunityActivityController的`init()`函数的一部分，添加了以下部分：

```js
cloudStreetMarketApp.controller('homeCommunityActivityController', 
        function ($scope, $rootScope, httpAuth, modalService, communityFactory, genericAPIFactory, $filter){

    var $this = this,
    socket = new SockJS('/api/users/feed/add'),
    stompClient = Stomp.over(socket);
    pageNumber = 0;
    $scope.communityActivities = {};
    $scope.pageSize=10;

    $scope.init = function () {
        $scope.loadMore();
        socket.onclose = function() {
            stompClient.disconnect();
        };
        stompClient.connect({}, function(frame) {
            stompClient.subscribe('/topic/actions', function(message){
            var newActivity = $this.prepareActivity(JSON.parse(message.body));
            $this.addAsyncActivityToFeed(newActivity);
            $scope.$apply();
        });
        });
        ...
    }
...
```

4.The` loadMore()` function is still invoked to pull new activities when the bottom of the scroll is reached. However now, because new activities can be inserted asynchronously, the communityActivities variable is no longer an array but an object used as a map with activity IDs as keys. Doing so allows us to merge the synchronous results with the asynchronous ones:

4.当到达滚动底部时，仍然调用`loadMore()`函数来拉取新活动。 但是现在，因为可以异步插入新活动，所以community Activities变量不再是数组，而是用作具有活动ID作为键的映射的对象。 这样做允许我们将同步结果与异步结果合并：

```js
$scope.loadMore = function () {communityFactory.getUsersActivity(pageNumber,$scope.pageSize).then(function(response) {
    var usersData = response.data,
    status = response.status,
    headers = response.headers,
    config = response.config;
    $this.handleHeaders(headers);
    if(usersData.content){
        if(usersData.content.length > 0){
            pageNumber++;
        }
        $this.addActivitiesToFeed(usersData.content);
    }
    });
};
```

5.As before \(since the Chapter4, Building a REST API for a Stateless Architecture\), we loop over the community activities to build the activity feed. Now each activity carries a number of likes and comments. Currently, if a user is authenticated, he has the capability to see the number of likes:

5.像以前一样（_从第4章，为无状态体系结构构建REST API_），我们循环遍历community activities以构建活动馈送。 现在每个activity 都有许多喜欢和评论。 目前，如果用户被认证，他能够看到喜欢的数量：

[![](https://github.com/kakajing/SpringMVC-book/raw/master/assets/129.png)](https://github.com/kakajing/SpringMVC-book/blob/master/assets/129.png)

6.TheAngularized HTML bound to the thumb-up image is the following:

6.绑定到thumb-up图像的Angularized HTML如下：

```js
<span ng-if="userAuthenticated() && value.amountOfLikes == 0">
    <img ng-src="{{image}}" class="like-img"
        ng-init="image='img/iconfinder/1441189591_1_like.png'"
        ng-mouseover="image='img/iconfinder/1441188631_4_like.png'"
        ng-mouseleave="image='img/iconfinder/1441189591_1_like.png'"
        ng-click="like(value.id)"/>
</span>
```

7.In the controller, the `like() `scope function supports this DOM element to create a new like activity that targets the original activity:

7.在控制器中，`like()`scope函数支持此DOM元素来创建一个新的类似activity ，目标为原activity ：

```js
$scope.like = function (targetActionId){
    var likeAction = {
        id: null,
        type: 'LIKE',
        date: null,
        targetActionId: targetActionId,
        userId: httpAuth.getLoggedInUser()
    };
    genericAPIFactory.post("/api/actions/likes", likeAction);
}
```

8.The opposite logic can also be found to unlike an activity.

8.也可以发现相反的逻辑不同于activity。

## Backend

1. The following Maven dependencies have been added to cloudstreetmarket-api:

1.以下Maven依赖已添加到cloudstreetmarket-api：

```js
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-websocket</artifactId>
    <version>${spring.version}</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-messaging</artifactId>
    <version>${spring.version}</version>
</dependency>
```

2.In the web.xml file \(the one from cloudstreetmarket-api\), the following attribute must be added to our servlet and to each of its filters:

2.在web.xml文件（cloudstreetmarket-api中的一个）中，必须将以下属性添加到我们的servlet及其每个过滤器：

```js
<async-supported>true</async-supported>
```

3.The following dedicated configuration bean has been created:

3.已创建以下专用配置Bean：

```java
@Configuration
@ComponentScan("edu.zipcloud.cloudstreetmarket.api")
@EnableWebSocketMessageBroker
public class WebSocketConfig extends AbstractWebSocketMessageBrokerConfigurer {

    @Override
    public void registerStompEndpoints(final StompEndpointRegistry registry) {
        registry.addEndpoint("/users/feed/add")
    }

    @Override
    public void configureMessageBroker(final MessageBrokerRegistry registry) {
        registry.setApplicationDestinationPrefixes("/app");
        registry.enableSimpleBroker("/topic");
    }
}
```

A new controller ActivityFeedWSControllerhas been added as follows:

已添加新控制器ActivityFeedWSController，如下所示：

```java
@RestController
public class ActivityFeedWSController extends CloudstreetApiWCI{

    @MessageMapping("/users/feed/add")
    @SendTo("/topic/actions")
    public UserActivityDTO handle(UserActivityDTO message) throws Exception{
        return message;
    }

    @RequestMapping(value="/users/feed/info", method=GET)
    public String infoWS(){
        return "v0";
    }
}
```

4.As Spring configuration, we have added the following bean to the dispatcherservlet.xml:

4.作为Spring配置，我们将以下bean添加到dispatcherservlet.xml中：

```js
<bean class="org.sfw.web.socket.server.support.OriginHandshakeInterceptor">
    <property name="allowedOrigins">
        <list>
            <value>http://cloudstreetmarket.com</value>
        </list>
    </property>
</bean>
```

In security-config.xml, the following configuration has been added to the http Spring Security namespace:

在security-config.xml中，以下配置已添加到http Spring Security namespace：

```js
<security:http create-session="stateless" entry-point-ref="authenticationEntryPoint"
    authentication-manager-ref="authenticationManager">
    ...
    <security:headers>
        <security:frame-options policy="SAMEORIGIN"/>
    </security:headers>
    ...
</security:http>
```

Now let's see how events are generated.

现在让我们看看如何生成事件。

1. When a new financial transaction is created, a message is sent to the topic /topic/actions. This is done in the TransactionController:

1.创建的financial transaction时，将向topic /topic/actions发送消息。 这是在TransactionController中完成的：

```java
@RestController
@ExposesResourceFor(Transaction.class)
@RequestMapping(value=ACTIONS_PATH + TRANSACTIONS_PATH, produces={"application/xml", "application/json"})
public class TransactionController extends CloudstreetApiWCI<Transaction> {

    @Autowired
    private SimpMessagingTemplate messagingTemplate;

    @RequestMapping(method=POST)
    @ResponseStatus(HttpStatus.CREATED)
    public TransactionResource post(@Valid @RequestBody Transaction transaction, 
            HttpServletResponse response, BindingResult result) {
        ...
        messagingTemplate.convertAndSend("/topic/actions", new UserActivityDTO(transaction));
        ...
    }
}
```

Similarly, when a like activity is created, a message is also sent to the /topic/actions topic in LikeActionController:

类似地，当创建类似的activity 时，也会向LikeActionController中的/topic/actions主题发送一条消息：

```java
@RequestMapping(method=POST)
@ResponseStatus(HttpStatus.CREATED)
public LikeActionResource post(@RequestBody LikeAction ikeAction, HttpServletResponse response) {
    ...
    likeAction = likeActionService.create(likeAction);
    messagingTemplate.convertAndSend("/topic/actions", new UserActivityDTO(likeAction));
    ...
}
```

2.Now start the Tomcat server. Log in to the application using Yahoo! Oauth2 and your personal Yahoo! account \(if you don't have one yet, please create one\). Register a new user for the Cloudstreet Market application.

3.In your web browser, open two different tabs in the application with your logged-in user. Keep one of these tabs on the landing page.

4.With the other tab, navigate to the Prices and market \| All prices search menu.Search for a ticker, let's say Facebook, and buy three stocks of it.

5.Wait to receive the information message:

2.现在启动Tomcat服务器。 使用Yahoo! Oauth2和您的个人Yahoo!帐户登录应用程序（如果您还没有帐户，请创建一个）。 为Cloudstreet Market应用程序注册新用户。

3.在Web浏览器中，使用您登录的用户在应用程序中打开两个不同的选项卡。 将这些标签中的一个保留在着陆页上。

4.使用其他选项卡，导航到Prices and market \| All prices search菜单。搜索ticker，让我们说Facebook，并买它三股票。

5.等待接收信息消息：

[![](https://github.com/kakajing/SpringMVC-book/raw/master/assets/130.png)](https://github.com/kakajing/SpringMVC-book/blob/master/assets/130.png)

Then check the first tab of the browser \(the tab you were not using\).

然后检查浏览器的第一个选项卡（您没有使用的选项卡）。

[![](https://github.com/kakajing/SpringMVC-book/raw/master/assets/131.png)](https://github.com/kakajing/SpringMVC-book/blob/master/assets/131.png)

You will notice that the activity feed has received a new element at the top!

6.Also, in the console you should have the following log trace:

您会注意到activity feed顶部收到了一个新元素！

6.另外，在控制台中应该有以下日志跟踪：

[![](https://github.com/kakajing/SpringMVC-book/raw/master/assets/132.png)](https://github.com/kakajing/SpringMVC-book/blob/master/assets/132.png)

7.Similarly, like events are refreshed in real time:

7.类似地，类似的事件被实时刷新：

[![](https://github.com/kakajing/SpringMVC-book/raw/master/assets/133.png)](https://github.com/kakajing/SpringMVC-book/blob/master/assets/133.png)

## How it works...

Here, we are going to look at a couple of general concepts about WebSockets, STOMP, and SockJS before introducing the Spring-WebSocket support tools.

在这里，我们将介绍一些关于WebSockets，STOMP和SockJS的一般概念，然后介绍Spring-WebSocket支持工具。

### An introduction to WebSockets

WebSocket is a full-duplex communication protocol based on TCP. A full-duplex communication system allows two parties to speak and to be heard simultaneously through a bidirectional channel. A conversation by telephone is probably the best example of a full-duplex system.

This technology is particularly useful for applications that need to leverage the overhead induced by new HTTP connections. Since 2011, the WebSocket protocol has been an Internet Standard \(\[[https://tools.ietf.org/html/rfc6455\\]\(https://tools.ietf.org/html/rfc6455\\)\](https://tools.ietf.org/html/rfc6455%5C%5D(https://tools.ietf.org/html/rfc6455%5C)%5C\)\).

WebSocket是基于TCP的全双工通信协议。 全双工通信系统允许双方通过双向信道同时讲话和听到。 电话交谈可能是全双工系统的最好例子。

此技术对需要利用由新的HTTP连接引起的开销的应用程序特别有用。 自2011年以来，WebSocket协议已经是一个互联网标准（https//tools.ietf.org/html/rfc6455）。

### WebSocket Lifecycle

Before the WebSocket connection is established, the client initiates a handshake HTTP to which the server responds. The handshake request also represents a protocol upgrade request \(from HTTP to WebSocket\), formalized with an Upgrade header. The server confirms this protocol upgrade with the same Upgrade header \(and value\) in its response. In addition to the Upgrade header, and in a perspective of protection against caching-proxy attacks, the client also sends a base-64 encoded random key. To this, the server sends back a hash of this key in a Sec-WebSocket-Accept header.

Here is an example of a handshake occurring in our application:

在建立WebSocket连接之前，客户端发起服务器响应的握手HTTP。 握手请求还表示协议升级请求（从HTTP到WebSocket），通过升级头部形式化。 服务器在其响应中使用相同的升级标头（和值）确认此协议升级。 除了升级标头，并且在防止缓存代理攻击的角度来看，客户端还发送基本64编码的随机密钥。 为此，服务器在Sec-WebSocket-Accept报头中发回此密钥的散列。

这里是在我们的应用程序中发生握手的示例：

[![](https://github.com/kakajing/SpringMVC-book/raw/master/assets/134.png)](https://github.com/kakajing/SpringMVC-book/blob/master/assets/134.png)

The protocol lifecycle can be summarized by the following sequence diagram:

协议生命周期可以通过以下序列图总结：

[![](https://github.com/kakajing/SpringMVC-book/raw/master/assets/135.png)](https://github.com/kakajing/SpringMVC-book/blob/master/assets/135.png)

### Two dedicated URI schemes

The protocol defines two URI schemes for WebSockets ws:// and wss:// \(with wss allowing encrypted connections\).

两个专用的URI方案

协议为WebSockets ws://和wss://定义了两个URI方案（使用wss允许加密连接）。

### The STOMP protocol

STOMP stands for Simple Text Oriented Messaging Protocol. This protocol provides a frame-based interoperable format that allows STOMP clients to communicate with STOMP message brokers.

It is a messaging protocol that requires and trusts an existing 2-way streaming network protocol on a higher level. WebSocket provides a frame-based data-transfer, and the WebSocket frames can indeed be STOMP-formatted frames.

STOMP代表简单文本定向消息协议。 它提供了一种基于帧的可互操作的连接格式，允许STOMP客户端与STOMP消息代理进行通信。

它是一种消息传递协议，它需要和信任在更高级别上的现有的双向流网络协议。 WebSocket提供基于帧的数据传输，WebSocket帧确实可以是STOMP格式的帧。

Here is an example of a STOMP frame:

以下是STOMP框架的示例：

```java
CONNECTED
session:session-4F_y4UhJTEjabe0LfFH2kg
heart-beat:10000,10000
server:RabbitMQ/3.2.4
version:1.1
user-name:marcus
```

A frame has the following structure:

框架具有以下结构：

[![](https://github.com/kakajing/SpringMVC-book/raw/master/assets/136.png)](https://github.com/kakajing/SpringMVC-book/blob/master/assets/136.png)

The STOMP protocol specification defines a set of client commands \(SEND, SUBSCRIBE,UNSUBSCRIBE, BEGIN, COMMIT, ABORT, ACK, NACK, DISCONNECT, CONNECT, and STOMP\) and server commands \(CONNECTED, MESSAGE, RECEIPT, and ERROR\).

Only SEND, MESSAGE, and ERROR frames can have a body. The protocol specification can be found online at:[http://stomp.github.io/stomp-specification-1.2.html](http://stomp.github.io/stomp-specification-1.2.html).

On the client side, we have used the JavaScript library**STOMP Over WebSocket**identified with the file stomp.js. This library maps STOMP formatted frames to WebSocket frames. By default, it looks up the web browser WebSocket class to make the STOMP client create the WebSocket.

The library can also create STOMP clients from custom WebSocket implementations. From the SockJS WebSockets, we create STOMP clients like so:

STOMP协议规范定义了一组客户端命令（SEND，SUBSCRIBE，UNSUBSCRIBE，BEGIN，COMMIT，ABORT，ACK，NACK，DISCONNECT，CONNECT和STOMP）和服务器命令（CONNECTED，MESSAGE，RECEIPT和ERROR）。

只有SEND，MESSAGE和ERROR帧可以有一个主体。 协议规范可以在以下网址找到：http//stomp.github.io/stomp-specification-1.2.html。

在客户端，我们使用JavaScript库**STOMP Over WebSocket**标识与文件stomp.js。 此库将STOMP格式的帧映射到WebSocket帧。 默认情况下，它查找Web浏览器WebSocket类，使STOMP客户端创建WebSocket。

该库还可以从自定义WebSocket实现创建STOMP客户端。 从SockJS WebSockets，我们创建STOMP客户端：

```js
var socket = new SockJS('/app/users/feed/add');
var stompClient = Stomp.over(socket);
stompClient.connect({}, function(frame) {
    ...
});
socket.onclose = function() {
    stompClient.disconnect();
};
```

### SockJS

WebSockets are supported by almost all browsers nowadays. Still, we don't have control over the versions that our customers are using. In many cases, hiding such a technology from 7 to 15% of the audience is simply not an option.

On the client side, SockJS provides a custom implementation that can be seen as a decorator around the browser-native WebSocket implementation. With a simple and handy library, SockJS ensures cross-browser compatibility. With a list of fallback transport options \(xhr-streaming, xdr-streaming, iframe-eventsource, iframe-htmlfile, xhrpolling, and so on\), it emulates WebSockets as much as possible.

For server implementations, to match the clients' fallback behaviors, SockJS also defines its own protocol:

几乎所有的浏览器都支持WebSockets。 尽管如此，我们无法控制客户使用的版本。 在许多情况下，将这种技术从7％到15％的观众隐藏根本不是一种选择。

在客户端，SockJS提供了一个自定义实现，可以看作是浏览器本地WebSocket实现的装饰器。 使用简单方便的库，SockJS确保跨浏览器兼容性。 使用回退传输选项列表（xhr-streaming，xdr-streaming，iframe-eventsource，iframe-htmlfile，xhrpolling等），它尽可能模拟WebSockets。

对于服务器实现，为了匹配客户端的回退行为，SockJS还定义了自己的协议：

[http://sockjs.github.io/sockjs-protocol/sockjs-protocol-0.3.3.html](http://sockjs.github.io/sockjs-protocol/sockjs-protocol-0.3.3.html)

### Spring WebSocket support

As per the Java WebSocket API specification \(JSR-356\), Spring 4+ provides a solution that is packaged within the modules spring-websocket and spring-messaging. But Spring provides more than just an implementation of JSR-356. For example, based upon the facts that:

* WebSockets without the use of a message protocol are too low level to be directly used in applications without custom handling frameworks: the Spring team made the choice to provide and support a messaging protocol implementation \(STOMP\).

* WebSockets are not supported by all browsers yet: Spring also provides a WebSocket fallback support with its implementation of the SockJS protocol.

Spring WebSocket支持

根据Java WebSocket API规范（JSR-356），Spring 4提供了一个包含在模块spring-websocket和spring-messaging中的解决方案。 但是Spring提供的不仅仅是JSR-356的实现。 例如，基于以下事实：

* 没有使用消息协议的WebSockets太低级别，无法直接在没有自定义处理框架的应用程序中使用：Spring团队选择提供和支持消息协议实现（STOMP）。

* 所有浏览器都不支持WebSockets：Spring还提供了一个WebSocket回退支持，它实现了SockJS协议。

### All-in-one configuration

We have enabled the WebSocket engine and configured it for SockJS and STOMP from only one configuration bean—WebSocketConfig:

一体化配置

我们启用了WebSocket引擎，并从一个配置bean-WebSocketConfig为SockJS和STOMP配置它：

```java
@Configuration
@ComponentScan("edu.zipcloud.cloudstreetmarket.api")
@EnableWebSocketMessageBroker
public class WebSocketConfig extends AbstractWebSocketMessageBrokerConfigurer {

    @Override
    public void registerStompEndpoints(final StompEndpointRegistry registry) {
        registry.addEndpoint("/users/feed/add").withSockJS();
    }

    @Override
    public void configureMessageBroker(final MessageBrokerRegistry registry) {
        registry.setApplicationDestinationPrefixes("/app");
        registry.enableSimpleBroker("/topic");
    }
}
```

The WebSocket endPoint is defined for the context path /users/feed/add. It matches on the client side, the defined SockJS client constructor argument:

WebSocket endPoint是为上下文路径/users/feed/add定义的。 它在客户端匹配，定义的SockJS客户端构造函数参数：

```js
var socket = new SockJS('/api/users/feed/add');
```

From the endpoint \(clientInboundChannel\), the WebSocket engine needs to choose where to route the message to, and we have two options here for this. Depending on the situation and what we want to achieve, we can target an in-app consumer \(message handler\) or directly the message broker in order to dispatch the message to the subscribed clients.

This split is configured by defining two different destination prefixes. In our case, we decided to use the /app prefix to route messages to the corresponding message handlers and the /topic prefix to identify messages that are ready to be dispatched to clients.

Let's see now how message handlers can be defined and how they can be used.

从端点\(clientInboundChannel\)，WebSocket引擎需要选择将消息路由到哪里，我们有两个选项。 根据情况和我们想要实现的目标，我们可以定位应用程序内消费者（消息处理程序）或直接定向消息代理，以便将消息分发到订阅的客户端。

此拆分通过定义两个不同的目标前缀配置。 在我们的示例中，我们决定使用/app 前缀将消息路由到相应的消息处理程序并使用/topic前缀来标识已准备好分发到客户端的消息。

现在让我们看看如何定义消息处理程序以及如何使用它们。

### Defining message handlers via `@MessageMapping`

`@MessageMapping`annotations are used in Spring MVC controller methods to mark them available as message handler methods.

From a message in the clientInboundChannel to be routed to a message handler, the WebSocket engine narrows down the right`@MessageMapping`method based upon their configured value.

As usual in Spring MVC, this value can be defined in an Ant-style \(such as/targets/\*\* for example\). However, in the same way as the`@RequestParam`and`@PathVariable`annotations, template variables can also be passed through using`@DestinationVariable`annotations on method arguments \(destination templates are defined like so: /targets/{target}\).

通过`@MessageMapping`定义消息处理程序

`@MessageMapping`注解在Spring MVC控制器方法中使用，将以它们标记为消息处理程序方法。

从要被路由到消息处理程序的clientInboundChannel中的消息，WebSocket引擎根据其配置的值缩小正确的`@MessageMapping`方法。

像Spring MVC一样，这个值可以在Ant风格中定义（例如/targets/\*\*）。 但是，与`@RequestParam`和`@PathVariable`注解相同，模板变量也可以通过对方法参数使用`@DestinationVariable`注解来传递（目标模板定义如下：/targets/{target}）。

### Sending a message to dispatch

A message broker must be configured. In the case of this recipe, we are using a simple message broker \(simpMessageBroker\) that we have enabled from MessageBrokerRegistry. This type of in-memory broker is suited to stack STOMP messages when there is no need for external brokers \(RabbitMQ, ActiveMQ, and so on\). When there is availability to dispatch messages to WebSocket clients, these messages are sent to clientOutboundChannel.

We have seen that when message destinations are prefixed with /topic \(like in our case\),messages are directly sent to the message broker. But what about sending messages for dispatch when we are in a message handler method or elsewhere in the back-end code? We can use for this the SimpMessagingTemplate described in the next section.

发送消息以进行分派

必须配置消息代理。 在这个配方的里，我们使用一个简单的消息代理（simpMessageBroker），我们已从MessageBrokerRegistry启用。 当不需要外部代理（RabbitMQ，ActiveMQ等）时，这种类型的内存代理适合堆栈STOMP消息。 当有可用性分派消息到WebSocket客户端时，这些消息被发送到clientOutboundChannel。

我们已经看到，当消息目的地前缀为/topic（就像我们的情况），消息被直接发送到消息代理。 但是当我们在消息处理程序方法或后端代码中的其他地方发送消息时如何发送消息进行派遣？ 我们可以为此使用下一节中描述的SimpMessagingTemplate。

### SimpMessagingTemplate

We auto-wired a SimpMessagingTemplate in the CSMReceiver class and we will use it later to forward the payload of AMQP messages to WebSocket clients.

A SimpMessagingTemplate serves the same purpose as the Spring JmsTemplate \(if you are familiar with it\), but it suits simple messaging protocols \(such as STOMP\).

A handy and inherited famous method is the convertAndSend method, which tries to identify and use a MessageConverter to serialize an object and put it into a new message before sending this message to the specified destination:

我们在CSMReceiver类中自动连接了一个SimpMessagingTemplate，我们稍后将使用它将AMQP消息的有效负载转发到WebSocket客户端。

SimpMessagingTemplate提供与Spring JmsTemplate（如果你熟悉它）相同的目的，但它适合简单的消息协议（如STOMP）。

一个方便和继承的著名方法是convertAndSend方法，它尝试识别和使用MessageConverter来序列化一个对象，并将其发送到一个新的消息，然后将该消息发送到指定的目标：

```java
simpMessagingTemplate.convertAndSend(String destination, Object message);
```

The idea is to target an identified destination \(with a /topic prefix in our case\) for a message broker.

这个想法是针对消息代理的目标（在我们的例子中有一个/topic前缀）。

### The @SendTo annotation

This annotation saves us from having to explicitly use the SimpMessagingTemplate. The destination is specified as the annotation value. This method will also handle the conversion from payload to message:

这个注解使我们不必显式地使用SimpMessagingTemplate。 目标指定为注解值。 此方法还将处理从有效载荷到消息的转换：

```java
@RestController
public class ActivityFeedWSController extends CloudstreetApiWCI{

    @MessageMapping("/users/feed/add")
    @SendTo("/topic/actions")
    public UserActivityDTO handle(UserActivityDTO payload) throws Exception{
        return payload;
    }
}
```

## There's more…

In this section, we provide and extra source of information related to the SockJS fallback options.

在本节中，我们提供了与SockJS后备选项相关的额外信息源。

As introduced earlier, Spring provides a SockJS protocol implementation. It is easy to configure SockJS in Spring using the`withSockJS()`functional method during the StompEndPoint registration. This little piece of configuration alone, tells Spring to activate SockJS fallback options on our endpoint.

如前所述，Spring提供了一个SockJS协议实现。 在StompEndPoint注册期间，可以使用`withSockJS()`函数方法在Spring中轻松配置SockJS。 这个单独小块的配置，告诉Spring在我们的端点上激活SockJS回退选项。

The very first call of the SockJS client to the server is an HTTP request to the endpoint path concatenated with /info to assess the server configuration. If this HTTP request does not succeed, no other transport is attempted \(not even WebSocket\).

You can read more in the Spring reference guide if you want to understand how a SockJS client queries the server for a suitable fallback option:

SockJS客户端到服务器的第一次调用是对与用于评估服务器配置的/info连接的端点路径的HTTP请求。 如果此HTTP请求不成功，则不会尝试任何其他传输（甚至WebSocket）。

如果您想了解SockJS客户端如何查询服务器以获取合适的后备选项，可以在Spring参考指南中阅读更多内容：

[http://docs.spring.io/spring/docs/current/spring-framework-reference/html/websocket.html\\#websocket-server-handshake](http://docs.spring.io/spring/docs/current/spring-framework-reference/html/websocket.html#websocket-server-handshake)

