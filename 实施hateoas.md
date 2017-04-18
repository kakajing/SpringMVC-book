This chapter contains the following recipes:

* Turning DTOs into Spring HATEOAS resources

* Building links for a Hypermedia-driven API

* Choosing a strategy to expose JPA entities

* Retrieving data from a third-party API with OAuth

本章包含以下谱 方：

* 将DTO转换为Spring HATEOAS资源

* 构建超媒体驱动API的链接

* 选择公开JPA实体的策略

* 使用OAuth从第三方API检索数据

# Introduction

What is HATEOAS? If you have never seen this word before, it can appear to be difficult to pronounce. Some pronounce it hate-ee-os; others say hate O-A-S. The important point is to remember that this abbreviation stands for **Hypermedia as the Engine of Application State\(HATEOAS\)**. At the very least, you should remember Hypermedia. Hypermedia as a resource's capability to embed nodes that target external resources. Being connected to other resources,a hypermedia resource is also constrained to its domain, as it can't technically develop \(as part of itself\) other resources' domains.

Think of it as Wikipedia. If we create a page whose sections are not self contained in the page title \(domain\), and if one of these sections is already covered in an external page, there are few chances that this situation will be raised by an administrator.

HATEOAS is a constraint applicable to a REST architecture. It imposes on its resources a domain consistency, and at the same time, it imposes an explicit self documentation that the owner has to maintained for the sake of the whole cohesion.

什么是HATEOAS？ 如果你从来没有见过这个词，它似乎很难发音。 有些人说它hate-ee-os; 其他人说hate O-A-S。 重要的一点是要记住，这个缩写代表**超媒体作为应用程序状态引擎（HATEOAS）**。 至少，你应该记住超媒体。 超媒体作为资源嵌入指向外部资源的节点的能力。 由于连接到其他资源，超媒体资源也被限制在其域中，因为它不能在技术上（作为自身的一部分）开发其他资源的域。

想想它作为维基百科。 如果我们创建一个页面的页面标题（域）中不是自包含的页面，并且如果这些部分之一已经被外部页面覆盖，管理员很少有机会提出这种情况。

HATEOAS是适用于REST架构的约束。 它给它的资源施加了一个域一致性，同时，它强加了一个明确的自我文档，业主必须维持为了整体的凝聚力。

## The Richardson Maturity Model

The Richardson Maturity Model \(by Leonard Richardson\) provides a way to grade and qualify a REST API by its level of REST constraints:

Richardson Maturity Model（由Leonard Richardson提供）提供了一种根据REST约束级别对REST API进行分级和限定的方法：

![](/assets/103.png)

The more REST-compliant an API is, the higher its grade.

The initial state in this model is Level 0: The Swamp of POX. Here, the protocol \(usually HTTP\) is only used for its transport capabilities \(not for its state description features\). Also, there are no resource-specific URIs here, just one endpoint is used for one method \(normally,POST in HTTP\).

Level 1: Resources is characterized by the implementation of resource-specific URIs. The resource identifiers can be found in URIs. However, still, only one method of the protocol is used \(POST for HTTP again\).

Level 2: HTTP Verbs reflects an improved use of the protocol properties. For HTTP, this actually means that the API is making use of the HTTP methods for their purpose \(GET to read,POST to create, PUT to edit, DELETE to delete, and so on\). Also, the API provides response codes that reliably inform the user about the operation state.

Level 3: Hypermedia Controls is the highest level in this model. It indicates the use of HATEOAS, which provides API-discovery features to the client.

You can read more on the Richardson Maturity Model on Martin Fowler's blog at:

API越符合REST标准，其等级越高。

此模型中的初始状态为级别0：POX。 这里，协议（通常为HTTP）仅用于其传输能力（不用于其状态描述特性）。 此外，这里没有资源特定的URI，只有一个端点用于一个方法（通常，HTTP中的POST）。

级别1：资源的特征在于实现资源特定的URI。 资源标识符可以在URI中找到。 然而，仍然，只使用协议的一种方法（再次POST为HTTP）。

级别2：HTTP动词反映了协议属性的改进使用。 对于HTTP，这实际上意味着API使用HTTP方法用于其目的（GET读取，POST创建，PUT编辑，DELETE删除等）。 此外，API提供可靠地通知用户关于操作状态的响应代码。

级别3：超媒体控制是此模型中的最高级别。 它指示使用HATEOAS，它向客户端提供API发现功能。

您可以在Martin Fowler的博客上阅读更多Richardson Maturity Model：

[http://martinfowler.com/articles/richardsonMaturityModel.html](http://martinfowler.com/articles/richardsonMaturityModel.html)

