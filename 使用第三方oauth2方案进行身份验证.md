This recipe uses the Spring social project in order to use the OAuth2 protocol from a client perspective.

此配方使用Spring social 项目，以便从客户端角度使用OAuth2协议。

## Getting ready

We won't create an **OAuth2 Authentication Server \(AS\)** here. We will establish connections to third-party Authentication servers \(Yahoo!\) to authenticate on our application. Our application will be acting as a **Service Provider \(SP\)**.

我们不会在此创建**OAuth2 Authentication Server \(AS\)**。 我们将建立与第三方认证服务器（Yahoo!）的连接，以在我们的应用程序上进行身份验证。 我们的应用程序将充当**Service Provider \(SP\)**。

We will use Spring social whose first role is to manage social connections transparently and to provide a facade to invoke the provider APIs \(Yahoo! Finance\) using Java objects.

我们将使用Spring social ，其第一个角色是透明地管理social 连接，并提供一个场面以使用Java对象调用提供程序API（Yahoo! Finance）。

## How to do it...

1.Two Maven dependencies have been added for Spring social:

1.为Spring social添加了两个Maven依赖项：

```js
<!– Spring Social Core –>
<dependency>
    <groupId>org.springframework.social</groupId>
    <artifactId>spring-social-core</artifactId>
    <version>1.1.0.RELEASE</version>
</dependency>
<!– Spring Social Web (login/signup controllers) –>
<dependency>
    <groupId>org.springframework.social</groupId>
    <artifactId>spring-social-web</artifactId>
    <version>1.1.0.RELEASE</version>
</dependency>
```

2.If we want to handle an OAuth2 connection to Twitter or Facebook, we would have to add the following dependencies as well:

2.如果我们要处理到Twitter或Facebook的OAuth2连接，我们还必须添加以下依赖关系：

```js
<!– Spring Social Twitter –>
<dependency>
    <groupId>org.springframework.social</groupId>
    <artifactId>spring-social-twitter</artifactId>
    <version>1.1.0.RELEASE</version>
</dependency>
<!– Spring Social Facebook –>
<dependency>
    <groupId>org.springframework.social</groupId>
    <artifactId>spring-social-facebook</artifactId>
    <version>1.1.0.RELEASE</version>
</dependency>
```

3.After the BASIC authentication section, the Spring Security configuration file hasn't changed much. A few interceptors can be noticed in the http bean:

3.在BASIC认证部分之后，Spring Security配置文件没有太大变化。 一些拦截器可以在http bean中注意到：

```js
<security:http create-session="stateless" entry-pointref="authenticationEntryPoint" authentication-managerref="authenticationManager">
    <security:custom-filter ref="basicAuthenticationFilter" after="BASIC_AUTH_FILTER" />
    <security:csrf disabled="true"/>
    <security:intercept-url pattern="/signup" access="permitAll"/>
    ...
    <security:intercept-url pattern="/**" access="permitAll"/>
</security:http>
```

With the following SocialUserConnectionRepositoryImpl, we have created our own implementation of org.sfw.social.connect.ConnectionRepository,  
which is a Spring Social core interface with methods to manage the social-users connections. The code is as follows:

通过以下SocialUserConnectionRepositoryImpl，我们创建了我们自己的org.sfw.social.connect.ConnectionRepository实现，它是一个Spring Social核心接口，具有管理social-users connections的方法。 代码如下：

```java
@Transactional(propagation = Propagation.REQUIRED)
@SuppressWarnings("unchecked")
public class SocialUserConnectionRepositoryImpl implements ConnectionRepository {

    @Autowired
    private SocialUserRepository socialUserRepository;

    private final String userId;
    private final ConnectionFactoryLocator connectionFactoryLocator;
    private final TextEncryptor textEncryptor;

    public SocialUserConnectionRepositoryImpl(String userId,SocialUserRepository socialUserRepository,
                ConnectionFactoryLocator connectionFactoryLocator,TextEncryptor textEncryptor){

        this.socialUserRepository = socialUserRepository;
        this.userId = userId;
        this.connectionFactoryLocator = connectionFactoryLocator;
        this.textEncryptor = textEncryptor;
}
    ...
    public void addConnection(Connection<?> connection) {
        try {
        ConnectionData data = connection.createData();
        int rank = socialUserRepository.getRank(userId, data.getProviderId()) ;
        socialUserRepository.create(userId,data.getProviderId(),data.getProviderUserId(),
                    rank, data.getDisplayName(),data.getProfileUrl(),data.getImageUrl(),
                    encrypt(data.getAccessToken()),encrypt(data.getSecret()),
                    encrypt(data.getRefreshToken()),data.getExpireTime() );

        } catch (DuplicateKeyException e) {
            throw new DuplicateConnectionException(connection.getKey());
        } 
    }
    ...
    public void removeConnections(String providerId) {
        socialUserRepository.delete(userId,providerId);
    }
    ...
}
```

> In reality, this custom implementation extends and adapts the work from [https://github.com/mschipperheyn/spring-social-jpa](https://github.com/mschipperheyn/spring-social-jpa) published under a GNU GPL license.
>
> 实际上，这种自定义实现扩展和适应了根据GNU GPL许可证发布的[https://github.com/mschipperheyn/spring-social-jpa的工作。](https://github.com/mschipperheyn/spring-social-jpa的工作。)

4.As you can see, SocialUserConnectionRepositoryImpl makes use of a custom Spring Data JPA SocialUserRepository interface whose definition is as follows:

4.你可以看到，SocialUserConnectionRepositoryImpl使用一个自定义的Spring Data JPA SocialUserRepository接口，其定义如下：

```java
public interface SocialUserRepository {
    List<SocialUser> findUsersConnectedTo(String providerId);
    ...
    List<String> findUserIdsByProviderIdAndProviderUserIds(String providerId, Set<String> providerUserIds);
    ...
    List<SocialUser> getPrimary(String userId, String providerId);
    ...
    SocialUser findFirstByUserIdAndProviderId(String userId, String providerId);
}
```

5.This Spring Data JPA repository supports a SocialUser entity \(social connections\) that we have created. This Entity is the direct model of the UserConnection SQL table that JdbcUsersConnectionRepository would expect to find if we would use this implementation rather than ours. The SocialUser definition is the following code:

5.这个Spring Data JPA仓库支持我们创建的SocialUser实体\(social connections\)。 这个实体是JConfigUsersConnectionRepository期望找到的UserConnection SQL表的直接模型，如果我们使用这个实现而不是我们的话。 SocialUser定义如下代码：

```java
@Entity
@Table(name="userconnection", uniqueConstraints ={@UniqueConstraint(columnNames = { ""userId", "providerId","providerUserId" }),
@UniqueConstraint(columnNames = { "userId", "providerId","rank" })})
public class SocialUser {
    @Id
    @GeneratedValue
    private Integer id;

    @Column(name = "userId")
    private String userId;

    @Column(nullable = false)
    private String providerId;

    private String providerUserId;

    @Column(nullable = false)
    private int rank;

    private String displayName;

    private String profileUrl;

    private String imageUrl;

    @Lob
    @Column(nullable = false)
    private String accessToken;

    private String secret;

    private String refreshToken;

    private Long expireTime;

    private Date createDate = new Date();
    //+ getters / setters
    ...
}
```

6.The SocialUserConnectionRepositoryImpl is instantiated in a higher-level service layer: SocialUserServiceImpl, which is an implementation of the Spring UsersConnectionRepository interface. This implementation is created as follows:

6.SocialUserConnectionRepositoryImpl在更高级别的服务层实例化：SocialUserServiceImpl，它是Spring UsersConnectionRepository接口的一个实现。 此实现创建如下：

```java
@Transactional(readOnly = true)
public class SocialUserServiceImpl implements SocialUserService {

    @Autowired
    private SocialUserRepository socialUserRepository;
    @Autowired
    private ConnectionFactoryLocator connectionFactoryLocator;
    @Autowired
    private UserRepository userRepository;

    private TextEncryptor textEncryptor = Encryptors.noOpText();

    public List<String> findUserIdsWithConnection(Connection<?> connection) {
        ConnectionKey key = connection.getKey();
        return socialUserRepository.findUserIdsByProviderIdAndProviderUserId(key.getProviderId(), key.getProviderUserId());
    }

    public Set<String> findUserIdsConnectedTo(String providerId, Set<String> providerUserIds) {
        return Sets.newHashSet(socialUserRepository.findUserIdsByProviderIdAndProviderUserIds(providerId,providerUserIds));
    }

    public ConnectionRepository createConnectionRepository(String userId) {
        if (userId == null) {
            throw new IllegalArgumentException("userId cannot be null");
        }
        return new SocialUserConnectionRepositoryImpl(userId,socialUserRepository,connectionFactoryLocator,textEncryptor);
    }
...
}
```

7.This higher level SocialUserServiceImpl is registered in the cloudstreetmarket-api Spring configuration file \(dispatchercontext.xml\) as a factory-bean that has the capability to produce SocialUserConnectionRepositoryImpl under a request-scope \(for a specific social-user profile\). The code is as follows:

7.这个更高级别的SocialUserServiceImpl在cloudstreetmarket-api Spring配置文件（dispatchercontext.xml）中注册为工厂bean，具有在请求范围（针对特定社交用户配置文件）生成SocialUserConnectionRepositoryImpl的能力。 代码如下：

```js
<bean id="usersConnectionRepository" class="edu.zc.csm.core.services.SocialUserServiceImpl"/>
<bean id="connectionRepository" factorymethod="createConnectionRepository" factorybean="usersConnectionRepository" scope"="request">
    <constructor-arg value="#{request.userPrincipal.name}"/>
    <aop:scoped-proxy proxy-target-class"="false"" />
</bean>
```

8.Three other beans are defined in this dispatcher-context.xml file:

8.此dispatcher-context.xml文件中定义了三个其他bean：

```js
<bean id="signInAdapter" class="edu.zc.csm.api.signin.SignInAdapterImpl"/>
    <bean id="connectionFactoryLocator" class="org.sfw.social.connect.support.ConnectionFactoryRegistry">
    <property name="connectionFactories">
        <list>
            <bean class"="org.sfw.social.yahoo.connect.YahooOAuth2ConnectionFactory"">
                <constructor-arg value="${yahoo.client.token}"/>
                <constructor-arg value="${yahoo.client.secret}" />
                <constructor-arg value="${yahoo.signin.url}" />
            </bean>
        </list>
    </property>
</bean>

<bean class="org.sfw.social.connect.web.ProviderSignInController">
    <constructor-arg ref="connectionFactoryLocator"/>
    <constructor-arg ref="usersConnectionRepository"/>
    <constructor-arg ref="signInAdapter"/>
    <property name="signUpUrl" value="/signup"/>
    <property name="postSignInUrl" value="${frontend.home.page.url}"/>
</bean>
```

9.The The SignInAdapterImpl signs in a user in our application after the OAuth2 authentication. It performs what we want it to perform at this step from the application business point of view. The code is as follows:

9.在OAuth2身份验证之后，SignInAdapterImpl在我们的应用程序中的用户中签名。 它从应用程序业务角度执行我们希望它在此步骤执行的操作。 代码如下：

```java
@Transactional(propagation = Propagation.REQUIRED)
@PropertySource("classpath:application.properties")
public class SignInAdapterImpl implements SignInAdapter{

    @Autowired
    private UserRepository userRepository;
    @Autowired
    private CommunityService communityService;
    @Autowired
    private SocialUserRepository socialUserRepository;

    @Value("${oauth.success.view}")
    private String successView;

    public String signIn(String userId, Connection<?> connection, NativeWebRequest request) {

        User user = userRepository.findOne(userId);
        String view = null;
        if(user == null){
        //Spring Security的临时用户
        //将不会被持久化
        user = new User(userId, communityService.generatePassword(),
                    null,true, true, true, true,
                    communityService.createAuthorities(new Role[]{Role.ROLE_BASIC, Role.ROLE_OAUTH2}));
        }else{
            //我们以前有一个成功的oAuth
            //认证
            //用户已注册
            //只有guid被发回
            List<SocialUser> socialUsers = socialUserRepository.findByProviderUserIdOrUserId(userId, userId);

            if(CollectionUtils.isNotEmpty(socialUsers)){
                //现在我们只处理Yahoo!
                view = successView.concat("?spi=" + socialUsers.get(0).getProviderUserId());
            }
        }
        communityService.signInUser(user);
        return view;
    }
}
```

10.The `connectionFactoryLocator` can also refer to more than one connection factories. In our case, we have only one: `YahooOAuth2ConnectionFactory`. These classes are the entry points of social providers APIs \(written for Java\). We can normally find them on the web \(from official sources or not\) for the OAuth protocol we target \(OAuth1, OAuth1.0a, and OAuth2\).

10.`connectionFactoryLocator`也可以引用多个连接工厂。 在我们的例子中，我们只有一个：`YahooOAuth2ConnectionFactory`。 这些类是social providers API（为Java编写）的入口点。 我们通常可以在网络上（从官方来源或不是官方来源）找到我们定位的OAuth协议（OAuth1，OAuth1.0a和OAuth2）。

> There are few existing OAuth2 adaptors right now for Yahoo! We've had to do it ourselves. That's why these classes are available as sources and not as jar dependencies \(in the Zipcloud project\).
>
> 现在很少有适用于Yahoo!的现有OAuth2适配器。 这就是为什么这些类可用作源，而不是作为jar依赖（在Zipcloud项目中）。

11.When it comes to controllers' declarations, the dispatcher-context.xml configures a ProviderSignInController, which is completely abstracted in Spring Social Core. However, to register a OAuth2 user in our application \(the first time the user visits the site\), we have created a custom SignUpController:

11.当涉及到控制器的声明时，dispatcher-context.xml配置一个ProviderSignInController，它在Spring Social Core中是完全抽象的。 但是，要在我们的应用程序中注册OAuth2用户（用户第一次访问网站时），我们创建了一个自定义的SignUpController：

```java
@Controller
@RequestMapping"("/signup"")
@PropertySource"("classpath:application.properties"")
public class SignUpController extends CloudstreetApiWCI{

    @Autowired
    private CommunityService communityService;
    @Autowired
    private SignInAdapter signInAdapter;
    @Autowired
    private ConnectionRepository connectionRepository;

    @Value("${oauth.signup.success.view}")
    private String successView;

    @RequestMapping(method = RequestMethod.GET)
    public String getForm(NativeWebRequest request,@ModelAttribute User user) {

        String view = successView;
        // 检查是否是通过新用户登录
        //Spring Social
        Connection<?> connection = ProviderSignInUtils.getConnection(request);
        if (connection != null) {
            // 从social connection填充新用户
            //用户资料
            UserProfile userProfile = connection.fetchUserProfile();
            user.setUsername(userProfile.getUsername());
            // 完成social 注册/登录
            ProviderSignInUtils.handlePostSignUp(user.getUsername(),request);
            // 签署用户并将其发送给用户
            //主页
            signInAdapter.signIn(user.getUsername(),connection, request);
            view += ?spi=+ user.getUsername();
        }
        return view;
    }
}
```

12.It's time to try it now. To proceed, we suggest you to create a Yahoo! account. We are not actually sponsored by Yahoo! It is only for the strategy of our great Zipcloud company which is oriented on financial services. It is not only for Marissa Mayer's blue eyes! \([https://login.yahoo.com\](https://login.yahoo.com\)\).

13.Start your Tomcat server and click on the login button \(at the far right of the main menu\). Then hit the **Sign-in with Yahoo! **button

14.You should be redirected to the Yahoo! servers in order for you to authenticate on their side \(if you are not logged-in already\):

12.现在是时候试试吧。 要继续，我们建议你创建一个Yahoo!帐户。 我们实际上不是由雅虎赞助它只是为我们伟大的Zipcloud公司的战略，以金融服务为主。 这不仅是玛丽莎·梅尔的蓝眼睛！  （https//login.yahoo.com）。

13.启动Tomcat服务器，然后单击login按钮（位于主菜单的最右侧）。 然后点击**Sign-in with Yahoo!**按钮

14.你应该被重定向到Yahoo!服务器，以便您在其身边进行身份验证（如果您尚未登录）：

![](/assets/87.png)

15.Once logged-in, agree that Cloudstreet Market will be able to access your profile and your contacts. We won't make use of contacts; however, we have the Java adaptors to access them. If it's too scary, just create an empty new Yahoo! account:

15.登录后，同意CloudStreet Market将能够访问你的个人资料和你的联系人。 我们不会使用联系人; 但是，我们有Java适配器来访问它们。 如果太害怕，只需创建一个空的新的Yahoo!帐户：

![](/assets/88.png)

16.Click on the **Agree **button.

17.Yahoo! should now redirect to the local cloudstreetmarket.com server and specifically to the /api/signin/yahoo handler with an authorization code as URL parameter.

18.The application detects when in the Cloudstreet Market database there isn't any User registered for the SocialUser. This triggers the following popup and it should come back to the user until the account actually gets created:

16.单击**Agree **按钮。

17.Yahoo!现在应该重定向到本地cloudstreetmarket.com服务器，具体到/ api / signin / yahoo处理程序，并使用授权代码作为URL参数。

18.应用程序检测CloudStreet Market database中何时没有为SocialUser注册的用户。 这将触发以下弹出窗口，它应该回到用户，直到帐户实际创建：

![](/assets/89.png)

19.Fill the form with the following data:

19.向表单填写以下数据：

```java
username: <marcus>
email: <marcus@chapter5.com>
password: <123456>
preferred currency: <USD>
```

Also, click on the user icon in order to upload a profile picture \(if you wish\). While doing so, make sure the property pictures.user.path in cloudstreetmarketapi/src/main/resources/application.properties is pointing to a created path on the filesystem.

此外，点击用户图标为了上传个人资料图片（如果你愿意）。 在这样做时，请确保cloudstreetmarketapi / src / main / resources / application.properties中的属性pictures.user.path指向文件系统上创建的路径。

20.Once this step is done, the new public activity **Marcus registers a new account** should appear on the welcome page.

20.一旦这一步骤完成，新的公共活动**Marcus registers a new account**应该出现在欢迎页面。

21.Also, bound to each REST response from the API, the extra-headers **Authenticated **and **WWW-Authenticate** must be present. This is proof that we are authenticated with OAuth2 capability in the application.

21.此外，绑定到来自API的每个REST响应，必须存在extra-headers **Authenticated**和**WWW-Authenticate**。 这是我们在应用程序中使用OAuth2功能进行身份验证的证明。

![](/assets/90.png)

## How it works...

We perform in this recipe a social integration within our application. An OAuth2 authentication involves a service provider \(cloudstreetmarket.com\) and an identity provider \(Yahoo!\).

This can only happen if a user owns \(or is ready to own\) an account on both parties. It is a very popular authentication protocol nowadays. As most of Internet users have at least one account in one of the main Social SaaS providers \(Facebook, Twitter, LinkedIn, Yahoo!, and so on\), this technique dramatically drops the registration time and login time spent on web service providers.

我们在这个谱方中执行我们的应用程序中的social 整合。  OAuth2身份验证涉及服务提供商（cloudstreetmarket.com）和身份提供商（Yahoo!）。

这只有在用户拥有（或准备拥有）双方的帐户时才会发生。 这是一种非常受欢迎的认证协议。 由于大多数互联网用户在一个主要的社交SaaS提供商（Facebook，Twitter，LinkedIn，Yahoo！等）中至少有一个帐户，这种技术大大减少了在Web服务提供商上花费的注册时间和登录时间。

### From the application point of view

When the sign in with Yahoo! button is clicked by the user, a HTTP POST request is made to one of our API handler /api/signin/yahoo. This handler corresponds to ProviderSignInController, which is abstracted by Spring Social.

从应用的角度来看

当用户点击使用Yahoo!按钮登录时，会向我们的API处理程序/ api / signin / yahoo之一发出HTTP POST请求。 此处理程序对应于ProviderSignInController，它由Spring Social抽象。

* This handler redirects the user to the Yahoo! servers where he can authenticate and give the right for the application to use his social identity and access some of his Yahoo! data.

* Yahoo! sends an authorization-code to the application as a parameter of the redirection to the callback URL it performs.

* The application processes the callback with the authorization code as parameter. This callback targets a different method-handler in the abstracted ProviderSignInController. This handler completes the connection recalling Yahoo! in order to exchange the authorization code against a **refresh token** and an **access token**. This operation is done transparently in the Spring Social background.

* The same handler looks-up in database for an existing persisted social connection for that user:

* [ ] If one connection is found, the user is authenticated with it in Spring Security and redirected to the home page of the portal with the Yahoo! user-ID as request parameter \(parameter named spi\).

* [ ]  If no connection is found, the user is redirected to the SignupController where his connection is created and persisted. He is then authenticated in Spring Security and redirected to the portal's home page with the Yahoo! user ID as request parameter \(named spi\).

* When the portal home page is loaded, the Yahoo! user ID request parameter is detected and this identifier is stored in the HTML5 sessionStorage \(we have done all this\).

* From now on, in every single AJAX request the user makes to the API, the spi identifier will be passed as a request header, until the user actually logs out or closes his browser.

* 此处理程序将用户重定向到Yahoo!服务器，在那里他可以进行身份​​验证，并赋予应用程序使用其社交身份并访问其某些Yahoo!数据的权限。

* Yahoo!向应用程序发送授权码，作为重定向到其执行的回调URL的参数。

* 应用程序使用授权代码作为参数处理回调。 此回调的目标是抽象的ProviderSignInController中的一个不同的方法处理程序。 此处理程序完成连接调用Yahoo!以便将授权代码与**刷新令牌**和**访问令牌**交换。 此操作在Spring Social背景中透明地完成。

* 同一处理程序在数据库中查找该用户的现有持久性social connection

* [ ] 如果找到一个连接，则用户在Spring Security中使用它进行身份验证，并以Yahoo! user-ID作为请求参数（名为spi的参数）重定向到门户的主页。

* [ ] 如果未找到连接，则将用户重定向到SignupController，在那里创建并保留其连接。 然后，他在Spring Security中进行身份验证，并重定向到门户网站的主页，将Yahoo!用户ID作为请求参数（名为spi）。

* 加载门户网站主页时，会检测到Yahoo!用户ID请求参数，并将此标识符存储在HTML5会话存储器中（我们已完成所有这些操作）。

* 从现在起，在用户对API的每个单个AJAX请求中，spi标识符将作为请求头传递，直到用户实际注销或关闭其浏览器。

### From the Yahoo! point of view

The Yahoo! APIs provide two ways of authenticating with OAuth2. This induces two different flows: the Explicit OAuth2 flow, suited for a server-side \(web\) application and the Implicit OAuth2 flow that particularly benefits to frontend web clients. We will focus on the implemented explicit flow here.  
从雅虎的角度来看

Yahoo! API提供了两种使用OAuth2进行身份验证的方法。 这导致两个不同的流：显式OAuth2流，适合于服务器侧（web）应用和隐式OAuth2流，其特别有益于前端web客户端。 我们将在这里集中于实现的显式流。

### OAuth2 explicit grant flow

Here's a summary picture of the communication protocol between our application and Yahoo!.This is more or less a standard OAuth2 conversation:

OAuth2显式授权流

这里是我们的应用程序和雅虎之间的通信协议的摘要图片。这或多或少是标准的OAuth2对话：

![](/assets/91.png)

The parameters marked with the \* symbol are optional in the communication. This flow is also detailed on the OAuth2 Yahoo! guide:

用\*符号标记的参数在通信中是可选的。 此流程也在OAuth2 Yahoo！指南上详细说明：

[https://developer.yahoo.com/oauth2/guide/flows\\_authcode](https://developer.yahoo.com/oauth2/guide/flows\_authcode)

### Refresh-token and access-token

The difference between these two tokens must be understood. An access-token is used to identify the user \(Yahoo! user\) when performing operations on the Yahoo! API. As an example, below is a GET request that can be performed to retrieve the Yahoo! profile of a user identified by the Yahoo! ID abcdef123:

刷新令牌和访问令牌

这两个令牌之间的区别必须理解。 访问令牌用于在对Yahoo! API执行操作时标识用户（Yahoo!用户）。 作为示例，以下是可以执行以获取由Yahoo! ID abcdef123标识的用户的Yahoo!简档的GET请求：

```java
GET https://social.yahooapis.com/v1/user/abcdef123/profile
Authorization: Bearer aXJUKynsTUXLVY
```

To provide identification to this call, the** access-token** must be passed in as the value of the Authorization request header with the Bearer keyword. In general, access-tokens have a very limited life \(for Yahoo!, it is an hour\).

A refresh-token is used to request new access-tokens. Refresh-tokens have much longer lives\(for Yahoo!, they actually never expire, but they can be revoked\).

为了向该调用提供标识，必须将访问令牌作为具有Bearer关键字的授权请求头的值传递。 一般来说，访问令牌有非常有限的生命（对于雅虎，它是一个小时）。

刷新令牌用于请求新的访问令牌。 刷新令牌有更长的寿命（对于雅虎，他们实际上永远不会过期，但他们可以撤销）。

### Spring social – role and key features

The role of Spring social is to establish connections with Software-as-a-Service \(SaaS\) providers such as Facebook, Twitter, or Yahoo! Spring social is also responsible for invoking APIs on the application \(Cloudstreet Market\) server side on behalf of the users.

These two duties are both served in the spring-social-core dependency using the Connect Framework and the OAuth client support, respectively.

In short, Spring social is:

* A Connect Framework handling the core authorization and connection flow with service providers

* A Connect Controller that handles the OAuth exchange between a service provider, consumer, and user in a web application environment

* A Sign-in Controller that allows users to authenticate in our application, signing in with their Saas provider account

Spring social 的作用是与诸如Facebook，Twitter或Yahoo!之类的Software-as-a-Service \(SaaS\)提供商建立连接。Spring social 也负责在应用程序（Cloudstreet Market）服务器端代表用户。

这两个职责分别在使用Connect框架和OAuth客户端支持的spring-social-core依赖中提供。

总之，Spring social是：

* Connect框架处理与服务提供商的核心授权和连接流

* Connect控制器，用于处理Web应用程序环境中服务提供者，使用者和用户之间的OAuth交换

* 登录控制器，允许用户在我们的应用程序中进行身份验证，并使用他们的SaaS提供商帐户登录

### Social connection persistence

The Spring social core provides classes able to persist social connections in database using JDBC \(especially with JdbcUsersConnectionRepository\). The module even embeds a SQL script for the schema definition:  
social connections持久性

Spring social core 提供了能够使用JDBC（尤其是使用JdbcUsersConnectionRepository）在数据库中social connections连接的类。 模块甚至嵌入用于模式定义的SQL脚本：

```java
create table UserConnection (userId varchar(255) not null,
    providerId varchar(255) not null,
    providerUserId varchar(255),
    rank int not null,
    displayName varchar(255),
    profileUrl varchar(512),
    imageUrl varchar(512),
    accessToken varchar(255) not null,
    secret varchar(255),
    refreshToken varchar(255),
    expireTime bigint,
    primary key (userId, providerId, providerUserId));
create unique index UserConnectionRank on UserConnection(userId,providerId, rank);
```

When an application \(like ours\) uses JPA, an Entity can be created to represent this table in the persistence context. We have created the SocialUser Entity for this purpose in the sixth step of the recipe.

当一个应用程序（如我们的）使用JPA时，可以创建一个Entity来在持久化上下文中表示这个表。 我们在配方的第六步中为此创建了SocialUser实体。

In this table Entity, you can see the following fields:

* userId: This field matches the `@Id (username)` of the User when the user is registered. If the user is not yet registered, userId is the GUID \(Yahoo! user ID,also called spi on the web side\)

* providerId: This field is the lowercase name of the provider: Yahoo, Facebook or Twitter.

* providerUserId: This field is the GUID, the unique identifier in the provider's system \(Yahoo! user ID or spi.\).

* accessToken, secret, refreshToken, and expireTime: These are the OAuth2 tokens \(credentials\) for the connection and their related information.

在此表Entity中，你可以看到以下字段：

* userId：当用户注册时，此字段与用户的`@Id (username)`匹配。 如果用户尚未注册，userId是GUID（Yahoo!用户ID，也称为web侧的spi）

* fproviderId：此字段是提供商的小写名称：Yahoo，Facebook或Twitter。

* providerUserId：此字段是GUID，提供商系统中的唯一标识符（Yahoo!用户ID或spi。）。

* accessToken，secret，refreshToken和expireTime：这些是连接的OAuth2令牌（凭据）及其相关信息。

Two interfaces come with the framework:

* ConnectionRepository: This manages the persistence of one user connection.Implementations are request-scoped for the identified user.

* UsersConnectionRepository: This provides access to the global store of connections across all users.

两个接口带有框架：

* ConnectionRepository：它管理一个用户连接的持久性。实现是对标识的用户的请求作用域。

* UsersConnectionRepository：这提供对跨所有用户的连接的全局存储的访问。

If you remember, we created our own UsersConnectionRepository implementatio\(SocialUserServiceImpl\). Registered in the dispatcher-servlet.xml file, this implementation acts as a factory to produce request-scope connectionRepository implementations \(SocialUserConnectionRepositoryImpl\):  
如果你还记得，我们创建了自己的UsersConnectionRepository implementsatio（SocialUserServiceImpl）。 注册在dispatcher-servlet.xml文件中，此实现充当一个工厂来生成请求范围的connectionRepository实现（SocialUserConnectionRepositoryImpl）：

```js
<bean id="connectionRepository" factorymethod="createConnectionRepository" factorybean="usersConnectionRepository" scop="request">
    <constructor-arg value="#{request.userPrincipal.name}" />
    <aop:scoped-proxy proxy-target-class="false" />
</bean>
<bean id="usersConnectionRepository" class="edu.zc.csm.core.services.SocialUserServiceImpl"/>
```

Those two custom implementations both use the Spring Data JPA SocialUserRepository that we have created for finding, updating, persisting, and removing connections.

In the SocialUserServiceImpl implementation of the UsersConnectionRepository interface, a ConnectionFactoryLocator property is autowired and a TextEncryptor property is initialized with a default NoOpTextEncryptor instance.

这两个自定义实现都使用我们创建的用于查找，更新，持久化和删除连接的Spring Data JPA SocialUserRepository。

在UsersConnectionRepository接口的SocialUserServiceImpl实现中，将自动连接一个ConnectionFactoryLocator属性，并使用默认的NoOpTextEncryptor实例初始化一个TextEncryptor属性。

> The default TextEncryptor instance can be replaced with a proper encryption for the SocialUser data maintained in the database. Take a look at the spring-security-crypto module:
>
> 默认的TextEncryptor实例可以用对数据库中维护的SocialUser数据的正确加密替换。 看看spring-security-crypto模块：
>
> [http://docs.spring.io/spring-security/site/docs/3.1.x/reference/crypto.html](http://docs.spring.io/spring-security/site/docs/3.1.x/reference/crypto.html)

### Provider-specific configuration

The provider-specific configuration \(Facebook, Twitter, Yahoo!\) starts with definitions of the connectionFactoryLocator bean.

提供程序特定的配置

提供者特定的配置（Facebook，Twitter，Yahoo!）从connectionFactoryLocator bean的定义开始。

### One entry-point – connectionFactoryLocator

The connectionFactoryLocator bean that we have defined in the dispatcherservlet.xml plays a central role in Spring Social. Its registration is as follows:

一个入口点 -  connectionFactoryLocator

我们在dispatcherservlet.xml中定义的connectionFactoryLocator bean在Spring Social中扮演了核心角色。 其注册如下：

```js
<bean id="connectionFactoryLocator" class="org.sfw.social.connect.support.ConnectionFactoryRegistry">
    <property name="connectionFactories">
        <list>
        <bean class"="org.sfw.social.yahoo.connect.YahooOAuth2ConnectionFactory"">
            <constructor-arg value="${yahoo.client.token}" />
            <constructor-arg value="${yahoo.client.secret}" />
            <constructor-arg value="${yahoo.signin.url}" />
        </bean>
        </list>
    </property>
</bean>
```

With this bean, Spring social implements a ServiceLocator pattern that allows us to easily plug-in/plug-out new social connectors. Most importantly, it allows the system to resolve at runtime a provider-specific connector \(a connectionFactory\).

The specified Type for our connectionFactoryLocator is ConnectionFactoryRegistry, which is a provided implementation of the ConnectionFactoryLocator interface:

有了这个bean，Spring social 实现了一个ServiceLocator模式，允许我们轻松插入/插出新的social connectors。 最重要的是，它允许系统在运行时解析特定于提供程序的连接器（connectionFactory）。

我们的connectionFactoryLocator的指定类型是ConnectionFactoryRegistry，它是ConnectionFactoryLocator接口的提供实现：

```java
public interface ConnectionFactoryLocator {
    ConnectionFactory<?> getConnectionFactory(String providerId);
    <A> ConnectionFactory<A> getConnectionFactory(Class<A> apiType);
    Set<String> registeredProviderIds();
}
```

We have an example of the connectionFactory lookup in the` ProviderSignInController.signin` method:

我们有一个在`ProviderSignInController.signin`方法中的connectionFactory查找的例子：

```java
ConnectionFactory<?> connectionFactory =
 connectionFactoryLocator.getConnectionFactory(providerId);
```

Here, the providerId argument is a simple String \(yahoo in our case\).

这里，providerId参数是一个简单的String（在我们的例子中是yahoo）。

### Provider-specific ConnectionFactories

The ConnectionFactory such as YahooOAuth2ConnectionFactory are registered in ConnectionFactoryRegistry with the OAuth2 consumer key and consumer secret, which identify \(with authorization\) our application on the provider's side.

We have developed the YahooOAuth2ConnectionFactory class, but you should be able to find your ProviderSpecificConnectionFactory either from official Spring Social subprojects \(spring-social-facebook, spring-social-twitter, and so on\) or from open sources projects.

ConnectionFactory例如YahooOAuth2ConnectionFactory在ConnectionFactoryRegistry中与OAuth2使用者密钥和使用者密钥一起注册，其在提供者侧识别（授权）我们的应用。

我们已经开发了YahooOAuth2ConnectionFactory类，但是你应该能够从官方Spring Social subprojects（spring-social-facebook，spring-social-twitter等）或开源项目中找到你的ProviderSpecificConnectionFactory。

### Signing in with provider accounts

In order to perform the OAuth2 authentication steps, Spring social provides an abstracted Spring MVC Controller: ProviderSignInController.

This controller performs the OAuth flows and establishes connections with the provider. It tries to find a previously established connection and uses the connected account to authenticate the user in the application.

使用提供商帐户登录

为了执行OAuth2身份验证步骤，Spring social提供了一个抽象的Spring MVC控制器：ProviderSignInController。

此控制器执行OAuth流并与提供程序建立连接。 它尝试查找先前建立的连接，并使用连接的帐户在应用程序中验证用户。

If no previous connection matches, the flow is sent to the created SignUpController matching the specific request mapping/signup. The user is not automatically registered as a CloudStreetMarket User at this point. We force the user to create his account manually via a Must-Register response header when an API call appears OAuth2 authenticated without a bound local user. This Must-Register response header triggers the create an account now popup on the client side \(see in home\_community\_activity.js, the loadMore function\).

如果先前的连接不匹配，则流被发送到与特定请求映射/注册匹配的创建的SignUpController。 此时，用户不会自动注册为CloudStreetMarket用户。 当API调用出现未绑定本地用户的OAuth2身份验证时，我们强制用户通过必须注册响应标头手动创建其帐户。 这个必须注册响应头触发在客户端创建一个现在弹出的帐户（参见home\_community\_activity.js，loadMore函数）。

It is during this registration that the connection \(the SocialUser Entity\) is synchronized with the created User Entity \(see the `CommunityController.createUser` method\).

The ProviderSignInController works closely with a SignInAdapter implementation\(that we had to build as well\) which actually authenticates the user into CloudStreetMarket with Spring Security. The authentication is triggered with the call to `communityService. signInUser(user)`.

Here are the details of the method that creates the Authentication object and stores it into the SecurityContext:

正是在此注册期间，连接（SocialUser实体）与创建的用户实体同步（请参阅`CommunityController.createUser`方法）。

ProviderSignInController与SignInAdapter实现（我们必须构建的实现）密切合作，实际上使用Spring Security将用户验证到CloudStreetMarket中。 通过调用`communityService. signInUser(user)`来触发认证。

下面是创建Authentication对象并将其存储到SecurityContext中的方法的详细信息：

```java
@Override
public Authentication signInUser(User user) {

    Authentication authentication = new UsernamePasswordAuthenticationToken(user,user.getPassword(), 
                    user.getAuthorities());

    SecurityContextHolder.getContext().setAuthentication(authentication);

    return authentication;
}
```

We register and initialize a Spring bean for ProviderSigninController with the following configuration:

我们使用以下配置注册并初始化ProviderSigninController的Spring bean：

```java
<bean class="org.sfw.social.connect.web.ProviderSignInController">
    <constructor-arg ref="connectionFactoryLocator"/>
    <constructor-arg ref="usersConnectionRepository"/>
    <constructor-arg ref="signInAdapter"/>
    <property name="signUpUrl" value = "/signup"/>
    <property name="postSignInUrl" value="${frontend.home.page.url}"/>
</bean>
```

As you can see, we have specified the signUpUrl request mapping to redirect to our custom SignupController when no previous connection is found in database.

Alternatively, the specified postSignInUrl allows the user to be redirected to the home page of the portal when the ProviderSignInController resolves an existing connection to reuse.

正如你所看到的，我们已经指定了signUpUrl请求映射，以便在数据库中找不到以前的连接时重定向到我们的自定义SignupController。

或者，指定的postSignInUrl允许在ProviderSignInController解析现有连接以重新使用时将用户重定向到门户的主页。

## There is more…

Let's have a look at other features of Spring social.

让我们来看看Spring social的其他功能。

### Performing authenticated API calls

In this recipe, we focused on presenting the OAuth2-client authentication process. In the next chapter, we will see how to use Spring social to perform requests to Yahoo! APIs on behalf of the users. We will see how can be used existing libraries in this purpose and how they work. In aour case, we had to develop API connectors to Yahoo! financial APIs.

执行经过身份验证的API调用

在本文中，我们专注于介绍OAuth2客户端身份验证过程。 在下一章中，我们将看到如何使用Spring social来代表用户向Yahoo! API执行请求。 我们将看到如何使用现有的库来实现这个目的，以及它们如何工作。 在这种情况下，我们不得不开发API连接器到Yahoo! financial API。

### The Spring social ConnectController

Spring social web provides another abstracted controller which allows social users to directly interact with their social connections to connect, disconnect, and obtain their connection status. The ConnectController can also be used to build an interactive monitoring screen for managing connections to all the providers a site could possibly handle. Check out the Spring social reference for more details:

Spring social网络提供了另一个抽象的控制器，允许social用户直接与他们的social connections交互以连接，断开连接并获得他们的连接状态。  ConnectController还可以用于构建交互式监视屏幕，用于管理与站点可能处理的所有提供者的连接。 有关更多详细信息，请参阅Spring社交参考：

[http://docs.spring.io/spring-social/docs/current/reference/htmlsingle/\\#connecting](http://docs.spring.io/spring-social/docs/current/reference/htmlsingle/\#connecting)

## See also

### SocialAuthenticationFilter

This is a filter to be added to Spring Security so that a social authentication can be performed from the Spring Security filter-chain \(and not externally as we did\).

这是一个要添加到Spring Security的过滤器，以便可以从Spring Security过滤器链（而不是从外部执行）执行社交身份验证。

[http://docs.spring.io/spring-social/docs/current/reference/htmlsingle/\\#enabling-provider-sign-in-with-codesocialauthenticationfilter-code](http://docs.spring.io/spring-social/docs/current/reference/htmlsingle/\#enabling-provider-sign-in-with-codesocialauthenticationfilter-code)

### The list of Spring social connectors

You will find a list of implemented connectors to Saas-providers from the main page of the project:

您可以从项目的主页面找到Saas-providers的实现连接器列表：

[http://projects.spring.io/spring-social](http://projects.spring.io/spring-social)

### Implementing an OAuth2 authentication server

You can use the Spring Security OAuth project:

您可以使用Spring Security OAuth项目：

[http://projects.spring.io/spring-security-oauth](http://projects.spring.io/spring-security-oauth)

### The harmonic development blog

The articles about Spring social have inspired this recipe. Feel free to visit this blog:

关于Spring social的文章启发了这个谱方。 请随时访问此博客：

[http://harmonicdevelopment.tumblr.com/post/13613051804/adding-springsocial-to-a-spring-mvc-and-spring](http://harmonicdevelopment.tumblr.com/post/13613051804/adding-springsocial-to-a-spring-mvc-and-spring)

