This recipe presents how to create Spring HATEOAS resources. Even if the emphasis here is on one specific resource—IndexResource \(in place of the former IndexOverviewDTO\),feel free to browse cloudstreetmarket-api and cloudstreetmarket-core to discover more changes.

The HATEOAS principle has been applied at this stage to all the resources that make the core of our business, which strongly reflects the financial data structure of Yahoo! \(indices, quotes,products, historical data, graphs, and so on\).

这个谱方介绍如何创建Spring HATEOAS资源。 即使这里的重点是一个特定的资源IndexResource（代替以前的IndexOverviewDTO），也可以浏览cloudstreetmarket-api和cloudstreetmarket-core来发现更多的变化。

HATEOAS原则在这一阶段已被应用于构成我们业务核心的所有资源，这强烈反映了Yahoo!的财务数据结构（指数，报价，产品，历史数据，图表等）。

## How to do it…

1. From the Git Perspective in Eclipse, checkout the latest version of the v6.x.x branch. Then, run a maven clean install command on the cloudstreetmarketparent module \(right-click on the Maven Clean menu under Run as… and then again on Maven Install under Run as…\) followed by a click on Maven Update Project menu to synchronize Eclipse with the Maven configuration \(right-click on the module and then navigate to Maven \| Update Project….\)

1.从Eclipse中的Git Perspective中，检查v6.x.x分支的最新版本。 然后，在cloudstreetmarketparent模块上运行maven clean install命令（右键单击Run as ...下的Maven Clean菜单，然后再单击Run as ...下的Maven Install），然后单击Maven Update Project菜单，将Eclipse与 Maven配置（右键单击模块，然后导航到Maven \| Update Project...。）

> This branch includes SQL scripts that prepopulate the database with real financial data coming from Yahoo!.
>
> 此分支包括使用来自Yahoo！的真实财务数据预填充数据库的SQL脚本。

1. Among the pulled changes, a new /app configuration directory shows up at the same level as cloudstreetmarket-parent and zipcloud-parent. This /app directory has to be copied to your system's home directory:

2.在拉取的更改中，新的/ app配置目录显示在与cloudstreetmarket-parent和zipcloud-parent相同的级别。 此/ app目录必须复制到系统的主目录：

* [ ] Copy it to C:\Users{system.username}\app if you are on Windows

* [ ] Copy it to /home/usr/{system.username}/app if you are on Linux

* [ ]  If you are on Mac OS X, copy it at /Users/{system.username}/app

1. Spring HATEOAS comes with the following dependency. This dependency has been added to cloudstreetmarket-parent, cloudstreetmarket-core, and cloudstreetmarket-api:

Spring HATEOAS有以下依赖。 此依赖项已添加到cloudstreetmarket-parent，cloudstreetmarket-core和cloudstreetmarket-api：

```
<dependency>
    <groupId>org.springframework.hateoas</groupId>
    <artifactId>spring-hateoas</artifactId>
    <version>0.17.0.RELEASE</version>
</dependency>
```

1. As the recipe title suggests, the goal is to get rid of the existing DTOs that were exposed with the REST API. We have, for now, removed IndexOverviewDTO, MarketOverviewDTO, ProductOverviewDTO, and StockProductOverviewDTO.

2. Those DTOs have been replaced by these classes: IndexResource,StockProductResource, ChartResource, ExchangeResource, IndustryResource,and MarketResource.

4.如配方标题所示，目标是摆脱使用REST API公开的现有DTO。 我们现在已经删除了IndexOverviewDTO，MarketOverviewDTO，ProductOverviewDTO和StockProductOverviewDTO。

5.这些DTO已经被这些类取代：IndexResource，StockProductResource，ChartResource，ExchangeResource，IndustryResource和MarketResource。

1. As shown with IndexResource, which is presented as follows, all these new classes inherit the Spring HATEOAS Resource class:

6.如IndexResource所示，如下所示，所有这些新类继承Spring HATEOAS Resource类：

```
@XStreamAlias("resource")
public class IndexResource extends Resource<Index> {
    public static final String INDEX = "index";
    public static final String INDICES = "indices";
    public static final String INDICES_PATH = "/indices";
    public IndexResource(Index content, Link... links) {
        super(content, links);
    }
}
```

1. As you can see, with IndexResource, resources are created from JPA entities \(here,Index.java\). These entities are stored in the Resource supertype under the content property name.

2. We have transformed the JPA entities, abstracting their @Id in an implementation of the Identifiable interface:

7.您可以看到，使用IndexResource，从JPA实体（这里是Index.java）创建资源。 这些实体存储在内容属性名称下的资源超类中。

8.我们已经转换了JPA实体，在Identifiable接口的实现中抽象了他们的@Id：

```
@Entity
@Table(name="index_value")
@XStreamAlias("index")
public class Index extends ProvidedId<String> {

    private String name;

    @Column(name="daily_latest_value")
    private BigDecimal dailyLatestValue;

    @Column(name="daily_latest_change")
    private BigDecimal dailyLatestChange;

    @Column(name="daily_latest_change_pc")
    private BigDecimal dailyLatestChangePercent;

    @Column(name = "previous_close")
    private BigDecimal previousClose;

    private BigDecimal open;
    private BigDecimal low;

    @ManyToOne(fetch = FetchType.EAGER)
    @JsonSerialize(using=IdentifiableSerializer.class)
    @JsonProperty("exchangeId")
    @XStreamConverter(value=IdentifiableToIdConverter.class,strings={"id"})
    @XStreamAlias("exchangeId")
    private Exchange exchange;

    @JsonIgnore
    @XStreamOmitField
    @ManyToMany(fetch = FetchType.LAZY)
    @JoinTable(name = "stock_indices", joinColumns ={@JoinColumn(name = "index_code") },
            inverseJoinColumns = {@JoinColumn(name = "stock_code")})
    private Set<StockProduct> components = new LinkedHashSet<>();

    @Column(name="last_update", insertable=false,columnDefinition="TIMESTAMP DEFAULT CURRENT_TIMESTAMP")
    @Temporal(TemporalType.TIMESTAMP)
    private Date lastUpdate;

    public Index(){}

    public Index(String indexId) {
    setId(indexId);
    }

    //getters & setters

    @Override
    public String toString() {
    return "Index [name=" + name + ", dailyLatestValue=" +
    dailyLatestValue + ", dailyLatestChange=" +
    dailyLatestChange + ", dailyLatestChangePercent=" +
    dailyLatestChangePercent + ", previousClose=" +
    previousClose + ", open=" + open + ", high=" + high + ",
    low=" + low + ", exchange=" + exchange + ", lastUpdate="
    + lastUpdate + ", id=" + id + "]";
    }
}
```

1. Here are the details of the ProvidedId class, which is one of our Identifiable implementations:

9.这里是ProvideId类的细节，这是我们的Identifiable实现之一：

```
@MappedSuperclass
public class ProvidedId<ID extends Serializable> implements Identifiable<ID> {
    @Id
    protected ID id;

    @Override
    public ID getId() {
        return id;
    }
    public void setId(ID id) {
        this.id = id;
    }

    @Override
    public String toString() {
        return id;
    }

    @Override
    public int hashCode() {
        return Objects.hash(id);
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj)
            return true;
        if (obj == null)
            return false;
        if (getClass() != obj.getClass())
            return false;
        ProvidedId <?> other = (ProvidedId <?>) obj;
            return Objects.equals(this.id, other.id);
    }
}
```

## How it works...

One new Spring dependency, a few new resource objects \(Resource subclasses\), and finally some modifications to our Entities so that they implement the Identifiable interface. Let's debrief all this in detail.

一个新的Spring依赖，一些新的资源对象（Resource子类），最后一些修改我们的Entities，使他们实现Identifiable接口。 让我们详细地总结这一切。

### Spring HATEOAS resources

As introduced at the beginning of this chapter, HATEOAS is about links. It is fair to say that we can expect, as part of the Framework, an existing Type that supports and standardizes the representation of links.

This is the role of the ResourceSupport class \(part of Spring HATEOAS\): to support the collection and management of links attached to a resource.

Alternatively, a REST resource is also a content. The Framework also offers a Resource class that already inherits ResourceSupport.

To summarize, using Spring HATEOAS, we can decide to model our resource objects\(IndexResource, StockProductResource, and so on\) in two different ways:

* We can model them either by making them inherit ResourceSupport directly. By doing so, we have to manage the resource-content as part of the wrapper object by ourselves. The content here is out of control for the Framework.

* We can also model them by making them inherit the generic Resource&lt;T&gt; class whose Type T corresponds to the Type of the POJO content for the resource. This is the strategy we have chosen. The Framework offers goodies for our resource-object\(Inde3xResource\) on content binding, link creation, and even at the controller's level. We will see all this soon.

正如本章开头所介绍的，HATEOAS是关于链接。 可以公平地说，作为框架的一部分，我们可以期望支持和标准化链接表示的现有类型。

这是ResourceSupport类（Spring HATEOAS的一部分）的作用：支持连接到资源的链接的收集和管理。

或者，REST资源也是内容。 框架还提供了一个已经继承了ResourceSupport的Resource类。

总之，使用Spring HATEOAS，我们可以决定以两种不同的方式来建模我们的资源对象（IndexResource，StockProductResource等）：

* 我们可以通过使它们直接继承ResourceSupport来对它们建模。 通过这样做，我们必须自己管理资源内容作为包装器对象的一部分。 这里的内容是对框架失去控制。

* 我们还可以通过使它们继承其类型T对应于资源的POJO内容的类型的通用Resource &lt;T&gt;类来对它们建模。 这是我们选择的策略。 框架为我们的资源对象（Inde3xResource）提供内容绑定，链接创建，甚至在控制器级别的好东西。 我们很快就会看到这一切。

The ResourceSupport class  
The ResourceSupport class is an object that implements Identifiable&lt;Link&gt;:  
The following is a sample from the ResourceSupport JavaDoc, which will provide you with an insight on its constructors and methods:

ResourceSupport类是实现Identifiable &lt;Link&gt;的对象：

```
public class ResourceSupport extends Object implements Identifiable<Link>
```

以下是来自ResourceSupport JavaDoc的示例，它将为您提供对其构造函数和方法的了解：

![](/assets/104.png)

![](/assets/105.png)

As introduced earlier, this class is all about links! We will see that Spring HATEOAS provides a small machinery around links.

如前所述，这个类是关于链接！ 我们将看到Spring HATEOAS提供了一个围绕链接的小型机器。

The Resource class

The Resource class is a wrapper for a POJO. The POJO is stored in a content property of this class. A Resource class natively extends ResourceSupport:

Resource类是POJO的包装器。  POJO存储在此类的内容属性中。  Resource类本身扩展了ResourceSupport：

`public class Resource <T> extends ResourceSupport`

Here is a sample from the Resource JavaDoc that provides an insight into its constructors and methods:

下面是来自Resource JavaDoc的一个示例，它提供了对其构造函数和方法的洞察：

![](/assets/106.png)

![](/assets/107.png)

Two handy constructors, one getter for the content, and all the link-related helpers, this is what the Resource class is made of.

两个方便的构造函数，一个内容的getter，以及所有与链接相关的助手，这就是Resource类。

The Identifiable interface

The Identifiable interface plays a central role in Spring HATEOAS, since the key classes Resource, ResourceSupport, Resources, and PagedResources classes, which we'll present later on, are all Identifiable implementations. We will present later on, all these key classes.

The Identifiable interface is a Spring HATEOAS one-method interface \(a generic interface\) that is used to define an Id in an object:

可识别的接口

Identifiable接口在Spring HATEOAS中起着核心作用，因为关键类Resource，ResourceSupport，Resources和PagedResources类（稍后我们将介绍）都是可识别的实现。 我们将在后面介绍所有这些关键类。

Identifiable接口是一个Spring HATEOAS单方法接口（一个通用接口），用于在对象中定义一个Id：

```
public interface Identifiable<ID extends Serializable> {
    ID getId();
}
```

Consequently, the Framework uses this method to retrieve the ID with very few requirements about the nature of the passed-in object. With the capability of a class to implement several interfaces, it is costless to add such a qualifier to an object. Also, the contract of this interface is minimal.

因此，框架使用此方法检索ID，对传入对象的性质只有很少的要求。 通过一个类实现几个接口的能力，向对象添加这样的限定符是无成本的。 此外，此接口的合同最小。

The most important use of this interface \(and method\) by the framework is to build links out of a Resource object. Have a look at the slash method of LinkBuilderSupport. You will note that, if ID is not an instance of Identifiable \(this is what it usually ends up with\), the Link is appended with the toString\(\) representation of the ID type.

框架对这个接口（和方法）的最重要的使用是从Resource对象构建链接。 看看LinkBuilderSupport的斜杠方法。 您将注意到，如果ID不是Identifiable的实例（这通常是最终结果），则该链接将附加ID类型的toString\(\)表示。

> Bear this behavior in mind if you are thinking of implementing custom ID types.
>
> 如果你正在考虑实现自定义ID类型，记住这个行为。

Abstracting the Entities' @Id

If you plan to stick with Spring HATEOAS without extending it to Spring Data REST, it is probably not an absolute necessity to decouple the base entities from their @Id. At least not in the way we did it.

Here, this practice comes from Oliver Gierke, in his Spring RestBucks application. Spring RestBucks is a showcase application for several modern Spring REST features.

抽象实体的@Id

如果你计划坚持使用Spring HATEOAS而不将其扩展到Spring Data REST，则可能不是绝对必要的将基本实体与其@Id解耦。 至少不是我们这样做的方式。

在这里，这种做法来自Oliver Gierke，在他的Spring RestBucks应用程序。  Spring RestBucks是几个现代Spring REST功能的展示应用程序。

> Oliver Gierke is the Spring Data lead developer at Pivotal Software,Inc.. He has also been involved in Spring HATEOAS. Spring Data is an amazing project and product. We can trust Oliver Gierke for his vision and decisions.
>
> Oliver Gierke是Pivotal Software，Inc.的Spring Data首席开发人员。他还参与了Spring HATEOAS。  Spring Data是一个了不起的项目和产品。 我们可以信任奥利弗·吉尔克的愿景和决定。

In his AsbtractId implementation, O. Gierke defines the Id property as private and annotates it as @JsonIgnore. He drives us toward the nonexposure of the Id attribute as part of the resource-content. In REST, the ID of a resource should be its URI.

If you have the chance to take a look at Spring Data REST, this approach fully makes sense as part of the Framework, which strongly correlates REST resources to Spring Data repositories.

We have made the choice of not covering Spring Data REST as part of this book. However, not exposing entity IDs is not critical for our application. For these reasons, and also because we wish to maintain conventionality on this point in regard to the Chapter 7, Developing CRUD Operations and Validations, IDs will be exposed as resource-attributes.

在他的AsbtractId实现中，O. Gierke将Id属性定义为`private`，并将其注释为`@JsonIgnore`。 他驱使我们朝着Id属性的非曝光，作为资源内容的一部分。 在REST中，资源的ID应为其URI。

如果你有机会来看一看Spring Data REST，这种方法完全有意义作为框架的一部分，这将REST资源与Spring Data repositories紧密相关。

我们已经选择了不覆盖Spring Data REST作为本书的一部分。 然而，不暴露实体ID对于我们的应用程序不是至关重要的。 由于这些原因，并且也因为我们希望就第7章，开发CRUD操作和验证的这一点保持一致性，ID将被暴露为资源属性。

## There's more…

If our HATEOAS introduction wasn't clear enough to give you an idea of the principle, do read this presentation from Pivotal \(Spring.io\) at:

如果我们的HATEOAS介绍不够清楚，给你一个想法的原则，请阅读这个演示文稿从Pivotal（Spring.io）在：

[https://spring.io/understanding/HATEOAS](https://spring.io/understanding/HATEOAS)

## See also

* We recommend that you visit O. Gierke's Spring REST showcase application, which presents both Spring HATEOAS in practice coupled or not to Spring Data REST, at  
   [https://github.com/olivergierke/spring-restbucks](https://github.com/olivergierke/spring-restbucks).

* You can find a few discussions about ID exposure at [https://github.com/spring-projects/spring-hateoas/issues/66](https://github.com/spring-projects/spring-hateoas/issues/66).

* We advise you to read more about Spring Data REST since we have only introduced a little bit of it. Spring Data REST builds REST resources on top of Spring Data repositories and automatically publishes their CRUD services. You can learn more  
   about it at [http://docs.spring.io/spring-data/rest/docs/current/reference/html](http://docs.spring.io/spring-data/rest/docs/current/reference/html).

* 我们建议您访问O. Gierke的Spring REST展示应用程序，它显示了Spring HATEOAS在实践中是否与Spring Data REST相耦合，  
  https//github.com/olivergierke/spring-restbucks。

* 您可以在https//github.com/spring-projects/spring-hateoas/issues/66找到有关ID暴露的几个讨论。

* 我们建议您阅读有关Spring Data REST的更多信息，因为我们只介绍了一点。  Spring Data REST在Spring数据存储库之上构建REST资源，并自动发布其CRUD服务。 您可以了解更多  
  关于它在http//docs.spring.io/spring-data/rest/docs/current/reference/html。



