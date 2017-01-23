This recipe will demonstrate how to implement a Message-oriented-Middleware \(MoM\). This is a very popular technique in scalability based on asynchronous communication between components.

该配方将演示如何实现面向消息的中间件\(MoM\)。 这是基于组件之间的异步通信的可扩展性中非常流行的技术。

## Getting ready

We have already introduced the new cloudstreetmarket-shared and cloudstreetmarket-websocket Java projects. WebSockets are now split away from cloudstreetmarket-api, but cloudstreetmarket-websocket and cloudstreetmarket-api will still communicate with each other using messaging.

In order to decouple secondary tasks from the request thread \(secondary tasks like event producing\), you need to learn how to configure and use AMQP message templates and listeners with RabbitMQ.

我们已经介绍了新的cloudstreetmarket-shared和cloudstreetmarket-websocket Java项目。 WebSockets现在已经从cloudstreetmarket-api分离，但是cloudstreetmarket-websocket和cloudstreetmarket-api仍然会使用消息传递来相互通信。

为了将辅助任务与请求线程（辅助任务，如事件生成）分离，您需要了解如何使用RabbitMQ配置和使用AMQP消息模板和侦听器。

## How to do it…

1. Access the RabbitMQ web console at http://localhost:15672.

1.http://localhost:15672上访问RabbitMQ Web控制台。  
![](/assets/140.png)

### Sender side

When the API is requested to perform operations such as create a transaction or create a like activity, we produce events.

当请求API执行操作，例如创建事务或创建一个类似的行为，我们产生事件。



> With very few adjustments changes, we now use the RabbitTemplate rather than the former SimpMessagingTemplate and we target an intermediate AMQP queue instead of the ultimate STOMP client.
>
> 除了极少数的调整变化，我们现在使用的RabbitTemplate而非前者SimpMessagingTemplate和我们的目标中间AMQP队列，而不是最终的STOMP客户端。



In the TransactionController, the POST handler has been updated as follows:

在TransactionController中，POST处理程序已更新如下：

```java
import org.springframework.amqp.rabbit.core.RabbitTemplate;

@RestController
public class TransactionController extends CloudstreetApiWCI<Transaction> {

    @Autowired
    private RabbitTemplate messagingTemplate;
    
    @RequestMapping(method=POST)
    @ResponseStatus(HttpStatus.CREATED)
    public TransactionResource post(@Valid @RequestBody Transaction transaction, 
                HttpServletResponse response, BindingResult result) {
        ...
        messagingTemplate.convertAndSend("AMQP_USER_ACTIVITY", new UserActivityDTO(transaction));
        ...
        return resource;
    }
}
```

In the LikeActionController, the POST handler has been updated as follows:

在LikeActionController中，POST处理程序已更新如下：

```java
import org.springframework.amqp.rabbit.core.RabbitTemplate;
@RestController
public class LikeActionController extends CloudstreetApiWCI<LikeAction> {

    @Autowired
    private RabbitTemplate messagingTemplate;
    
    @RequestMapping(method=POST)
    @ResponseStatus(HttpStatus.CREATED)
    public LikeActionResource post(@RequestBody LikeAction likeAction, HttpServletResponse response) {
        messagingTemplate.convertAndSend("AMQP_USER_ACTIVITY", new UserActivityDTO(likeAction));
        ...
        return resource;
    }
}
```

### Consumer side

As explained previously, the cloudstreetmarket-websocket module now listens to the AMQP\_USER\_ACTIVITY queue.

如前所述，cloudstreetmarket-websocket模块现在侦听AMQP\_USER\_ACTIVITY队列。

1. The necessary configuration is set in the displatcher-servlet.

1.需要在dispatcher-servlet.xml（cloudstreetmarket-websocket）配置中设置。 在那里，我们创建一个rabbitConnectionFactory和一个rabbitListenerContainerFactory bean：

```java
<rabbit:connection-factory id="rabbitConnectionFactory" username="guest" host="localhost" password="guest"/>
    <bean id="rabbitListenerContainerFactory" class="org.sfw.amqp.rabbit.config.SimpleRabbitListenerContainerFactory">
    <property name="connectionFactory" ref="rabbitConnectionFactory"/>
    <property name="concurrentConsumers" value="3"/>
    <property name="maxConcurrentConsumers" value="10"/>
    <property name="prefetchCount" value="12"/>
</bean>
```

2. Finally, the listener bean is created as follows with a CSMReceiver class:

最后，使用CSMReceiver类创建侦听器bean如下：

```java
@Component
public class CSMReceiver {

    @Autowired
    private SimpMessagingTemplate simpMessagingTemplate;
    
    @RabbitListener(queues = "AMQP_USER_ACTIVITY_QUEUE")
    public void handleMessage(UserActivityDTO payload) {
        simpMessagingTemplate.convertAndSend("/topic/actions", payload);
    }
}
```

> You can recognize the SimpMessagingTemplate used here to forward incoming message payloads to the final STOMP clients.
>
> 您可以识别此处用于将传入邮件有效内容转发到最终STOMP客户端的SimpMessagingTemplate。



3. A new WebSocketConfig bean has been created in cloudstreetmarketwebsocket. This one is very similar to the one we had in cloudstreetmarket-api.

3.在cloudstreetmarketwebsocket中创建了一个新的WebSocketConfig bean。 这个非常类似于一个我们在cloudstreetmarket-API了。

### Client-side

We haven't changed many things on the client side \(cloudstreetmarket-webapp\), as we are still focused on the landing page \(home\_community\_activity.js\) at this point.

The main difference is that the STOMP endpoint now targets the /ws context path. WebSockets are initiated from the init\(\) function after a 5-second delay. Also, the SockJS socket and the STOMP client are now centralized in global variables \(using the Window object\) to simplify the WebSockets lifecycle during user navigation:

我们没有在客户端\(cloudstreetmarket-webapp\)上更改很多东西，因为我们现在仍然关注登录页面\(home\_community\_activity.js\)。

主要区别在于STOMP端点现在定位到/ws上下文路径。 WebSockets在5秒延迟后从init\(\)函数启动。 此外，SockJS socket 和STOMP client 现在集中在全局变量（使用Window对象），以简化用户导航期间的WebSockets生命周期：

```java
var timer = $timeout( function(){
    window.socket = new SockJS('/ws/channels/users/broadcast');
    window.stompClient = Stomp.over(window.socket);
    
    window.socket.onclose = function() {
        window.stompClient.disconnect();
    };
    
    window.stompClient.connect({}, function(frame) {
        window.stompClient.subscribe('/topic/actions', function(message){
            var newActivity = $this.prepareActivity(JSON.parse(message.body));
            $this.addAsyncActivityToFeed(newActivity);
            $scope.$apply();
        });
    });
    $scope.$on("$destroy", function( event ) {
        $timeout.cancel( timer );
        window.stompClient.disconnect();
    });
}, 5000);
```

## How it works...

This type of infrastructure couples application components together in a loose but reliable way.

这种类型的基础设施以宽松但可靠的方式将应用程序组件耦合在一起。

### Messaging architecture overview

In this recipe, we have given our application a MoM. The main idea was to decouple processes as much as possible from the client-request lifecycle.

In an effort to keep our REST API focused on resource handling, some business logic clearly appeared secondary, such as:

* Notifying the community that a new user has registered an account

* Notifying the community that a user has performed a specific transaction

* Notifying the community that a user has liked another user's action

  
消息架构概述

在这个谱方中，我们给了我们的应用程序MoM。 主要想法是尽可能地从客户端请求生命周期中分离进程。

为了使我们的REST API专注于资源处理，一些业务逻辑显然是次要的，例如：

* 通知社群新用户已注册了帐户

* 通知社区用户已执行特定事务

* 通知社区用户已经喜欢其他用户的操作

We have decided to create a new webapp dedicated to handle WebSockets. Our API now communicates with the ws web app by sending messages to it.

The message payloads are community Action objects \(from the Action.java superclass\). From the cloudstreetmarket-api web app to the cloudstreetmarket-websocket webapp, these action objects are serialized and wrapped in AMQP messages. Once sent, they are stacked in one single RabbitMQ queue \(AMQP\_USER\_ACTIVITY\).

我们决定创建一个新的webapp专用于处理WebSockets。 我们的API现在通过发送消息与ws web应用程序通信。

消息有效载荷是社区Action对象（来自Action.java超类）。 从cloudstreetmarket-api web应用程序到cloudstreetmarket-websocket webapp，这些操作对象被序列化并包装在AMQP消息中。 一旦发送，它们被堆叠在一个单一的RabbitMQ队\(AMQP\_USER\_ACTIVITY\)中。

Both the sender and the receiver parts are AMQP implementations \(RabbitTemplate and RabbitListener\). This logic will now be processed at the pace that the websocket web app can afford without having an impact on the user experience. When received \(on the cloudstreetmarket-websocket side\), message payloads are sent on the fly to WebSocket clients as STOMP messages.

发送方和接收方都是AMQP实现（RabbitTemplate和RabbitListener）。 这个逻辑现在将以websocket web应用程序可以承受的速度进行处理，而不影响用户体验， 当接收到时\(在cloudstreetmarket-websocket端\)，消息有效载荷作为STOMP消息被即时发送到WebSocket客户端。

The benefit in direct performance here is arguable \(in this example\). We have after all mostly deferred the publishing of secondary events with an extra messaging layer. However, the benefits in design clarity and business components separation are priceless.

在这里直接表现的优点是（在本实施例）可以争论。 我们毕竟主要通过额外的消息层推迟次级事件的发布。 然而，设计清晰度和业务组件分离的好处是无价的。

### A scalable model

We have talked much about the benefits of keeping web apps stateless. This is what we have tried to do so far with the API and we have been proud of it!

Without HTTP sessions, it would be pretty easy for us to react to traffic surges on the api web app, or on the portal web app. Without too much hassle, we would be able to set up a load balancer on the Apache HTTP proxy with mod\_proxy\_balancer for HTTP connections.

You can read more about this in the Apache HTTP doc: http://httpd.apache.org/docs/2.2/mod/mod\_proxy\_balancer.html

可扩展模型

我们已经讲过很多关于保持Web应用无状态的好处。 这是我们为已经尝试过的API感到自豪！

没有HTTP会话，我们很容易应对api网络应用程序或门户网站应用程序上的流量激增做出处理。 没有太多麻烦，我们将能够在Apache HTTP代理上使用mod\_proxy\_balancer在HTTP连接上设置负载均衡器。

您可以在Apache HTTP文档中阅读有关此内容的更多信息：http：//httpd.apache.org/docs/2.2/mod/mod\_proxy\_balancer.html

![](/assets/141.png)

For WebSocket web apps, it would work basically the same in stateless. In the Apache HTTP configuration, a configured mod\_proxy\_wstunnel should handle load balancing over WebSockets and provide an application failover.

对于WebSocket Web应用程序，它将在无状态下工作基本相同。 在Apache HTTP配置中，配置的mod\_proxy\_wstunnel应处理通过WebSockets的负载平衡并提供应用程序故障转移。

### AMQP or JMS?

**Advanced Message Queuing Protocol \(AMQP\)** defines a **wire-level** protocol and guarantees interoperability between senders and consumers. Any party compliant with this protocol can create and interpret messages, and thus interoperate with any other compliant component regardless of the underlying technologies.

AMQP还是JMS？

**高级消息队列协议\(AMQP\)**定义**电线级**协议，并保证发送方和使用方之间的互操作性。任何一方符合本协议都可以创建和解释信息，从而与不管底层技术的其他组件兼容互操作。

In comparison, JMS is part of Java platform **Enterprise Edition \(EE\)**. Coming with the JSR-914, JMS is a standard for APIs that defines how APIs should create, send, receive, and read messages. JMS does not provide wire-level guidance, and it doesn't guarantee interoperability between parties either.

相比之下，JMS是Java平台企业版\(EE\)的一部分。 使用JSR-914，JMS是一个API标准，用于定义API应如何创建，发送，接收和读取消息。 JMS不提供线级指导，也不保证各方之间的互操作性。

AMQP controls the format of the messages and the flow these messages go through, while JMS controls the technical implementations of the boundaries \(operators\). When we look for communication consistency within a potentially complex environment, AMQP appears to be a good choice for MoM protocols.

AMQP控制消息的格式和这些消息经历的流程，而JMS控制边界（运算符）的技术实现。 当我们在潜在的复杂环境中寻找通信一致性时，AMQP似乎是MoM协议的不错选择。

## There's more…

This section provides external resources to extend your knowledge about AMQP and about event publishing methods.

本节提供外部资源，以扩展您对AMQP和事件发布方法的了解。

A great introduction to AMQP by pivotal

If you want to get a better understanding of AMQP and its differences with JMS, check out the following article on the spring.io website:

通过关键的AMQP的一个伟大的介绍

如果您想更好地了解AMQP及其与JMS的差异，请查看spring.io网站上的以下文章：

https://spring.io/understanding/AMQP

A better way to publish application events

Right now, we didn't implement a proper pattern to publish events. The article accessible from the link below comes from the spring.io blog. It introduces best practices for event publishing with Spring 4.2+:

发布应用程序事件的更好方法

现在，我们没有实现一个适当的模式来发布事件。 可以从下面的链接访问的文章来自spring.io博客。 它介绍了与Spring 4.2+的事件发布的最佳实践：

https://spring.io/blog/2015/02/11/better-application-events-inspring-framework-4-2



