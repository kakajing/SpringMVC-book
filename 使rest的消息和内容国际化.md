It was necessary to talk about validation before talking about internationalizing content and messages. With global and cloud-based services, supporting content in only one language is often not sufficient.

In this recipe, we provide an implementation that suits our design and therefore continue to meet our scalability standards for not relying on HTTP Sessions.

We will see how to define the MessageSource beans in charge of fetching the most suited message for a given location. We will see how to serialize resource properties to make them available to the frontend. We will implement a dynamic translation of content on this frontend with AngularJS and angular-translate.

在谈论国际化内容和消息之前，有必要谈论验证。 使用全球和基于云的服务，仅支持一种语言的内容通常是不够的。

在这个配方中，我们提供了一个适合我们设计的实现，因此继续满足我们的可扩展性标准，不依赖HTTP会话。

我们将看到如何定义MessageSource bean，负责获取给定位置的最适合的消息。 我们将看到如何序列化资源属性以使它们可用于前端。 我们将使用AngularJS和angular-translate实现这个前端的内容的动态翻译。

## How to do it…

There is both backend and a frontend work in this recipe.

在这个谱方中有后端和前端工作。

### Backend

1. The following bean has been registered in the core context \(csm-core-config.xml\):

后端

1.以下bean已在核心上下文（csm-core-config.xml）中注册：

```java
<bean id="messageBundle" class="edu.zc.csm.core.i18n.SerializableResourceBundleMessageSource">
    <property name="basenames" value="classpath:/METAINF/i18n/messages,classpath:/META-INF/i18n/errors"/>
    <property name="fileEncodings" value="UTF-8" />
    <property name="defaultEncoding" value="UTF-8" />
</bean>
```

2.This bean references a created SerializableResourceBundleMessageSource that gathers the resource files and extracts properties:

2.这个bean引用一个创建的SerializableResourceBundleMessageSource，它收集资源文件并提取属性：

```java
/**
* @author rvillars
* {@link https://github.com/rvillars/bookapp-rest}
*/
public class SerializableResourceBundleMessageSource extends ReloadableResourceBundleMessageSource {

    public Properties getAllProperties(Locale locale) {

        clearCacheIncludingAncestors();
        PropertiesHolder propertiesHolder = getMergedProperties(locale);
        Properties properties = propertiesHolder.getProperties();
        return properties;
    }
}
```

3.This bean bundle is accessed from two places:

A newly created PropertiesController exposes publicly \(serializing\) all the messages and errors for a specific location \(here, just a language\):

3.这个bean包从两个地方访问：

新创建的PropertiesController公开（序列化）特定位置（这里，只是一种语言）的所有消息和错误：

```java
@RestController
@ExposesResourceFor(Transaction.class)
@RequestMapping(value="/properties")
public class PropertiesController{
    @Autowired
    protected SerializableResourceBundleMessageSource messageBundle;

    @RequestMapping(method = RequestMethod.GET, produces={"application/json; charset=UTF-8"})
    @ResponseBody
    public Properties list(@RequestParam String lang) {
        return messageBundle.getAllProperties(new Locale(lang));
    }
}
```

A specific service layer has been built to easily serve messages and errors across controllers and services:

已经构建了特定的服务层，以便轻松地在控制器和服务之间提供消息和错误：

```java
@Service
@Transactional(readOnly = true)
public class ResourceBundleServiceImpl implements ResourceBundleService {

    @Autowired
    protected SerializableResourceBundleMessageSource messageBundle;

    private static final Map<Locale, Properties> localizedMap = new HashMap<>();

    @Override
    public Properties getAll() {
        return getBundleForUser();
    }

    @Override
    public String get(String key) {
        return getBundleForUser().getProperty(key);
    }

    @Override
    public String getFormatted(String key, String...arguments) {
        return MessageFormat.format(getBundleForUser().getProperty(key), arguments);
    }

    @Override
    public boolean containsKey(String key) {
        return getAll().containsKey(key);
    }

    private Properties getBundleForUser(){
        Locale locale = AuthenticationUtil.getUserPrincipal().getLocale();
        if(!localizedMap.containsKey(locale)){
            localizedMap.put(locale,messageBundle.getAllProperties(locale));
        }

        return localizedMap.get(locale);
    } 
}
```

> The ResourceBundleServiceImpl uses the same SerializableResourceBundleMessageSource for  
>  now. It also extracts the locale from the logged-in user \(Spring Security\) with a fallback to English.
>
> ResourceBundleServiceImpl现在使用相同的SerializableResourceBundleMessageSource。 它还从登录用户\(Spring Security\)中提取语言环境，并返回English。

4.This ResourceBundleServiceImpl service is injected in our WebContentInterceptor CloudstreetApiWCI:

4.这个ResourceBundleServiceImpl服务注入我们的WebContentInterceptor CloudstreetApiWCI：

```java
@Autowired
protected ResourceBundleService bundle;
```

5.In the TransactionController, for example, the bundle is targeted to extract error messages:

5.在TransactionController中，例如，bundle 包的目标是提取错误消息：

```java
if(!transaction.getUser().getUsername().equals(getPrincipal().getUsername())){
    throw new AccessDeniedException(bundle.get(I18nKeys.I18N_TRANSACTIONS_USER_FORBIDDEN));
}
```

6.I18nKeys is just a class that hosts resource keys as constants:

6.I18nKeys只是一个将资源键作为常量托管的类：

```java
public class I18nKeys {
    //Messages
    public static final String I18N_ACTION_REGISTERS = "webapp.action.feeds.action.registers";
    public static final String I18N_ACTION_BUYS = "webapp.action.feeds.action.buys";
    public static final String I18N_ACTION_SELLS = "webapp.action.feeds.action.sells";
    ...
}
```

7.The resource files are located in the core module:

7.资源文件位于核心模块中：

![](/assets/124.png)

### Frontend

1. Two dependencies for `angular-translate` have been added in the index.jsp:

前端

1.在index.jsp中添加了两个用于`angular-translate`的依赖关系：

```js
<script src="js/angular/angular-translate.min.js"></script>
<script src="js/angular/angular-translate-loader-url.min.js"></script>
```

2.The translate module is configured as follows in the index.jsp:

2.在index.jsp中translate 翻译模块如下：

```js
cloudStreetMarketApp.config(function ($translateProvider) {
    $translateProvider.useUrlLoader('/api/properties.json');
    $translateProvider.useStorage('UrlLanguageStorage');
    $translateProvider.preferredLanguage('en');
    $translateProvider.fallbackLanguage('en');
});
```

3.The user language is set from the main menu \(main\_menu.js\). The user is loaded and the language is extracted from user object \(defaulted to EN\):

3.从主菜单\(main\_menu.js\)设置用户语言。 用户被加载并且语言从用户对象（默认为EN）中提取：

```js
cloudStreetMarketApp.controller('menuController', function($scope, $translate, $location, modalService, httpAuth, genericAPIFactory) {
    $scope.init = function () {
        ...
        genericAPIFactory.get("/api/users/"+httpAuth.getLoggedInUser()+".json")
                    .success(function(data, status, headers, config) {
        $translate.use(data.language);
        $location.search('lang', data.language);
    });
    }
    ...
}
```

4.In the DOM, the i18n content is directly referenced to be translated through a translate directive. Check out in the stock-detail.html file for example:

4.在DOM中，i18n内容直接引用通过translate指令翻译。 例如签出stock-detail.html文件：

```js
<span translate="screen.stock.detail.will.remain">Will remain</span>
```

Another example from the index-detail.html file is the following:

index-detail.html文件的另一个示例如下：

```js
<td translate>screen.index.detail.table.prev.close</td>
```

In home.html, you can find scope variables whose values are translated as follows:

在home.html中，您可以找到其值按如下转换的作用域变量：

```js
{{value.userAction.presentTense | translate}}
```

5.In the application, update your personal preferences and set your language to **French **for example. Try to access, for example, a **stock-detail** page that can be reached from the **stock-search** results:

5.在应用程序中，更新您的个人首选项，并将您的语言设置为**法语**。 尝试访问例如**stock-search**结果可以访问的**stock-detail**页面：

![](/assets/125.png)

6.From a** stock-detail** page, you can process a transaction \(in French!\):

6.从**stock-detail**页面，您可以处理交易（法语！）：

![](/assets/126.png)

## How it works...

Let's have a look at the backend changes. What you first need to understand is the autowired SerializableResourceBundleMessageSource bean from which internationalized messages are extracted using a message key.

This bean extends a specific `MessageSource`implementation. Several types of `MessageSource` exist and it is important to understand the differences between them. We will revisit the way we extract a Locale from our users and we will see how it is possible to use a `LocaleResolver`to read or guess the user language based on different readability paths \(`Sessions`, `Cookies`, `Accept header`, and so on\).

让我们来看看后端的变化。 您首先需要了解的是自动连接的SerializableResourceBundleMessageSource bean，使用消息键从中提取国际化消息。

这个bean扩展了一个特定的`MessageSource`实现。 存在几种类型的`MessageSource`，了解它们之间的差异很重要。 我们将重新审视我们从用户中提取Locale的方式，我们将看到如何使用`LocaleResolver`根据不同的可读性路径\(`Sessions`, `Cookies`,`Accept header`等\)读取或猜测用户语言。

### MessageSource beans

First of all, a MessageSource is a Spring interface \(`org.sfw.context.MessageSource`\).The MessageSource objects are responsible for resolving messages from different arguments.

首先，MessageSource是一个Spring接口\(`org.sfw.context.MessageSource`\).SourceSource对象负责解析来自不同参数的messages。

The most interesting arguments being the key of the message we want and the Locale\(language/country combination\) that will drive the right language selection. If no Locale is provided or if the MessageSource fails to resolve a matching language/country file or message entry, it falls back to a more generic file and tries again until it reaches a successful resolution.

As shown here, `MessageSource`implementations expose only `getMessage(…)` methods

最有趣的参数是我们想要的message 的关键，以及将驱动正确的语言选择的语言\(language/country combination\)。 如果未提供区域设置或`MessageSource`无法解析匹配的语言/国家/地区文件或消息条目，则会返回到更通用的文件，并再次尝试，直到达到成功的解决方案。

如图所示，MessageSource实现仅暴露`getMessage(…)` 方法

```java
public interface MessageSource {
    String getMessage(String code, Object[] args, String defaultMessage, Locale locale);
    String getMessage(String code, Object[] args, Locale locale) throws NoSuchMessageException;
    String getMessage(MessageSourceResolvable resolvable, Locale locale) throws NoSuchMessageException;
}
```

This lightweight interface is implemented by several objects in Spring \(especially in context components\). However, we are looking specifically for MessageSource implementations and three of them in Spring 4+ particularly deserve to be mentioned.

这个轻量级接口由Spring中的几个对象实现（特别是在context components中）。 但是，我们正在寻找`MessageSource`实现，其中三个在Spring 4+特别值得一提。

### ResourceBundleMessageSource

This `MessageSource`implementation accesses the resource bundles using specified basenames. It relies on the underlying JDK's `ResourceBundle`implementation, in combination with the JDK's standard message-parsing provided by `MessageFormat` \(`java.text.MessageFormat`\).

Both the accessed `ResourceBundle` instances and the generated `MessageFormat` are cached for each message. The caching provided by ResourceBundleMessageSource is significantly faster than the built-in caching of the`java.util.ResourceBundle` class.

With `java.util.ResourceBundle`, it's not possible to reload a bundle when the JVM is running. Because ResourceBundleMessageSource relies on `ResourceBundle`, it faces the same limitation.

此`MessageSource`实现使用指定的basenames访问资源包。 它依赖于底层JDK的`ResourceBundle`实现，以及由`MessageFormat`\(`java.text.MessageFormat`\)提供的JDK的标准消息解析。

对于每个message，访问的`ResourceBundle`实例和生成的`MessageFormat`都被缓存。  ResourceBundleMessageSource提供的缓存比`java.util.ResourceBundle`类的内置缓存快得多。

使用`java.util.ResourceBundle`，当JVM运行时，不可能重新加载bundle。 因为ResourceBundleMessageSource依赖于`ResourceBundle`，它面临着同样的限制。

### ReloadableResourceBundleMessageSource

In contrast to ResourceBundleMessageSource, this class uses `Properties` instances as custom data structure for messages. It loads them via a PropertiesPersister strategy using Spring Resource objects.

This strategy is not only capable of reloading files based on timestamp changes, but also loads properties files with a specific character encoding.

ReloadableResourceBundleMessageSource supports reloading of properties files using the cacheSeconds setting and also supports the programmatic clearing of the properties cache.

与ResourceBundleMessageSource相反，此类使用`Properties`实例作为消息的自定义数据结构。 它使用Spring Resource对象通过PropertiesPersister策略加载它们。

此策略不仅能够基于时间戳更改重新加载文件，还可以加载具有特定字符编码的属性文件。

ReloadableResourceBundleMessageSource支持使用cacheSeconds设置重新加载属性文件，并且还支持对属性缓存进行编程清除。

The base names for identifying resource files are defined with the basenames property \(in the ReloadableResourceBundleMessageSource configuration\). The defined base names follow the basic ResourceBundle convention that consists in not specifying the file extension nor the language code. We can refer to any Spring resource location. With a classpath: prefix,resources can still be loaded from the classpath, but cacheSeconds values other than -1 \(caching forever\) will not work in this case.

用于标识资源文件的基本名称使用`basenames`属性（在ReloadableResourceBundleMessageSource配置中）定义。 定义的基本名称遵循基本的`ResourceBundle`约定，其中不包括指定文件扩展名和语言代码。 我们可以参考任何Spring resource位置。 使用classpath：prefix，资源仍然可以从类路径加载，但是cacheSeconds值不是-1（永远高速缓存）将不会在这种情况下工作。

### StaticMessageSource

The StaticMessageSource is a simple implementation that allows messages to be registered programmatically. It is intended for testing rather than for a use in production.

StaticMessageSource是一个简单的实现，允许以编程方式注册消息。 它用于测试而不是用于生产。

### Our MessageSource bean definition

We have implemented a specific controller that serializes and exposes the whole aggregation of our resource bundle properties-files \(errors and message\) for a given language passed in as a query parameter.

To achieve this, we have created a custom SerializableResourceBundleMessageSource object, borrowed from Roger Villars,and its bookapp-rest application \([https://github.com/rvillars/bookapp-rest\](https://github.com/rvillars/bookapp-rest\)\).

This custom MessageSource object extends ReloadableResourceBundleMessageSource. We have made a Spring bean of it with the following definition:

我们的MessageSource bean定义

我们已经实现了一个特定的控制器，对于作为查询参数传递的给定语言，序列化并公开了我们的资源束属性 - 文件\(errors and message\)的整个聚合。

为了实现这一点，我们创建了一个自定义SerializableResourceBundleMessageSource对象，借鉴自Roger Villars和它的bookapp-rest应用程序（https//github.com/rvillars/bookapp-rest）。

此自定义MessageSource对象扩展ReloadableResourceBundleMessageSource。 我们已经使用了以下定义的Spring bean：

```js
<bean id="messageBundle" class="edu.zc.csm.core.i18n.SerializableResourceBundleMessageSource">
    <property name="basenames" value="classpath:/METAINF/i18n/messages,classpath:/META-INF/i18n/errors"/>
    <property name="fileEncodings" value="UTF-8" />
    <property name="defaultEncoding" value="UTF-8" />
</bean>
```

We have specifically specified the paths to our resource files in the classpath. This can be avoided with a global resource bean in our context:

我们已经在类路径中明确指定了我们的资源文件的路径。 这可以在我们的上下文中使用全局资源bean来避免：

```js
<resources location="/, classpath:/META-INF/i18n" mapping="/resources/**"/>
```

Note that Spring MVC, by default, expects the i18n resource files to be located in a /WEB-INF/i18n folder.

注意，Spring MVC默认情况下期望i18n资源文件位于/WEB-INF/i18n文件夹中。

### Using a LocaleResolver

In our application, in order to switch the Locale to another language/country, we pass through the user preferences screen. This means that we somehow persist this information in the database. This makes easy the LocaleResolution that is actually operated on the client side, reading the user data and calling the internationalized messages for the language preference asynchronously.

However, some other applications might want to operate LocaleResolution on the server side. To do so, a LocaleResolver bean must be registered.

LocaleResolver is a Spring Interface \(`org.springframework.web.servlet.LocaleResolver`\):

在我们的应用程序中，为了将区域设置切换到another language/country，我们通过user preferences界面。 这意味着我们以某种方式在数据库中保存此信息。 这使得易于实现在客户端上操作的LocaleResolution，读取用户数据并异步地调用用于语言偏好的国际化消息。

但是，一些其他应用程序可能需要在服务器端操作LocaleResolution。 为此，必须注册LocaleResolver bean。

LocaleResolver是一个Spring接口\(`org.springframework.web.servlet.LocaleResolver`\)：

```java
public interface LocaleResolver {
    Locale resolveLocale(HttpServletRequest request);
    void setLocale(HttpServletRequest request, HttpServletResponse response, Locale locale);
}
```

There are four concrete implementations in Spring MVC \(version four and above\):

Spring MVC有四个具体的实现（版本四及以上）：

### AcceptHeaderLocaleResolver

The AcceptHeaderLocaleResolver makes use of the `Accept-Language header` of the HTTP request. It extracts the first Locale that the value contains. This value is usually set by the client’s web browser that reads it from OS configuration.

AcceptHeaderLocaleResolver使用HTTP请求的`Accept-Language header`。 它提取值包含的第一个区域设置。 此值通常由客户端的Web浏览器设置，从操作系统配置读取它。

### FixedLocaleResolver

This resolver always returns a fixed default Locale with optionally a time zone. The default Locale is the current JVM's default Locale.

此解析器始终返回固定的默认区域设置，并可选择时区。 默认区域设置是当前JVM的默认区域设置。

### SessionLocaleResolver

This resolver is the most appropriate when the application actually uses user sessions. It reads and sets a session attribute whose name is only intended for internal use:

当应用程序实际使用用户会话时，此解析器是最合适的。 它读取并设置其名称仅供内部使用的会话属性：

```java
public static final String LOCALE_SESSION_ATTRIBUTE_NAME = SessionLocaleResolver.class.getName() + ".LOCALE";
```

By default, it sets the value from the default Locale or from the Accept-Language header.The session may also optionally contain an associated time zone attribute. Alternatively, we may specify a default time zone.

The practice in these cases is to create an extra and specific web filter.

默认情况下，它设置默认区域设置或接受语言标题的值。会话也可以可选地包含关联的时区属性。 或者，我们可以指定默认时区。

在这些情况下的做法是创建一个额外的和特定的Web过滤器。

### CookieLocaleResolver

CookieLocaleResolver is a resolver that is well suited to stateless applications like ours.The cookie name can be customized with the `cookieName`property. If the Locale is not found in an internally defined request parameter, it tries to read the cookie value and falls back to the `Accept-Language header`.

The cookie may optionally contain an associated time zone value as well. We can still specify a default time zone as well.

CookieLocaleResolver是一个非常适合像我们的无状态应用程序的解析器。Cookie名称可以使用`cookieName`属性定制。 如果在内部定义的请求参数中找不到Locale，它将尝试读取cookie值，并返回`Accept-Language header`。

Cookie还可以可选地包含相关联的时区值。 我们仍然可以指定默认时区。

## There's more…

### Translating client-side with angular-translate.js

We used angular-translate.js to handle translations and to switch the user Locale from the client side. angular-translate.js library is very complete and well documented. As a dependency, it turns out to be extremely useful.

The main points of this product are to provide:

* Components \(filters/directives\) to translate contents

* Asynchronous loading of i18n data

* Pluralization support using MessageFormat.js

* Expandability through easy to use interfaces

A quick overview of angular-translate is shown in this figure:

我们使用angular-translate.js来处理翻译，并从客户端切换用户Locale。 angular-translate.js库非常完整和有据可查。 作为依赖，结果是非常有用。

本产品的要点是提供：

* 用于翻译内容的组件\(filters/directives\)

* 异步加载i18n 数据

* 使用MessageFormat.js进行多元化支持

* 通过易于使用的接口实现扩展性

**angular-translate**的快速概述如图所示：

![](/assets/127.png)

International resources are pulled down either dynamically from an API endpoint \(as we did\),either from static resource files published on the web application path. These resources for their specific Locale are stored on the client-side using LocalStorage or using cookies.

The stored data corresponds to a variable\(UrlLanguageStorage in our case\) that is accessible and injectable in whatever module that may require translation capabilities.

As shown in the following examples, the translate directive can be used to actually render translated messages:

国际资源从API端点（如我们所做的）动态下拉，或者从在Web应用程序路径上发布的静态资源文件。 这些资源的特定区域设置存储在客户端使用LocalStorage或使用Cookie。

存储的数据对应于可以在任何可能需要翻译功能的模块中访问和注入的变量（在我们的例子中为UrlLanguageStorage）。

如以下示例所示，translate指令可用于实际呈现已翻译的消息：

```js
<span translate>i18n.key.message</span> or
<span translate=" i18n.key.message" >fallBack translation in English (better for Google indexes) </span>
```

Alternatively, we can use a predefined translate filter to translate our translation keys in the DOM, without letting any controller or service know of them:

或者，我们可以使用预定义的translate filter来翻译DOM中的translation keys，而不让任何控制器或服务知道它们：

```java
{{data.type.type == 'BUY' ?
    'screen.stock.detail.transaction.bought' : 'screen.stock.detail.transaction.sold' | translate}}
```

You can read more about angular-translate on their very well done documentation:

你可以阅读更多关于angular-translate的文档：

[https://angular-translate.github.io](https://angular-translate.github.io)

