This topic is critical because a well-designed mapping prevents errors, saves a lot of time and has a big impact on performance.

此主题是至关重要的，因为精心设计的映射可防止错误，节省大量时间并对性能有很大影响。

### Getting ready

In this section, we are going to present most of the Entities that we needed for the application. A couple of implementation techniques \(from inheritance types to relationship cases\) have been chosen here and highlighted for example purposes.

The How it works… section will explain why and how things are defined in the way they are and what were the thoughts that drove us toward the Entities' definitions we made.

在本节中，我们将介绍我们应用程序所需的大部分实体。 这里选择了几种实现技术（从继承类型到关系情况），并且为了示例的目的突出显示。

它如何工作...部分将解释为什么和如何定义的方式，它们是什么和什么是驱使我们走向实体的定义我们所做的。

### How to do it...

The following steps will help you create Entities in the application:

1. All the changes from this recipe are located in the new package edu.zipcloud. cloudstreetmarket.core.entities. First, created three simple entities as shown here:

以下步骤将帮助您在应用程序中创建实体：

1.此配方的所有更改都位于新软件包edu.zipcloud. cloudstreetmarket.core.entities中。 首先，创建三个简单的实体，如这里所示：

* The User entity:

```
@Entity
@Table(name="user")
public class User implements Serializable{

    private static final long serialVersionUID = 1990856213905768044L;

    @Id
    @Column(nullable = false)
    private String loginName;

    private String password;

    private String profileImg;

    @OneToMany(mappedBy="user", cascade = {CascadeType.ALL}, fetch = FetchType.LAZY)
    @OrderBy("id desc")
    private Set<Transaction> transactions = new LinkedHashSet< >();
    ...
}
```

* The Transaction entity:

```
@Entity
@Table(name="transaction")
public class Transaction implements Serializable{

    private static final long serialVersionUID = -6433721069248439324L;

    @Id
    @GeneratedValue
    private int id;

    @ManyToOne(fetch = FetchType.EAGER)
    @JoinColumn(name = "user_name")
    private User user;

    @Enumerated(EnumType.STRING)
    private Action type;

    @OneToOne(fetch = FetchType.EAGER)
    @JoinColumn(name = "stock_quote_id")
    private StockQuote quote;

    private int quantity;
    ...
}
```

And the Market entity:

```
@Entity
@Table(name="market")
public class Market implements Serializable {

    private static final long serialVersionUID = -6433721069248439324L;

    @Id
    private String id;

    private String name;

    @OneToMany(mappedBy = "market", cascade = { CascadeType.ALL }, fetch = FetchType.EAGER)
    private Set<Index> indices = new LinkedHashSet<>();
    ...
}
```

1. Then, we have created some more complex entity Types such as the abstract Historic entity:

2.然后，我们创建了一些更复杂的实体类型，如抽象的Historic实体：

```
@Entity
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
@DiscriminatorColumn(name = "historic_type")
@Table(name="historic")
public abstract class Historic {

    private static final long serialVersionUID = -802306391915956578L;

    @Id
    @GeneratedValue
    private int id;

    private double open;

    private double high;

    private double low;

    private double close;

    private double volume;

    @Column(name="adj_close")
    private double adjClose;

    @Column(name="change_percent")
    private double changePercent;

    @Temporal(TemporalType.TIMESTAMP)
    @Column(name="from_date")
    private Date fromDate;

    @Temporal(TemporalType.TIMESTAMP)
    @Column(name="to_date")
    private Date toDate;

    @Enumerated(EnumType.STRING)
    @Column(name="interval")
    private QuotesInterval interval;
    ...
}
```

We have also created the two Historic subtypes, HistoricalIndex and HistoricalStock:

我们还创建了两个Historic 子类型，HistoricalIndex和HistoricalStock：

```
@Entity
@DiscriminatorValue("idx")
public class HistoricalIndex extends Historic implementsSerializable {

    private static final long serialVersionUID = -802306391915956578L;

    @ManyToOne(fetch = FetchType.EAGER)
    @JoinColumn(name = "index_code")
    private Index index;
    ...
}

@Entity
@DiscriminatorValue("stk")
public class HistoricalStock extends Historic implements Serializable {

    private static final long serialVersionUID = -802306391915956578L;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "stock_code")
    private StockProduct stock;

    private double bid;

    private double ask;
    ...
}
```

1. Then, we also created the Product entity with its StockProduct subtypes:

然后，我们还创建了带有StockProduct子类型的Product实体：

```
@Entity
@Inheritance(strategy = InheritanceType.TABLE_PER_CLASS)
public abstract class Product {

    private static final long serialVersionUID = -802306391915956578L;

    @Id
    private String code;

    private String name;
    ...
}

@Entity
@Table(name="stock")
public class StockProduct extends Product implements Serializable{

    private static final long serialVersionUID = 1620238240796817290L;

    private String currency;

    @ManyToOne(fetch = FetchType.EAGER)
    @JoinColumn(name = "market_id")
    private Market market;
    ...
}
```

1. In reality, in the financial world, an index \(S&P 500 or NASDAQ\) cannot be bought as
   such; therefore, indices haven’t been considered as products:

4.实际上，在金融世界中，不能购买指数\(S&P 500 or NASDAQ\)； 因此，指数未被视为产品：

```
@Entity
@Table(name="index_value")
public class Index implements Serializable{

    private static final long serialVersionUID = -2919348303931939346L;

    @Id
    private String code;

    private String name;

    @ManyToOne(fetch = FetchType.EAGER)
    @JoinColumn(name = "market_id", nullable=true)
    private Market market;

    @ManyToMany(fetch = FetchType.LAZY)
    @JoinTable(name = "stock_indices",joinColumns={@JoinColumn(name = "index_code")},inverseJoinColumns={@JoinColumn(name ="stock_code")})
    private Set<StockProduct> stocks = new LinkedHashSet<>();
    ...
}
```

1. Finally, the Quote abstract entity with its two subtypes, StockQuote and IndexQuote, have created \(indices are not products, but we can still get instant snapshots from them, and the Yahoo! financial data provider will later be called to get these instant quotes\):

5.最后，Quote抽象实体及其两个子类型StockQuote和IndexQuote已创建（索引不是产品，但我们仍然可以从它们获取即时快照，并且稍后将调用Yahoo!财务数据提供程序来获取这些即时 报价）

```
@Entity
@Inheritance(strategy = InheritanceType.TABLE_PER_CLASS)
public abstract class Quote {

    @Id
    @GeneratedValue(strategy = GenerationType.TABLE)
    protected Integer id;

    private Date date;

    private double open;

    @Column(name = "previous_close")
    private double previousClose;

    private double last;
    ...
}

@Entity
@Table(name="stock_quote")
public class StockQuote extends Quote implements Serializable{

    private static final long serialVersionUID = -8175317254623555447L;

    @ManyToOne(fetch = FetchType.EAGER)
    @JoinColumn(name = "stock_code")
    private StockProduct stock;

    private double bid;

    private double ask;
    ...
}

@Entity
@Table(name="index_quote")
public class IndexQuote extends Quote implements Serializable{

    private static final long serialVersionUID = -8175317254623555447L;

    @ManyToOne(fetch = FetchType.EAGER)
    @JoinColumn(name = "index_code")
    private Index index;
    ...
}
```

## How it works...

We are going to go through some basic and more advanced concepts that we have used to build our relational mapping.

我们将经历一些我们用来构建关系映射的基本和更高级的概念。

### Entity requirements

An entity, to be considered as such by the API requires the following conditions:

* It has to be annotated on The type level with the @Entity annotation.
* It needs to have a defined identifier with either a basic or a complex type. In most cases, a basic identifier is sufficient \(the @Id annotation on a specific entity field\).

* It must be defined as public and not declared as final.

* It needs to have a default constructor \(implicit or not\).

实体要求

API将被视为API的实体需要满足以下条件：

* 必须在类型级别上使用@Entity注释注释。
* 它需要一个带有基本类型或复杂类型的定义标识符。 在大多数情况下，基本标识符就足够了（特定实体字段上的@Id注释）。
* 它必须定义为public，而不是声明为final。
* 它需要有一个默认构造函数（implicit 或 not）。

### Mapping the schema

Both databases and Java objects have specific concepts. The metadata annotations for Entities, along with the configuration by default, describe the relational mapping.

映射模式

数据库和Java对象都有特定的概念。 实体的元数据注释以及默认的配置描述了关系映射。

### Mapping tables

An entity class maps a table. Not specifying a @Table\(name="xxx"\) annotation on the Type level will map the entity class to the table named with the entity name \(this is the default naming\).

实体类映射表。 在类型级别上不指定@Table（name =“xxx”）注释会将实体类映射到使用实体名称命名的表（这是默认命名）。

> The Java's class-naming standard is CamelCased with a capital case for the first letter. This naming scheme doesn't really match the database table-naming standards. For this reason, the @Table annotation is often used.
>
> Java的类命名标准是CamelCase，第一个字母大小写。 这种命名方案并不真正匹配数据库表命名标准。 为此，经常使用@Table注释。

The @Table annotation also has an optional schema attribute, which allows us to bind the table to a schema in the SQL queries \(for example public.user.ID\). This schema attribute will override the default schema JPA property, which can be defined on the persistence unit.

@Table注释也有一个可选的schema属性，它允许我们将表绑定到SQL查询中的一个模式（例如public.user.ID）。 此模式属性将覆盖默认模式JPA属性，该属性可以在持久性单元上定义。

### Mapping columns

As with the table names, the column name to map a field to is specified with the @Column\(name="xxx"\) annotation. Again, this annotation is optional, and not specifying it will make the mapping fall back to the default naming scheme, which is literally the cased name of the field \(in case of single words, it is often a good option\).

The fields of an entity class must not be defined as public. Also keep in mind that you can almost persist all the standard Java Types \(primitive Types, wrappers, Strings, Bytes or Character arrays, and enumerated\) and large numeric Types, such as BigDecimals or BigIntegers, but also JDBC temporal types \(java.sql.Date, java.sql.TimeStamp\) and even serializable objects.

映射列

与表名称一样，将字段映射到的列名称使用@Column（name =“xxx”）注释指定。 再次，此注释是可选的，不指定它将使映射回退到默认命名方案，这是字面的字段名称（在单个字的情况下，它通常是一个很好的选择）。

实体类的字段不能定义为public。 还要记住，你几乎可以坚持所有标准的Java类型（原始类型，包装器，字符串，字节或字符数组和枚举）和大数字类型，如BigDecimals或BigIntegers，还有JDBC时间类型（java.sql  .Date，java.sql.TimeStamp）甚至可序列化对象。

### Annotating fields or getters

The fields of an entity \(if not tagged as @Transient\) correspond to the values that the database row will have for each column. A column mapping can also be defined from a getter \(without necessarily having a corresponding field\).

The @Id annotation defines the entity identifier. Also, defining this @Id annotation on a field or getter defines whether the table columns should be mapped by a field or on a by getters.

When using a getter access mode, and when a @Column annotation is not specified, the default naming scheme for the column name uses the JavaBeans property naming standard \(for example, the getUser\(\) getter would correspond to the user column\).

注释字段或getters

实体的字段（如果未标记为@Transient）对应于数据库行为每列将具有的值。 列映射也可以从getter（不必具有相应的字段）定义。

@Id注释定义实体标识符。 此外，在字段或getter上定义此@Id注释定义表字段是由字段映射还是由getter映射。

当使用getter访问模式，并且未指定@Column注释时，列名称的默认命名方案使用JavaBeans属性命名标准（例如，getUser（）getter将对应于用户列）。

### Mapping primary keys

As we have seen already, the @Id annotation defines the entity's identifier. A persistence context will always manage no more than one instance of an entity with a single identifier.

The @Id annotation on an entity class must map the persistent identifier for a table, which is the primary key.

映射主键

正如我们已经看到的，@Id注释定义实体的标识符。 持久化上下文将始终管理具有单个标识符的实体的不超过一个实例。

实体类上的@Id注释必须映射表的持久标识符，这是主键。

### Identifier generation

A `@GeneratedValue` annotation allows ID generation from the JPA level. This value may not be populated until the object is persisted.

A `@GeneratedValue` annotation has a strategy attribute that is used to configure the generation method \(to rely, for example, on existing database sequences\).

标识符生成

`@GeneratedValue`注释允许从JPA级别生成ID。 在对象持久化之前，可能不会填充此值。

`@GeneratedValue`注释具有用于配置生成方法（例如，依赖于现有数据库序列）的策略属性。

### Defining inheritance

We have defined entity inheritance for subtypes of Products, Historics, and Quotes. When two Entities are close enough to be grouped into a single concept, and if they actually can be associated with a parent entity in the application, it is worth using the JPA inheritance.

Depending upon the persistence strategy for specific data, different storage options can be considered for inheritance mapping.

The JPA allows us to configure an inheritance model from different strategies.

定义继承

我们已经为Products, Historics, 和Quotes定义了实体继承。 当两个实体足够靠近以分组到一个单一的概念，并且如果他们实际上可以与应用程序中的父实体相关联，那么值得使用JPA继承。

根据特定数据的持久性策略，可以考虑将不同的存储选项用于继承映射。

JPA允许我们从不同的策略配置继承模型。

### The single-table strategy

This strategy expects or creates one big table with a discriminator field on the schema. This table hosts the parent-entity fields; these are common to all subentities. It also hosts all the fields of subentity classes. Consequently, if an entity corresponds to one subtype or another, it will populate the specific fields and leave the others blank.

The following table represents the Historic table with its HISTORIC\_TYPE discriminator:

单表策略

这个策略期望或创建一个大表，在模式上有一个discriminator字段。 此表托管父实体字段; 这些是所有子实体的共同。 它还承载子实体类的所有字段。 因此，如果实体对应于一个或另一个子类型，则它将填充特定字段并将其他字段留空。

下表表示具有其HISTORIC\_TYPE鉴别符的历史表：

![](/assets/52.png)

### The table-per-class strategy

This strategy uses specific tables for concrete Entities. There is no discriminator involved here, just specific tables for subtypes. These tables carry both common and specific fields.

We have, for example, implemented this strategy for the Quote entity and its concrete StockQuote and IndexQuote Entities:

table-per-class策略

此策略为具体实体使用特定的表。 这里没有涉及到区分器，只是子类型的特定表。 这些表携带公共和特定字段。

例如，我们为Quote实体及其具体的StockQuote和IndexQuote实体实现了这个策略：

![](/assets/53.png)

### Defining relationships

Entities have the capability to reflect database foreign keys and table to table relationships in their class attributes.

On the application side, because these relationships are built transparently by the entity managers, a huge amount of developments are bypassed.

定义关系

实体能够在其类属性中反映数据库外键和表到表关系。

在应用方面，因为这些关系由实体管理器透明地构建，所以大量的开发被绕过。

### How relationships between entities have been chosen

Before talking about relationships between entities, it is necessary to understand what we plan to do in the cloudstreet-market application.

As introduced in Chapter 1, Setup Routine for an Enterprise Spring Application, we will pull financial data from providers that open their APIs \(Yahoo! actually\). To do so, there are always limitations to keep in mind in terms of call frequency per IP or per authenticated user. Our application will also have its community inside of which financial data will be shared. For financial data providers, when talking about a given stock, the historical view of a stock and an instant quote of a stock are two different things. We had to deal with these two concepts to build our own data set.

如何选择实体之间的关系

在谈论实体之间的关系之前，有必要了解我们计划在cloudstreet-market应用程序中做什么。

如第1章“企业Spring Application的安装例程”中所介绍的，我们将从打开它们的API（实际上是Yahoo!）的提供者拉取财务数据。 为了这样做，总是存在限制以记住每个IP或每个认证的用户的呼叫频率。 我们的应用程序还将有其社区内部的财务数据将共享。 对于金融数据提供商来说，当谈论一个给定的股票时，股票的历史视图和股票的即时报价是两个不同的事情。 我们必须处理这两个概念来构建我们自己的数据集。

In our application, Users will be able to buy and sell Products \(stock, fund, option, and so on\) by executing Transactions:

* First, let's consider the User\(s\)/Transaction\(s\) relationship with the following screenshot:

在我们的应用程序中，用户将能够通过执行交易来购买和销售产品（股票，基金，期权等）

* 首先，让我们考虑用户/事务关系与以下截图：

![](/assets/54.png)

* A User entity can have many Transactions Entities.

* 用户实体可以有多个事务实体。

> In the User class, the second part of the @OneToMany relationship annotation \(the Many element\) drives the Type of attribute we are creating. Specifying Many as the second part declares that the origin entity \(User\) can have several target Entities \(Transactions\). These targets will have to be wrapped  
>  in a collection type. If the origin entity cannot have several targets, then the second part of the relationship has to be One.
>
> 在User类中，@OneToMany关系注释的第二部分（Many元素）驱动我们正在创建的属性的类型。 指定Many作为第二部分声明，源实体（User）可以有多个目标Entities（事务）。 这些目标必须包装在集合类型中。 如果原始实体不能有多个目标，则关系的第二部分必须是一个。

A Transaction can have only one User entity.

* 事务只能有一个用户实体。

> Still in the User class, the first part of the @OneToMany relationship \(the @One element\) is the second part of the relationship annotation defined in the target entity \(if defined\). It is necessary to know whether the target entity can have several origins or not, in order to complete the annotation in the origin.
>
> 仍然在User类中，@OneToMany关系的第一部分（@One元素）是在目标实体（如果定义）中定义的关系注释的第二部分。 有必要知道目标实体是否可以具有多个源，以便在源中完成注释。

* We can then deduce the two annotations: @OneToMany in User and @ManyToOne in Transactions.
* If we are not in the case of a @ManyToMany relationship, we are talking about a unidirectional relationships. From a database's point of view, this means that one of the two tables having a join column that targets the other table. In the JPA, the table that has this join column is the relationship's **owner**.

* 然后我们可以推导出两个注释：@OneToMany在User和@ManyToOne在Transactions。

* 如果我们不是在@ManyToMany关系的情况下，我们谈论一个单向关系。 从数据库的角度来看，这意味着两个表中的一个具有以另一个表为目标的连接列。 在JPA中，具有此连接列的表是关系的所有者。

> The entity, which is the relationship's owner has to be specified by a @JoinColumn annotation on the relationship. The entity that is not the owner, has to provide for its relationship annotation a mappedBy attribute that targets the corresponding Java field name in the opposite entity.
>
> 作为关系的所有者的实体必须由关系上的@JoinColumn注释指定。 不是所有者的实体必须为其关系注释提供一个mappedBy属性，该属性定位对面实体中的相应Java字段名称。

This can now explain the relationship in Transaction:

现在可以解释事务中的关系：

```
@ManyToOne(fetch = FetchType.EAGER)
@JoinColumn(name = "user_name")
private User user;
```

The user\_name column is expected \(or automatically added\) in the transaction table. We will talk about the fetch type later in the There’s more… section.

* The relationship in the User entity is defined as follows:

user\_name列在事务表中是预期的（或自动添加的）。 我们稍后将在“还有更多...”部分中讨论抓取类型。

* 用户实体中的关系定义如下：

```
@OneToMany(mappedBy="user", cascade ={CascadeType.ALL},fetch = FetchType.LAZY)
@OrderBy("id desc")
private Set<Transaction> transactions = new LinkedHashSet<>();
```

> The `@OrderBy` annotation tells the JPA implementation to add an ORDER BY clause to its SQL query.
>
> `@OrderBy`注释告诉JPA实现在其SQL查询中添加一个ORDER BY子句。

An Index entity has one Market entity. We have decided that a market is the geographical area \(Europe, the US, Asia, and so on\). A market has several concrete indices.

This looks like a @OneToMany/@ManyToOne relation again. The relationship's owner is the Index entity because we expect to have a Market column in the Index table \(and not an Index column in the Market table\).

一个Index实体有一个Market实体。 我们决定一个市场是地理区域（欧洲，美国，亚洲等）。 市场有几个具体indices。

这看起来像一个@OneToMany/@ManyToOne关系。 关系的所有者是Index实体，因为我们希望在Index表中有一个Market列（而不是Market表中的Index列）。

It is the same story between the concrete Product \(such as StockProduct\) and Market entities, except that, since it doesn't look mandatory in the application to retrieve stocks directly from Market, the relationship has not been declared on the Market entity side. We have kept only the owner side.

About the concrete Quotes entity \(such as StockQuote\) and the concrete Products entity \(such as StockProduct\), one quote will have one product. If we were interested in retrieving Quote from a Product entity, one product would have many quotes. The owner of the relationship is the concrete Quote entity.

It is the same logic as the previous point for IndexQuote and Index.

在具体产品（例如StockProduct）和市场实体之间是相同的故事，除了这一点之外，由于在直接从市场检索股票的应用程序中看起来不是强制性的，所以该关系没有在市场实体方面声明。 我们只保留所有者一方。

关于具体的Quotes实体（如StockQuote）和具体的Products实体（如StockProduct），一个报价将有一个产品。 如果我们有兴趣从产品实体检索报价，一个产品将有很多报价。 关系的所有者是具体的Quote实体。

它与IndexQuote和Index的上一点是相同的逻辑。

Between Index and StockProduct, in reality, indices \(S&P 500, NASDAQ, and so on\) have constituents, and the sum of the constituents' values makes the index value. Thus, one Index entity has several potential StockProduct entities. Also one StockProduct can belong to several Indices. This looks like a bidirectional relationship. We present here the Index side:

在Index和StockProduct之间，实际上，指数（S&P 500，NASDAQ等）具有成分，并且成分的值的总和成为指数值。 因此，一个Index实体具有几个潜在的StockProduct实体。 另外一个StockProduct可以属于几个指数。 这看起来像一个双向关系。 我们在这里介绍索引一方：

```
@ManyToMany(fetch = FetchType.LAZY)
@JoinTable(name = "stock_indices", joinColumns={@JoinColumn(name = "index_code")}, inverseJoinColumns={@JoinColumn(name ="stock_code")})
private Set<StockProduct> stocks = new LinkedHashSet<>();
```

This relationship is specified an extra join table \(expected or generated by the JPA\). It is basically a table with two join columns pointing to the @Ids fields of the respective entities.

此关系指定了一个额外的连接表（由JPA预期或生成）。 它基本上是一个表，它有两个指向各个实体的@Ids字段的连接列。

## There's more...

We are going to visit two metadata attributes that we didn’t explain yet: the FetchType attribute and the Cascade attribute.

我们将访问我们还没有解释的两个元数据属性：FetchType属性和Cascade属性。

### The FetchType attribute

We have seen that the relationship annotations @OneToOne, @OneToMany, and @ManyToMany can be specified in a fetch attribute, which can either be FetchType.EAGER or FetchType.LAZY.

When a FetchType.EAGER attribute is chosen, relationships are automatically loaded by the entityManager when the entity gets managed. The overall amount of SQL queries executed by JPA is significantly increased, especially because some related entities that may not be required each time are loaded anyway. If we have two, three, or more levels of entities bound to a root entity, we should probably consider switching some fields locally to FetchType.LAZY.

我们已经看到，可以在fetch属性中指定关系注释@OneToOne，@OneToMany和@ManyToMany，它可以是FetchType.EAGER或FetchType.LAZY。

当选择FetchType.EAGER属性时，当实体被管理时，entityManager会自动加载关系。  JPA执行的SQL查询的总量显着增加，特别是因为一些可能不需要的相关实体每次都被加载。 如果我们有两个，三个或更多级别的实体绑定到根实体，我们应该考虑将一些字段本地切换到FetchType.LAZY。

A FetchType.LAZY attribute specifies the JPA implementation not to populate a field value on the entity-loading SQL query. The JPA implementation generates extra-asynchronous SQL queries to populate the LAZY fields when the program specifically asks for it \(for example, when getStock\(\) is called in the case of a HistoricalStock entity\). When using Hibernate as an implementation, FetchType.LAZY is taken as the default fetch type for relationships.

It is important to think about lightening the relationship loading, especially on collections.

FetchType.LAZY属性指定JPA实现不填充实体加载SQL查询上的字段值。  JPA实现生成非异步SQL查询以在程序特别要求时填充LAZY字段（例如，在HistoricalStock实体的情况下调用getStock（）时）。 当使用Hibernate作为实现时，FetchType.LAZY作为关系的默认访存类型。

重要的是要考虑减轻关系加载，尤其是对集合。

### The Cascade attribute

Another attribute to be mentioned in relationship annotations is the Cascade attribute. This attribute can take the values CascadeType.DETACH, CascadeType.MERGE, CascadeType.PERSIST, CascadeType.REFRESH, CascadeType.REMOVE, and CascadeType.ALL.

This attribute specifies how the JPA implementation should process the related Entities when asked to perform an operation \(such as persist, update, delete, find, and so on.\) on the main Entity. It is an optional attribute which is usually defaulted to **no-cascaded** operations.

级联属性

在关系注释中要提到的另一个属性是Cascade属性。 此属性可以取值CascadeType.DETACH，CascadeType.MERGE，CascadeType.PERSIST，CascadeType.REFRESH，CascadeType.REMOVE和CascadeType.ALL。

此属性指定当被要求对主实体执行操作（例如持久，更新，删除，查找等）时，JPA实现应如何处理相关实体。 它是一个可选属性，通常默认为**no-cascaded**操作。

## See also

There is a third strategy to define Entity inheritance:

* The joined-table inheritance strategy: We haven't implemented it yet, but this strategy is a bit similar to the table-per-class strategy. It differs from it in the fact that, instead of repeating the parent-entity fields \(columns\) in the concrete tables, the JPA creates or expects an extra table with only the parent-entity columns and manages the joins transparently with this table.

有第三个策略来定义实体继承：

* 连接表继承策略：我们还没有实现它，但这个策略有点类似于table-per-class策略。 它与它的不同之处在于，JPA不是重复具体表中的父实体字段（列），而是仅创建或期望一个只有父实体列的额外表，并使用此表透明地管理连接。



