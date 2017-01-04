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



```
z
```



