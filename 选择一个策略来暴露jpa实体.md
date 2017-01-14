The content object\(s\) exposed in resources are JPA Entities. The interesting point about wrapping a JPA Entity in a resource comes with the low-level nature of an Entity itself, which supposedly represents a restricted identifiable domain area. This definition should ideally be entirely translated to the exposed REST resources.

So, how do we represent an Entity in REST HATEOAS? How do we safely and uniformly represent the JPA associations?This recipe presents a simple and conservative method to answer these questions.

在资源中公开的内容对象是JPA实体。 在资源中包装JPA实体的有趣的一点是实体本身的低级本质，它本应代表一个受限的可识别域区域。 理想情况下，此定义应完全转换为公开的REST资源。

那么，我们如何在REST HATEOAS中表示一个实体？ 我们如何安全，统一地代表JPA协会？这个谱方提出了一个简单和保守的方法来回答这些问题。

## How to do it…

1. We have presented one entity used as a resource \(Index.java\). Here is another entity that is used: Exchange.java. This entity presents a similar strategy to expose its JPA associations:

1.我们提出了一个用作资源的实体（Index.java）。 这里是另一个使用的实体：Exchange.java。 这个实体提出了一个类似的策略来公开其JPA关联：

```
import edu.zc.csm.core.converters.IdentifiableSerializer;
import edu.zc.csm.core.converters.
IdentifiableToIdConverter;
@Entity
public class Exchange extends ProvidedId<String> {

    private String name;
    
    @ManyToOne(fetch = FetchType.EAGER)
    @JoinColumn(name = "market_id", nullable=true)
    @JsonSerialize(using=IdentifiableSerializer.class)
    @JsonProperty("marketId")
    @XStreamConverter(value=IdentifiableToIdConverter.class,strings={"id"})
    @XStreamAlias("marketId")
    private Market market;
    
    @OneToMany(mappedBy = "exchange", cascade = CascadeType.ALL, fetch=FetchType.LAZY)
    @JsonIgnore
    @XStreamOmitField
    private Set<Index> indices = new LinkedHashSet<>();
    
    @OneToMany(mappedBy = "exchange", cascade = CascadeType.ALL, fetch=FetchType.LAZY)
    @JsonIgnore
    @XStreamOmitField
    private Set<StockProduct> stocks = new LinkedHashSet<>();
    
    public Exchange(){}
    
    public Exchange(String exchange) {
        setId(exchange);
    }
    
    //getters & setters
    
    @Override
    public String toString() {
        return "Exchange [name=" + name + ", market=" + market + ", id=" + id+ "]";
    }
}
```

2. The Exchange.java Entity references two custom utility classes that are used to transform the way external Entities are fetched as part of the main entity rendering\(JSON or XML\). Those utility classes are the following IdentifiableSerializer and the IdentifiableToIdConverter:

* [ ]  The IdentifiableSerializer class is used for JSON marshalling:

2.Exchange.java实体引用两个自定义实用程序类，用于转换外部实体作为主实体呈现（JSON或XML）的一部分提取的方式。 这些实用程序类是以下IdentifiableSerializer和IdentifiableToIdConverter：

* [ ] IdentifiableSerializer类用于JSON编组：

```
import org.springframework.hateoas.Identifiable;
import com.fasterxml.jackson.core.JsonGenerator;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.JsonSerializer;
import com.fasterxml.jackson.databind.SerializerProvider;
public class IdentifiableSerializer extends JsonSerializer<Identifiable<?>> {
    @Override
    public void serialize(Identifiable<?> value, JsonGenerator jgen, SerializerProvider provider) throws IOException,
            JsonProcessingException {
            
        provider.defaultSerializeValue(value.getId(), jgen);
    }
}
```

* [ ] The IdentifiableToIdConverter class is used for XML marshlling and is built with XStream dependencies:

IdentifiableToIdConverter类用于XML编组，并使用XStream依赖项构建：

```
import com.thoughtworks.xstream.converters.Converter;
public class IdentifiableToIdConverter implements Converter{

    private final Class <Identifiable<?>> type;
    
    public IdentifiableToIdConverter(final Class<Identifiable<?>> type, final Mapper mapper, 
            final ReflectionProvider reflectionProvider, final ConverterLookup lookup, final String valueFieldName) {
            
        this(type, mapper, reflectionProvider, lookup,valueFieldName, null);
    }
    
    public IdentifiableToIdConverter(final Class<Identifiable<?>> type, final Mapper mapper, 
            final ReflectionProvider reflectionProvider, final ConverterLookup lookup, 
            final String valueFieldName,Class valueDefinedIn) {
            
        this.type = type;
        Field field = null;
        
        try {
            field = (valueDefinedIn != null? valueDefinedIn : type.getSuperclass()).getDeclaredField("id");
            if (!field.isAccessible()) {
                field.setAccessible(true);
            }
        } catch (NoSuchFieldException e) {
            throw new IllegalArgumentException(e.getMessage()+": "+valueFieldName);
        }
    }
    
    public boolean canConvert(final Class type) {
        return type.isAssignableFrom(this.type);
    }
    
    public void marshal(final Object source, final HierarchicalStreamWriter writer,final MarshallingContext context) {
        if(source instanceof Identifiable){
            writer.setValue(((Identifiable<?>)source).getId().toString());
        }
    }
    
    public Object unmarshal(final HierarchicalStreamReader reader, final UnmarshallingContext context) {
        return null;
    }
}
```

## How it works...

Let's understand how this strategy works.

让我们来了解这个策略的工作原理。

### The REST CRUD principle

One REST architectural constraint is to present a uniform interface. A uniform interface is achieved by exposing resources from endpoints that can all be targeted from different HTTP methods \(if applicable\).

Resources can also be exposed under several representations \(json, xml, and so on\), and information or error messages must be self-descriptive. The implementation of HATEOAS provides a great bonus for the self-explanatory character of an API.

REST CRUD原则

一个REST架构约束是提供一个统一的接口。 通过可以从不同HTTP方法（如果适用）定向的端点公开资源来实现统一接口。

资源也可以在几种表示（json，xml等）下公开，并且信息或错误消息必须是自描述的。  HATEOAS的实现为API的自解释特性提供了极大的好处。

In REST, the more intuitive and inferable things are, the better. From this perspective, as a web/UI developer, I should be able to assume the following:

* The structure of the object I receive from the GET call on an endpoint will be the expected structure that I have to send back with a PUT call \(the edition of the object\)

* Similarly, the same structure should be used for the creation of a new object\(the POST method\)

This consistency of payload structures among different HTTP methods is a SOLID and conservative argument that is used when it is time to defend the API interests. It's pretty much always the time to defend the API interests.

在REST中，更直观和可推断的事情，越好。 从这个角度来看，作为一个web / UI开发者，我应该能够假设：

* 从端点上的GET调用接收的对象的结构将是我必须使用PUT调用（对象的版本）发送回来的预期结构，

* 同样，应该使用相同的结构来创建一个新对象（PO​​ST方法）

不同HTTP方法之间的有效载荷结构的一致性是一个SOLID和保守的参数，在保护API利益的时候使用。 几乎总是保护API利益的时候。

### Exposing the minimum

Exposing the minimum amount of information has been the core idea during the refactoring for this chapter. It's usually a great way to ensure that one endpoint won't be used to expose information data that would be external to the initial controller.

A JPA Entity can have associations to other Entities \(@OneToOne, @OneToMany, @ManyToOne,or @ManyToMany\).

Some of these associations have been annotated with @JsonIgnore \(and @XStreamOmitField\), and some other associations have been annotated with @JsonSerialize and @JsonProperty \(and @XStreamConverter and @XStreamAlias\).

公开最小值

公开最小量的信息一直是本章重构期间的核心思想。 这通常是确保一个端点不会用于暴露初始控制器外部的信息数据的好方法。

JPA实体可以具有与其他实体\(`@OneToOne`, `@OneToMany`, `@ManyToOne`,or `@ManyToMany`\)的关联。

这些关联中的一些已经用`@JsonIgnore`（和`@XStreamOmitField`）注释，并且一些其他关联已经用`@JsonSerialize`和`@JsonProperty`（和`@XStreamConverter`和@`XStreamAlias`）注释。

### If the Entity doesn't own the relationship

In this situation, the database table of the Entity doesn't have a foreign key to the table of the targeted second Entity.

The strategy here is to completely ignore the relationship in REST to reflect the database state.

The ignore instructions depend on the supported representations and the chosen implementations.

For json, we are using Jackson, the solution has been: @JsonIgnore.

For xml, we are using XStream, the solution has been: @XstreamOmitField.

如果实体不拥有该关系

在这种情况下，实体的数据库表不具有针对目标第二实体的表的外键。

这里的策略是完全忽略REST中的关系以反映数据库状态。

忽略指令取决于支持的表示和所选择的实现。

对于json，我们使用的是Jackson，解决方案是：`@JsonIgnore`。

对于xml，我们使用XStream，解决方案是：`@XstreamOmitField`。

If the Entity owns the relationship

Here, the database table of the Entity has a foreign key the table of the targeted second Entity.

If we plan to update an entity of this table, which depends on an entity of the other table, we will have to provide this foreign key for the entity.

The idea then is to expose this foreign key as a dedicated field just as all the other columns of the database table. Again, the solution to implement this depends on the supported representations and the configured marshallers.

For json and Jackson, we have done it with the following code snippet:

如果实体拥有关系

这里，实体的数据库表有一个外键的目标第二Entity的表。

如果我们计划更新这个表的实体，这取决于另一个表的实体，我们将必须为实体提供这个外键。

然后想法是将此外键作为专用字段公开，就像数据库表的所有其他列。 同样，实现此的解决方案取决于支持的表示和配置的编组。

对于json和Jackson，我们使用下面的代码片段：

```
@JsonSerialize(using=IdentifiableSerializer.class)
@JsonProperty("marketId")
```

As you can see, we rename the attribute to suggest that we are presenting \(and expecting\) an ID. We have created the IdentifiableSerializer class that extracts the ID from the entity\(from the Identifiable interface\) and places only this ID into the value of the attribute.

For xml and XStream, it has been:

正如你所看到的，我们重命名属性以建议我们呈现（和期望）一个ID。 我们创建了IdentifiableSerializer类，从实体中提取ID（从Identifiable接口），并且只将此ID放在属性的值中。

对于xml和XStream，它一直：

```
@XStreamConverter(value=IdentifiableToIdConverter.class, strings={"id"})
@XStreamAlias("marketId")
```

In the same way, we rename the attribute to suggest that we are presenting an ID, and we target the custom converter IdentifiableToIdConverter that also chooses only the ID of the Entity as a value for the attribute.

Here is an example of the xml representation example of the ^AMBAPT index:

以同样的方式，我们重命名属性以建议我们提供一个ID，并且我们定制自定义转换器IdentifiableToIdConverter，它也只选择实体的ID作为属性的值。

下面是^ AMBAPT索引的xml表示示例的示例：

![](/assets/111.png)

### Separation of resources

This strategy promotes a clear separation between resources. The displayed fields for each resource match the database schema entirely. This is a standard practice in web developments to keep the HTTP request payload unchanged for the different HTTP methods.

When HATEOAS is adopted, we should then fully encourage the use of links to access related entities instead of nested views.

The previous recipe Building links for a Hypermedia-Driven API features examples to access\(using links\) the Entities that are associated with `@...ToOne `and `@...ToMany`. Below is an example of these links in an exposed Entity as it is achieved in the previous recipe:

资源分离

这一战略促进资源之间的明确分离。 每个资源的显示字段完全匹配数据库模式。 这是Web开发中的一种标准做法，用于为不同的HTTP方法保持HTTP请求有效载荷不变。

当采用HATEOAS时，我们应该充分鼓励使用链接访问相关实体，而不是嵌套视图。

上一个配方构建Hypermedia-Driven API的链接提供了访问（使用链接）与`@ ... ToOne`和`@ ... ToMany`相关联的实体的示例。 下面是公开的实体中的这些链接的示例，如在先前的配方中实现的：

![](/assets/112.png)

## There's more…

We detail here official sources of information for the implemented marshallers.

我们在这里详细介绍了实施编组人员的官方信息来源。

### Jackson custom serializers

You can find the official wiki page guide for these serializers at:

您可以在以下位置找到这些序列化程序的官方wiki页面指南：

http://wiki.fasterxml.com/JacksonHowToCustomSerializers

### XStream converters

XStream has been migrated from codehaus.org to Github. To follow an official tutorial about XStream converters, go to:

XStream已从codehaus.org迁移到Github。 要遵循有关XStream转换器的官方教程，请访问：

http://x-stream.github.io/converter-tutorial.html



