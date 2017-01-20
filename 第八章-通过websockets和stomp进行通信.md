Four recipes are covered in this chapter. All of them fully embrace our CloudStreet Market application. Improving, they make it more reactive, more interactive.

These recipes have the following titles:

* Streaming social events with STOMP over SockJS

* Using RabbitMQ as a multiprotocol message broker

* Stacking and consuming tasks in RabbitMQ with AMQP

* Securing messages with Spring Session and Redis

本章涵盖四种谱方。 他们都完全支持我们的CloudStreet Market应用程序。 改进，它们使它更具反应性，更具互动性。

这些食方有以下标题：

* 通过SockJS流式传输STOMP的社交活动

* 使用RabbitMQ作为多协议消息代理

* 使用AMQP在RabbitMQ中执行堆栈和消耗任务

* 使用Spring Session和Redis保护消息

# Introduction

Let's quickly review what we hope you have learned so far in the previous chapters. Chapter by chapter, you must have found out:

* How to initiate a project, and how to rely on standards to keep the code base scalable and adaptive. These standards came from a selection of tools such as Maven or the Java Persistence API. The presented standards also came with a selection of common practices, on the client side for example, with the AngularJS MVC pattern or the Bootstrap Framework UI.



* How to make the most of Spring MVC while facing modern challenges. Spring MVC has been demonstrated as a web MVC framework \(with its request flow, the content negotiation, the view resolution, the model binding, the exception handling, and so on\), but also demonstrated as an integrated Spring component within its Spring environment. An integrated framework able to relay the Spring Security authentication or the Spring Social abstraction. It is also able to serve Spring Data pagination tools as well as a very competitive implementation of the HTTP specification.



* How to design a Microservice architecture implementing an advanced stateless and HyperMedia API that promotes the segregation of duties. A segregation of duties between the front end and the back end, but also a segregation of duties in the functional divisibility of components \(horizontal scalability\) into independent web archives \(.war\).

让我们快速回顾一下我们希望你在前几章中学到的东西。 按章，你一定已经发现：

* 如何启动项目，以及如何依靠标准来保持代码基础可扩展和自适应。 这些标准来自一些工具，例如Maven或Java Persistence API。 所提出的标准还伴随着一些常用实践，例如在客户端，使用AngularJS MVC模式或Bootstrap框架UI。



* 如何充分利用Spring MVC，同时面对现代挑战。  Spring MVC已经被证明为一个Web MVC框架（包括请求流，内容协商，视图分辨率，模型绑定，异常处理等等），但也被展示为其Spring环境中的集成的Spring组件。 一个集成框架，能够传递Spring Security身份验证或Spring Social抽象。 它还能够提供Spring Data分页工具以及非常具有竞争力的HTTP规范实现。



* 如何设计实现高级无状态和HyperMedia API的Microservice架构，促进职责分离。 前端和后端之间的职责分离，以及将组件的功能可分性（水平可扩展性）中的职责分离成独立的网络档案（.war）。

This chapter focuses on the emerging WebSocket technology and on building a **Messagingoriented-middleware \(MOM\)** for our application. It is a rare showcase that implements so much about WebSockets in Spring. From the use of the default embedded WebSocket message broker to a full-featured RabbitMQ broker \(using STOMP and AMQP protocols\).We will see how to broadcast messages to multiple clients and defer the execution of timeconsuming tasks, offering significant scalability benefits.

With a new Java project dedicated to WebSockets that required access to the common database Server, and also in the perspective of a production-like environment, we are going to replace HSQLDB with MySQL Server.

本章重点介绍新兴的WebSocket技术以及为我们的应用程序构建**Messagingoriented-middleware \(MOM\)**。 这是一个罕见的展示，在Spring中实现如此多的WebSockets。 从使用默认的嵌入式WebSocket消息代理到全功能的RabbitMQ代理（使用STOMP和AMQP协议）。我们将看到如何广播消息到多个客户端，延迟执行耗时任务，提供巨大的可扩展性好处。

使用专用于需要访问公共数据库服务器的WebSockets的新Java项目，以及在类似生产环境的角度，我们将使用MySQL Server替换HSQLDB。

We will see how to dynamically create private queues and how to get authenticated clients to post and receive messages from these private queues. We will do all of this, to implement real application features in our application.

To achieve a WebSocket authentication and an authentication of messages, we will turn the API stateful. By stateful, this means that the API will use HTTP sessions to keep users authenticated between their requests. With the support of Spring Session and the use of the highly clusterable Redis Server, the sessions will be shared across multiple webapps.

我们将看到如何动态创建私有队列，以及如何获得经过身份验证的客户端发布和接收来自这些私有队列的消息。 我们将做所有这些，在我们的应用程序中实现真正的应用程序功能。

为了实现WebSocket认证和消息的认证，我们将把状态转换为API。 通过有状态，这意味着API将使用HTTP会话来保持用户在其请求之间的身份验证。 在Spring Session的支持和使用高度可集群的Redis服务器的情况下，会话将在多个Web应用程序之间共享。





