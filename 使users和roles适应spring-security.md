We have thought interesting to split apart this section, since users and roles are usually borderline between the application and Spring Security.

我们认为有趣的是拆分这部分，因为users 和roles 通常是应用程序和Spring Security之间的边界。

## Getting ready

In this recipe, we will install the Spring Security dependencies and update the User Entity. We will also create an Authority entity that is based on a custom Role enum that we created. Finally, we update the init.sql script to add a set of existing users.

在这个配方中，我们将安装Spring Security依赖项并更新用户实体。 我们还将创建一个基于我们创建的自定义角色枚举的Authority实体。 最后，我们更新init.sql脚本以添加一组现有用户。

## How to do it...

1. From the Git Perspective in Eclipse, checkout the latest version of the branch v5.x.x. Then, run a maven clean install command on the cloudstreetmarket-parent module \(right-click on the module, go to Run as… \| Maven Clean, and then navigate to Run as… \| Maven Install\). Execute a Maven Update Project to synchronize Eclipse with the maven configuration \(right-click on the module and then navigate to Maven \| Update Project…\).

1.从Eclipse中的**Git Perspective** 中，检出最新版本的分支v5.x.x. 然后，在cloudstreetmarket-parent模块上运行`maven clean install`命令（右键单击模块，转到**Run as ... \| Maven Clean**，然后导航到**Run as ... \| Maven Install**）。 执行**Maven Update Project**以使Eclipse与maven配置同步（右键单击模块，然后导航到**Maven \| Update Project...**）。

> You will notice a few changes both in the frontend and backend of the code.
>
> 你会注意到在代码的前端和后端的几个更改。

1. Spring Security comes with the following dependencies, added in cloudstreetmarket-parent, cloudstreetmarket-core and

cloudstreetmarket-api:

1. Spring Security具有以下依赖项，在cloudstreetmarket-parent，cloudstreetmarket-core和cloudstreetmarket-api中添加：

```
<!-- Spring Security -->
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-web</artifactId>
    <version>4.0.0.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-config</artifactId>
    <version>4.0.0.RELEASE</version>
</dependency>
```

3. The User entity has been updated. It now reflects the users table \(instead of the previous user table\). It also implements the UserDetails interface:

3.用户实体已更新。 它现在反映users表（而不是以前的用户表）。 它还实现UserDetails接口：

```
@Entity
@Table(name="users")
public class User implements UserDetails{

    private static final long serialVersionUID = 1990856213905768044L;
    
    @Id
    @Column(name = "user_name", nullable = false)
    private String username;
    
    @Column(name = "full_name")
    private String fullName;
    
    private String email;
    
    private String password;
    
    private boolean enabled = true;
    
    private String profileImg;
    
    @Column(name="not_expired")
    private boolean accountNonExpired;
    
    @Column(name="not_locked")
    private boolean accountNonLocked;
    
    @Enumerated(EnumType.STRING)
    private SupportedCurrency currency;
    
    @OneToMany(mappedBy= "user", cascade = CascadeType.ALL,fetch = FetchType.LAZY)
    @OrderBy("id desc")
    private Set<Action> actions = new LinkedHashSet<Action>();
    
    @OneToMany(mappedBy="user", cascade = CascadeType.ALL,fetch = FetchType.EAGER)
    private Set<Authority> authorities = new LinkedHashSet<Authority>();
    
    @OneToMany(cascade=CascadeType.ALL, fetch = FetchType.LAZY)
    private Set<SocialUser> socialUsers = new LinkedHashSet<SocialUser>();
    //getters and setters as per the UserDetails interface
    ...
}
```

This User Entity has a relationship with SocialUser. SocialUser comes into play with the OAuth2 authentication, and we will develop this part later.

此用户实体与SocialUser具有关系。 SocialUser开始使用OAuth2身份验证，我们稍后将开发此部分。

4. An Authority Entity has been created and maps a authorities table. This Entity also implements the GrantedAuthority interface. The class is the following:

4.已创建权限实体并映射权限表。 此实体还实现GrantedAuthority接口。 该类如下：

```
@Entity
@Table(name"="authorities"",uniqueConstraints={@UniqueConstraint(columnNames = "{"username""","authority""})})
public class Authority implements GrantedAuthority{

    private static final long serialVersionUID = 1990856213905768044L;
    
    public Authority() {}
    
    public Authority(User user, Role authority) {
    this.user = user;
    this.authority = authority;
    }
    
    @Id
    @GeneratedValue
    private Long id;
    
    @OneToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = ""username"", nullable=false)
    private User user;
    
    @Column(nullable = false)
    @Enumerated(EnumType.STRING)
    private Role authority;
    
    //getters and setters as per the GrantedAuthority
    //interface
    ...
}
```

5. For a more readable code, we have created a Role Enum in the cloudstreetmarket-core module, for the different roles:

5.对于更可读的代码，我们在cloudstreetmarket-core模块中为不同的roles创建了一个Role Enum：

```
public enum Role {
    ROLE_ANONYMOUS,
    ROLE_BASIC,
    ROLE_OAUTH2,
    ROLE_ADMIN,
    ROLE_SYSTEM,
    IS_AUTHENTICATED_REMEMBERED; //Transitory role
}
```

6. Also, we have made a few changes in the init.sql file. The existing pre-initialization scripts related to users, have been adapted to suit the new schema:

此外，我们在init.sql文件中进行了一些更改。 与用户相关的现有预初始化脚本已经适应了新的模式：

```
insert into users(username, fullname, email, password, profileImg, enabled, not_expired, not_locked) values
('userC', '', 'fake12@fake.com', '123456', '', true, true, true);
insert into authorities(username, authority) values ('userC', 'ROLE_'BASIC');

```

7. Start the application. \(No exceptions should be observed\).

8. Click on the **login **button \(on the right-hand side of the main menu\). You will see the following popup that allows entering a username and a password to log in:

7.启动应用程序。 （不应观察到例外）。

8.单击**login **按钮（在主菜单的右侧）。 您将看到以下弹出窗口，允许输入用户名和密码登录：

![](/assets/78.png)

9. You also have the option to create a new user. In the previous popup, click on the **Create new account** link that can be found at the bottom right. This will load the following pop-up content:

9.您还可以选择创建新用户。 在上一个弹出窗口中，点击右下角的**Create new account**。 这将加载以下弹出内容：

![](/assets/79.png)

10. Let's create a new user with the following values:

10.让我们使用以下值创建一个新用户：

```
username: <marcus>
email: <marcus@chapter5.com>
password: <123456>
preferred currency: <USD>
```

> For the profile picture, you must create on your file system, the directory structure corresponding to the property pictures. user.path in cloudstreetmarket-api/src/main/resources/application.properties.
>
> 对于配置文件图片，必须在文件系统上创建与属性图片对应的目录结构。 user.path在cloudstreetmarket-api / src / main / resources / application.properties中。

Then, click on the user icon in order to upload a profile picture.

然后，单击用户图标以上传个人资料照片。

![](/assets/80.png)

Finally, hit the **Sign up** button and the popup should disappear.

最后，点击**Sign up**按钮，弹出窗口应该消失。

11. Now, call the following URI: http://cloudstreetmarket.com/api/users/marcus. The application should fetch the following persisted data for the Marcus user:

11.现在，调用以下URI：http://cloudstreetmarket.com/api/users/marcus。 应用程序应为Marcus用户提取以下持久性数据：

![](/assets/81.png)

## How it works...

The recipe at this stage preconfigures our entities so they comply with Spring Security. A couple of concepts about Spring Security are mentioned in this part and developed in the following sections.

在这个阶段的配方预先配置我们的实体，使他们遵守Spring Security。 本部分提到了有关Spring Security的几个概念，并在以下部分中进行了开发。

### Introduction to Spring Security

Spring Security is built around three core components: the SecurityContextHolder object, the SecurityContext, and the Authentication object.

The SecurityContextHolder object allows us to define and carry for one JVM a SecurityContextHolderStrategy implementation \(focused on storing and retrieving a SecurityContext\).

Spring Security基于三个核心组件构建：SecurityContextHolder对象，SecurityContext和Authentication对象。

SecurityContextHolder对象允许我们定义和携带一个JVM一个SecurityContextHolderStrategy实现（集中在存储和检索一个SecurityContext）。



> The SecurityContextHolder has the following static field: private static SecurityContextHolderStrategy strategy;
>
> SecurityContextHolder具有以下静态字段：private static SecurityContextHolderStrategy strategy;



By default, and in most of the designs, the selected-strategy uses Threadlocals \(ThreadLocalSecurityContextHolderStrategy\).

默认情况下，在大多数设计中，选择策略使用Threadlocals（ThreadLocalSecurityContextHolderStrategy）。

### ThreadLocal context holders

A Tomcat instance manages a Spring MVC servlet \(like any other servlet\) with multiple threads as the multiple HTTP requests come in. The code is as follows:

Tomcat实例在多个HTTP请求进入时管理具有多个线程的Spring MVC servlet（就像任何其他servlet一样）。代码如下：

```
final class ThreadLocalSecurityContextHolderStrategy implements SecurityContextHolderStrategy {
    private static final ThreadLocal<SecurityContext>
    contextHolder = new ThreadLocal<SecurityContext>();
    ...
}
```

Each thread allocated to a request on Spring MVC has access to a copy of the SecurityContext carrying an Authentication object for one user \(or one other identifiable thing\).

Once a copy of the SecurityContext is no longer referred, it gets garbage-collected.

Spring MVC上分配给请求的每个线程都可以访问一个SecurityContext的副本，该副本携带一个用户（或一个其他可识别的东西）的Authentication对象。

一旦SecurityContext的副本不再被引用，它就会被垃圾回收。

### Noticeable Spring Security interfaces

There is a bunch of noticeable interfaces in Spring Security. We will particularly visit Authentication, UserDetails, UserDetailsManager, and GrantedAuthority.

可见的Spring Security接口

Spring Security中有一堆值得注意的接口。 我们将特别访问Authentication，UserDetails，UserDetailsManager和GrantedAuthority。



### The Authentication interface

The Spring Authentication object can be retrieved from the SecurityContext. This object is usually managed by Spring Security but applications still often need to access it for their business.

Here is the interface for the Authentication object:

认证接口

Spring Authentication对象可以从SecurityContext中检索。 这个对象通常由Spring Security管理，但应用程序仍然需要为其业务访问它。

这里是Authentication对象的接口：

```
public interface Authentication extends Principal, Serializable {
    Collection<? extends GrantedAuthority> getAuthorities();
    Object getCredentials();
    Object getDetails();
    Object getPrincipal();
    boolean isAuthenticated();
    void setAuthenticated(boolean isAuthenticated) throws IllegalArgumentException;
}
```

It provides access to the Principal \(representing the identified user, entity, company or customer\), its credentials, its authorities and to some extra-details that may be needed. Now let's see how, from the SecurityContextHolder, a user can be retrieved:

它提供对Principal（表示所识别的用户，实体，公司或客户），其凭证，其权限以及可能需要的一些额外细节的访问。 现在让我们看看如何从SecurityContextHolder，用户可以检索：

```
Object principal = SecurityContextHolder.getContext().getAuthentication().getPrincipal();
if (principal instanceof UserDetails) {
    String username = ((UserDetails) principal).getUsername();
} else {
    String username = principal.toString();
}
```

The Principal class can be cast into the Spring UserDetails Type, which is exposed by the core framework. This interface is used as a standard bridge in several extension-modules\(Spring Social, Connect, Spring Security SAML, Spring Security LDAP, and so on.\).

Principal类可以转换为由Spring框架公开的Spring UserDetails类型。 此接口用作几个扩展模块（Spring Social，Connect，Spring Security SAML，Spring Security LDAP等）中的标准桥接。

### The UserDetails interface

The UserDetails implementations represent a Principal in an extensible and applicationspecific way.

You must be aware of the one-method UserDetailsService interface that provides the key-method loadUserByUsername for account-retrieval within the core framework:

UserDetails实现以可扩展和应用程序特定的方式表示Principal。

您必须知道单方法UserDetailsService接口，该接口为核心框架中的account-retrieval提供了键方法loadUserByUsername：

```
public interface UserDetailsService {
    UserDetails loadUserByUsername(String username) throws UsernameNotFoundException;
}

```

Spring Security offers two implementations for this interface:

CachingUserDetailsService and JdbcDaoImpl, whether we want to benefit from an in-memory UserDetailsService or from a JDBC-based UserDetailsService implementation. More globally, what usually matters is where and how users and roles are persisted so Spring Security can access this data on itself and process authentications.

Spring Security为此接口提供了两种实现：

CachingUserDetailsService和JdbcDaoImpl，无论我们想从内存中UserDetailsService还是从基于JDBC的UserDetailsService实现中受益。 在全球范围内，通常重要的是users和roles在何处以及如何持久化，因此Spring Security可以访问此数据本身并处理身份验证。

### Authentication providers

The way Spring Security accesses the user and role data is configured with the selection or the reference of an authentication-provider in the Spring Security configuration file with the security namespace.

Here are two examples of configuration when using the native UserDetailsService implementations:

验证提供程序

Spring Security访问用户和角色数据的方式使用具有安全命名空间的Spring Security配置文件中的认证提供程序的选择或引用进行配置。

下面是使用本机UserDetailsService实现时的两个配置示例：

```
<security:authentication-manager alias="authenticationManager">
    <security:authentication-provider>
        <security:jdbc-user-service data-source-ref="dataSource" />
    </security:authentication-provider>
</security:authentication-manager>
```

This first example specifies a JDBC-based UserDetailsService. The next example specifies an in-memory UserDetailsService.

第一个示例指定基于JDBC的UserDetailsService。 下一个示例指定一个内存中的UserDetailsService。

```
<security:authentication-manager alias="authenticationManager">
    <security:authentication-provider>
        <security:user-service id="inMemoryUserDetailService"/>
    </security:authentication-provider>
</security:authentication-manager>

```

In our case, we have registered our own UserDetailsService implementation\(communityServiceImpl\) as follows:

在我们的例子中，我们注册了我们自己的UserDetailsService实现（communityServiceImpl），如下所示：

```
<security:authentication-manager alias="authenticationManager">
    <security:authentication-provider user-serviceref='communityServiceImpl'>
        <security:password-encoder ref="passwordEncoder"/>
    </security:authentication-provider>
</security:authentication-manager>
```

We thought more appropriate to continue accessing the database layer through the JPA abstraction.

我们认为更适合继续通过JPA抽象来访问数据库层。

### The UserDetailsManager interface

Spring Security provides a UserDetails implementation org.sfw.security.core.userdetails.User, which can be used directly or extended. The User class is defined as follows:

Spring Security提供了一个UserDetails实现org.sfw.security.core.userdetails.User，它可以直接使用或扩展。 User类定义如下：

```
public class User implements UserDetails, CredentialsContainer {
    private String password;
    private final String username;
    private final Set<GrantedAuthority> authorities;
    private final boolean accountNonExpired;
    private final boolean accountNonLocked;
    private final boolean credentialsNonExpired;
    private final boolean enabled;
    ...
}
```

> Managing users \(create, update, and so on\) can be a shared responsibility for Spring Security. It is usually mainly performed by the application though.
>
> 管理用户（创建，更新等）可以是Spring Security的一个共同责任。 它通常主要由应用程序执行。

Guiding us towards a structure for UserDetails, Spring Security also provides a UserDetailsManager interface for managing users:

指导我们走向UserDetails的结构，Spring Security还提供了一个用于管理用户的UserDetailsManager接口：

```
public interface UserDetailsManager extends UserDetailsService {
    void createUser(UserDetails user);
    void updateUser(UserDetails user);
    void deleteUser(String username);
    void changePassword(String oldPassword, String newPassword);
    boolean userExists(String username);
}
```

Spring Security has two native implementations for non-persistent\(InMemoryUserDetailsManager\) and JDBC-based \(JdbcUserDetailsManager\)user-managements.

When deciding not to use a built-in authentication-provider, it is a good practice to implement the presented interfaces, especially for guaranteeing backward compatibility on the coming versions of Spring Security.

Spring Security有两个非持久性（InMemoryUserDetailsManager）和基于JDBC（JdbcUserDetailsManager）用户管理的本地实现。

当决定不使用内置的认证提供程序时，实现所提供的接口是一个很好的做法，特别是为了保证未来版本的Spring Security的向后兼容性。

The GrantedAuthority interface

Within Spring Security, GrantedAuthorities reflects the application-wide permissions granted to a Principal. Spring Security guides us towards a role-based authentication. This kind of authentication imposes the creation of groups of users able to perform operations.

在Spring Security中，GrantedAuthorities反映授予Principal的应用程序范围的权限。 Spring Security指导我们实现基于角色的身份验证。 这种认证强加了能够执行操作的用户组的创建。



> Unless there is a strong business meaning for a feature, do prefer for example ROLE\_ADMIN or ROLE\_GUEST to ROLE\_DASHBOARD or ROLE\_PAYMENT…
>
> 除非功能具有较强的业务含义，否则请优先选择例如ROLE\_ADMIN或ROLE\_GUEST到ROLE\_DASHBOARD或ROLE\_PAYMENT ...



Roles can be pulled out of the `Authentication` object from `getAuthorities()`, as an array of GrantedAuthority implementations.

The GrantedAuthority interface is quite simple:

Roles 可以从`getAuthorities()`中的`Authentication`对象中拉出，作为GrantedAuthority实现的数组。

GrantedAuthority接口很简单：

```
public interface GrantedAuthority extends Serializable {
    String getAuthority();
}
```

The GrantedAuthority implementations are wrappers carrying a textual representation for a role. These textual representations are potentially matched against the configuration attributes of secure objects \(we will detail this concept in the Authorizing on services and controllers recipe\).

The Role embedded in a GrantedAuthority, which is accessed from the `getAuthority()` getter, is more important to Spring Security than the wrapper itself.

GrantedAuthority实现是包含role的文本表示的包装器。 这些文本表示可能与安全对象的配置属性匹配（我们将在授权服务和控制器配方中详细说明此概念）。

嵌入在GrantedAuthority中的role（从`getAuthority()` getter访问）对于Spring Security比包装器本身更重要。

We have created our own implementation: the Authority that entity that has an association to User. The framework also provides the SimpleGrantedAuthority implementation.

In the last recipe, we will talk about the Spring Security authorization process. We will see that Spring Security provides an AccessDecisionManager interface and several AccessDecisionManager implementations. These implementations are based on voting and use AccessDecisionVoter implementations. The most commonly used of these implementations is the RoleVoter class.

我们创建了自己的实现：与User有关联的实体的权限。 该框架还提供了SimpleGrantedAuthority实现。

在最后一个配方中，我们将讨论Spring Security授权过程。 我们将看到Spring Security提供了一个AccessDecisionManager接口和几个AccessDecisionManager实现。 这些实现基于投票和使用AccessDecisionVoter实现。 这些实现中最常用的是RoleVoter类。

> The RoleVoter implementation votes positively for the user authorization when a configuration attribute \(the textual representation of an Authority\) starts with a predefined prefix. By default, this prefix is set to ROLE\_.
>
> 当配置属性（权限的文本表示）以预定义的前缀开始时，RoleVoter实现对用户授权投票为肯定。 默认情况下，此前缀设置为ROLE\_。

## There is more…

The Spring Security authentication and authorization process will be covered in depth in the Authorizing on services and controllers recipe. This section introduces more details from the Spring Security reference document.

Spring Security认证和授权过程将在授权服务和控制器配方中深入讨论。 本节介绍Spring Security参考文档的更多细节。

### Spring Security reference

The Spring Securitysecurity reference is an amazing source of theoretical and practical information.

Spring安全性参考

Spring Security安全参考是一个了不起的理论和实践信息源。

### Technical overview

The technical overview is a great introduction to the Spring Security Framework:

技术概述

技术概述是Spring Security Framework的一个很好的介绍：

http://docs.spring.io/spring-security/site/docs/3.0.x/reference/technical-overview.html

### Sample applications

The Spring Security reference provides many Spring Security examples on different authentications types \(LDAP, OPENID, JAAS, and so on.\). Other role-based examples can also be found at:

示例应用程序

Spring Security参考提供了许多针对不同认证类型（LDAP，OPENID，JAAS等）的Spring Security示例。 其他基于角色的例子也可以在：

http://docs.spring.io/spring-security/site/docs/3.1.5.RELEASE/reference/sample-apps.html

### Core services

Find out more about the built-in UserDetailsService implementations \(in-memory or JDBC\) at:

核心服务

有关内置UserDetailsService实现（内存或JDBC）的更多信息，请访问：

http://docs.spring.io/spring-security/site/docs/3.1.5.RELEASE/reference/core-services.html





