In this recipe, we will focus on how to create links with Spring HATEOAS and how to bind them to resources.

We will detail the resource assemblers, which are reusable transition components used to pass from entities \(such as Index\) to their resources \(IndexResource\). These components also provide support for link creation.

在本文中，我们将重点介绍如何创建与Spring HATEOAS的链接以及如何将它们绑定到资源。

我们将详细描述资源汇编器，它们是可重用的转移组件，用于从实体（如Index）传递到它们的资源（IndexResource）。 这些组件还提供对链接创建的支持。

## How to do it…

1. The created resources \(IndexResource, ChartResource, ExchangeResource, IndustryResource, MarketResource, and so on\) are created from their associated Entity \(Index, ChartIndex, ChartStock, Exchange, Industry, Market, and so on\) using resource assemblers registered as @Component:

1.使用注册为`@Component`的资源汇编器，从它们关联的实体（Index，ChartIndex，ChartStock，Exchange，Industry，Market等）创建创建的资源（IndexResource，ChartResource，ExchangeResource，IndustryResource，MarketResource等）：

```
import static org.sfw.hateoas.mvc.ControllerLinkBuilder.linkTo;
import static org.sfw.hateoas.mvc.ControllerLinkBuilder.methodOn;
import org.sfw.hateoas.mvc.ResourceAssemblerSupport;
import org.sfw.hateoas.EntityLinks;
import static edu.zc.csm.api.resources.ChartResource.CHART;
import static edu.zc.csm.api.resources.ExchangeResource.EXCHANGE;
import static edu.zc.csm.api.resources.StockProductResource.COMPONENTS;

@Component
public class IndexResourceAssembler extends ResourceAssemblerSupport<Index, IndexResource> {

    @Autowired
    private EntityLinks entityLinks;
    
    public IndexResourceAssembler() {
        super(IndexController.class, IndexResource.class);
    }
    
    @Override
    public IndexResource toResource(Index index) {
        IndexResource resource = createResourceWithId(index.getId(), index);
        resource.add(entityLinks.linkToSingleResource(index.getExchange()).withRel(EXCHANGE));
        resource.add(linkTo(methodOn(ChartIndexController.class).get(index.getId(), 
                ".png", null, null, null, null, null, null,null)).withRel(CHART));
        resource.add(linkTo(methodOn(StockProductController.class).getSeveral(null, null, 
                index.getId(), null, null, null,null)).withRel(COMPONENTS));
        return resource;
    }
    
    @Override
    protected IndexResource instantiateResource(Index entity) {
        return new IndexResource(entity);
    }
}
```

> We've used these assemblers to generate the links that come with a resource. They use static methods from ControllerLinkBuilder \(linkTo and methodOn\) and explicit labels defined as constants in the resources themselves\(EXCHANGE, CHART, and COMPONENTS\).
>
> 我们使用这些汇编器来生成与资源一起提供的链接。 它们使用ControllerLinkBuilder（linkTo和methodOn）中的静态方法和在资源本身（EXCHANGE，CHART和COMPONENTS）中定义为常量的显式标签。



2. We have altered our previous SwaggerConfig class so that this class can be used for annotation-based configuration in other domains that Swagger. This class has been renamed to AnnotationConfig.

3. We have also added to this AnnotationConfig class the following two annotations:

2.我们改变了我们以前的SwaggerConfig类，以便这个类可以用于Swagger的其他域中基于注释的配置。 此类已重命名为AnnotationConfig。

3.我们还向这个AnnotationConfig类添加了以下两个注释：

```
@EnableHypermediaSupport(type = { HypermediaType.HAL })
@EnableEntityLinks(Because these two annotations don't have an XML equivalent yet).
```

4. All the targeted controllers in these converters have been annotated with the `@ExposesResourceFor` annotation \(on the class level\).

5. These controllers now also return the created resources or pages of resources:

4.这些转换器中的所有目标控制器都已注释了`@ExposesResourceFor`注释（在类级别上）。

5.这些控制器现在还返回创建的资源或资源页面：

```
@RestController
@ExposesResourceFor(Index.class)
@RequestMapping(value=INDICES_PATH,produces={"application/xml", "application/json"})
public class IndexController extends CloudstreetApiWCI<Index> {

    @Autowired
    private IndexService indexService;
    
    @Autowired
    private IndexResourceAssembler assembler;
    
    @RequestMapping(method=GET)
    public PagedResources<IndexResource> getSeveral(@RequestParam(value="exchange", required=false) String exchangeId,
            @RequestParam(value="market", required=false) MarketId marketId, 
            @PageableDefault(size=10, page=0,sort={"previousClose"}, direction=Direction.DESC) Pageable pageable){
            
        return pagedAssembler.toResource(indexService.gather(exchangeId,marketId,pageable), assembler);
    }
    
    @RequestMapping(value="/{index:[a-zA-Z0-9^.-]+}{extension:\\.[az]+}", method=GET)
    public IndexResource get(@PathVariable(value="index") String indexId,
            @PathVariable(value="extension") String extension){
            
        return assembler.toResource(indexService.gather(indexId));
    } 
}
```

6. Here, we have made CloudstreetApiWCI generic. In this way, CloudstreetApiWCI can have a generic PagedResourcesAssembler @Autowired:

在这里，我们已经使CloudstreetApiWCI通用。 这样，CloudstreetApiWCI可以有一个通用的PagedResourcesAssembler `@Autowired`：

```
@Component
@PropertySource("classpath:application.properties")
public class CloudstreetApiWCI<T extends Identifiable<?>> extends WebContentInterceptor {
    ...
    @Autowired
    protected PagedResourcesAssembler<T> pagedAssembler;
    ...
}
```

> Since it is not the legacy purpose of a WebCommonInterceptor class to be used as a super controller sharing properties and utility methods, we will create an intermediate component between controllers and WebCommonInterceptor.
>
> 由于不是WebCommonInterceptor类的传统目的用作超级控制器共享属性和实用程序方法，我们将在控制器和WebCommonInterceptor之间创建一个中间组件。



7. In order to @Autowire the PagedResourcesAssemblers, as we did, we have registered a PagedResourcesAssembler bean in dispatcher-servlet.xml:

7.为了@Autowire PagedResourcesAssemblers，像我们一样，我们在dispatcher-servlet.xml中注册了一个PagedResourcesAssembler bean：

```
<bean class="org.sfw.data.web.PagedResourcesAssembler">
    <constructor-arg><null/></constructor-arg>
    <constructor-arg><null/></constructor-arg>
</bean>
```

8. As a result, now calling the API for a ^GDAXI index code \(http://cloudstreetmarket.com/api/indices/%5EGDAXI.xml\) produces the following output:

8.因此，现在调用^ GDAXI索引代码的API（http://cloudstreetmarket.com/api/indices/%5EGDAXI.xml）会生成以下输出：

![](/assets/108.png)

> As links, we have expressed endpoints and URI paths. From those links we can retrieve other Entities in relationship with an index \(if we want to expose them obviously\).
>
> 作为链接，我们已经表达了端点和URI路径。 从这些链接，我们可以检索与索引相关的其他实体（如果我们想要暴露它们）。

## How it works...

This section specifically details the links creation.

本节具体详细说明链接创建。

### Resource assemblers

This kind of specialized converters \(Resource assemblers\) are thought for reusability. Their main functions are as follows:

* Instantiating the resource and hydrating it with content

* Creating the resource's links from the Entity state or from the static global design

The Framework provides a ResourceAssemblerSupport super-class whose role is to reduce boilerplate code in the assemblers' duties.

The ResourceAssemblerSupport class is an abstract generic class. It enriches an assembler by providing a couple of extra methods. With T being a controller's class or super.Type, its signature is the following:

资源汇编器

这种专门的转换器（资源组装器）被认为是可重用的。 其主要功能如下：

* 实例化资源并使其与内容水化

* 从实体状态或从静态全局设计创建资源的链接

框架提供了一个ResourceAssemblerSupport超类，其作用是在组装者的职责中减少模板代码。

ResourceAssemblerSupport类是一个抽象类。 它通过提供一些额外的方法丰富了汇编器。  T是控制器的类或super.Type，它的签名如下：

```
public abstract class ResourceAssemblerSupport<T, D extends ResourceSupport> implements ResourceAssembler<T, D>
```

The table here provides a glimpse of the ResourceAssemblerSupport JavaDoc:

下面的表格提供了ResourceAssemblerSupport JavaDoc的一览：

![](/assets/109.png)

The ResourceAssemblerSupport class also implements ResourceAssembler,which is the one-method interface presented here that forces the assembler to provide a toResource\(T entity\) method:

```
public interface ResourceAssembler<T, D extends ResourceSupport> {
    D toResource(T entity);
}
```

It can be noticed that we have overridden the instantiateResource method in our assemblers. As specified in the JavaDoc, not overriding it causes the Framework to instantiate the resource by reflection, looking for a no-arg constructor in the resource.

We have preferred here to avoid such constructors in our resources, as they can be a bit of an overhead.

可以注意到，我们在我们的汇编器中覆盖了instantiateResource方法。 如JavaDoc中所指定的，不覆盖它会导致框架通过反射来实例化资源，在资源中寻找一个无参数的构造函数。

我们在这里喜欢在我们的资源中避免这样的构造函数，因为它们可能有点开销。

### PagedResourcesAssembler

This amazing, generic super assembler is used to build link-based pages of resources for the client. With an incredibly small amount of configuration, Spring HATEOAS builds for us a complete and out-of-the-box, fully populated page of typed-resources.

Based on our presented configuration, you can try calling the following URL:

精彩，通用的超级汇编程序用于为客户端构建基于链接的资源页面。 有了非常少量的配置，Spring HATEOAS为我们构建了一个完整的，现成的，完全填充的类型资源页面。

根据我们提供的配置，您可以尝试调用以下URL：

http://cloudstreetmarket.com/api/indices.xml

Doing this, you should obtain the following output:

这样做，您应该获得以下输出：

![](/assets/110.png)

Can you see the **next rel** link and how it has been built by reflection from our method-handler annotations and their default and used values? Try to follow the **next** link to see how the navigation gets updated and incremented smoothly.

你能看到**next rel**链接，以及它是如何通过我们的方法处理程序注释的反射和它们的默认和使用值构建的？ 尝试按照**next**链接查看导航是如何更新和平滑增量。

In the IndexController.getSeveral\(\) method-handler \(shown in the following snippet\),we make sure that every single resource is built properly \(content and links\) by making the PagedResourcesAssembler using our custom IndexResourceAssembler:

在IndexController.getSeveral\(\)方法处理程序（如下面的代码片段所示）中，我们通过使用我们自定义的IndexResourceAssembler来使PagedResourcesAssembler正确地建立每个资源（内容和链接）：

```
@RequestMapping(method=GET)
public PagedResources<IndexResource> getSeveral(@RequestParam(value="exchange", required=false) String exchangeId,
                @RequestParam(value="market", required=false) MarketId marketId,
                @PageableDefault(size=10, page=0, sort={"previousClose"},direction=Direction.DESC) Pageable pageable){
                
    return pagedAssembler.toResource(indexService.gather(exchangeId, marketId, pageable),assembler);
}
```

### Building links

Let's have a look at the way we build resource links in assemblers. The presented toResource\(\) method in IndexResourceAssembler uses two different techniques.

The first technique through EntityLinks uses JPA Entities; the second one, through the ControllerLinkBuilder static methods, uses Controllers directly.

让我们看看我们在汇编器中构建资源链接的方式。  IndexResourceAssembler中提供的toResource（）方法使用两种不同的技术。

通过EntityLinks的第一种技术使用JPA实体; 第二个，通过ControllerLinkBuilder的静态方法，直接使用Controllers。

### EntityLinks

By declaring the @EnableEntityLinks annotation in a configuration class, an EntityLinks implementation gets registered: ControllerEntityLinks. All the Spring MVC controllers of ApplicationContext are looked up to search for the ones carrying a @ExposesResourceFor\(xxx.class\) annotation.

The @ExposesResourceFor annotation on a Spring MVC controller exposes the model Type that the controller manages. This registration enables the required mapping between the controller and a JPA entity.

It must also be noted that the registered ControllerEntityLinks implementation assumes a certain @RequestMapping configuration on controllers. The @RequestMapping configuration is made as follows:

* for a collection of resources, a class-level @RequestMapping annotation is expected. The controller then has to expose a method-handler mapped to an empty path, for example, @RequestMapping\(method = RequestMethod.GET\).

* For individual resources, those are exposed with the id of the managed JPA Entity,for example, @RequestMapping\("/{id}"\).

通过在配置类中声明`@EnableEntityLinks`注释，EntityLinks实现将注册：ControllerEntityLinks。 查找ApplicationContext的所有Spring MVC控制器以搜索携带`@ExposesResourceFor`（xxx.class）注释的控件。

Spring MVC控制器上的`@ExposesResourceFor`注释公开了控制器管理的模型类型。 此注册启用控制器和JPA实体之间所需的映射。

还必须注意，注册的ControllerEntityLinks实现假定控制器上有一个`@RequestMapping`配置。  `@RequestMapping`配置如下：

* 对于资源集合，需要类级别的`@RequestMapping`注释。 然后控制器必须公开映射到空路径的方法处理程序，例如，`@RequestMapping（method = RequestMethod.GET）`。

* 对于单个资源，它们使用受管JPA实体的ID进行公开，例如`@RequestMapping("/{id}")`。

Acknowledging these points, the EntityLinks implementation\(ControllerEntityLinks\) is used from @Autowiring to generate Links using the collection of methods it provides:

确认这些点，使用EntityLinks实现（ControllerEntityLinks）从`@Autowiring`使用它提供的方法集合来生成链接：

```
public interface EntityLinks extends Plugin<Class<?>>{
    LinkBuilder linkFor(Class<?> type);
    LinkBuilder linkFor(Class<?> type, Object... parameters);
    LinkBuilder linkForSingleResource(Class<?> type, Object id);
    LinkBuilder linkForSingleResource(Identifiable<?> entity);
    Link linkToCollectionResource(Class<?> type);
    Link linkToSingleResource(Class<?> type, Object id);
    Link linkToSingleResource(Identifiable<?> entity);
}
```

ControllerLinkBuilder

As introduced, Spring HATEOAS provides the ControllerLinkBuilder utility, which allows the creation of links by pointing to controller classes:

正如所介绍的，Spring HATEOAS提供了ControllerLinkBuilder实用程序，它允许通过指向控制器类来创建链接：

```
resource.add(
    linkTo(
    methodOn(StockProductController.class)
    .getSeveral(null, null, index.getId(), null, null, null, null))
    .withRel(COMPONENTS)
);
```

As specified in the Spring HATEOAS reference, ControllerLinkBuilder uses Spring's ServletUriComponentsBuilder under the hood to obtain the basic URI information from the current request.

If our application runs at http://cloudstreetmarket/api, then the Framework builds Links on top of this root URI, appending it with the root controller mapping\(/indices\) and then with the subsequent method-handler specific path.

如Spring HATEOAS引用中所指定的，ControllerLinkBuilder在引擎下使用Spring的ServletUriComponentsBuilder从当前请求中获取基本的URI信息。

如果我们的应用程序在http：// cloudstreetmarket / api上运行，那么Framework在这个根URI之上构建Links，将它附加到根控制器映射（/ indexes），然后使用随后的方法处理程序特定路径。

## There's more…

### The use of regular expressions in @RequestMapping

In IndexController, StockProductController, ChartStockController, and ChartIndexController, the GET method-handlers to retrieve single resources have a special @RequestMapping definition.

Here is the IndexController's get\(\) method:

在@RequestMapping中使用正则表达式

在IndexController，StockProductController，ChartStockController和ChartIndexController中，检索单个资源的GET方法处理程序有一个特殊的@RequestMapping定义。

这里是IndexController的get\(\)方法：

```
@RequestMapping(value="/{index:[a-zA-Z0-9^.-]+}{extension:\\.[a-z]+}", method=GET)
public IndexResource get(@PathVariable(value="index") String indexId,
                @PathVariable(value="extension") String extension){
                
    return assembler.toResource(indexService.gather(indexId));
}
```

We ended up with this option because the Yahoo! Index codes appeared a bit more complex than simple strings. Especially considering the fact that these codes can carry one or more dots.

This situation caused Spring MVC not to be able to distinguish correctly the @PathVariable index from extension \(stripping them out half the way\).

Luckily, Spring MVC allows us to define URI template patterns with regular expressions. The syntax is {varName:regex}, where the first part defines the variable name and the second defines the regular expression.

我们最终得到这个选项，因为Yahoo！Index代码比简单的字符串显得有点复杂。 特别是考虑到这些代码可以携带一个或多个点的事实。

这种情况导致Spring MVC无法正确区分@PathVariable索引和扩展（剥离它们的一半）。

幸运的是，Spring MVC允许我们使用正则表达式定义URI模板模式。 语法是varName：regex，其中第一部分定义变量名称，第二部分定义正则表达式。

You will note the regular expression we defined for our indices:

The \[a-zA-Z0-9^.-\]+ expression, which specifically allows the ^ and . characters, is commonly used in the index code by Yahoo!

您将注意到我们为我们的索引定义的正则表达式：

\[a-zA-Z0-9^.-\]+ 表达式，其特别允许^和. 字符，通常在索引代码中使用由Yahoo!

## See also

* To know more about Spring HATEOAS, refer to http://docs.spring.io/springhateoas/docs/current/reference/html/.

* The introduced HATEOAS representation implements the **Hypertext Application Language \(HAL\)**. HAL is supported by Spring HATEOAS as the default rendering. Learn more about the HAL specification at https://tools.ietf.org/html/draftkelly-json-hal-06 and http://stateless.co/hal\_specification.html.

* 要了解有关Spring HATEOAS的更多信息，请参阅http//docs.spring.io/springhateoas/docs/current/reference/html/。

* 引入的HATEOAS表示实现了**Hypertext Application Language \(HAL\)**。  HAL由Spring HATEOAS支持作为默认渲染。 有关HAL规范的详细信息，请访问https//tools.ietf.org/html/draftkelly-json-hal-06和http//stateless.co/hal\_specification.html。



