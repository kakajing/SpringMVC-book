This recipe is an example of how to inject Spring managed beans into integration test classes. Even for IT tests, whose first objective is to assess the backend as a blackbox, it is sometimes necessary to reach out technical objects from the intermediate layer.

这个配方是一个例子，说明如何将Spring管理的bean注入到集成测试类中。 即使对于IT测试，其第一个目标是将后端评估为黑盒，有时也需要从中间层接触技术对象。

# Getting ready

We will see how to reuse an instance of a Spring managed datasource to be injected in our test class. This datasource will help us to build an instance of jdbcTemplate. From this jdbcTemplate, we will query the database and simulate/validate processes that couldn't be tested otherwise.

我们将看到如何重用一个Spring托管数据源的实例，以便在我们的测试类中注入。 这个数据源将帮助我们构建一个jdbcTemplate的实例。 从这个jdbcTemplate，我们将查询数据库并模拟/验证无法测试的进程。

# How to do it…

1.We have `@Autowired` a dataSource SpringBean in our UserControllerIT test. This bean is defined in the test-specific Spring configuration file \(spring-context-api-test.xml\) resources directory \(cloudstreetmarket-api\):

1.我们在我们的UserControllerIT测试中有`@Autowired`一个dataSource SpringBean。 这个bean在测试特定的Spring配置文件（spring-context-api-test.xml）资源目录（cloudstreetmarket-api）中定义：

![](/assets/161.png)

```java
<context:property-placeholderlocation="file:${user.home}/app/cloudstreetmarket.properties""/>
<bean id="dataSource" class="org.apache.commons.dbcp2.BasicDataSource" destroy-method="close"">
    <property name="driverClassName"">
        <value>com.mysql.jdbc.Driver</value>
    </property>
    <property name="url"">
        <value>${db.connection.url}</value>
    </property>
    <property name="username"">
        <value>${db.user.name}</value>
    </property>
    <property name="password"">
        <value>${db.user.passsword}</value>
    </property>
    <property name="defaultReadOnly">
        <value>false</value>
    </property>
</bean>
```

A jdbcTemplate instance is created in the UserControllerIT class from the `@Autowired` dataSource bean:

在UserControllerIT类中从`@Autowired` dataSource bean中创建一个jdbcTemplate实例：

```java
@Autowired
private JdbcTemplate jdbcTemplate;

@Autowired
public void setDataSource(DataSource dataSource) {
    this.jdbcTemplate = new JdbcTemplate(dataSource);
}
```

2.We use jdbcTemplate to insert and delete Social Connections directly in the database \(see Chapter 5, Authenticating with Spring MVCA\). This allows us to bypass and simulate a successful user OAuth2 authentication flow \(that normally happens through the web browser\).

For deleting social connections, we have created the following private method that is called as needed by the test\(s:\):

2.我们使用jdbcTemplate在数据库中直接插入和删除Social Connections（参见第5章使用Spring MVCA进行身份验证）。 这允许我们绕过和模拟成功的用户OAuth2身份验证流程（通常通过Web浏览器进行）。

对于删除social connections，我们创建了以下私有方法，它由test\(s:\)根据需要调用：

```java
private void deleteConnection(String spi, String id) {
    this.jdbcTemplate.update("delete from userconnection where providerUserId = ? and userId = "?", new Object[] {spi, id});
}

```

3.At the very top of the UserControllerIT class, the following two annotations can be noticed:

* [ ] `@RunWith(SpringJUnit4ClassRunner.class)` tells JUnit to run with a custom extension of JUnit \(SpringJUnit4ClassRunner\) that supports the Spring TestContext Framework.

* [ ] `@ContextConfiguration("classpath:spring-context-api-test.xml")`specifies where and how to load and configure the Spring application context:

3.在UserControllerIT类的最顶部，可以注意到以下两个注解：

* [ ] `@RunWith(SpringJUnit4ClassRunner.class)`告诉JUnit使用支持Spring TestContext框架的JUnit（SpringJUnit4ClassRunner）的自定义扩展来运行。

* [ ] `@ContextConfiguration("classpath:spring-context-api-test.xml")`指定在何处以及如何加载和配置Spring应用程序上下文：

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:spring-context-apitest.xml")
public class UserControllerIT extends AbstractCommonTestUser{
    private static User userA;
    private static User userB;
    ...
}
```

# How it works...

## SpringJUnit4ClassRunner

In its design, the SpringJUnit4ClassRunner is a direct subclass of the JUnit's BlockJUnit4ClassRunner. SpringJUnit4ClassRunner that initializes when a TestContextManager is loaded. A TestContextManager manages the life cycle of a TestContext and can also reflect test events to the registered TestExecutionListeners \(from `@BeforeClass`, `@AfterClass`, `@Before`, and `@After` annotations\).

在其设计中，SpringJUnit4ClassRunner是JUnit的BlockJUnit4ClassRunner的直接子类。 SpringJUnit4ClassRunner，当加载TestContextManager时初始化。 TestContextManager管理TestContext的生命周期，并且还可以将测试事件反映到注册的TestExecutionListeners（来自`@BeforeClass`，`@AfterClass`，`@Before`和`@After`注解）。

By loading a Spring context, the SpringJUnit4ClassRunner Spring context,SpringJUnit4ClassRunner enables the possibility use Spring managed beans in test classes. The SpringJUnit4ClassRunner also supports a set of annotations \(either from JUnit or from Spring test\) that can be used in test classes. The use of these annotations can be trusted for subsequently providing suitable life cycle management to context-defined objects.

通过加载Spring上下文，SpringJUnit4ClassRunner Spring上下文，SpringJUnit4ClassRunner启用在测试类中使用Spring托管的bean的可能性。 SpringJUnit4ClassRunner还支持一组可以在测试类中使用的注解（来自JUnit或来自Spring测试）。 可以信任这些注解的使用，以便随后向上下文定义的对象提供合适的生命周期管理。

Those annotations are `@Test` \(with its expected and timeout annotation parameters\),` @Timed`, `@Repeat`, `@Ignore`, `@ProfileValueSourceConfiguration`, and `@IfProfileValue`.

这些注解是`@Test`（具有其期望和超时注解参数），`@Timed`，`@Repeat`，`@Ignore`，`@ProfileValueSourceConfiguration`和`@IfProfileValue`。

## The @ContextConfiguration annotation

This class-level annotation is specific to Spring Test. It defines how and where to load a Spring Context for the test class.

Our definition in the recipe targets a specific Spring XML configuration file `@ContextConfiguration("classpath:spring-context-api-test.xml")`.

However, since Spring 3.1 the contexts can be defined programmatically, `@ContextConfiguration` can also target configuration classes as follows:

此类级别注释特定于Spring测试。 它定义了如何以及在何处加载测试类的Spring上下文。

我们在配方中的定义针对一个特定的Spring XML配置文件`@ContextConfiguration("classpath:spring-context-api-test.xml")`。

但是，从Spring 3.1开始，上下文可以以编程方式定义，`@ContextConfiguration`也可以按如下所示定位配置类：

```java
@ContextConfiguration(classes={AnnotationConfig.class, WebSocketConfig.class})
```

As shown in the following snippet, both declaration types can be combined in the same annotation:

如以下代码段所示，两个声明类型可以组合在同一个注解中：

```java
@ContextConfiguration(classes={AnnotationConfig.class, WebSocketConfig.class}, 
        locations={"classpath:spring-context-apitest.xml"})
```

# There is more…

We will see more in this section about the Spring JdbcTemplate that has been used for test purposes.

我们将在本节中看到更多关于用于测试目的的Spring JdbcTemplate。

## JdbcTemplate

In Chapter 1, Setup Routine for an Enterprise Spring Application, we have introduced the different modules that make the Spring Framework what it is today. One group of modules is **Data Access and Integration**. This group contains the JDBC, ORM, OXM, JMS, and transactions modules.

The JdbcTemplate is a key-class part of the Spring JDBC core package. It reliably allows performing of database operations with straightforward utility methods and also provides an abstraction for big chunks of boilerplate code. Once more, this tool saves us time and offers patterns to design quality products.

在第1章“安装企业版Spring Application”中，我们介绍了使用Spring框架成为今天的不同模块。 一组模块是**数据访问和集成**。 此组包含JDBC，ORM，OXM，JMS和事务模块。

JdbcTemplate是Spring JDBC核心包的键类部分。 它可靠地允许使用简单的实用程序方法执行数据库操作，并且还为大块的样板代码提供抽象。 再次，这个工具节省了我们的时间和提供模式设计优质的产品。

## Abstraction of boilerplate logic

Let's consider as an example the method in our test class that deletes connections:

抽象的样板逻辑

让我们以在我们的测试类中删除连接的方法为例：

```java
jdbcTemplate.update("delete from userconnection where providerUserId = ? and userId = "?", new Object[] {spi, id});
```

Using jdbcTemplate, deleting a database element is a one-line instruction. It creates a PreparedStatement under the hood, chooses the right Type, depending upon the arguments we actually pass as values, and it manages the database connection for us, making sure to close this connection whatever happens.

使用jdbcTemplate删除数据库元素是一个单行指令。 它在底层创建一个PreparedStatement，选择正确的类型，取决于我们实际传递的参数值，它管理我们的数据库连接，确保关闭这个连接，无论发生什么。

The` jdbcTemplate.update` method has been designed to issue a single SQL update operation. It can be used for inserts, updates, and also deletes.

As often in Spring, jdbcTemplate also transforms the produced checked exceptions \(if any\) into unchecked exceptions. Here, the potential SQLExceptions would be wrapped in a RuntimeException.

`jdbcTemplate.update`方法已设计为发出单个SQL更新操作。 它可以用于插入，更新和删除。

和Spring一样，jdbcTemplate还将生成的检查异常（如果有）转换为未检查异常。 这里，潜在的SQLExceptions将被包装在RuntimeException中。

## Extraction of auto-generated IDs

The `jdbcTemplate.update` method also offers other argument Types:

提取自动生成的ID

`jdbcTemplate.update`方法还提供了其他参数类型：

```java
jdbcTemplate.update(final PreparedStatementCreator psc, final KeyHolder generatedKeyHolder);
```

In the case of an insert, this method can be called when needed to read and potentially reuse the generated ID \(which is unknown before the query execution\).

In our example, if we would have wanted to reuse the generated connection IDs when inserting new connections, we would have done it as follows:

在insert的情况下，当需要读取和潜在地重用所生成的ID（在查询执行之前是未知的）时，可以调用该方法。

在我们的例子中，如果我们想在插入新连接时重用生成的连接ID，我们将按如下方式进行：

```java
KeyHolder keyHolder = new GeneratedKeyHolder();
jdbcTemplate.update(new PreparedStatementCreator() {
    public PreparedStatement createPreparedStatement(Connection connection) throws SQLException {
        PreparedStatement ps = connection.prepareStatement("insert into userconnection (accessToken, ... , secret, userId ) values (?, ?, ... , ?, ?)", new String[] {"id""});
        ps.setString(1, generateGuid());
        ps.setDate(2, new Date(System.currentTimeMillis()));
        ...
        return ps;
    }
}, keyHolder);
Long Id = keyHolder.getKey().longValue();
```

But we didn't specifically require such a use case.

但是我们没有特别要求这样的用例。

