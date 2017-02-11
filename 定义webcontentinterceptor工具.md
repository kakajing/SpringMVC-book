In this recipe, we will highlight how we have implemented a WebContentInterceptor superclass for Controllers.

在这个配方中，我们将重点介绍如何为Controllers实现一个WebContentInterceptor超类。

## Getting ready**  **

We are about to present a Controller superclass having the specificity of being registered as a WebContentInterceptor. This superclass allows us to globally control sessions and to manage caching options.

It will help us understanding the request lifecycle throughout the Framework and through other potential interceptors.

我们将要呈现一个Controller超类，它具有被注册为WebContentInterceptor的特性。 这个超类允许我们全局控制会话和管理缓存选项。

它将帮助我们了解整个框架和通过其他潜在的拦截器的请求生命周期。

## How to do it...

1.Registering a default WebContentInterceptor with its specific configuration can be done entirely with the configuration approach:

1.使用配置方法可以完全注册具有其特定配置的默认WebContentInterceptor：

```js
<mvc:interceptors>
    <bean id="webContentInterceptor" class="org.sfw.web.servlet.mvc.WebContentInterceptor">
        <property name="cacheSeconds" value="0"/>
        <property name="requireSession" value="false"/>
        ...
    </bean>
<mvc:interceptors>
```

> In our application, we have registered custom WebContentInterceptors to override the behaviors of the default one.
>
> 在我们的应用程序中，我们注册了自定义WebContentInterceptors来覆盖默认行为。



2.In the codebase, still from the previously checked-out v2.x.x branch, a new cloudstreetApiWCI class can be found in cloudstreetmarket-api:

2.在代码库中，仍然从以前检出的v2.x.x分支，可以在cloudstreetmarket-api中找到一个新的cloudstreetApiWCI类：

```java
public class CloudstreetApiWCI extends WebContentInterceptor {

        public CloudstreetApiWCI(){
        
            setRequireSession(false);
            setCacheSeconds(0);
        }
        
        @Override
        public boolean preHandle(HttpServletRequest request, 
                HttpServletResponse response, Object handler) throws ServletException {
        
            super.preHandle(request, response, handler);
            return true;
        }
        
        @Override
        public void postHandle(HttpServletRequest request,
                HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        
        }
        
        @Override
        public void afterCompletion(HttpServletRequest request,
                HttpServletResponse response, Object handler, Exception ex) throws Exception {
        }
}
```

3.A similar CloudstreetWebAppWCI is also present in cloudstreetmarket-webapp:

3.cloudstreetmarket-webapp中也有类似的CloudstreetWebAppWCI：

```java
public class CloudstreetWebAppWCI extends WebContentInterceptor {

    public CloudstreetWebAppWCI(){

        setRequireSession(false);
        setCacheSeconds(120);
        setSupportedMethods("GET","POST", "OPTIONS", "HEAD");
    }

    @Override
    public boolean preHandle(HttpServletRequest request,
            HttpServletResponse response, Object handler) throws ServletException {

        super.preHandle(request, response, handler);
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request,
            HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
    }

    @Override
    public void afterCompletion(HttpServletRequest request,
            HttpServletResponse response, Object handler, Exception ex) throws Exception {
    }

}
```

4.In cloudstreetmarket-webapp, DefaultController and InfoTagController now both inherit CloudstreetWebAppWCI:

4.在cloudstreetmarket-webapp中，DefaultController和InfoTagController现在都继承了CloudStreetWebAppWCI：

```java
public class InfoTagController extends CloudstreetWebAppWCI {
...
}

public class DefaultController extends CloudstreetWebAppWCI {
...
}
```

5.In cloudstreetmarket-webapp the dispatcher-context.xml context file registers the interceptor:

5.在cloudstreetmarket-webapp中，dispatcher-context.xml上下文文件注册拦截器：

```js
<mvc:interceptors>
    <bean class="edu.zc...controllers.CloudstreetWebAppWCI">
        <property name="cacheMappings">
            <props>
                <prop key="/**/*.js">86400</prop>
                <prop key="/**/*.css">86400</prop>
                <prop key="/**/*.png">86400</prop>
                <prop key="/**/*.jpg">86400</prop>
            </props>
        </property>
    </bean>
</mvc:interceptors>
```

6.In the cloudstreetmarket-api, dispatcher-context.xml, the other interceptor has also been registered:

6.在cloudstreetmarket-api，dispatcher-context.xml中，另一个拦截器也已注册：

```js
<mvc:interceptors>
    <bean class="edu.zc...controllers.CloudstreetApiWCI"/>
</mvc:interceptors>
```

7.Finally, in both dispatcher-context.xml, the RequestMappingHandlerAdapter bean has been given the `synchronizeOnSession` property:

7.最后，在dispatcher-context.xml中，RequestMappingHandlerAdapter bean已被赋予`synchronizeOnSession`属性：

```js
<bean class="org.sfw...annotation.RequestMappingHandlerAdapter">
    <property name="synchronizeOnSession" value="true"/>
</bean>
```

## How it works...

In each web module, we have created a superclass for Controllers. In the cloudstreetmarketwebapp module for example, both InfoTagController and DefaultController now inherit the CloudstreetWebAppWCI superclass.

在每个Web模块中，我们为控制器创建了一个超类。 例如，在cloudstreetmarketwebapp模块中，InfoTagController和DefaultController都继承了CloudstreetWebAppWCI超类。

### Common behaviors for Controllers

Beyond the WebContentInterceptor capabilities, it is more than a good practice to share common logic and attributes between controllers if they relate to configuration \(application or business\); the idea is to avoid creating another service layer. We will see with further implementations that it is a good place for defining user contexts.

A WebContentInterceptor through its WebContentGenerator superclass offers useful request and session management tools that we are going to present now. As an interceptor, it must be registered declaratively. This is the reason why we have added two `<mvc:interceptors>` entries in our context files.

控制器的常见行为

除了WebContentInterceptor功能之外，如果它们与配置（应用程序或业务）相关，它是控制器之间共享公共逻辑和属性的好办法; 这个想法是避免创建另一个服务层。 我们将看到进一步的实现，它是定义用户上下文的好地方。

WebContentInterceptor通过其WebContentGenerator超类提供了有用的请求和会话管理工具，我们现在将介绍。 作为拦截器，它必须声明性地注册。 这就是为什么我们在上下文文件中添加了两个`<mvc：interceptors>`条目的原因。

### Global session control

A WebContentInterceptor, handling requests provides the ability to control how the application should react with HTTP sessions.

全局会话控制

WebContentInterceptor，处理请求提供了控制应用程序应如何与HTTP会话进行反应的特性。

### Requiring sessions

The WebContentInterceptor through WebContentGenerator offers the `setRequireSession(boolean)` method. This allows defining whether or not a session should be required when handling a request.

If there is no session bound to the request \(if the session has expired for example\), the controller will throw a SessionRequiredException method. In such cases, it is good to have a global ExceptionHandler defined. We will set up a global exception mapper when we will build the REST API. By default, the sessions are not required.**  
**

WebContentInterceptor通过WebContentGenerator提供`setRequireSession(boolean)`方法。 这允许定义在处理请求时是否需要会话。

如果没有会话绑定到请求（例如，如果会话已经过期），控制器将抛出一个SessionRequiredException方法。 在这种情况下，最好定义一个全局异常处理程序。 当我们构建REST API时，我们将设置一个全局异常映射器。 默认情况下，会话不是必需的。

### Synchronizing sessions

Another interesting feature comes with the `synchronizeOnSession` property that we have set to `true` in the RequestMappingHandlerAdapter definition. When set it to `true`, the session object is serialized and access to it is made in a synchronized block. This allows concurrent access to identical sessions and avoids issues that sometimes occur when using multiple browser windows or tabs.

另一个有趣的特性来自于`synchronizeOnSession`属性，我们在RequestMappingHandlerAdapter定义中设置为`true`。 当设置为`true`时，会话对象被序列化，并且在同步块中进行访问。 这允许并发访问相同的会话，并避免使用多个浏览器窗口或选项卡时有时会出现的问题。

### Cache-header management

With the `setCacheSeconds(int)` method that we have used in the constructors of CloudstreetWebAppWCI and CloudstreetApiWCI; the WebContentInterceptor with WebContentGenerator can manage a couple of HTTP response headers related to caching. Set to zero, it adds the extra headers in the response such as Pragma, Expires, Cache-control, and so on.

We have also defined custom caching for static files at the configuration level:

缓存头管理

使用我们在CloudstreetWebAppWCI和CloudstreetApiWCI的构造函数中使用的`setCacheSeconds(int)`方法;  WebContentInterceptor与WebContentGenerator可以管理与缓存相关的几个HTTP响应头。 设置为零，它在响应中添加额外的头部，例如Pragma，Expires，Cache-control等。

我们还在配置级别为静态文件定义了自定义缓存：

```js
<props>
    <prop key="/**/*.js">86400</prop>
    <prop key="/**/*.css">86400</prop>
    <prop key="/**/*.png">86400</prop>
    <prop key="/**/*.jpg">86400</prop>
</props>
```

All our static resources are cached in this way for 24 hours, thanks to the native `WebContentInterceptor.preHandle` method.

由于本地的`WebContentInterceptor.preHandle`方法，我们所有的静态资源都以这种方式缓存24小时。

### HTTP method support

We have also defined a high-level restriction for HTTP methods. It can be narrowed down by the `@RequestMapping` method attribute at the Controller level. Accessing a disallowed method will result in 405 HTTP error: Method not supported.

我们还定义了HTTP方法的高级限制。 它可以通过控制器级别的`@RequestMapping`方法属性缩小。 访问不允许的方法将导致405 HTTP错误：不支持方法。

### A high-level interceptor

In the Interceptor registration in dispatcher-context.xml, we haven't defined a path mapping for the interceptor to operate on. It is because by default Spring applies the double wildcard operator `/**` on such standalone interceptor definitions.

It is not because we have made DefaultController, extending an interceptor, that the interceptor is acting on the Controller `@RequestMapping` path. The interceptor's registration is only made through configuration. If the covered path mapping needs to be modified, we could override our registration in the following way:

一个高级拦截器

在dispatcher-context.xml中的拦截器注册中，我们没有定义一个用于拦截器操作的路径映射。 这是因为默认情况下，Spring在这样的独立拦截器定义上应用双通配符运算符`/ **`。

这不是因为我们已经使DefaultController扩展了一个拦截器，拦截器在Controller `@RequestMapping`路径上作用。 拦截器的注册只通过配置。 如果覆盖路径映射需要修改，我们可以通过以下方式覆盖我们的注册：

```js
<mvc:interceptors>
    <mvc:interceptor>
    <mvc:mapping path="/**"/>
    <bean class="edu.zc.csm.portal...CloudstreetWebAppWCI">
    <property name="cacheMappings">
        <props>
            <prop key="/**/*.js">86400</prop>
            <prop key="/**/*.css">86400</prop>
            <prop key="/**/*.png">86400</prop>
            <prop key="/**/*.jpg">86400</prop>
        </props>
    </property>
    </bean>
    </mvc:interceptor>
</mvc:interceptors>
```

We have also overridden the WebContentInterceptor method's `preHandle`, `postHandle`, and `afterCompletion`. It will allow us later to define common business related operations before and after the Controller request handling.

我们还覆盖了WebContentInterceptor方法的`preHandle`，`postHandle`和`afterCompletion`。 它将允许我们稍后在Controller请求处理之前和之后定义常见的业务相关操作。

### Request lifecycle

Throughout the interceptor\(s\), each request is processed according to the following lifecycle:

* Prepare the request's context

* Locate the Controller's handler

* Execute interceptor's preHandle methods

* Invoke the Controller's handler

* Execute interceptor's postHandle methods

* Handle the Exceptions

* Process the View

* Execute interceptor's afterCompletion methods

To better understand the sequence, especially when Exceptions occur, the following workflow is very useful:

请求生命周期

在整个拦截器中，每个请求根据以下生命周期进行处理：

* 准备请求的上下文

* 查找控制器的处理程序

* 执行拦截器的preHandle方法

* 调用控制器的处理程序

* 执行拦截器的postHandle方法

* 处理异常

* 处理视图

* 执行拦截器的afterCompletion方法

为了更好地理解序列，特别是当发生异常时，以下工作流非常有用：

![](/assets/50.png)

From this diagram, you can see that:

* The controller handler is invoked, unless one of the interceptors' preHandle methods throws an exception.

* An interceptor's postHandle method is called when the controller's handler finishes without throwing an exception and if no preceding postHandler method has thrown an exception.

* An interceptor's afterCompletion is always called, unless a preceding afterCompletion throws an exception.

Obviously, if no Interceptor is registered, the same sequence applies, skipping the interceptors' steps.

从此图中，你可以看到：

* 调用控制器处理程序，除非拦截器的preHandle方法之一抛出异常。

* 拦截器的postHandle方法在控制器的处理程序完成而不抛出异常并且没有预先的postHandler方法抛出异常时被调用。

* 拦截器的afterCompletion总是被调用，除非前面的afterCompletion抛出异常。

显然，如果没有注册Interceptor，则应用相同的序列，跳过拦截器的步骤。

### More features offered by WebContentGenerator

Again, WebContentGenerator is a superclass of WebContentInterceptor. From its JavaDoc page: [http://docs.spring.io/spring/docs/current/javadoc-api/org/](http://docs.spring.io/spring/docs/current/javadoc-api/org/) springframework/web/servlet/support/WebContentGenerator.html you can find the following for example:

* Three constants \(String\) METHOD\_GET, METHOD\_POST, and METHOD\_HEAD refer to the values GET, POST, and HEAD

* Some caching specific methods such as setUseExpiresHeader, setUseCacheControlHeader, setUseCacheControlNoStore, setAlwaysMustRevalidate, and preventCaching

WebContentGenerator提供的更多功能

再次，WebContentGenerator是WebContentInterceptor的超类。 从其JavaDoc页面：http//docs.spring.io/spring/docs/current/javadoc-api/org/ springframework / web / servlet / support / WebContentGenerator.html，你可以找到以下示例：

* 三个常量（String）METHOD\_GET，METHOD\_POST和METHOD\_HEAD指的是值GET，POST和HEAD

* 某些缓存特定方法，如setUseExpiresHeader，setUseCacheControlHeader，setUseCacheControlNoStore，setAlwaysMustRevalidate和preventCaching

Also, with WebApplicationObjectSupport, WebContentGenerator provides:

* Access to ServletContext out of the request or response object through `getServletContext()`.

* Access to the temporary directory for the current web application, as provided by the servlet container through `getTempDir()`.

* Access to the WebApplicationContext through `getWebApplicationContext()`.

此外，使用WebApplicationObjectSupport，WebContentGenerator提供：

* 通过`getServletContext()`访问请求或响应对象中的ServletContext。

* 访问当前Web应用程序的临时目录，由servlet容器通过`getTempDir()`提供。

* 通过`getWebApplicationContext()`访问WebApplicationContext。

We quickly passed through web caching. There are a lot of customizations and standards in this domain. Also, a new RequestMappingHandlerAdapter has been created with Spring MVC 3.1. It will be helpful to understand the change.

我们很快就通过了网络缓存。 这个领域有很多定制和标准。 此外，使用Spring MVC 3.1创建了一个新的RequestMappingHandlerAdapter。 这将有助于了解这种变化。

### Web caching

Find out more about web caching through this very complete caching tutorial:

通过这个非常完整的缓存教程了解更多关于网络缓存：

[https://www.mnot.net/cache\\_docs](https://www.mnot.net/cache\_docs)

### New support classes for @RequestMapping since Spring MVC 3.1

We have used the RequestMappingHandlerAdapter with its bean definition in dispatcher-context.xml. This bean is a new feature with Spring MVC 3.1 and has replaced the former AnnotationMethodHandlerAdapter. Also, the support class DefaultAnnotationHandlerMapping has now been replaced by RequestMappingHandlerMapping.

We will go deeper into RequestMappingHandlerAdapter in Chapter 4, Building a REST API for a Stateless Architecture.

In the meantime, you can read the official change note:

[http://docs.spring.io/spring-framework/docs/3.1.x/spring-frameworkreference/html/mvc.html\\#mvc-ann-requestmapping-31-vs-30](http://docs.spring.io/spring-framework/docs/3.1.x/spring-frameworkreference/html/mvc.html\#mvc-ann-requestmapping-31-vs-30)

Spring MVC 3.1以来的@RequestMapping的新支持类

我们在dispatcher-context.xml中使用了RequestMappingHandlerAdapter及其bean定义。 这个bean是Spring MVC 3.1的一个新功能，并取代了前面的AnnotationMethodHandlerAdapter。 此外，支持类DefaultAnnotationHandlerMapping现在已被RequestMappingHandlerMapping替换。

我们将在第4章“为无状态体系结构构建REST API”中更深入地讨论RequestMappingHandlerAdapter。

在此期间，您可以阅读官方变更通知：

http//docs.spring.io/spring-framework/docs/3.1.x/spring-frameworkreference/html/mvc.html\#mvc-ann-requestmapping-31-vs-30

