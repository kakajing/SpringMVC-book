Authenticating through a BASIC scheme is a popular solution for stateless applications like ours. Credentials are sent over with HTTP requests.

通过BASIC方案进行认证是像我们这样的无状态应用程序的流行解决方案。 凭证是通过HTTP请求发送的。

## Getting ready

In this recipe, we complete the Spring Security configuration. We make it support the BASIC authentication scheme required for the application.

We slightly customize the generated response-headers, so they don't trigger the browser to show-up a native BASIC authentication form \(which is not an optimal experience for our users\).

在这个配方中，我们完成了Spring Security配置。 我们使它支持应用所需的BASIC认证方案。

我们稍微自定义生成的响应头，因此它们不会触发浏览器显示本机BASIC身份验证表（这不是我们的用户的最佳体验）。

## How to do it...

1. In order to use the Spring security namespace, we add the following filter to the cloudstreetmarket-api web.xml:

1.为了使用Spring安全命名空间，我们将以下过滤器添加到cloudstreetmarket-api web.xml中：

```
<filter>
    <filter-name>springSecurityFilterChain</filter-name>
    <filter-class> org.sfw.web.filter.DelegatingFilterProxy</filter- class>
</filter>
<filter-mapping>
    <filter-name>springSecurityFilterChain</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

2. A Spring configuration file has been created specifically for Spring Security in the cloudstreetmarket-api module. This file hosts the following bean definitions:

2.在cloudstreetmarket-api模块中专门为Spring Security创建了Spring配置文件。 此文件托管以下bean定义：

```
<bean id="authenticationEntryPoint" class="edu.zc.csm.api.authentication.CustomBasicAuthenticationEntryPoint">
    <property name="realmName" value="cloudstreetmarket.com" />
</bean>

<security:http create-session="stateless" authenticationmanager-ref="authenticationManager" entry-pointref="authenticationEntryPoint">
    <security:custom-filter ref="basicAuthenticationFilter" after="BASIC_AUTH_FILTER" />
    <security:csrf disabled="true"/>
</security:http>

<bean id="basicAuthenticationFilter" class="org.sfw.security.web.authentication.www.BasicAuthenticationFilter">
    <constructor-arg name="authenticationManager" ref="authenticationManager" />
    <constructor-arg name="authenticationEntryPoint" ref="authenticationEntryPoint" />
</bean>
<security:authentication-manager alias="authenticationManager">
    <security:authentication-provider user-serviceref='communityServiceImpl'>
        <security:password-encoder ref="passwordEncoder"/>
    </security:authentication-provider>
</security:authentication-manager>
<security:global-method-security securedannotations="enabled" pre-post-annotations="enabled" authentication-manager-ref="authenticationManager"/>
```

3. This new configuration refers to the CustomBasicAuthenticationEntryPoint class. This class has the following content:

这个新配置引用了CustomBasicAuthenticationEntryPoint类。 此类具有以下内容：

```
public class CustomBasicAuthenticationEntryPoint extends BasicAuthenticationEntryPoint {

    @Override
    public void commence(HttpServletRequest request, HttpServletResponse response, 
                AuthenticationException authException) throws IOException, ServletException {
    
    response.setHeader("WWW-Authenticate", "CSM_Basicrealm=\ + getRealmName() + \");
    response.sendError(HttpServletResponse.SC_UNAUTHORIZED,authException.getMessage());
    }
}
```

4. A new @ExceptionHandler has been added to catch authentication Exceptions:

4.添加了一个新的@ExceptionHandler以捕获身份验证异常：

```
@ExceptionHandler({BadCredentialsException.class,AuthenticationException.class,AccessDeniedException.class})
protected ResponseEntity<Object> handleBadCredentials(final RuntimeException ex, final WebRequest request) {

    return handleExceptionInternal(ex, "The attempted operation has been denied!", new HttpHeaders(), FORBIDDEN,request);
}
...

```

> That's pretty much it! We have made our backend support a BASIC authentication. However, we haven't restricted our services \(as secure objects\) yet. We will do that now.
>
> 这就是它！ 我们已经使我们的后端支持BASIC身份验证。 然而，我们还没有限制我们的服务（作为安全对象）。 我们现在就这样做。

5. For the example purpose, please do update the IMarketService interface in cloudstreetmarket-core. Add the @Secured\("ROLE\_BASIC"\) annotation to the Type as follows:

5.为了示例的目的，请更新cloudstreetmarket-core中的IMarketService接口。 将@Secured（“ROLE\_BASIC”）注释添加到类型中，如下所示：

```
@Secured ("ROLE_BASIC")
public interface IMarketService {
    Page<IndexOverviewDTO> getLastDayIndicesOverview(MarketCode market, Pageable pageable);
    Page<IndexOverviewDTO> getLastDayIndicesOverview(Pageable pageable);
    HistoProductDTO getHistoIndex(String code, MarketCode market, Date fromDate, Date toDate, QuotesInterval interval);
}
```

6. Now restart the Tomcat server \(doing this will drop your previous user creation\).

7. In your favorite web browser, open the developer-tab and observe the AJAX queries when you refresh the home page. You should notice that two AJAX queries have returned a 403 status code \(FORBIDDEN\).

6.现在重新启动Tomcat服务器（这样做会删除以前的用户创建）。

7.在您喜爱的Web浏览器中，打开开发人员标签，并在刷新主页时观察AJAX查询。 您应该注意到，两个AJAX查询返回了403状态码（FORBIDDEN）。

![](/assets/82.png)

These queries have also returned the JSON response:

这些查询也返回了JSON响应：

```
{
    "error":"Access is denied",
    "message":"The attempted operation has been denied!",
    "status":403","date":"2015-05-05 18:01:14.917"
}
```

8. Now, using the login feature/popup, do log in with one of the previously created users that have a BASIC role:

8.现在，使用登录功能/弹出窗口，请登录具有BASIC角色的先前创建的用户之一：

```
Username: <userC>
Password: <123456>
```

9. Refresh the page and observe the same two AJAX queries. Amongst the request headers, you can see that our frontend has sent a special Authorization header:

9.刷新页面并观察相同的两个AJAX查询。 在请求标头中，您可以看到我们的前端发送了一个特殊的授权标头：

![](/assets/83.png)

10. This Authorization header carries the value: Basic dXNlckM6MTIzNDU2. The encoded dXNlckM6MTIzNDU2 is the base64-encoded value for userC:123456.

11. Let's have a look at the response to these queries:

10.此授权报头携带值：Basic dXNlckM6MTIzNDU2。 编码的dXNlckM6MTIzNDU2是userC：123456的base64编码值。

11.让我们看看这些查询的响应：

![](/assets/84.png)

The status is now 200 \(OK\) and you should also have received the right JSON result:

状态现在是200（确定），您应该也已经收到正确的JSON结果：

![](/assets/85.png)

12. The Server sent back a WWW-Authenticate header in the response to the value:

12.服务器在对值的响应中发回了WWW-Authenticate头：

`CSM_Basic realm"="cloudstreetmarket.com"`

13. Finally, do revert the change you made in IMarketService \(in the 5th step\).

13.最后，请还原您在IMarketService中所做的更改（在第5步）。

## How it works...

We are going to explore the concepts behind a BASIC authentication with Spring Security:

我们将探索Spring Security的BASIC身份验证背后的概念：

### The Spring Security namespace

As always, a Spring configuration namespace brings a specific syntax that suits the needs and uses for a module. It lightens the overall Spring configuration with a better readability. Namespaces often come with configuration by default or auto configuration tools.

The Spring Security namespace comes with the spring-security-config dependency and can be defined as follows in a Spring configuration file:

一如往常，Spring配置命名空间带来了一个特定的语法，适合模块的需要和用途。 它减轻了整个Spring配置，更好的可读性。 命名空间通常具有默认配置或自动配置工具。

Spring Security命名空间附带了spring-security-config依赖项，可以在Spring配置文件中定义如下：



```
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:security="http://www.springframework.org/schema/security"
xmlns:xsi=http://www.w3.org/2001/XMLSchema-instance
xsi:schemaLocation"="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/security
http://www.springframework.org/schema/security/spring-security-4.0.0.xsd">
...
</beans>
```

The namespace stages three top-level components:` <http>` \(about web and HTTP security\),`<authentication-manager>`, and `<global-method-security>` \(service or controller restriction\).

命名空间分为三个顶级组件：`<http>`（关于Web和HTTP安全性），`<authentication-manager>`和`<global-method-security>`（服务或控制器限制）。

Then, other concepts are referenced by those top-level components as attribute or as child element: `<authentication-provider>`, `<access-decision-manager> `\(provides access decisions for web and security methods\), and `<user-service>` \(as UserDetailsService implementations\).

然后，其他概念被那些顶级组件引用为属性或作为子元素：`<authentication-provider>`，`<access-decision-manager>`（提供Web和安全方法的访问决策）和`<user-service>` 作为UserDetailsS​​ervice实现）。



### The &lt;http&gt; component

The` <http> `component of the namespace provides an `auto-config` attribute that we didn't use here. The `<http auto-config"="true"> `definition would have been a shortcut for the following definition:

&lt;http&gt;组件

命名空间的`<http>`组件提供了一个我们在这里没有使用的`auto-config`属性。  `<http auto-config“=”true“>`定义将是以下定义的快捷方式：

```
<http>
    <form-login />
    <http-basic />
    <logout />
</http>
```

It isn't worth it for our REST API because we didn't plan to implement a server-side generated view for a form login. Also, the `<logout>` component would have been useless for us since our API doesn't manage sessions.

这对于我们的REST API是不值得的，因为我们没有计划为表单登录实现服务器端生成的视图。 此外，`<logout>`组件对我们来说是无用的，因为我们的API不管理会话。

Finally, the &lt;http-basic&gt; element creates underlying BasicAuthenticationFilter and BasicAuthenticationEntryPoint to the configuration.

We have made use of our own BasicAuthenticationFilter in order to customize the WWW-Authenticate response's header value from Basic base64token to CSM\_Basic base64token. This because the AJAX HTTP responses \(from our API\) containing a WWWAuthenticate header with a value starting with a Basic keyword automatically trigger the web-browser to open a native Basic-form popup. It was not the type of user experience we wanted to set up.

最后，`<http-basic>`元素会为配置创建基础BasicAuthenticationFilter和BasicAuthenticationEntryPoint。

我们使用了我们自己的BasicAuthenticationFilter，以便将WWW-Authenticate响应的头值从Basic base64token定制到CSM\_Basic base64token。 这是因为包含具有以Basic关键字开始的值的WWWAuthenticate头的AJAX HTTP响应（从我们的API）自动触发Web浏览器打开本机Basic形式弹出窗口。 这不是我们想要设置的用户体验类型。

### The Spring Security filter-chain

In the very first step of the recipe, we have declared a filter named springSecurityFilterChain in web.xml:

Spring Security过滤器链

在配方的第一步，我们在web.xml中声明了一个名为springSecurityFilterChain的过滤器

```
<filter>
    <filter-name>springSecurityFilterChain</filter-name>
    <filter-class>org.sfw.web.filter.DelegatingFilterProxy</filter-class>
</filter>
<filter-mapping>
    <filter-name>springSecurityFilterChain</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

here, springSecurityFilterChain is also a Spring bean that is created internally by the Spring Security namespace \(specifically the http component\). A DelegatingFilterProxy is a Spring infrastructure that looks for a specific bean in the application context and invokes it. The targeted bean has to implement the Filter interface.

The whole Spring Security machinery is hooked-up in this way through finally one single bean.

The configuration of the &lt;http&gt; element plays a central-role in the definition of what the filter-chain is made of. It is directly the elements it defines, that create the related filters.

这里，springSecurityFilterChain也是由Spring Security命名空间（特别是http组件）内部创建的Spring bean。 DelegatingFilterProxy是一个Spring基础结构，它在应用程序上下文中查找特定的bean并调用它。 目标bean必须实现Filter接口。

整个Spring Security机器以这种方式通过终止一个单一的bean。

&lt;http&gt;元件的配置在定义过滤器链是什么中起着中心作用。 它直接是它定义的元素，创建相关的过滤器。

"Some core filters are always created in a filter chain and others will be added to the stack depending on the attributes and child elements which are present."

“一些核心筛选器总是在筛选器链中创建，而其他核心筛选器将根据存在的属性和子元素添加到堆栈中。”

It is important to distinguish between the configuration-dependant filters and the core filters that cannot be removed. As core filters, we can count SecurityContextPersistenceFilter, ExceptionTranslationFilter, and FilterSecurityInterceptor. These three filters are natively bound to the &lt;http&gt; element and can be found in the next table.

区分配置相关的过滤器和无法删除的核心过滤器很重要。 作为核心过滤器，我们可以计算SecurityContextPersistenceFilter，ExceptionTranslationFilter和FilterSecurityInterceptor。 这三个过滤器本地绑定到&lt;http&gt;元素，可以在下表中找到。

This table comes from the Spring Security reference document and it contains all the core filters \(coming with the framework\) that can be activated using specific elements or attributes. They are listed here in the order of their position in the chain.

此表来自Spring Security参考文档，它包含所有可以使用特定元素或属性激活的核心筛选器（随框架一起提供）。 它们在这里按它们在链中的位置的顺序列出。

![](/assets/86.png)

Remember that custom filters can be positioned relatively, or can replace any of these filters using the custom-filter element:

请记住，自定义过滤器可以相对定位，或者可以使用`custom-filter`元素替换以下任何过滤器：

`<security:custom-filter ref="myFilter" after="BASIC_AUTH_FILTER"/>`

Our &lt;http&gt; configuration

We have defined the following configuration for the &lt;http&gt; 'namespace's component:

我们为&lt;http&gt;'命名空间的组件定义了以下配置：

```
<security:http create-session="stateless" entry-pointref="authenticationEntryPoint" authentication-managerref="authenticationManager">
    <security:custom-filter ref="basicAuthenticationFilter" after="BASIC_AUTH_FILTER" />
    <security:csrf disabled="true"/>
</security:http>
<bean id="basicAuthenticationFilter" class="org.sfw.security.web.authentication.www.BasicAuthenticationFilter">
    <constructor-arg name="authenticationManager" ref="authenticationManager" />
    <constructor-arg name="authenticationEntryPoint" ref="authenticationEntryPoint" />
</bean>
<bean id="authenticationEntryPoint" class="edu.zc.csm.api.authentication.CustomBasicAuthenticationEntryPoint">
    <property name="realmName" value="${realm.name}" />
</bean>
```

Here, we tell Spring not to create sessions and to ignore incoming sessions using create-session=="stateless". We have done this to pursue the stateless and scalable Microservices design.

We have also disabled the **Cross-Site Request Forgery \(csrf\)** support for now, for the same reason. This feature has been enabled by default by the framework since the Version 3.2.

在这里，我们告诉Spring不要创建会话，并使用`create-session ==“stateless”`忽略传入会话。 我们这样做是为了追求无状态和可扩展的微服务设计。

由于同样的原因，我们现在也禁用了**Cross-Site Request Forgery \(csrf\)**支持。 默认情况下，自3.2版本起，框架已启用此功能。

It has been necessary to define an entry-point-ref because we didn't implement any authentication strategy preconfigured by the namespace \(http-basic or login-form\).

We have defined a custom filter BasicAuthenticationFilter to be executed after the theoretical position of the core BASIC\_AUTH\_FILTER.

We are now going to see which roles play the three references made to: authenticationEntryPoint, authenticationManager, and basicAuthenticationFilter.

有必要定义一个entry-point-ref，因为我们没有实现任何由命名空间（http-basic或login-form）预先配置的认证策略。

我们定义了一个自定义过滤器BasicAuthenticationFilter，在核心BASIC\_AUTH\_FILTER的理论位置之后执行。

我们现在将看到哪些角色扮演三个引用：authenticationEntryPoint，authenticationManager和basicAuthenticationFilter。

### The AuthenticationManager interface

First of all, AuthenticationManager is a single-method interface:

首先，AuthenticationManager是一个单方法接口：

```
public interface AuthenticationManager {
    Authentication authenticate(Authentication authentication) throws AuthenticationException;
}
```

Spring Security provides one implementation: ProviderManager. This implementation allows us to plug in several AuthenticationProviders. The ProviderManager tries all the AuthenticationProviders in order, calling their authenticate method. The code is as follows:

Spring Security提供了一个实现：ProviderManager。 这个实现允许我们插入几个AuthenticationProvider。  ProviderManager按顺序尝试所有AuthenticationProviders，调用它们的authenticate方法。 代码如下：

```
public interface AuthenticationProvider {
    Authentication authenticate(Authentication authentication) throws AuthenticationException;
    boolean supports(Class<?> authentication);
}
```

The ProviderManager stops its iteration when it finds a non-null Authentication object.Alternatively, it fails the Authentication when an AuthenticationException is thrown.

Using the namespace, a specific AuthenticationProviders can be targeted using the ref element as shown here:

当发现一个非空的Authentication对象时，ProviderManager停止它的迭代。或者，当抛出一个AuthenticationException时，它会失败。

使用命名空间，可以使用ref元素来定位特定的AuthenticationProviders，如下所示：

```
<security:authentication-manager >
    <security:authentication-provider ref='myAuthenticationProvider'/>
</security:authentication-manager>
```

Now, here is our configuration:

```
<security:authentication-manager alias"="authenticationManager"">
    <security:authentication-provider user-serviceref='communityServiceImpl'>
        <security:password-encoder ref="passwordEncoder"/>
    </security:authentication-provider>
</security:authentication-manager>
```

There is no ref element in our configuration. The namespace will by default instantiate a DaoAuthenticationProvider. It will also inject our UserDetailsService implementation: communityServiceImpl, because we have specified it with userservice-ref.

This DaoAuthenticationProvider throws an AuthenticationException when the password submitted in a UsernamePasswordAuthenticationToken doesn't match the one which is loaded by UserDetailsService \(making use of the loadUserByUsername method\).

在我们的配置中没有ref元素。 命名空间将默认实例化DaoAuthenticationProvider。 它也将注入我们的UserDetailsS​​ervice实现：communityServiceImpl，因为我们已经指定了userservice-ref。

当在UsernamePasswordAuthenticationToken中提交的密码与由UserDetailsS​​ervice加载的密码（使用loadUserByUsername方法）不匹配时，此DaoAuthenticationProvider会抛出AuthenticationException。

It exists a few other AuthenticationProviders that could be used in our projects, for example,, RememberMeAuthenticationProvider, LdapAuthenticationProvider, CasAuthenticationProvider, or JaasAuthenticationProvider.

它存在一些其他AuthenticationProviders，可以在我们的项目中使用，例如，RememberMeAuthenticationProvider，LdapAuthenticationProvider，CasAuthenticationProvider或JaasAuthenticationProvider。

### Basic authentication

As we have said using a BASIC scheme is a great technique for REST applications. However when using it, it is critical to use an encrypted communication protocol \(HTTPS\) as the passwords are sent in plain text.

As demonstrated in the How to do it section, the principle is very simple. The HTTP requests are the same as usual with an extra header Authentication. This header has the value made of the keyword Basic followed by a space, followed by a String encoded in base 64.

We can find online a bunch of free services to quickly encode/decode in base 64 a String. The String to be encoded in base 64 has to be in the following form: &lt;username&gt;:&lt;password&gt;.

基本认证

正如我们所说的使用BASIC方案是REST应用程序的一个伟大的技术。 但是，当使用它时，使用加密通信协议（HTTPS）是至关重要的，因为密码以纯文本发送。

如“如何做到”部分所示，原理非常简单。 HTTP请求与通常一样具有额外的头部认证。 此标题具有由关键字Basic后跟一个空格组成的值，后跟一个在base 64中编码的String。

我们可以在网上找到一堆免费服务来快速编码/解码在base 64 a String。 要在base 64中编码的字符串必须采用以下格式：`<username>：<password>`。

### BasicAuthenticationFilter

To implement our Basic authentication, we have added BasicAuthenticationFilter to our filter chain. This BasicAuthenticationFilter \(org.sfw.security.web.authentication.www.BasicAuthenticationFilter\) requires an

authenticationManager and optionally an authenticationEntryPoint.

The optional configuration of an authenticationEntryPoint drives the filter towards two different behaviours presented next.

Both starts the same way: the filter is triggered from its position in the chain. It looks for the authentication header in the request and delegates to the authenticationManager,which then relies on the UserDetailsService implementation to compare it with the user credentials from the database.

为了实现我们的基本认证，我们在我们的过滤器链中添加了BasicAuthenticationFilter。 此BasicAuthenticationFilter\(org.sfw.security.web.authentication.www.BasicAuthenticationFilter\)需要

authenticationManager和可选的authenticationEntryPoint。

authenticationEntryPoint的可选配置将过滤器驱动为接下来呈现的两种不同行为。

两者以相同的方式开始：过滤器从其在链中的位置触发。 它在请求中查找认证头并委托给authenticationManager，然后authenticationManager依靠UserDetailsService实现将其与来自数据库的用户凭证进行比较。

### With an authenticationEntryPoint

This is our configuration, which behaves in the following way:

* When the authentication succeeds, the filter-chain stops, and an Authentication object is returned.

* When the authentication fails, the authenticationEntryPoint method is invoked in an interruption of the filter-chain. Our authentication entry-point sets a custom WWW-Authenticate response header and a 401 status-code \(FORBIDDEN\).

This type of configuration provides a preauthentication where the Authentication Header in the HTTP Request is checked to see whether or not the business services require an authorization \(Secure Object\).

This configuration allows a quick feedback with a potential native BASIC form prompted by the web browser. We have chosen this configuration for now in our application.

这是我们的配置，其行为如下：

* 当认证成功时，过滤器链停止，并返回一个Authentication对象。

* 当认证失败时，在过滤器链的中断中调用authenticationEntryPoint方法。 我们的认证入口点设置custom WWW-Authenticate response header和401状态码（FORBIDDEN）。

此类型的配置提供了预身份验证，其中检查HTTP请求中的认证头以查看业务服务是否需要授权（安全对象）。

此配置允许通过Web浏览器提示的潜在本地BASIC表单的快速反馈。 我们现在在我们的应用程序中选择了此配置。

### Without an authenticationEntryPoint

Without an authenticationEntryPoint, the filter behaves as follows:

* When the authentication succeeds, the filter-chain stops, and an Authentication object is returned.

* When the authentication fails, the filter chain continues. After that, if another authentication succeeds in the chain, the user is authenticated accordingly. But, if no other authentication succeeds in the chain, then the user is authenticated with an anonymous role and this may or may not suit the services access levels.

如果没有authenticationEntryPoint，过滤器的行为如下：

* 当认证成功时，过滤器链停止，并返回一个Authentication对象。

* 当认证失败时，过滤器链继续。 之后，如果另一个认证在链中成功，则是相应地认证用户。 但是，如果链中没有其他认证成功，则用户使用匿名角色进行认证，并且这可能或可能不适合服务访问级别。

## There is more…

### In the Spring Security reference

This section has been largely inspired from the Spring rsecurity reference, which is again a great resource:
本节主要是从Spring rsecurity引用，这也是一个伟大的资源：

http://docs.spring.io/spring-security/site/docs/current/reference/htmlsingle
An appendix provides a very complete guide to the Spring Security namespace:
附录提供了Spring Security命名空间的非常完整的指南：

http://docs.spring.io/spring-security/site/docs/current/reference/html/appendix-namespace.html
### The remember-me cookie/feature

We passed over the RememberMeAuthenticationFilter that provides different ways for the server to remember the identity of a Principal between sessions. The Spring Security reference provides extensive information on this topic.
我们通过了RememberMeAuthenticationFilter，它为服务器提供了不同的方式来记住会话之间Principal的身份。 Spring Security参考提供了有关此主题的大量信息。





