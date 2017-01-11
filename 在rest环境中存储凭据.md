This recipe presents a solution for storing credentials in RESTful applications.

此配方提供了在RESTful应用程序中存储凭据的解决方案。

## Getting ready

The solution is a compromise between temporary client-side storage and permanent serverside storage.

On the client side, we are using HTML5 session storage to store temporarily the usernames and passwords encoded in base 64. On the server side, only hashes are stored for passwords.hose hashes are created with passwordEncoder. This passwordEncoder is registered in Spring Security, autowired, and used in the UserDetailsService implementation.

该解决方案是临时客户端存储和永久服务器端存储之间的折衷。

在客户端，我们使用HTML5会话存储临时存储base 64中编码的用户名和密码。使用passwordEncoder创建哈希值。 此passwordEncoder在Spring Security中注册，自动连接，并在UserDetailsS​​ervice实现中使用。

## How to do it...

### Client side \(AngularJS\)

1. We have made use of the HTML5 sessionStorage attribute. The main change has been the creation of a httpAuth factory. Presented in the http\_authorized.js file, this factory is a wrapper around $http to take care transparently of client-side storage and authentication headers. The code for this factory is as follows: 

1.我们使用了HTML5 sessionStorage属性。 主要的变化是创建了一个httpAuth工厂。 提交在http\_authorized.js文件中，这个工厂是$ http包装，以便透明地处理客户端存储和认证头。 这个工厂的代码如下：

```
cloudStreetMarketApp.factory("httpAuth", function ($http) {
    return {
        clearSession: function () {
            var authBasicItem = sessionStorage.getItem('basicHeaderCSM');
            var oAuthSpiItem = sessionStorage.getItem('oAuthSpiCSM');

            if(authBasicItem || oAuthSpiItem){
                sessionStorage.removeItem('basicHeaderCSM');
                sessionStorage.removeItem('oAuthSpiCSM');
                sessionStorage.removeItem('authenticatedCSM');
                $http.defaults.headers.common.Authorization = undefined;
                $http.defaults.headers.common.Spi = undefined;
                $http.defaults.headers.common.OAuthProvider = undefined;
        }
},

        refresh: function(){
            var authBasicItem = sessionStorage.getItem('basicHeaderCSM');
            var oAuthSpiItem = sessionStorage.getItem('oAuthSpiCSM');
            if(authBasicItem){
                $http.defaults.headers.common.Authorization = $.parseJSON(authBasicItem).Authorization;
            }
            if(oAuthSpiItem){
                $http.defaults.headers.common.Spi = oAuthSpiItem;
                $http.defaults.headers.common.OAuthProvider = "yahoo";
            }
        },

        setCredentials: function (login, password) {
            //Encodes in base 64
            var encodedData = window.btoa(login+":"+password);
            var basicAuthToken = 'Basic '+encodedData;
            var header = {Authorization: basicAuthToken};
            sessionStorage.setItem('basicHeaderCSM', JSON.stringify(header));
            $http.defaults.headers.common.Authorization = basicAuthToken;
        },

        setSession: function(attributeName, attributeValue) {
            sessionStorage.setItem(attributeName, attributeValue);
        },

        getSession: function (attributeName) {
            return sessionStorage.getItem(attributeName);
        },

        post: function (url, body) {
            this.refresh();
            return $http.post(url, body);
        },
        post: function (url, body, headers, data) {
            this.refresh();
            return $http.post(url, body, headers, data);
        },
        get: function (url) {
            this.refresh();
            return $http.get(url);
        },
        isUserAuthenticated: function () {
            var authBasicItem = sessionStorage.getItem('authenticatedCSM');
            if(authBasicItem){
                return true;
            }
        return false;
        }
    }
});
```

1. This factory is invoked everywhere \(or almost\) in the former place of $http to pass and handle transparently the credentials or identification headers required for AJAX requests.

2. We have avoided dealing directly with the sessionStorage attribute from the different controllers, in order to prevent being tightly coupled with this storage solution.

3. The account\_management.js file regroups different controllers \(LoginByUsernameAndPasswordController, createNewAccountController, and OAuth2Controller\) that store credentials and provider IDs in sessionStorage through httpAuth.

4. A couple of factories have also been modified to pull and push data through the httpAuth factory. For example, the indiceTableFactory \(from home\_financial\_table.js\) requests the indices of a market with credentials handled transparently:

2.这个工厂在$ http的前一个地方（或几乎）被调用，以便透明地处理AJAX请求所需的凭证或标识头。

3.我们避免直接处理来自不同控制器的sessionStorage属性，以防止与此存储解决方案紧密耦合。

4.account\_management.js文件重新分组不同的控制器（LoginByUsernameAndPasswordController，createNewAccountController和OAuth2Controller），它们通过httpAuth在sessionStorage中存储凭据和提供程序标识。  
5.还修改了一些工厂来通过httpAuth工厂推送和推送数据。 例如，indiceTableFactory（从home\_financial\_table.js）请求具有透明处理的凭证的市场的索引：

```
cloudStreetMarketApp.factory("indicesTableFactory",function (httpAuth) {
    return {
        get: function (market) {
            return httpAuth.get("/api/indices/" + market + ".json?ps=6");
        }
    }
});
```

### Server side

1. We have declared a passwordEncoder bean in security-config.xml \(in the cloudstreetmarket-core module\):

1.我们在security-config.xml中（在cloudstreetmarket-core模块中）声明了一个passwordEncoder bean：

```
<bean id="passwordEncoder" class="org.sfw.security.crypto.bcrypt.BCryptPasswordEncoder"/>
```

1. In security-config.xml, a reference to the password-encoder is made, as follows, in our authenticationProvider to.

2.在security-config.xml中，对密码编码器的引用如下，在我们的authenticationProvider中。

```
<security:authentication-manager alias"="authenticationManager">
    <security:authentication-provider user-serviceref='communityServiceImpl'>
        <security:password-encoder ref="passwordEncoder"/>
    </security:authentication-provider>
</security:authentication-manager>
```

1. The passwordEncoder bean is autowired in CommunityServiceImpl \(our UserDetailsService implementation\). Passwords are hashed here with passwordEncoder when accounts are registered. The stored hash is then compared to the user-submitted password when the user attempts to log in. The CommunityServiceImpl code is as follows:

2. passwordEncoder bean在CommunityServiceImpl（我们的UserDetailsS​​ervice实现）中自动连接。 在注册帐户时，使用passwordEncoder在此处对密码进行散列。 当用户尝试登录时，存储的哈希值与用户提交的密码进行比较。CommunityServiceImpl代码如下：

```
@Service(value="communityServiceImpl")
@Transactional(propagation = Propagation.REQUIRED)
public class CommunityServiceImpl implements CommunityService {

    @Autowired
    private ActionRepository actionRepository;
    ...
    @Autowired
    private PasswordEncoder passwordEncoder;
    ...

    @Override
    public User createUser(User user, Role role) {

        if(findByUserName(user.getUsername()) != null){
            throw new ConstraintViolationException("The provided user name already exists!", null, null);
        }
        user.addAuthority(new Authority(user, role));
        user.addAction(new AccountActivity(user,UserActivityType.REGISTER, new Date()));
        user.setPassword(passwordEncoder.encode(user.getPassword()));
        return userRepository.save(user);
    }

    @Override
    public User identifyUser(User user) {

        Preconditions.checkArgument(user.getPassword() != null, "The provided password cannot be null!");
        Preconditions.checkArgument(StringUtils.isNotBlank(user.getPassword()), "The provided password cannot be empty!");
        User retreivedUser = userRepository.findByUsername(user.getUsername());
        if(!passwordEncoder.matches(user.getPassword(), retreivedUser.getPassword())){
            throw new BadCredentialsException"("No match has been found with the provided credentials!");
        }
        return retreivedUser;
        }
        ...
}
```

1. Our ConnectionFactory implementation SocialUserConnectionRepositoryImpl is instantiated in SocialUserServiceImpl with an instance of the Spring TextEncryptor. This gives the possibility to encrypt the stored connection-data for OAuth2 \(most importantly, the access-tokens and refresh-tokens\). At the moment, this data is not encrypted in our code.

4.我们的ConnectionFactory实现SocialUserConnectionRepositoryImpl在SocialUserServiceImpl中用Spring TextEncryptor的实例实例化。 这为加密存储的OAuth2连接数据（最重要的是，访问令牌和刷新令牌）提供了可能性。 目前，这些数据在我们的代码中没有加密。

## How it works...

In this chapter, wetried to maintain the statelessness of our RESTful API for the benefits it provides \(scalability, easy deployment, fault tolerance, and so on\).

在本章中，为了提供RESTful API的可维护性（可扩展性，易部署性，容错性等），我们坚持保持RESTful API的无状态性。

### Authenticating for Microservices

Staying stateless matches a key concept of Microservices: the self-sufficiency of our modules. We won't be using sticky sessions for scalability. When a state is maintained, it is only by the client, keeping for a limited time the user's identifier and/or his credentials.

认证微服务

保持无状态匹配微服务的关键概念：我们的模块的自给自足。 我们不会使用粘性会话来扩展。 当状态被维持时，仅由客户端保持有限的时间用户的标识符和/或其凭证。

Another key concept of Microservices is the concept of limited and identified responsibilities\(horizontal scalability\). Our design supports this principle even if the size of the application doesn't require domain segmentation. We can fully imagine splitting our API by domains\(community, indices and stocks, monitoring, and so on\). Spring Security, which is located in the core-module, would be embedded in every API war without any problem.

Let's focus on how a state is maintained on the client side. We offer to our users two ways of signing-in: using a BASIC scheme or using OAuth2.

* A user can register his account for a BASIC authentication and then later decide to sign-in using OAuth2 \(to do so, he has to bind his social account to his existing account\).

* Alternatively, a user can register his account with OAuth2 and later sign in with a BASIC form. His OAuth2 credentials will naturally be bound to his authentication.

微服务的另一个关键概念是有限和确定的责任（水平可扩展性）的概念。 我们的设计支持这个原则，即使应用程序的大小不需要域分割。 我们可以完全想象，通过域（社区，指数和股票，监控等）分离我们的API。  Spring Security位于核心模块中，将被嵌入在每一个API war中，没有任何问题。

让我们专注于如何在客户端维护一个状态。 我们向用户提供两种登录方式：使用BASIC方案或使用OAuth2。

* 用户可以为BASIC身份验证注册他的帐户，然后决定使用OAuth2登录（这样做，他必须将他的社交帐户绑定到他现有的帐户）。

* 或者，用户可以使用OAuth2注册他的帐户，以后可以使用BASIC表单登录。 他的OAuth2凭证自然会绑定到他的身份验证。

### Using the BASIC authentication

When a user registers an account, he defines a username and a password. These credentials are stored using the httpAuth factory and the setCredentials method.

In the account\_management.js file and especially in the createNewAccountController \(invoked through the create\_account\_modal.html modal\), the setCredentials call can be found in the success handler of the createAccount method:

当用户注册帐户时，他定义用户名和密码。 这些凭据使用httpAuth工厂和setCredentials方法存储。

在account\_management.js文件中，特别是在createNewAccountController（通过create\_account\_modal.html modal调用）中，setCredentials调用可以在createAccount方法的成功处理程序中找到：

```
httpAuth.setCredentials($scope.form.username,$scope.form.password);
```

Right now, this method uses HTML5 sessionStorage as storage device:

现在，这种方法使用HTML5 sessionStorage作为存储设备：

```
setCredentials: function (login, password) {
    var encodedData = window.btoa(login"+":"+password);
    var basicAuthToken = 'Basic '+encodedData;
    var header = {Authorization: basicAuthToken};
    sessionStorage.setItem('basicHeaderCSM', JSON.stringify(header));
    $http.defaults.headers.common.Authorization = basicAuthToken;
}
```

The window.btoa\(...\) function encodes in base 64 the provided String. The $httpProvider.defaults.headers configuration object is also added a new header which will potentially be used by the next AJAX request.

When a user signs in using the BASIC form \(see also the account\_management.js and especially the LoginByUsernameAndPasswordController that is invoked from the auth\_modal.html modal\), the username and password are stored using the same method:

window.btoa（...）函数在base 64中编码提供的String。  $ httpProvider.defaults.headers配置对象也添加了一个新的头，可能会被下一个AJAX请求使用。

当用户使用BASIC表单登录时（另请参阅account\_management.js，尤其是从auth\_modal.html modal调用的LoginByUsernameAndPasswordController），用户名和密码使用相同的方法存储：

```
httpAuth.setCredentials($scope.form.username, $scope.form.password);
```

Now with the httpAuth abstraction layer the angular $http service, we make sure that the Authorization header is set in each call to the API that is made using $http.

现在使用httpAuth抽象层角度$ http服务，我们确保授权头在每次调用使用$ http做的API设置。

![](/assets/92.png)

### Using OAuth2

Initiated from auth\_modal.html, signing in using OAuth2 creates a POST HTTP request to the API handler /api/signin/yahoo \(this handler is located in the abstracted ProviderSignInController\).

The sign in request is redirected to the Yahoo! authentication screens. The whole page goes to Yahoo! until completion. When the API ultimately redirects the request to the home page of the portal, a spi request parameter is added: [http://cloudstreetmarket.com/portal/index?spi=F2YY6VNSXIU7CTAUB2A6U6KD7E](http://cloudstreetmarket.com/portal/index?spi=F2YY6VNSXIU7CTAUB2A6U6KD7E)

This spi parameter is the Yahoo! user ID \(GUID\). It is caught by the DefaultController\(cloudstreetmarket-webapp\) and injected into the model:

从auth\_modal.html发起，使用OAuth2登录会向API处理程序/ api / signin / yahoo创建POST HTTP请求（此处理程序位于抽象的ProviderSignInController中）。

登录请求被重定向到Yahoo!身份验证屏幕。 整个页面转到Yahoo!，直到完成。 当API最终将请求重定向到门户网站的主页时，会添加spi请求参数：http//cloudstreetmarket.com/portal/index?spi=F2YY6VNSXIU7CTAUB2A6U6KD7E

此spi参数是Yahoo!用户ID（GUID）。 它被DefaultController（cloudstreetmarket-webapp）捕获并注入到模型中：

```
@RequestMapping(value="/*", method={RequestMethod.GET,RequestMethod.HEAD})
public String fallback(Model model, @RequestParam(value="spi",required=false) String spi) {

    if(StringUtils.isNotBlank(spi)){
        model.addAttribute("spi", spi);
    }
    return "index";
}
```

The index.jsp file renders the value directly in the top menu's DOM:

index.jsp文件直接在顶部菜单的DOM中呈现值：

```
<div id="spi" class="hide">${spi}</div>
```

When the menuController \(bound to the top menu\) initializes itself, this value is read and stored in sessionStorage:

当menuController（绑定到顶部菜单）初始化自身时，此值被读取并存储在sessionStorage中

```
$scope.init = function () {
    if($('#spi').text()){
    httpAuth.setSession('oAuthSpiCSM', $('#spi').text());
    }
}
```

In our httpAuth factory \(http\_authorized.js\), the refresh\(\) method that is invoked before every single call to the API checks if this value is present and add two extra headers:

Spi with the GUID value and the **OAuthProvider** \(yahoo in our case\). The code is as follows:

在我们的httpAuth工厂（http\_authorized.js）中，在每次调用API之前调用的refresh（）方法会检查此值是否存在，并添加两个额外的标头：

Spi与GUID值和**OAuthProvider**（在我们的例子中为yahoo）。 代码如下：

```
refresh: function(){
    var authBasicItem = sessionStorage.getItem('basicHeaderCSM');
    var oAuthSpiItem = sessionStorage.getItem('oAuthSpiCSM');
    if(authBasicItem){
        $http.defaults.headers.common.Authorization = $.parseJSON(authBasicItem).Authorization;
    }
    if(oAuthSpiItem){
        $http.defaults.headers.common.Spi = oAuthSpiItem;
        $http.defaults.headers.common.OAuthProvider = "yahoo";
    }
}
```

The screenshot here shows those two headers for one of our an AJAX requests:

这里的屏幕截图显示了我们的一个AJAX请求的两个标头：

![](/assets/93.png)

### HTML5 SessionStorage

We used the SessionStorage as storage solution on the client side for user credentials and social identifiers \(GUIDs\).

In HTML5, web pages have the capability to store data locally in the browser using the Web Storage technology. Data in stored Web Storage can be accessed from the page scripts' and values can be relatively large \(up to 5MB\) with no impact on client-side performance.

Web Storage is per origin \(the combination of protocol, hostname, and port number\). All pages from one origin can store and access the same data. There are two types of objects that can be used for storing data locally:

* window.localStorage: This stores data with no expiration date.

* window.sessionStorage: This stores data for one session \(data is lost when the tab is closed\).

我们在客户端使用SessionStorage作为存储解决方案来获取用户凭据和社区标识符（GUID）。

在HTML5中，网页具有使用Web存储技术在浏览器中本地存储数据的能力。 存储的Web存储中的数据可以从页脚本访问，值可以相对较大（高达5MB），对客户端性能没有影响。

Web存储是根据源（协议，主机名和端口号的组合）。 来自一个来源的所有页面都可以存储和访问相同的数据。 有两种类型的对象可用于本地存储数据：

* window.localStorage：存储没有过期日期的数据。

* window.sessionStorage：这存储一个会话的数据（数据在标签页关闭时丢失）。

These two objects can be accessed directly from the window object and they both come with the self-explanatory methods:  
这两个对象可以直接从窗口对象访问，它们都带有自解释的方法：

```
setItem(key,value);
getItem(key);
removeItem(key);
clear();
```

As indicated by [http://www.w3schools.com/](http://www.w3schools.com/), localStorage is almost supported by all browsers nowadays \(between 94% and 98% depending upon your market\). The following table shows the first versions that fully support it:

如[http://www.w3schools.com/所示，localStorage现在几乎被所有浏览器支持（根据您的市场，在94％和98％之间）。](http://www.w3schools.com/所示，localStorage现在几乎被所有浏览器支持（根据您的市场，在94％和98％之间）。) 下表显示了完全支持它的第一个版本：

![](/assets/94.png)

We should implement a fallback option with cookies for noncompliant web browsers, or at least a warning message when the browsers seem outdated.

我们应为不合规的网络浏览器使用Cookie实施备用选项，或者当浏览器显示为过时时，至少会显示一条警告消息。

### SSL/TLS

An encrypted communication protocol must be setup when using a BASIC authentication. We have seen that the credentials username:password and the Yahoo! GUID are sent as request headers. Even though those credentials are encoded in base 64, this doesn't represent a sufficient protection.

使用BASIC认证时必须设置加密通信协议。 我们已经看到凭证username：password和Yahoo! GUID作为请求头发送。 即使这些凭据在基本64中编码，这不表示足够的保护。

### BCryptPasswordEncoder

On the server side, we don't store the User passwords in plain text. We only store an encoded description of them \(a hash\). Therefore, a hashing function is supposedly not reversible.

"A hash function is any function that can be used to map digital data of arbitrary size to digital data of fixed size".

Let's have a look at the following mapping:

在服务器端，我们不以纯文本格式存储用户密码。 我们只存储它们的编码描述（哈希）。 因此，散列函数被认为是不可逆的。

“散列函数是可以用于将任意大小的数字数据映射到固定大小的数字数据的任何函数”。

让我们看看下面的映射：

![](/assets/95.png)

This diagram shows a hash function that maps names to integers from 0 to 15.

We used a PasswordEncoder implementation invoked manually while persisting and updating Users. Also PasswordEncoder is an Interface of Spring Security core:

此图显示一个散列函数，将名称映射到从0到15的整数。

我们使用手动调用的PasswordEncoder实现来保持和更新用户。 另外PasswordEncoder是Spring Security核心的接口：

```
public interface PasswordEncoder {
    String encode(CharSequence rawPassword);
    boolean matches(CharSequence rawPassword, String encodedPassword);
 }
```

Spring Security provides three implementations: StandardPasswordEncoder, NoOpPasswordEncoder, and BCryptPasswordEncoder.

We used BCryptPasswordEncoder as it is recommended on new projects. Instead of implementing a MD5 or SHA hashing algorithm, BCryptPasswordEncoder uses a stronger hashing algorithm with randomly generated salt.

This allows the storage of different HASH values for the same password. Here's an example of different BCrypt hashes for the 123456 value:

Spring Security提供三种实现：StandardPasswordEncoder，NoOpPasswordEncoder和BCryptPasswordEncoder。

我们使用BCryptPasswordEncoder作为新项目的建议。 代替实现MD5或SHA哈希算法，BCryptPasswordEncoder使用随机生成的salt的更强的哈希算法。

这允许为相同的密码存储不同的HASH值。 这里有一个不同的BCrypt哈希为123456值的例子：

```
$2a$10$Qz5slUkuV7RXfaH/otDY9udROisOwf6XXAOLt4PHWnYgOhG59teC6
$2a$10$GYCkBzp2NlpGS/qjp5f6NOWHeF56ENAlHNuSssSJpE1MMYJevHBWO
$2a$10$5uKS72xK2ArGDgb2CwjYnOzQcOmB7CPxK6fz2MGcDBM9vJ4rUql36
```



