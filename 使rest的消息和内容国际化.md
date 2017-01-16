It was necessary to talk about validation before talking about internationalizing content and messages. With global and cloud-based services, supporting content in only one language is often not sufficient.

In this recipe, we provide an implementation that suits our design and therefore continue to meet our scalability standards for not relying on HTTP Sessions.

We will see how to define the MessageSource beans in charge of fetching the most suited message for a given location. We will see how to serialize resource properties to make them available to the frontend. We will implement a dynamic translation of content on this frontend with AngularJS and angular-translate.

在谈论国际化内容和消息之前，有必要谈论验证。 使用全球和基于云的服务，仅支持一种语言的内容通常是不够的。

在这个配方中，我们提供了一个适合我们设计的实现，因此继续满足我们的可扩展性标准，不依赖HTTP会话。

我们将看到如何定义MessageSource bean，负责获取给定位置的最适合的消息。 我们将看到如何序列化资源属性以使它们可用于前端。 我们将使用AngularJS和angular-translate实现这个前端的内容的动态翻译。

