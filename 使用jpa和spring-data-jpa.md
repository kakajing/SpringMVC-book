In this section, we are going to wire the business logic we need for our application.

Because we have set up the configuration for the JPA and Spring Data JPA, and because we have defined our entities and their relationships, we can now use this model for time and energy-saving.

在本节中，我们将连接我们的应用程序所需的业务逻辑。

因为我们已经为JPA和Spring Data JPA设置了配置，并且因为已经定义了我们的实体及其关系，所以现在可以使用这个模型用于时间和节能。

## How to do it...

The following steps will guide you through the changes:

1. In the edu.zipcloud.cloudstreetmarket.core.daos package, we can find the following two interfaces:

以下步骤将指导你完成更改：

1.在edu.zipcloud.cloudstreetmarket.core.daos包中，我们可以找到以下两个界面：

```java
public interface HistoricalIndexRepository {

    Iterable<HistoricalIndex> findIntraDay(String code, Date of);
    Iterable<HistoricalIndex> findLastIntraDay(String code);
    HistoricalIndex findLastHistoric(String code);
}

public interface TransactionRepository {

    Iterable<Transaction> findAll();
    Iterable<Transaction> findByUser(User user);
    Iterable<Transaction> findRecentTransactions(Date from);
    Iterable<Transaction> findRecentTransactions(int nb);
}
```

2.These two interfaces come with their respective implementations. The HistoricalIndexRepositoryImpl implementation out of the two is defined as follows:

2.这两个接口带有它们各自的实现。 两者中的HistoricalIndexRepositoryImpl实现定义如下：

```java
@Repository
public class HistoricalIndexRepositoryImpl implements HistoricalIndexRepository{

    @PersistenceContext
    private EntityManager em;

    @Override
    public Iterable<HistoricalIndex> findIntraDay(String code,Date of){

        TypedQuery<HistoricalIndex> sqlQuery = 
                em.createQuery("from HistoricalIndex h where h.index.code = ? and h.fromDate >= ? and h.toDate <= ? ORDER BY h.toDate asc",
                HistoricalIndex.class);
        sqlQuery.setParameter(1, code);
        sqlQuery.setParameter(2, DateUtil.getStartOfDay(of));
        sqlQuery.setParameter(3, DateUtil.getEndOfDay(of));

        return sqlQuery.getResultList();
    }

    @Override
    public Iterable<HistoricalIndex> findLastIntraDay(String code) {

        return findIntraDay(code, findLastHistoric(code).getToDate());
    }

    @Override
    public HistoricalIndex findLastHistoric(String code){

        TypedQuery<HistoricalIndex> sqlQuery = 
                em.createQuery("from HistoricalIndex h where h.index.code = ? ORDER BY h.toDate desc", 
                HistoricalIndex.class);
        sqlQuery.setParameter(1, code);

        return sqlQuery.setMaxResults(1).getSingleResult();
    }
}
```

And the TransactionRepositoryImpl implementation is as follows:

而TransactionRepositoryImpl的实现如下：

```java
@Repository
public class TransactionRepositoryImpl implements TransactionRepository{

    @PersistenceContext
    private EntityManager em;

    @Autowired
    private TransactionRepositoryJpa repo;

    @Override
    public Iterable<Transaction> findByUser(User user) {
        TypedQuery<Transaction> sqlQuery = em.createQuery("from Transaction where user = ?", Transaction.class);

        return sqlQuery.setParameter(1, user).getResultList();
    }

    @Override
    public Iterable<Transaction> findRecentTransactions(Date from) {
        TypedQuery<Transaction> sqlQuery = em.createQuery("from Transaction t where t.quote.date >= ?",Transaction.class);
    
        return sqlQuery.setParameter(1, from).getResultList();
    }

    @Override
    public Iterable<Transaction> findRecentTransactions(int nb) {
        TypedQuery<Transaction> sqlQuery = em.createQuery("from Transaction t ORDER BY t.quote.date desc",Transaction.class);
        return sqlQuery.setMaxResults(nb).getResultList();
    }

    @Override
    public Iterable<Transaction> findAll() {
        return repo.findAll();
    }
}
```

3.All the other interfaces in the dao package don't have explicitly defined implementations.

4.The following bean has been added to the Spring configuration file:

3.dao包中的所有其他接口没有明确定义的实现。

4.以下bean已添加到Spring配置文件中：

```java
<jdbc:initialize-database data-source="dataSource">
    <jdbc:script location="classpath:/METAINF/db/init.sql"/>
</jdbc:initialize-database>
```

5.This last configuration allows the application to execute the created init.sql file **on startup**.

6.You will notice that the cloudstreetmarket-core module has been added in its pom.xml file, a dependency to zipcloud-core for the DateUtil class that we created.

7.To replace the two dummy implementations that we created in Chapter 2, Designing a Microservice Architecture with Spring MVC, the CommunityServiceImpl and MarketServiceImpl implementations have been created.

5.最后一个配置允许应用程序在启动时执行创建的init.sql文件。

6.你会注意到，cloudstreetmarket-core模块已经添加到它的pom.xml文件中，这是我们创建的DateUtil类对zipcloud-core的依赖。

7.要替换我们在第2章使用Spring MVC设计微服务架构中创建的两个虚拟实现，已经创建了CommunityServiceImpl和MarketServiceImpl实现。



> We have injected repository dependencies in these implementations using `@Autowired` annotations.
>
> Also,we have tagged these two implementations with the Spring `@Service` annotations using a declared value identifier:
>
> 我们在这些实现中使用`@Autowired`注解注入了存储库依赖性。
>
> 另外，我们使用一个声明的值标识符将这两个实现标记为Spring `@Service`注解：
>
> `@Service(value="marketServiceImpl")`
>
> `@Service(value="communityServiceImpl")`



8.In the cloudstreetmarket-webapp module, the DefaultController has been modified in its `@Autowired` field to target these new implementations and no longer the dummy ones. This is achieved by specifying the `@Qualifier` annotations on the `@Autowired` fields.

8.在cloudstreetmarket-webapp模块中，DefaultController已在其`@Autowired`字段中进行了修改，以定位这些新实现，而不再是伪实例。 这是通过在`@Autowired`字段上指定`@Qualifier`注解来实现的。

9.Starting the server and calling the home page URL, [http://localhost:8080/portal/index](http://localhost:8080/portal/index), should log a couple of SQL queries into the console:

9.启动服务器并调用主页URL（[http：// localhost：8080 / portal / index](http://localhost:8080/portal/index)）应该将几个SQL查询记录到控制台中：

![](/assets/55.png)

Also, the Welcome page should remain the same.

此外，“欢迎”页面应保持不变。

## How it works...

Let's see the breakdown of this recipe with the following sections.

让我们看看这个配方的细节与以下部分。

### Injecting an EntityManager instance

We saw in the first recipe of this chapter that the configuration of the entityManagerFactory bean reflects the persistence unit's configuration.

Historically created by the container, EntityManagers need to handle transactions \(user or container-manager transactions\).

The `@PersistenceContext` annotation is a JPA annotation. It allows us to inject an instance of EntityManager, whose lifecycle is managed by the container. In our case, Spring handles this role. With an EntityManager, we can interact with the persistence context, get managed or detached entities, and indirectly query the database.

注入EntityManager实例

我们在本章的第一个配方中看到，entityManagerFactory bean的配置反映了持久化单元的配置。

过去由容器创建的EntityManager需要处理事务（用户或容器管理器事务）。

`@PersistenceContext`注解是一个JPA注解。 它允许我们注入一个EntityManager的实例，它的生命周期由容器管理。 在我们的例子中，Spring处理这个角色。 使用EntityManager，我们可以与持久化上下文进行交互，获取受管或分离的实体，并间接查询数据库。

### Using JPQL

Using **Java Persistence Query Language \(JPQL\)** is a standardized way of querying the persistence context and, indirectly, the database. JPQL looks like SQL in the syntax, but operates on the JPA-managed entities.

You must have noticed the following query in the repositories:

使用Java持久性查询语言（JPQL）是一种查询持久性上下文并间接查询数据库的标准化方法。  JPQL在语法中看起来像SQL，但是在JPA管理的实体上操作。

你必须在存储库中注意到以下查询：

`from Transaction where user = ?`

The select part of the query is optional. Parameters can be injected into the query and this step is managed by the persistence providers’ implementation. Those implementations offer protections against SQL injection \(using Prepared Statements\) With the example here, take a look at how practical it is to filter a subentity attribute:

查询的选择部分是可选的。 参数可以注入到查询中，并且此步骤由持久性提供程序的实现来管理。 这些实现提供了防止SQL注入（使用预准备语句）的保护。在这里的示例中，请查看过滤子属性的实用性：

`from Transaction t where t.quote.date >= ?`

It avoids declaring a join when the situation is appropriate. We can still declare a JOIN though:

它避免在情况合适时声明联接。 我们仍然可以声明一个JOIN：

`from HistoricalIndex h where h.index.code = ? ORDER BY h.toDate desc`

A couple of keywords \(such as ORDER\) can be used as part of JPQL to operate functions that are usually available in SQL. Find the full list of keywords in the JPQL grammar from the JavaEE 6 tutorial at [http://docs.oracle.com/javaee/6/tutorial/doc/bnbuf.html](http://docs.oracle.com/javaee/6/tutorial/doc/bnbuf.html).

JPQL has been inspired from the earlier-created Hibernate Query Language \(HQL\).

一些关键字（例如ORDER）可以用作JPQL的一部分来操作SQL中通常可用的函数。 从JavaEE 6教程（位于http//docs.oracle.com/javaee/6/tutorial/doc/bnbuf.html）中查找JPQL语法中的完整关键字列表。

JPQL的灵感来自早期创建的Hibernate查询语言（HQL）。

### Reducing boilerplate code with Spring Data JPA

We have discussed in the How to do it… section that some of our repository interfaces don't have explicitly defined implementations. This is a very powerful feature of Spring Data JPA.

使用Spring Data JPA减少样板代码

我们已经在“如何做到...”一节中讨论了我们的一些存储库接口没有明确定义的实现。 这是Spring Data JPA的一个非常强大的功能。

### Query creation

Our UserRepository interface is defined as follows:

我们的UserRepository接口定义如下：

```java
@Repository
public interface UserRepository extends JpaRepository<User, String>{

    User findByUserName(String username);
    User findByUserNameAndPassword(String username, String password);
}
```

We have made it extend the JpaRepository interface, passing through the generic types User \(the entity type this repository will relate to\) and String \(the type of the user's identifier field\).

By extending JpaRepository, UserRepository gets from Spring Data JPA capability to define query methods from Spring Data JPA by simply declaring their method signature. We have done this with the methods findByUserName and findByUserNameAndPassword.

我们已经扩展了JpaRepository接口，传递了通用类型User（与该仓库相关的实体类型）和String（用户标识符字段的类型）。

通过扩展JpaRepository，UserRepository从Spring Data JPA中获取，通过简单地声明它们的方法签名来从Spring Data JPA中定义查询方法。 我们使用方法findByUserName和findByUserNameAndPassword做到了。

Spring Data JPA transparently creates an implementation of our UserRepository interface at runtime. It infers the JPA queries from the way we have named our methods in the interface. Keywords and field names are used for this inference.

Find the following keywords table from the Spring Data JPA doc:

Spring Data JPA在运行时透明地创建了我们的UserRepository接口的实现。 它从我们在接口中命名我们的方法的方式推断JPA查询。 关键字和字段名称用于此推断。

从Spring Data JPA文档中查找以下关键字表：

![](/assets/56.png)

Without specifying anything in the configuration, we have fallen back to the configuration by default for JPA repositories, which injects an instance of our single EntityManagerFactory bean and of our single TransactionManager bean.

Our custom TransactionRepositoryImpl is an example that uses both custom JPQL queries and a JpaRepository implementation. As you might guess, the TransactionRepositoryJpa implementation , which is autowired in TransactionRepositoryImpl, inherits several methods for saving, deleting, and finding,Transaction Entities.

We will also use interesting paging features offered with these methods. The `findAll()` method, which we have pulled, is one of them.

没有在配置中指定任何内容，我们已经默认为JPA存储库的配置，它注入了我们的单个EntityManagerFactory bean和单个TransactionManager bean的实例。

我们的自定义TransactionRepositoryImpl是一个使用自定义JPQL查询和JpaRepository实现的示例。 正如你可能猜到的，TransactionRepositoryImpl中自动连接的TransactionRepositoryJpa实现继承了几种保存，删除和查找方法，事务实体。

我们还将使用这些方法提供的有趣的分页功能。  `findAll()`方法，我们已经拉过，是其中之一。

### Persisting Entities

Spring Data JPA also specifies the following:

Saving an entity can be performed via the `CrudRepository.save(…)` method. It will persist or merge the given entity using the underlying JPA EntityManager. If the entity has not been persisted yet, Spring Data JPA will save the entity via a call to the `entityManager.persist(…)` method; otherwise, the `entityManager.merge(…)` will be called.

This is interesting behavior that we will use to prevent again, a significant amount of boilerplate code.

持久实体

Spring Data JPA还规定如下：

保存实体可以通过`CrudRepository.save(…)` 方法执行。 它将使用底层的JPA EntityManager持久化或合并给定的实体。 如果实体尚未被持久化，Spring Data JPA将通过对`entityManager.persist(…) `方法的调用来保存实体; 否则，将调用`entityManager.merge(…)`。

这是一个有趣的行为，我们将使用它来防止再次，大量的样板代码。

## There's more...

There are more aspects that can be explored around this topic.

在这个主题周围可以探索更多的方面。

### Using native SQL queries

We haven't made use of native SQL queries yet, but we will. It is important to know how to implement them because bypassing the JPA layer can sometimes be a better option performance-wise.

The following link points to an article from the Oracle website, which is interesting as it relates to native SQL queries:

[http://www.oracle.com/technetwork/articles/vasiliev-jpql-087123.html](http://www.oracle.com/technetwork/articles/vasiliev-jpql-087123.html)

使用本机SQL查询

我们还没有使用本地SQL查询，但我们会的。 重要的是要知道如何实现它们，因为绕过JPA层有时可能是更好的选择性能。

以下链接指向Oracle网站上的一篇文章，该文章很有趣，因为它与本机SQL查询相关：

http//www.oracle.com/technetwork/articles/vasiliev-jpql-087123.html

### Transactions

We haven't applied any specific transaction configuration to our repository implementations. Refer to Chapter 7, Developing CRUD Operations and Validations, for more details about transactions.

我们没有对我们的库实现应用任何特定的事务配置。 有关事务的更多详细信息，请参阅第7章“开发CRUD操作和验证”。

## See also

f Custom implementations for Spring Data repositories: With the TransactionRepositoryImpl example, by redefining the methods we need from TransactionRepositoryJpa, we present a pattern for creating custom implementations of data repositories. It somehow forces us to maintain an intermediate proxy. The related Spring document proposes a different technique that solves this issue. This technique is detailed online at [http://docs.spring.io/spring-data/jpa/docs/current/reference/html/\\#repositories.custom-implementations](http://docs.spring.io/spring-data/jpa/docs/current/reference/html/\#repositories.custom-implementations).

Spring Data存储库的自定义实现：使用TransactionRepositoryImpl示例，通过重新定义我们需要的TransactionRepositoryJpa方法，我们提供了一种用于创建数据存储库的自定义实现的模式。 它不知何故迫使我们保持一个中间代理。 相关的Spring文档提出了一种解决这个问题的不同技术。 此技术在http//docs.spring.io/spring-data/jpa/docs/current/reference/html/\#repositories.custom-implementations中详细地在线。

