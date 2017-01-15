# Preface

Welcome to the singular universe of Spring MVC Cookbook. We hope you are ready for this journey that will take you through modern Spring web development practices. We have been building the cloudstreetmarket.com , a stock exchange platform with social capabilities.We are about to take you through each step of its development process.

欢迎来到Spring MVC Cookbook的奇特天地。 我们希望您准备好这个旅程，将带您通过现代Spring Web开发实践。 我们一直在建立cloudstreetmarket.com，一个具有社交能力的股票交易平台。我们将带您通过其发展过程的每一步。

## What this book covers

Chapter 1, Setup Routine for an Enterprise Spring Application, introduces a set of practices that are standard in the industry. From the configuration of an Eclipse IDE for optimized support of Java 8, Tomcat 8, GIT and Maven, to a proper understanding of Maven as a build automation tool and as a dependency management tool, you will learn how to deploy the Spring Framework on a sustainable base in this chapter.

Whether a project is meant to become a massively profitable product or to remain a rewarding experience, it should always begin with the same Enterprise-level patterns.

This chapter is more than a guide through the first development stage of the book's application, Cloud Street Market. A bunch of standard practices are presented here as a routine for developers toward enterprise Spring applications.

**第1章**，Enterprise Spring Application的安装例程，介绍了一系列行业标准的实践。 从用于优化支持Java 8，Tomcat 8，GIT和Maven的Eclipse IDE的配置中，为了正确理解Maven作为构建自动化工具和作为依赖关系管理工具，您将学习如何在一个 可持续发展的基础。

无论项目是要成为一个大规模盈利的产品还是保持一个有价值的体验，它应该总是从相同的企业级模式开始。

本章不仅仅是本书应用的第一个发展阶段，即云街市场的指南。 这里提供了一系列标准实践作为开发人员用于企业Spring应用程序的例程。

Chapter 2, Designing a Microservice Architecture with Spring MVC, is a slightly longer chapter.It covers the core principles of Spring MVC, such as its request flow or the central role of DispatcherServlet. It is also about learning how to configure Spring MVC controllers and controller method-handlers with an extended source of information related to a controller's annotations.

In the path of a Microservice architecture, we install Spring and Spring MVC across modules and web projects to build functional units that are easy to deploy and easy to scale. In this perspective, we are going to shape our application with a web module that is responsible for serving a Twitter Bootstrap template along with another web module specialized in REST Web Services.

In this chapter, you also will learn how to transfer the model from controllers to JSP views using the JSTL and how to design a JavaScript MVC pattern with AngularJS.

**第2章**，使用Spring MVC设计微服务体系结构，是一个稍长一点的章节。它涵盖了Spring MVC的核心原理，例如它的请求流或DispatcherServlet的核心角色。 它也是关于学习如何配置Spring MVC控制器和控制器方法处理程序与一个扩展的信息源相关的控制器的注释。

在微服务架构的路径中，我们在模块和Web项目之间安装Spring和Spring MVC，以构建易于部署和易于扩展的功能单元。 在这个角度来看，我们将使用一个Web模块来构建我们的应用程序，该模块负责服务一个Twitter Bootstrap模板以及另一个专门用于REST Web服务的Web模块。

在本章中，您还将学习如何使用JSTL将模型从控制器传输到JSP视图，以及如何使用AngularJS设计JavaScript MVC模式。

Chapter 3, Working with Java Persistence and Entities, gives you a glimpse of . It is necessary at this stage to learn how persistent data can be handled in a Spring ecosystem and thus in a Spring MVC application. We will see how to configure, a JPA persistence provider \(Hibernate\) from dataSource and entityManagerFactory in Spring. You will learn how to build a beneficial JPA object-relational mapping from EJB3 Entities and then how to query repositories using Spring Data JPA.

**第3章**，使用Java持久性和实体，让您一瞥。 在这个阶段有必要了解如何在Spring生态系统中处理持久性数据，因此在Spring MVC应用程序中。 我们将在Spring中看到如何配置来自dataSource和entityManagerFactory的JPA持久性提供程序（Hibernate）。 您将学习如何从EJB3实体构建有益的JPA对象关系映射，然后如何使用Spring Data JPA查询存储库。

Chapter 4, Building a REST API for a Stateless Architecture, provides insights into Spring MVC as a REST Web Services engine. We will see the amazing support the Framework provides,with several annotations acting as doorknobs on method-handlers to abstract web-related logic and thus only focus on the business. This principle appears with annotations for request binding \(binding of parameters, URL paths, headers, and so on\) and Response Marshalling and also for integrated support of Spring Data pagination.

This chapter also presents how to set up exception handling as part of Spring MVC to translate predefined exception-Types into generic error responses. You will understand how to configure the content negotiation, an important bit for REST APIs, and finally, how to expose and document REST Endpoints using Swagger and the Swagger UI.

**第4章**，为无状态体系结构构建REST API，为Spring MVC提供了一个REST Web服务引擎的见解。 我们将看到Framework提供的惊人的支持，有几个注释作为doorknobs的方法处理程序来抽象web相关的逻辑，因此只专注于业务。 这个原则出现了请求绑定（绑定参数，URL路径，标头等）和响应编组的注解，也用于集成支持Spring Data分页。

本章还介绍了如何将异常处理设置为Spring MVC的一部分，将预定义的异常类型转换为通用错误响应。 您将了解如何配置内容协商，REST API的一个重要位置，最后，如何使用Swagger和Swagger UI显示和记录REST端点。

Chapter 5, Authenticating with Spring MVC, presents how to configure an authentication on controllers and services from standard protocols such as HTTP BASIC and OAuth2. You will learn several concepts and practices related to **Spring Security**, such as the Filters chain, the &lt;http&gt; namespace, the Authentication Manager, or the management of roles and users. Our OAuth2 flow is a client implementation. We authenticate users in our application when they are first authenticated with a third-party provider: Yahoo! These Yahoo! authentications and connections are later used to pull fresh financial data from Yahoo! Finance. It will be very interesting to see how the OAuth2 flow can be entirely abstracted, in the backend, with the Spring Social library.

**第5章**，使用Spring MVC进行身份验证，介绍如何使用标准协议（如HTTP BASIC和OAuth2）配置控制器和服务的身份验证。 您将学习与**Spring Security**相关的几个概念和实践，例如过滤器链，&lt;http&gt;命名空间，验证管理器或角色和用户的管理。 我们的OAuth2流是一个客户端实现。 当我们的应用程序中的用户首次通过第三方提供商身份验证时，我们对用户进行身份验证：Yahoo!这些Yahoo!身份验证和连接稍后用于从Yahoo! Finance提取新的财务数据。 这将是非常有趣的看看如何OAuth2流可以完全抽象，在后端，与Spring Social library。

Chapter 6, Implementing HATEOAS, demonstrates how to take a RESTful Spring MVC API a step further. A Hypermedia-driven application provides links along with every single requested resource. These links reflect URLs of related resources. They provide to the user client\(whatever type of client it may be\) real-time navigation options—precious documentation that is also the actual implementation.

We will see how to build such links from JPA Entities associations or from the controller layer.

**第6章**，实现HATEOAS，演示了如何使RESTful Spring MVC API更进一步。 Hypermedia-driven application 提供链接以及每个请求的资源。 这些链接反映相关资源的URL。 它们向用户客户端（任何类型的客户端）提供实时导航选项 - 也是实际实现的宝贵文档。

我们将看到如何从JPA实体关联或从控制器层构建此类链接。

Chapter 7, Developing CRUD Operations and Validations, goes into the more advanced concepts of Spring MVC. Presenting the tools and techniques that support interactive HTTP methods\(PUT, POST, and DELETE\), we will lean on the HTTP1/1 specification \(RFC 7231 Semantics and Content\) to learn how to return the appropriate response status code or headers.

Our use of the Spring Validator together with the ValidationUtils utility class provides a compliant implementation of the validation-related JSR-303 and JSR-349 specifications.

The last recipe is the place where an internationalization \(I18N\) of messages and content happens. We also present a client-side implementation, with AngularJS, that relies on published internationalization web services.

**第7章**，开发CRUD操作和验证，进入Spring MVC的更高级的概念。 介绍支持交互式HTTP方法（PUT，POST和DELETE）的工具和技术，我们将使用HTTP1 / 1规范（RFC 7231语义和内容）来学习如何返回适当的响应状态代码或头。

我们使用Spring Validator和ValidationUtils实用程序类提供了与验证相关的JSR-303和JSR-349规范的兼容实现。

最后一个配方是消息和内容的国际化（I18N）发生的地方。 我们还介绍了一个基于AngularJS的客户端实现，它依赖于已发布的国际化Web服务。

Chapter 8, Communicating Through WebSockets and STOMP, focuses on the uprising WebSocket technology and on building Message-Oriented-Middleware for our application. This chapter provides a rare showcase that implements so much about WebSockets in Spring. From the use of the default embedded WebSocket message broker to a full-featured external broker\(with STOMP and AMQP protocols\), we will see how to broadcast messages to multiple clients and also how to defer the execution of time-consuming tasks with great scalability benefits.

You will learn how to dynamically create private queues and how to get authenticated clients to post and receive messages from these private queues.

To achieve a WebSocket authentication and an authentication of messages, we will make the API stateful. By stateful, understand that the API will use HTTP sessions to keep users authenticated between their requests. With the support of Spring Session and the highly clusterable Redis server, sessions will be shared across multiple web apps.

**第8章**通过WebSockets和STOMP进行通信，重点介绍了WebSocket技术的兴起以及为我们的应用程序构建面向消息的中间件。 本章提供了一个罕见的展示，在Spring中实现了如此多的WebSockets。 从使用默认的嵌入式WebSocket消息代理到全功能的外部代理（使用STOMP和AMQP协议），我们将看到如何广播消息到多个客户端，以及如何推迟耗时任务的执行，具有很大的可扩展性 好处。

您将学习如何动态创建专用队列，以及如何获得经过身份验证的客户端发布和接收来自这些专用队列的消息。

为了实现WebSocket认证和消息的认证，我们将使API成为有状态的。 通过有状态，了解API将使用HTTP会话来保持用户在其请求之间的身份验证。 在Spring Session和高度可集群的Redis服务器的支持下，会话将在多个Web应用程序之间共享。

Chapter 9, Testing and Troubleshooting, introduces a set of tools and common practices to maintain, debug, and improve an application's state. As a way of finishing this journey, we will visit how to upgrade the database schema from one version of the application to another as part of the Maven builds with the Flyway Maven Plugin. We will also go through how to write and automate unit tests \(with Maven Surefire and Mockito\) and integration tests \(using a set of libraries such as Cargo, Rest-assured, and Maven Failsafe\).

The last recipe provides insightful guidelines in order to apply Log4j2 globally as a logging framework, as it is more than important to rely on a relevant logging solution to troubleshoot efficiently, whichever environment we may be on.

**第9章**，测试和故障排除，介绍了一组工具和常见做法来维护，调试和改进应用程序的状态。 作为完成这个旅程的一种方式，我们将访问如何将数据库模式从一个应用程序版本升级到另一个版本，作为使用Flyway Maven插件构建Maven的一部分。 我们还将学习如何编写和自动化单元测试（使用Maven Surefire和Mockito）和集成测试（使用一组库，如Cargo，Rest-sure和Maven Failsafe）。

最后一个谱方提供了深刻的指导，以便将Log4j2作为日志框架应用于全局，因为依靠相关的日志解决方案来有效地排除故障是非常重要的，无论我们在哪个环境。











  


