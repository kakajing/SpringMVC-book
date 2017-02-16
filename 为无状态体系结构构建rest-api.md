This chapter will present the following recipes:

* Binding requests and marshalling responses
* Configuring the content-negotiation \(json, xml, and so on\)

* Adding pagination, filters, and sorting capabilities

* Handling exceptions globally

* Documenting and exposing an API with Swagger

本章将介绍以下谱方：

* 绑定请求和编组响应
* 配置内容协商（json，xml等）

* 添加分页，过滤器和排序功能

* 全局处理异常

* 使用Swagger记录和展示API

### Introduction

In this chapter, quite a few changes will be implemented. In fact, this chapter really sets our application development on an acceleration ramp.

Before diving into the code, we need to brush up on a few concepts about REST.

在本章中，将实施相当多的变更。 事实上，本章真正将我们的应用开发设置在加速度上。

在深入代码之前，我们需要了解一些关于REST的概念。

REST is an architecture style. Its name is an abbreviation for Representational State Transfer. The term was invented by Roy Fielding, one of the principal authors of the HTTP specification. A REST architecture is designed around a few markers:

REST是一种架构风格。 它的名称是Representational State Transfer的缩写。 该术语由Roy Fielding发明，Roy Fielding是HTTP规范的主要作者之一。  REST架构围绕几个标记设计：

* **Identifiable resources**: Resources define the domain. A resource must be identifiable by a URI. This URI must be as self-explanatory as possible using resource categories and hierarchies. Our resources will be indices snapshots, stock snapshots, historical index data, historical stock data, users, and so on.
* **HTTP as a communication protocol**: We interact with resources using a limited number of HTTP methods \(GET, POST, PUT, DELETE, HEAD, and OPTIONS\).

* **Resource representation**: A resource is visualized under a specific representation. A representation usually corresponds to a media type \(application/json,application/xml, text/html\) and/or a file extension \(\*.json, \*.xml, \*.html\).

* **Stateless conversations**: The server must not keep traces of a conversation. The use of HTTP sessions must be forbidden and replaced by navigating through the links provided with resources \(hypermedia\). The client authentication is repeated on every single request.

* **Scalability**: Stateless design implies easy scalability. One request can be dispatched to one or another server. This is the role of the load balancers.

* **Hypermedia**: As we just mentioned, with resources come links, and those links drive conversation transitions.

* **可识别资源**：资源定义域。 资源必须可以通过URI标识。 使用资源类别和层次结构，此URI必须尽可能不言自明。 我们的资源将是索引快照，股票快照，历史索引数据，历史股票数据，用户等。
* **HTTP作为通信协议**：我们使用有限数量的HTTP方法（GET，POST，PUT，DELETE，HEAD和OPTIONS）与资源交互。
* **资源表示**：资源在特定表示下可视化。 表示通常对应于媒体类型（application / json，application / xml，text / html）和/或文件扩展名（\* .json，\* .xml，\* .html）。
* **无状态对话**：服务器不得保留对话的痕迹。必须禁止使用HTTP会话，并通过导航通过资源提供的链接（超媒体） 。 对每个请求重复客户端认证。
* **可扩展性**：无状态设计意味着轻松的可扩展性。 一个请求可以分派到一个或另一个服务器。 这是负载平衡器的作用。
* **超媒体**：正如我们刚才提到的，随着资源的链接，这些链接驱动对话转换。

## RESTful CloudStreetMarket

From this chapter on, all of the implemented data retrievals are now handled with REST using AngularJS. We use Angular routing to complete single-page application design \(loaded once from the server\).There are also a couple of new services that support three new screens about stocks and indices.

The REST implementation is still partial though. We have only implemented data retrievals \(GET\); we haven't got an effective authentication yet, and hypermedia will also be introduced later on.

从本章开始，所有实现的数据检索现在都使用AngularJS通过REST处理。 我们使用Angular路由完成单页面应用程序设计（从服务器加载一次）。还有一些新的服务支持三个关于股票和指数的新屏幕。

REST实现仍然是部分的。 我们只实现了数据检索（GET）; 我们还没有有效的身份验证，超媒体也将稍后介绍。

### 



