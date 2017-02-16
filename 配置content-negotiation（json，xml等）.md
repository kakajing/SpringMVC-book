In this recipe, we will see how to configure the way we want the system to decide which format to render depending upon the client expectations.

在这个配方中，我们将看到如何配置我们希望系统根据客户期望决定呈现哪种格式的方式。

## Getting ready

We are mostly going to review the XML configuration here. Then, we will test the API with different requests to ensure support is provided to the XML format.

我们主要将在这里审查XML配置。 然后，我们将使用不同的请求测试API，以确保为XML格式提供支持。

## How to do it...

1.The RequestMappingHandlerAdapter configuration has been altered in dispatcher-context.xml. A contentNegotiationManager property has been added, as well as an xmlConverter bean:

1.在dispatcher-context.xml中更改了RequestMappingHandlerAdapter配置。 添加了一个contentNegotiationManager属性，以及一个xmlConverter bean：

```js
<bean class="org.sfw.web...method.annotation.RequestMappingHandlerAdapter">
    <property name="messageConverters">
        <list>
            <ref bean="xmlConverter"/>
            <ref bean="jsonConverter"/>
        </list>
    </property>
    <property name="customArgumentResolvers">
        <list>
            <bean class="net.kaczmarzyk.spring.data.jpa.web.SpecificationArgumentResolver"/>
            <bean class="org.sfw.data.web.PageableHandlerMethodArgumentResolver">
                <property name="pageParameterName" value="pn"/>
                <property name="sizeParameterName" value="ps"/>
            </bean>
        </list>
    </property>
    <property name="requireSession" value="false"/>
    <property name="contentNegotiationManager" ref="contentNegotiationManager"/>
</bean>

<bean id="contentNegotiationManager" class="org.sfw.web.accept.ContentNegotiationManagerFactoryBean">
    <property name="favorPathExtension" value="true" />
    <property name="favorParameter" value="false" />
    <property name="ignoreAcceptHeader" value="false"/>
    <property name="parameterName" value="format" />
    <property name="useJaf" value="false"/>
    <property name="defaultContentType" value="application/json" />
    <property name="mediaTypes">
        <map>
            <entry key="json" value="application/json" />
            <entry key="xml" value="application/xml" />
        </map>
    </property>
</bean>

<bean id="xmlConverter" class="org.sfw.http...xml.MarshallingHttpMessageConverter">
    <property name="marshaller">
        <ref bean="xStreamMarshaller"/>
    </property>
    <property name="unmarshaller">
        <ref bean="xStreamMarshaller"/>
    </property>
</bean>

<bean id="xStreamMarshaller" class="org.springframework.oxm.xstream.XStreamMarshaller">
    <property name="autodetectAnnotations" value="true"/>
</bean>
```

2.A Maven dependency has been added to XStream as follows:

2.XStream中添加了一个Maven依赖项，如下所示：

```js
<dependency>
    <groupId>com.thoughtworks.xstream</groupId>
    <artifactId>xstream</artifactId>
    <version>1.4.3</version>
</dependency>
```

3.Calling the URL: [http://localhost:8080/api/indices/EUROPE/^GDAXI/histo.json](http://localhost:8080/api/indices/EUROPE/^GDAXI/histo.json) should target the `getHistoIndex()` handler the same way as before and you should receive the same json response:

3.调用URL：[http://localhost:8080/api/indices/EUROPE/^GDAXI/histo.json](http://localhost:8080/api/indices/EUROPE/^GDAXI/histo.json) 应该像以前一样以`getHistoIndex()`处理程序为目标，你应该收到相同的json响应：

![](/assets/63.png)

4.Also, calling the URL [http://localhost:8080/api/indices/EUROPE/^GDAXI/histo.xml](http://localhost:8080/api/indices/EUROPE/^GDAXI/histo.xml) should now generate the following XML formatted response:

4.此外，调用URL [http://localhost:8080/api/indices/EUROPE/^GDAXI/histo.xml ](http://localhost:8080/api/indices/EUROPE/^GDAXI/histo.xml现在应该生成以下XML格式化响应：) 现在应该生成以下XML格式化响应：

![](/assets/64.png)

## How it works...

We have added support for XML using the MarshallingHttpMessageConverter bean, defined a default media type \(application/json\), and defined a global content negotiation strategy.

我们使用MarshallingHttpMessageConverter bean添加了对XML的支持，定义了默认媒体类型（application / json），并定义了全局内容协商策略。

### Support for XML marshalling

As we said in the previous recipe, MarshallingHttpMessageConverter comes with the framework, but it requires the spring-oxm dependency, as well as a definition for a marshaller and unmarshaller. spring-oxm is the Maven artefact to reference here:

支持XML编组

正如我们在前面的配方中所说的，MarshallingHttpMessageConverter自带了框架，但它需要spring-oxm依赖，以及marshaller和unmarshaller的定义。  spring-oxm是Maven的参考文献：

```js
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-oxm</artifactId>
    <version>${spring.version}</version>
</dependency>
```

### The XStream marshaller

We chose XStreamMarshaller as the provider for the XML marshalling operations:

我们选择XStreamMarshaller作为XML编组操作的提供者：

```js
<bean class="org.springframework.oxm.xstream.XStreamMarshaller">
    <property name="autodetectAnnotations" value="true"/>
</bean>
```

The XStream marshaller is part of the spring-oxm project. Even if it is not recommended for external source parsing \(which is not what we intend to do\), it is very good and requires very few configuration by default \(no specific class registration or initial mapping strategy required\).

Types and fields can be annotated to customize the default behavior. You can find some examples here from their documentation:

* `@XStreamAlias`: Used on the type, field, or attribute

* `@XStreamImplicit`: Used in collections or arrays

* `@XStreamAsAttribute`: Used to mark a field as an attribute

* `@XStreamConverter`: Targets a specific converter for the field

XStream marshaller是spring-oxm项目的一部分。 即使不推荐用于外部源解析（这不是我们打算做的），它是非常好的，需要很少的配置默认情况下（没有特定的类注册或初始映射策略需要）。

可以注解类型和字段以自定义默认行为。 你可以从他们的文档中找到一些例子：

* `@XStreamAlias`：用于类型，字段或属性

* `@XStreamImplicit`：用于集合或数组

* `@XStreamAsAttribute：`用于将字段标记为属性

* `@XStreamConverter`：针对字段的特定转换器

In our case, we have applied a minimal marshalling customization in DTOs.

You can find more information about XStream on their official website: [http://xstream.codehaus.org](http://xstream.codehaus.org).

在我们的例子中，我们在DTO中应用了最小的编组定制。

你可以在他们的官方网站上找到更多关于XStream的信息：http//xstream.codehaus.org。

### Negotiation strategies with ContentNegotiationManager

Here, we are talking about the way we configure the system to choose one media type over another, for responses. The client shows expectations in its request and the server tries to satisfy them at best from the available resolutions.

There are three ways for the client to specify its media type expectations. We discuss them in the following sections.

与ContentNegotiationManager的协商策略

在这里，我们正在谈论我们如何配置系统以选择一种媒体类型而不是另一种媒体类型，用于响应。 客户端在其请求中显示期望，服务器尝试从可用的分辨率中最好地满足它们。

客户端有三种方式指定其媒体类型预期。 我们将在以下部分讨论它们。

### The Accept header

The client request specifies a mime type or a list of mime types \(application/json, application/xml, and so on\) as a value of the Accept header. It is the default choice for Spring MVC.

Web browsers can send various Accept headers though, and it would be risky to rely entirely on these headers. Therefore, it is good to support at least one alternative.

These headers can even be completely ignored with the ignoreAcceptHeader Boolean property in ContentNegotiationManager.

客户端请求指定mime类型或mime类型的列表（application / json，application / xml等）作为Accept头的值。 它是Spring MVC的默认选择。

Web浏览器可以发送各种Accept标头，完全依赖这些头是很危险的。 因此，支持至少一个替代方案是好的。

这些头甚至可以被ContentNegotiationManager中的ignoreAcceptHeader Boolean属性完全忽略。

### The file extension suffix in the URL path

Allowing the specification of a file extension suffix in the URL path is one alternative. It is the discriminator option in our configuration.

The favorPathExtension Boolean property in ContentNegotiationManager has been set to true for this purpose and our AngularJS factories actually request .json paths.

URL路径中的文件扩展名后缀

允许在URL路径中指定文件扩展名后缀是一种替代方法。 它是我们配置中的discriminator选项。

为此，ContentNegotiationManager中的favorPathExtension Boolean属性已设置为true，并且我们的AngularJS工厂实际请求.json路径。

### The request parameter

You can define a specific query parameter if you dislike the path extension option. The default name of this parameter is format. It is customizable with the parameterName property, and the potential expected values are the registered format suffixes \(xml, html, json, csv, and so on\).

This option can be set as the discriminator option with the favorParameter Boolean property.

如果你不喜欢路径扩展选项，则可以定义特定的查询参数。 此参数的默认名称为format。 它可以使用parameterName属性自定义，潜在的期望值是注册的格式后缀（xml，html，json，csv等）。

此选项可以使用favorParameter Boolean属性设置为discriminator选项。

### Java Activation Framework

Setting the useJaf Boolean property to true configures to rely on the Java Activation Framework, rather than Spring MVC itself, for the suffix to-media type mappings \(json to correspond to application/json, xml to correspond to application/xml, and so on\).

Java激活框架

将useJaf Boolean属性设置为true配置为依赖于Java Activation Framework，而不是Spring MVC本身，用于后缀到介质类型映射（json对应于application / json，xml对应于application / xml，等等 ）。

### `@RequestMapping` annotations as ultimate filters

Finally, the controller with the `@RequestMapping` annotations and especially the producesattribute should have the final word on which format will be rendered.

`@RequestMapping`注解作为最终过滤器

最后，带有`@RequestMapping`注解的控制器，特别是produce属性，应该有最后一个字，它将被渲染的格式。

## There's more...

Now we will look at the implementation of JAXB2 as an XML parser and the ContentNegotiationManagerFactoryBean configuration.

现在我们将把JAXB2的实现看作一个XML解析器和ContentNegotiationManagerFactoryBean配置。

### Using a JAXB2 implementation as an XML parser

JAXB2 is the current Java specification for XML bindings. Our example with XStream was just an example and another XML marshaller can of course be used. Spring supports JAXB2. It even provides a default JAXB2 implementation in the spring-oxm package: org. springframework.oxm.jaxb.Jaxb2Marshaller.

Using JAXB2 annotations in DTOs is probably a better choice for portability. Visit the Jaxb2Marshaller JavaDoc for more details about its configuration: [http://docs.spring.io/autorepo/docs/spring/4.0.4.RELEASE/javadoc-api/org/springframework/oxm/jaxb/Jaxb2Marshaller.html](http://docs.spring.io/autorepo/docs/spring/4.0.4.RELEASE/javadoc-api/org/springframework/oxm/jaxb/Jaxb2Marshaller.html).

使用JAXB2实现作为XML解析器

JAXB2是当前用于XML绑定的Java规范。 我们使用XStream的例子只是一个例子，当然可以使用另一个XML编组器。  Spring支持JAXB2。 它甚至在spring-oxm软件包中提供了一个默认的JAXB2实现：org.SpringFramework.oxm.jaxb.Jaxb2Marshaller。

在DTO中使用JAXB2注注解可能是可移植性的更好选择。 有关其配置的更多详细信息，请访问Jaxb2Marshaller JavaDoc：http//docs.spring.io/autorepo/docs/spring/4.0.4.RELEASE/javadoc-api/org/springframework/oxm/jaxb/Jaxb2Marshaller.html。

### The ContentNegotiationManagerFactoryBean JavaDoc

The full possible configuration for ContentNegotiationManagerFactoryBean is accessible again in its JavaDoc:

[http://docs.spring.io/spring/docs/current/javadoc-api/org/](http://docs.spring.io/spring/docs/current/javadoc-api/org/)springframework/web/accept/ContentNegotiationManagerFactoryBean.html

ContentNegotiationManagerFactoryBean的完整可能配置可在其JavaDoc中再次访问：http：//docs.spring.io/spring/docs/current/javadoc-api/org/springframework /web/accept/ContentNegotiationManagerFactoryBean.html

