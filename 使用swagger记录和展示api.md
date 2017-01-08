This section details how to provide and expose metadata about the REST API using Swagger.

本节详细介绍如何使用Swagger提供和公开有关REST API的元数据。

## Getting ready

We are often required to document APIs for users and customers. When documenting an API,depending on the tools we use, we often get a few extras such as the ability to generate client code from the API metadata or even the generation of integrated test harnesses for the API.

There isn't yet a recognized and universal standard for the format of the API metadata. This lack of standards leads to a quite a few different solutions on the market for the REST documentation.

We have chosen Swagger here because it has the largest and the most active community. It has existed since 2011, and it offers a very nice UI/test harness and great configuration by default.

我们经常需要为用户和客户记录API。 在记录API时，根据我们使用的工具，我们经常会得到一些额外的功能，例如能够从API元数据生成客户端代码，甚至生成API的集成测试工具。

对于API元数据的格式，还没有公认的通用标准。 这种标准的缺乏导致了REST文档市场上的一些不同的解决方案。

我们在这里选择了Swagger，因为它拥有最大和最活跃的社区。 它自2011年以来一直存在，它提供了一个非常漂亮的UI /测试工具和伟大的配置默认情况下。

## How to do it...

This section details what can be done and also what we have done in the code base of the checked-out v4.x.x branch.

这一节详细介绍了可以做什么，以及我们在签出v4.x.x分支的代码库中做了什么。

1. We have added a Maven dependency for the swagger-springmvc project \(version 0.9.5\) to cloudstreetmarket-core and cloudstreetmarket-parent:

1.我们为swagger-springmvc项目（版本0.9.5）添加了一个Maven依赖项到cloudstreetmarket-core和cloudstreetmarket-parent：

```
<dependency>
    <groupId>com.mangofactory</groupId>
    <artifactId>swagger-springmvc</artifactId>
    <version>${swagger-springmvc.version}</version>
</dependency>
```

1. The following swagger configuration class has been created:

2.创建了以下swagger配置类：

```
@Configuration
@EnableSwagger //Loads the beans required by the framework
public class SwaggerConfig {

    private SpringSwaggerConfig springSwaggerConfig;

    @Autowired
    public void setSpringSwaggerConfig(SpringSwaggerConfig springSwaggerConfig) {
        this.springSwaggerConfig = springSwaggerConfig;
    }

    @Bean
    public SwaggerSpringMvcPlugin customImplementation(){
        return new SwaggerSpringMvcPlugin(this.springSwaggerConfig)
                    .includePatterns(".*")
                    .apiInfo(new ApiInfo("Cloudstreet Market / Swagger UI",
                        "The Rest API developed with Spring MVC Cookbook [PACKT]",
                        "",
                        "alex.bretet@gmail.com",
                        "LGPL",
                        "http://www.gnu.org/licenses/gpl-3.0.en.html"
        ));
    }
}
```

1. The following configuration has been added to the dispatch-context.xml:

3.以下配置已添加到dispatch-context.xml中：

```
<bean class="com.mangofactory.swagger.configuration.SpringSwaggerConfig"/>
<bean class="edu.zc.csm.api.swagger.SwaggerConfig"/>
<context:property-placeholder location="classpath*:/METAINF/properties/swagger.properties" />
```

1. As per the previous configuration, a swagger.properties file has been added at the path src/main/resources/META-INF/properties with the content:

4.根据先前的配置，在路径src / main / resources / META-INF / properties中添加了一个swagger.properties文件，其内容如下：

```
documentation.services.version=1.0
documentation.services.basePath=http://localhost:8080/api
```

1. Our three controllers have been added a basic documentation. See the following documentation annotations added to IndexController:

5.我们的三个控制器已经添加了一个基本文档。 请参阅添加到IndexController的以下文档注释：

```
@Api(value = "indices", description = "Financial indices")
@RestController
@RequestMapping(value="/indices",produces={"application/xml", "application/json"})
public class IndexController extends CloudstreetApiWCI {

    @RequestMapping(method=GET)
    @ApiOperation(value = "Get overviews of indices", notes = "Return a page of index-overviews")
    public Page<IndexOverviewDTO> getIndices(@ApiIgnore @PageableDefault(size=10, page=0, sort={"dailyLatestValue"}, 
                    direction=Direction.DESC) Pageable pageable){

        return marketService.getLastDayIndicesOverview(pageable);
    }

    @RequestMapping(value="/{market}", method=GET)
    @ApiOperation(value = "Get overviews of indices filtered by market", notes = "Return a page of index-overviews")
    public Page<IndexOverviewDTO> getIndicesPerMarket(@PathVariable MarketCode market,
                @ApiIgnore
                @PageableDefault(size=10, page=0,sort={"dailyLatestValue"}, direction=Direction.DESC)Pageable pageable){
                    return marketService.getLastDayIndicesOverview(market, pageable);
    }

    @RequestMapping(value="/{market}/{index}/histo",method=GET)
    @ApiOperation(value = "Get historical-data for one index", notes = "Return a set of historical-data from one index")
    public HistoProductDTO getHistoIndex(@PathVariable("market") MarketCode market,
                    @ApiParam(value="Index code: ^OEX")
                    @PathVariable("index") String indexCode,
                    @ApiParam(value="Start date: 2014-01-01")
                    @RequestParam(value="fd",defaultValue="") Date fromDate,
                    @ApiParam(value="End date: 2020-12-12")
                    @RequestParam(value="td",defaultValue="") Date toDate,
                    @ApiParam(value="Period between snapshots")
                    @RequestParam(value="i",defaultValue="MINUTE_30") QuotesInterval interval){

        return marketService.getHistoIndex(indexCode, market, fromDate, toDate, interval);
    }
}
```

1. We have downloaded the swagger UI project from [https://github.com/swagger-api/swagger-ui.This](https://github.com/swagger-api/swagger-ui.This) is a collection of static files \(JS, CSS, HTML, and pictures\). It has been pasted in the webapp directory of our cloudstreetmarket-api project.

6.我们从[https://github.com/swagger-api/swagger-ui下载了swagger](https://github.com/swagger-api/swagger-ui下载了swagger) UI项目。这是一个静态文件（JS，CSS，HTML和图片）的集合。 它已粘贴在cloudstreetmarket-api项目的webapp目录中。

1. Finally, the following mvc namespace configuration has been added to dispatch-context.xml again in order for the Spring MVC to open access to static files in the project:

7.最后，下面的mvc命名空间配置已被添加到dispatch-context.xml中，以便Spring MVC打开对项目中的静态文件的访问权限：

```
<!-- Serve static content-->
<mvc:default-servlet-handler/>
```

1. When we have this configuration, accessing the following URL on the server [http://localhost:8080/api/index.html](http://localhost:8080/api/index.html) brings up the Swagger UI documentation portal:

8.当我们进行此配置时，访问服务器[http://localhost:8080/api/index.html上的以下URL，将显示Swagger](http://localhost:8080/api/index.html上的以下URL，将显示Swagger) UI文档门户：

![](/assets/74.png)

More than just a REST documentation repository, it is also a handy test harness:

不仅仅是一个REST文档存储库，它也是一个方便的测试工具：

![](/assets/75.png)

## How it works...

Swagger has its own controller that publishes the metadata of our API. The Swagger UI targets this metadata, parses it, and represents it as a usable interface.

Swagger有自己的控制器来发布我们的API的元数据。  Swagger UI定位此元数据，对其进行解析，并将其表示为可用界面。

### An exposed metadata

On the server side, with the com.mangofactory/swagger-springmvc dependency added to the swagger-springmvc project and with the presented SwaggerConfig class, the library creates a controller on the root path: /api-docs and publishes the entire metadata there for the REST API.

If you visit [http://localhost:8080/api/api-docs](http://localhost:8080/api/api-docs), you will reach the root of our REST API documentation:

暴露的元数据

在服务器端，将com.mangofactory / swagger-springmvc从属项添加到swagger-springmvc项目中并使用所呈现的SwaggerConfig类，库在根路径上创建一个控制器：/ api-docs并发布整个元数据 REST API。

如果您访问http：// localhost：8080 / api / api-docs，您将访问我们的REST API文档的根目录：

![](/assets/76.png)

This content is the exposed metadata that implements the Swagger specification. The metadata is a navigable structure. Links to other parts of the metadata can be found in the `<path>` nodes of the XML content.

此内容是实现Swagger规范的公开元数据。 元数据是可导航结构。 到元数据的其他部分的链接可以在XML内容的`<path>`节点中找到。

### The Swagger UI

The Swagger UI is only made of static files \(CSS, HTML, JavaScript, and so on\). The JavaScript logic implements the Swagger specification and recursively parses the entire exposed metadata. It then dynamically builds the API documentation website and test harness that we have presented, digging out every single endpoint and its metadata.

Swagger UI仅由静态文件（CSS，HTML，JavaScript等）构成。  JavaScript逻辑实现Swagger规范，并递归地解析整个暴露的元数据。 然后动态构建我们提供的API文档网站和测试工具，挖掘每个单个端点及其元数据。

## There's more...

In this section, we suggest you to look further into Swagger and its Spring MVC project implementation.

在本节中，我们建议您进一步了解Swagger及其Spring MVC项目实施。

### The Swagger.io

Visit the framework's website and its specification: [http://swagger.io](http://swagger.io).

访问框架的网站及其规范：http//swagger.io。

### The swagger-springmvc documentation

The swagger-springmvc project is changing as it is becoming part of a bigger project named SpringFox. SpringFox now also supports the second version of the Swagger specification. We recommend you to visit their current reference document:

swagger-springmvc项目正在改变，因为它正成为一个名为SpringFox的更大项目的一部分。  SpringFox现在也支持第二个版本的Swagger规范。 我们建议您访问他们当前的参考文档：

[http://springfox.github.io/springfox/docs/current](http://springfox.github.io/springfox/docs/current)

They also provide a migration guide to move from the swagger specification 1.2 \(that we have implemented here\) to the swagger specification 2.0:

他们还提供了一个迁移指南，从swagger规范1.2（我们在这里实现）到swagger规范2.0：

[https://github.com/springfox/springfox/blob/master/docs/transitioning-to-v2.md](https://github.com/springfox/springfox/blob/master/docs/transitioning-to-v2.md)

## 

See also

This section guides you toward alternative tools and specification to Swagger:

本节指导您使用Swagger的替代工具和规范：

Different tools, different standards

We have mentioned that there isn't a common standard yet that would clearly legitimize one tool over another. Thus, it is probably good to acknowledge tools other than Swagger because things are moving quite fast in this domain. Here, you can find two great comparison articles:

不同的工具，不同的标准

我们提到，没有一个共同的标准，明显使一种工具相对于另一种工具合法化。 因此，感谢Swagger以外的工具可能是好的，因为在这个领域里，事情正在快速地进行。 在这里，你可以找到两个伟大的比较文章：

* [http://www.mikestowe.com/2014/07/raml-vs-swagger-vs-apiblueprint.php](http://www.mikestowe.com/2014/07/raml-vs-swagger-vs-apiblueprint.php)

* [http://apiux.com/2013/04/09/rest-metadata-formats](http://apiux.com/2013/04/09/rest-metadata-formats)



