Now that we have introduced JPA, its role, and the benefits of using Entities, we can now focus on how to configure our Spring application to handle them.

现在我们已经介绍JPA，它的作用以及使用Entities的好处，现在我们可以专注于如何配置我们的Spring应用程序来处理它们。

### Getting ready

As we said earlier, the JPA is a specification. Choosing a persistence provider \(Hibernate, OpenJPA, TopLink, and so on\) or a database provider for an application won't be a commitment as long as they match the standards.

We will see that our JPA configuration in Spring is done by defining two beans: **datasource **and **entityManagerFactory**. Then, the optional Spring Data JPA library offers a JPA repository abstraction that is able to surprisingly simplify some database operations.

正如我们前面所说，JPA是一个规范。 选择持久性提供程序（Hibernate，OpenJPA，TopLink等）或一个应用程序的数据库提供程序将不会是承诺，只要它们符合标准。

我们将看到Spring中的JPA配置是通过定义两个bean来完成的：**datasource**和**entityManagerFactory**。 然后，可选的Spring Data JPA库提供了一个JPA存储库抽象，能够令人惊讶地简化一些数据库操作。

### How to do it...

1. From the **Git Perspective** in Eclipse, check out the latest version of the v3.x.x branch.

2. As previously introduced, we have added a couple of beans to the Spring configuration file \(in the core module\) csmcore-config.xml:

1.从Eclipse中的**Git Perspective**中，查看v3.x.x分支的最新版本。

2.如前所述，我们向Spring配置文件（在核心模块中）添加了一些bean csmcore-config.xml：

```
<jpa:repositories base-package="edu.zc.csm.core.daos" />
<bean id="dataSource" class="org.sfw.jdbc.datasource.DriverManagerDataSource>
    <property name="driverClassName">
        <value>org.hsqldb.jdbcDriver</value>
    </property>
    <property name="url">
        <value>jdbc:hsqldb:mem:csm</value>
    </property>
    <property name="username">
        <value>sa</value>
    </property>
</bean>

<bean id="entityManagerFactory" class="org.sfw.orm.jpa.LocalContainerEntityManagerFactoryBean">
    <property name="persistenceUnitName" value="jpaData"/>
    <property name="dataSource" ref="dataSource" />
    <property name="jpaVendorAdapter">
        <beanclass="org.sfw.orm.jpa.vendor.HibernateJpaVendorAdapter"/>
    </property>
    <property name="jpaProperties">
        <props>
            <prop key="hibernate.dialect">
                org.hibernate.dialect.HSQLDialect
            </prop>
            <prop key="hibernate.show_sql">true</prop>
            <prop key="hibernate.format_sql">false</prop>
            <prop key="hibernate.hbm2ddl.auto">create-drop</prop>
            <prop key="hibernate.default_schema">public</prop>
        </props>
    </property>
</bean>
```

3. Finally, the following dependencies have been added to the parent and core projects:

3.最后，以下依赖项已添加到父项目和核心项目中：

*  org.springframework.data:spring-data-jpa \(1.0.2.RELEASE\)
* org.hibernate.javax.persistence:hibernate-jpa-2.0-api\(1.0.1.Final\)

* org.hibernate:hibernate-core \(4.1.5.SP1\)



Adding this dependency causes the Maven enforcer plugin to raise a version conflict with jboss-logging. This is why jboss-logging has been excluded from this thirdparty library and referenced as a dependency on its own:

添加此依赖性会导致Maven实施程序插件引发与jboss-logging的版本冲突。 这就是为什么jboss-logging已经从这个第三方库中排除，并且被引用为它自己的依赖：

* org.hibernate:hibernate-entitymanager \(4.1.5.SP1\)

jboss-logging has also been excluded from this third-party library because, it is now referenced as a dependency on its own:

jboss-logging也被排除在这个第三方库之外，因为它现在被引用为它自己的依赖：

* org.jboss.logging:jboss-logging \(3.1.0.CR1\)
* org.hsqldb:hsqldb \(2.3.2\)

* org.javassist:javassist \(3.18.2-GA\)

*  org.apache.commons:commons-dbcp2 \(2.0.1\)

 

## How it works...

We are going to review these three configuration points: the **dataSource **bean, the **entityManagerFactory **bean, and Spring Data JPA.

我们将回顾这三个配置点：**dataSource** bean，**entityManagerFactory** bean和Spring Data JPA。

### The Spring-managed DataSource bean

Because creating a database connection is time consuming, especially through the network layers, and because it is sensible to share and reuse an opened connection or a connection pool, a datasource has the duty of optimizing the use of these connections. It is a scalability indicator and also a highly configurable interface between the database and the application.

In our example, Spring manages the datasource just as for any other bean. The datasource can be created through the application or can be accessed remotely from a JNDI lookup \(if the choice is made of giving up the connection management to the container\). In both cases, Spring will manage the configured bean, providing the proxy that our application needs.

Also in our example, we are making use of the Apache Common DBCP 2 datasource\(released in 2014\).

Spring管理的DataSource bean

因为创建数据库连接是耗时的，特别是通过网络层，并且因为共享和重用已打开的连接或连接池是合理的，所以数据源具有优化这些连接的使用的义务。 它是一个可扩展性指标，也是数据库和应用程序之间高度可配置的接口。

在我们的示例中，Spring像任何其他bean一样管理数据源。 数据源可以通过创建应用程序，或者可以通过JNDI查找（如果选择放弃连接管理到容器）来远程访问。 在这两种情况下，Spring将管理配置的bean，提供我们的应用程序需要的代理。

同样在我们的例子中，我们使用Apache Common DBCP 2数据源（2014年发布）。



> In a production environment, it might be a good idea to switch to a JNDI-based datasource, such as the native Tomcat JDBC pool.
>
> The Tomcat website clearly suggests a significant gain in performance when using the Tomcat JDBC pool instead of DBCP1.x on highly concurrent systems.
>
> 在生产环境中，切换到基于JNDI的数据源（例如本机Tomcat JDBC池）可能是个好主意。
>
> Tomcat网站清楚地表明，在高并发系统上使用Tomcat JDBC池而不是DBCP1.x时，性能会显着提高。



### The EntityManagerFactory bean and its persistence unit

As its name suggests, the EntityManagerFactory bean produces entity managers. The configuration of EntityManagerFactory conditions the entity manager behavior.

The configuration of the EntityManagerFactory bean reflects the configuration of one persistence unit. In a Java EE environment, one or more persistent units can be defined and configured inside a persistence.xml file, which is unique in the application archive.

In a Java SE environment \(our case\), the presence of a persistence.xml file is made optional with Spring. The configuration of the EntityManagerFactory bean almost completely overrides the configuration of the persistence unit.

The configuration of a persistence unit, and, therefore, of an EntityManagerFactory bean, can either declare the covered Entities individually or scan packages to find them.

EntityManagerFactory bean及其持久化单元

顾名思义，EntityManagerFactory bean生成实体管理器。  EntityManagerFactory的配置条件实体管理器行为。

EntityManagerFactory bean的配置反映一个持久性单元的配置。 在Java EE环境中，可以在persistence.xml文件中定义和配置一个或多个持久性单元，persistence.xml文件在应用程序归档中是唯一的。

在Java SE环境（我们的示例）中，persistence.xml文件的存在对于Spring是可选的。  EntityManagerFactory bean的配置几乎完全覆盖持久性单元的配置。

因此，持久化单元的配置以及EntityManagerFactory bean的配置可以单独声明覆盖的实体，也可以扫描包以查找它们。



> A persistence unit can be seen as a subarea among the horizontal scaling ecosystem. A product can be broken down into wars \(web archives\) for each the functional area. Functional areas can be represented with a selection of Entities that are delimited by a persistence unit.
>
> The main point is to avoid creating Entities that overlap different persistence units.
>
> 持久性单元可以被看作是水平扩展生态系统中的子区域。 产品可以分为每个功能区域的wars （网络存档）。 功能区域可以用由持久性单元定界的实体的选择来表示。
>
> 主要的是避免创建重叠不同持久性单元的实体。



### The Spring Data JPA configuration

We are about to use some very useful tools from the Spring Data JPA project. These tools aim to simplify the development \(and maintenance\) of the persistence layers. The most interesting tool is probably the repository abstraction. You will see that providing implementations for some database queries can be optional. An implementation of the repository interface will be generated at runtime from the method signatures if they match a standard in their declarations.

For example, Spring will infer the implementation of the following method signature \(if the User entity has a String userName field\):

`List<User> findByUserName(String username);`

A more extended example of our bean configuration on Spring Data JPA could be the following:

Spring Data JPA配置

我们将使用Spring Data JPA项目中的一些非常有用的工具。 这些工具旨在简化持久层的开发（和维护）。 最有趣的工具可能是存储库抽象。 您将看到，为某些数据库查询提供实现是可选的。 如果运行时它们的声明中的标准匹配，那么将在运行时从方法签名生成仓库接口的实现。

例如，Spring将推断出以下方法签名的实现（如果User实体有一个String userName字段）：

`List<User> findByUserName(String username);`

Spring Data JPA上我们的bean配置的更多扩展示例可以是以下：

```
<jpa:repositories base-package="edu.zipcloud.cloudstreetmarket.core.daos" 
    entity-manager-factory-ref="entityManagerFactory"
    transaction-manager-ref="transactionManager"/>
```

As you can see, Spring Data JPA contains a custom namespace that allows us to define the following repository beans. This namespace can be configured as follow:

* Providing a base-package attribute in this namespace is mandatory to restrict the lookup for Spring Data repositories.
* Providing an entity-manager-factory-ref attribute is optional if you have only one EntityManagerFactory bean configured in ApplicationContext. It explicitly wires EntityManagerFactory, which is to be used with the detected repositories.

* Providing a transaction-manager-ref attribute is also optional if you have only one PlatformTransactionManager bean configured in ApplicationContext. It explicitly wires PlatformTransactionManager, which is to be used with the detected repositories.

如您所见，Spring Data JPA包含一个自定义命名空间，允许我们定义以下存储库bean。 此命名空间可以配置如下：

* 在此命名空间中提供`base-package`属性是强制的，以限制对Spring数据存储库的查找。
* 如果您在ApplicationContext中只配置了一个EntityManagerFactory bean，则提供`entity-manager-factory-ref`属性是可选的。 它显式地连接EntityManagerFactory，它将与检测到的存储库一起使用。

* 如果您在ApplicationContext中只配置了一个PlatformTransactionManager bean，则提供`transaction-manager-ref`属性也是可选的。 它显式地传递PlatformTransactionManager，它将与检测到的存储库一起使用。



More details can be found about this configuration on the project website at:

有关此配置的更多详细信息，请访问项目网站：

http://docs.spring.io/spring-data/jpa/docs/1.4.3.RELEASE/reference/html/jpa.repositories.html.

### See also

f **HikariCP DataSource**: HikariCP \(from its BoneCP ancestor\) is an open source Apache v2 licensed project. It appears to perform better in speed and reliability than any other DataSource. This product should probably be considered considered when choosing a datasource nowadays. Refer to https://brettwooldridge.github.io/HikariCP for more information on this.

**HikariCP DataSource**：HikariCP（来自其BoneCP祖先）是一个开源Apache v2许可项目。 它看起来在速度和可靠性上比任何其他DataSource更好。 在现在选择数据源时，应考虑此产品。 有关这方面的更多信息，请参考https//brettwooldridge.github.io/HikariCP。





