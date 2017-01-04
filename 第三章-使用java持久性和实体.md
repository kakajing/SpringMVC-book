In this chapter, we will develop the following recipes:

* Configuring the Java Persistence API \(JPA\) in Spring

* Defining useful EJB3 entities and relationships

* Making use of the JPA and Spring Data JPA

在本章中，我们将开发以下配方：

* 在Spring中配置Java持久性API（JPA）

* 定义有用的EJB3实体和关系

* 使用JPA和Spring数据JPA

## Introduction

The **Java Persistence API \(JPA\)** is a specification that has been produced in different releases from 2006 \(JPA 1.0\) to 2013 \(JPA 2.1\) by a group of various experts. Historically, it is one of the three pieces of the EJB 3.0 specification, which has come along with JEE5.

More than an upgrade of **Enterprise JavaBeans \(EJB\)**, JPA was pretty much a complete redesign. At the time, the leading providers of Object Relational Mapping solution \(such as Hibernate\) and of J2EE application servers \(such as WebSphere, JBoss\) have been involved, and the global result has been unarguably simpler. All the types of EJBs \(stateful, stateless, and entities\) are now simple **Plain Old Java Objects \(POJOs\)** that are enriched with specific metadata that is nicely presented as annotations

Java持久性API（JPA）是在不同版本中生成的规范从2006年（JPA 1.0）到2013年（JPA 2.1）由一组专家组成。 历史上，它是其中之一EJB 3.0规范的三个部分，它与JEE5一起提供。

不止是**Enterprise JavaBeans \(EJB\)**的升级，JPA几乎是一个完整的重新设计。 当时，领先的对象关系映射解决方案提供商（如

Hibernate）和J2EE应用服务器（如WebSphere，JBoss）一起参与，并且全局结果已经不可思议地更简单。 所有类型的EJB（有状态，无状态，和实体）现在是简单的纯旧Java对象（PO​​JO），富有特定元数据作为注释很好地呈现。

### The Entities' benefits

Entities play a key role in the EJB3 model. As simple POJOs, they can be used in every single layer of the application.

Ideally, an entity represents an identifiable functional unit within a business domain. The norm is to make an entity representing a database table row. As simple POJOs, entities can rely on inheritance \(the IS-A relationship\) and can have attributes \(the HAS-A relationship\), just as a database schema is normally be described with. Through these relationships, an entity establishes connections with other Entities. These connections are described with @Annotations, which make the entity metadata.

实体的好处

实体在EJB3模型中发挥关键作用。 作为简单的POJO，它们可以用于应用程序的每一层。

理想地，实体表示业务域内的可识别功能单元。 规范是使实体表示数据库表行。 作为简单的POJO，实体可以依赖于继承（IS-A关系）并且可以具有属性（HAS-A关系），正如通常用数据库模式描述的那样。 通过这些关系，实体建立与其他实体的连接。 这些连接用@Annotations描述，它们构成实体元数据。

An entity must be seen as the application-equivalent element of a database table row. JPA allows to operate this element and its whole ecosystem as a Java object hierarchy and to persist it as such.

Entities have brought an amazing radicalization of the persistence layer \(by decreasing the number of hardcoded SQL queries to be maintained\), and also the simplification of the service and transformation layers. Being able to pass through all the levels \(they are used even in views\), they dramatically drive the domain-specific names and concepts used within the application \(methods, classes, and attributes\). They indirectly focus on the essentials, and impose consistency between application concepts and database concepts.

It is obviously a plus to have a solid and well-thought schema from the beginning.

必须将实体视为数据库表行的应用程序等效元素。  JPA允许将此元素及其整个生态系统作为Java对象层次结构操作并持久化。

实体带来了持久层的惊人的激进化（通过减少要保持的硬编码的SQL查询的数量），以及服务和变换层的简化。 能够通过所有级别（它们甚至在视图中使用），它们显着地驱动在应用程序（方法，类和属性）中使用的特定于域的名称和概念。 他们间接地关注基本要素，并且强调应用程序概念和数据库概念之间的一致性。

显然，从一开始就有一个坚实和精心设计的模式。

> JPA brings amazing performance and maintainability results on UI applications. However, it may not always suit performance expectations if it is used to accomplish batches or bulk database operations. It can sometimes be sensible to instead consider direct JDBC accesses.
>
> JPA在UI应用程序上带来惊人的性能和可维护性结果。 但是，如果它用于完成批处理或批量数据库操作，它可能不总是适合性能期望。 有时可以考虑直接JDBC访问。

### The Entity manager and its persistence context

We have seen that an entity can have relations with other entities. In order for us to be able to operate on an entity \(read from a database, update, delete, and persist\), there is a background API that generates the preparation of SQL queries. This API in a persistence provider \(Hibernate, Toplink, and so on\) is the EntityManager. Once it loads the object for the purpose of the application, we can trust it to manage its life cycle.

There are a couple of concepts attached to the EntityManager that we need to review before moving forward. An entity is managed once the EntityManager gets an instance of it from a database read \(explicit or implicit\). The JPA persistence context is formed by the conceptual aggregation of the whole set of managed entities. A persistence context will always carry no more than one instance of an entity discriminated by its identifier \(@Id or a unique ID class\).

If, for some reason, an entity is not managed, it is said to be detached \(understand detached from the persistence context\).

实体管理器及其持久化上下文

我们已经看到一个实体可以与其他实体有关系。 为了使我们能够对实体（从数据库读取，更新，删除和持久化）进行操作，有一个后台API生成SQL查询的准备。 这个API在持久性提供程序（Hibernate，Toplink等）中是EntityManager。 一旦它为应用程序的目的加载对象，我们可以相信它来管理它的生命周期。

有一些附加到EntityManager的概念，我们需要在向前移动之前检查。 一旦EntityManager从数据库读取（显式或隐式）获取其实例，就管理实体。  JPA持久化上下文由整套被管实体的概念聚合形成。 持久化上下文将总是携带由其标识符（@Id或唯一ID类）区分的实体的不超过一个实例。

如果由于某种原因，一个实体没有被管理，它被称为是分离的（理解从持久化上下文分离）。

