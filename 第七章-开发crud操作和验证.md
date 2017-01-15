Until now, we have seen how to build the read-only HTTP methods of an API. These methods in Spring MVC Controllers required you to master, or at least understand the presentation of a few techniques. Developing non-readonly HTTP methods raises a new set of underlying topics.Each of these topics has a direct impact on the customer experience and therefore each of them is important. We introduce the following four recipes as a frame to cover the subject:

* Extending REST handlers to all HTTP methods

* Validating resources using bean validation support

* Internationalizing messages and contents for REST

* Validating client-side forms with HTML5 and AngularJS

到目前为止，我们已经了解了如何构建API的只读HTTP方法。  Spring MVC控制器中的这些方法需要你掌握，或者至少理解几种技术的介绍。 开发非唯读HTTP方法会产生一组新的基础主题。每个主题都直接影响客户体验，因此每个主题都很重要。 我们介绍以下四个谱方作为框架来涵盖主题：

* 将REST处理程序扩展到所有HTTP方法

* 使用bean验证支持验证资源

* 使REST的消息和内容国际化

* 使用HTML5和AngularJS验证客户端表单

# Introduction

Developing CRUD operations and validations at this stage turns out to be one of the topics with the widest spectrum.

Our application will be transformed in many ways, from the transaction management standardisation to the internationalization of errors \(and content\), passing through the REST handlers, HTTP compliance.

在这个阶段开发CRUD操作和验证是最广泛的主题之一。

我们的应用程序将以许多方式进行转换，从事务管理标准化到错误（和内容）的国际化，通过REST处理程序，HTT规则。



  


