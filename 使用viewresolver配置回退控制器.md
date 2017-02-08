This recipe introduces some more advanced concepts and tools related to Controllers such as ViewResolvers, URI Template Patterns, and Spring MVC's injection-as-argument. The recipe is quite simple but there is more to talk about.

该配方引入了一些与控制器相关的更高级的概念和工具，例如ViewResolvers，URI模板模式和Spring MVC的注入参数。 谱方很简单，但有更多的谈论。

## Getting ready **           **

We will keep working from the same codebase state as the previous recipe where we have pulled the v2.2.1 tag from the remote repository. It will only be about creating one Controller with its handler method.**            **

我们将继续使用与先前的配方相同的代码库状态，其中我们已从远程存储库中提取了v2.2.1标记。 它将只是关于使用其处理程序方法创建oneController。

## How to do it...

In the cloudstreetmarket-webapp module and in the package edu.zipcloud.cloudstreetmarket.portal.controllers, the following DefaultController has been created:

在**cloudstreetmarket-webapp**模块和包edu.zipcloud.cloudstreetmarket.portal.controllers中，以下DefaultController已创建：

```java
@Controller
public class DefaultController {

    @RequestMapping(value="/*", method={RequestMethod.GET,RequestMethod.HEAD})
    public String fallback() {
        return "index";
    }
}
```

> We will explain in detail how this method-handler serves as a fallback interceptor.
>
> 我们将详细解释这个方法处理程序如何作为后备拦截器。

Access the [http://localhost:8080/portal/whatever](http://localhost:8080/portal/whatever) or [http://localhost:8080/portal/index](http://localhost:8080/portal/index) URL with your browser.

You should also receive the HTML content we saw earlier:

使用浏览器访问http：// localhost：8080 / portal / whatever或http：// localhost：8080 / portal / index URL。

你还应该收到我们之前看到的HTML内容：

![](/assets/32.png)

## How it works...   **       **

This second recipe revisits the use of the `@RequestMapping` annotation. With no longer a fixed URI as a path value but with an opened-pattern \(fallback\). The recipe also makes use of the configured View resolver that we didn't use before.**      **

第二个方法重新使用`@RequestMapping`注解。 不再使用固定的URI作为路径值，而是使用开放模式（回退）。 该配方还使用我们以前没有使用的配置的View解析器。

### URI template patterns  **        **

The template word is recurring in the Spring terminology. It usually refers to generic support Spring APIs to be instantiated in order to fill specific implementations or customisations \(REST template to make REST HTTP requests, JMS template to send JMS messages, WS template to make SOAP webservices requests, JDBC template, and so on\). They are bridge the developer needs to Spring core features.

Under this light, URI templates allow configuring generic URIs with patterns and variables for controller end points. It is possible to instantiate URI builders that will implement URI templates but developers probably mostly use URI templates in the support they provide to `@RequestMapping` annotations.

URI模板模式   **       **

模板代码在Spring术语中重复出现。 它通常指的是通用支持Spring API，以便填充特定的实现或定制（用于创建REST HTTP请求的REST模板，用于发送JMS消息的JMS模板，用于生成SOAP Web服务请求的WS模板，JDBC模板等）  。 它们是开发者需要的Spring核心特性的桥梁。

在这个光环下，URI模板允许为控制器端点配置带有模式和变量的通用URI。 可以实例化将构建URI模板的URI构建器，但是开发人员可能主要在`@RequestMapping注解`的支持中使用URI模板。

### Ant-style path patterns   **       **

We have made use of these types of pattern to define the path value for our fallback handler method:

`@RequestMapping(value="/*", ...)`

Ant风格路径模式   **       **

我们使用这些类型的模式来定义我们的后备处理程序方法的路径值：

`@RequestMapping（value =“/ *”，...）`

This specific case, with the \* wildcard, allows whichever request URI starts with a / after the application display name to be eligible for being handled by this method.

The wildcard can match a character, a word, or a sequence of words. Consider the following example:

`/portal/1, /portal/foo, /portal/foo-bar`

A limitation would be to use another slash in the last sequence:

`/portal/foo/bar`

Remember the difference with the table here:

使用**\***通配符的此特定情况允许以应用程序显示名称后的/开头的任何请求URI有资格由此方法处理。

通配符可以匹配字符，单词或单词序列。 考虑下面的例子：

`/portal/1, /portal/foo, /portal/foo-bar`

限制是在最后一个序列中使用另一个斜杠：

`/portal/foo/bar`

记住与表格的区别：

| /\* | All resources and directories at the level |
| :--- | :--- |
| /\*\* | All resources and directories at the level and sublevels |

We have been using the single wildcard on purpose in the cloudstreetmarket-webapp application. It might be more suited for other types of applications to redirect every unmatched URI to a default one. In our case of a single page application that will be strongly REST oriented, it is nicer to inform the client with a 404 error when a resource hasn't been found.

It is not the only option to use wildcards at the end of path patterns. We could have  
 implemented the following type of pattern if needed:

`/portal/**/foo/*/bar`

\(not for fallback purposes, though\).

我们一直在**cloudstreetmarket-webapp**应用程序中使用单个通配符。 它可能更适合于其他类型的应用程序将每个不匹配的URI重定向到默认的URI。 在我们的情况下，单一页面应用程序将强烈面向REST，当没有找到资源时，通知客户端404错误是更好的。

它不是在路径模式结尾使用通配符的唯一选项。 如果需要，我们可以实现以下类型的模式：

`/portal/**/foo/*/bar`

级别和子级别上的所有资源和目录...

We will see that Spring MVC, to select one handler compares, all the matching Path patterns and selects the most specific of them.

我们将看到Spring MVC，选择一个处理程序进行比较，匹配所有匹配的Path模式，并选择其中最具体的。



> At the Controller type-level, we haven't specified a @RequestMapping. If we had done so, the specified path for the method-level would have been concatenated to type-level one \(implementing a narrowing\).
>
> 在Controller类型级别，我们没有指定@RequestMapping。 如果我们这样做，方法级别的指定路径将被连接到类型级别1（实现变窄）。



For example, the following definition would have defined the path pattern /portal/default/\* for our fallback controller:

例如，以下定义将为我们的回退控制器定义路径模式/portal/default/\*：

```java
@RequestMapping(value="/default"...)
@Controller
public class DefaultController…{
    @RequestMapping(value="/*"...)
    public String fallback(Model model) {...}
}
```

### Path pattern comparison **       **

A pathpattern comparison is done by Spring MVC when a given URL matches more than one registered path-pattern, to choose which handler the request will be mapped to.

路径模式比较 **       **

当给定的URL匹配多个注册的路径模式时，Spring MVC进行路径模式比较，以选择请求将被映射到哪个处理程序。



> The pattern considered the most specific will be selected
>
> 将选择被认为是最具体的模式



The first criterion is the number of counted variables and wildcards in the compared path patterns: the pattern having the lowest number of variables and wildcards is considered the most specific.

To discriminate two path-patterns that have the same cumulated number of variables and wildcards, remember that the one with the lowest number of wildcards will be the most specific and then the longest path will be the most specific.

Finally a pattern with double wildcards is always less specific than a pattern without any. To illustrate this selection, let's consider the following hierarchy going from the most to the least specific:

`/portal/foo`

`/portal/{foo}`

`/portal/*`

`/portal/{foo}/{bar}`

`/portal/default/*/{foo}`

`/portal/{foo}/*`

`/portal/**/*`

`/portal/**`

第一个标准是在比较的路径模式中计数的变量和通配符的数量：具有最低数量的变量和通配符的模式被认为是最具体的。

要区分具有相同累计数量的变量和通配符的两个路径模式，请记住具有最低通配符数量的路径模式将是最具体的，然后最长的路径将是最具体的。

最后，具有双通配符的模式总是比没有任何模式的模式特定。 为了说明这种选择，让我们考虑从最多到最不具体的以下层次结构：

`/portal/foo`

`/portal/{foo}`

`/portal/*`

`/portal/{foo}/{bar}`

`/portal/default/*/{foo}`

`/portal/{foo}/*`

`/portal/**/*`

`/portal/**`

### ViewResolvers   **   **

In dispatcher-context.xml of cloudstreetmarket-webapp, we have defined the viewResolver bean:

在**cloudstreetmarket-webapp**的**dispatcher-context.xml**中，我们定义了**viewResolver bean**：

```java
<bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="viewClass" value="org.springframework.web.servlet.view.JstlView" />
    <property name="prefix" value="/WEB-INF/jsp/" />
    <property name="suffix" value=".jsp" />
</bean>
```

A **viewResolver bean** is a specific instance of a predefined class used to serve an organized and uniform set of view layers. In the case that we have configured, the **viewResolver bean** is an instance of InternalResourceViewResolver, which can serve JSP pages, handle the JSTL and tiles. This class also inherits **UrlBasedViewResolver** that can navigate the application resources and can bind a logical view name to a View resource file. This capability prevents the creation of extramappings.

In our configuration, we have defined the view repository \(/WEB-INF/jsp/\*.jsp\) and we can directly refer to index.jsp with the String index.

It is better practice to set up the JSP repository under /WEB-INF so those JSPs cannot be targeted publicly. Rather than a JSP templating, we could have used Velocity or Freemarker respectively using the view resolvers VelocityViewResolver or FreeMarkerViewResolver.

Also, we will talk about the ContentNegotiatingViewResolver later on when we build the REST API.

**viewResolver bean**是用于提供有组织和统一的视图层集合的预定义类的特定实例。 在我们配置的情况下，**viewResolver bean**是**InternalResourceViewResolver**的一个实例，它可以提供JSP页面，处理JSTL和图块。 此类还继承**UrlBasedViewResolver**，可以导航应用程序资源，并可将逻辑视图名称绑定到View资源文件。 此功能防止创建extramappings。

在我们的配置中，我们定义了视图存储库（`/WEB-INF/jsp/*.jsp`），我们可以直接使用String索引引用index.jsp。

更好的做法是在/ WEB-INF下设置JSP存储库，以使这些JSP无法公开定向。 而不是一个JSP模板，我们可以使用Velocity或Freemarker分别使用视图解析器VelocityViewResolver或FreeMarkerViewResolver。

此外，稍后我们将在构建REST API时讨论ContentNegotiatingViewResolver。

## There's more...   ** **

This section highlights particularly the `@PathVariable` annotation. This annotation is an annotation for controller method-handler arguments \(we have introduced all of them in the previous recipe\).



本节特别强调`@PathVariable`注解。 这个注解是控制器方法处理程序参数的注解（我们已经在前面的配方中引入了它们）。

### @PathVariable to read variables in URI template patterns**    **

You will find later, on several examples, the method-level `@RequestMapping` annotations . Those annotations will sometimes be related to `@PathVariable` annotations on the methodhandler arguments. For now, let's consider the following example:

@PathVariable读取URI模板模式中的变量**    **

稍后，您会在几个示例中找到方法级别的`@RequestMapping`注解。 这些注解有时会与methodhandler参数的`@PathVariable`注解相关。 现在，让我们考虑下面的例子：

```java
@RequestMapping(value="/example/{param}")
public HttpEntity<String> example(@PathVariable("param") String parameter) {

    return new HttpEntity<>(parameter);

}
```

As announced before, `@PathVariable` tells Spring MVC where and how to realize its injection-as-argument from the request URI. The framework will parse the current URI Template pattern to extract the variable named param and will inject the matching value in the current URI into the targeted method-argument.

We also declare an HTTPEntity to be returned as a response. This HTTPEntity will be a wrapper of a String generic type. Inside the method-handler, we instantiate this wrapper with the necessary String element.

If we would call for the /portal/example/foo URI, it would be displayed as a response from the body of the returned HTTPEntity: the String foo.

With another interesting feature, we could have built this last scenario with the following declaration for `@PathVariable`:

如前所述，`@PathVariable`告诉Spring MVC在何处以及如何从请求URI实现其注入参数。 框架将解析当前URI模板模式以提取名为`param`的变量，并将当前URI中的匹配值注入到目标方法参数中。

我们还声明一个`HTTPEntity`作为响应返回。 此HTTPEntity将是String通用类型的包装器。 在方法处理程序中，我们用必要的String元素实例化这个包装器。

如果我们调用`/portal/example/foo URI`，它将显示为来自返回的HTTPEntity的主体的响应：String foo。

有了另一个有趣的特性，我们可以使用`@PathVariable`的以下声明构建最后一个场景：

```java
@RequestMapping(value="/example/{param}")
public HttpEntity<String> example(@PathVariable String param) {

    return new HttpEntity<>(param);

}
```

> Not providing a value to the annotation, Spring MVC will by default look in the URI Template pattern for a variable of the same name as the targeted argument.
>
> 不为注解提供值，Spring MVC将默认在URI模板模式中查找与目标参数同名的变量。

We will explore other features with regard to `@RequestMapping` and `@PathVariable`.

我们将探讨关于`@RequestMapping`和`@PathVariable`的其他功能。

