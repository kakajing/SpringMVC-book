Integration Tests are as important as unit tests. They validate a feature from a higher level, and involve more components or layers at the same time. Integration tests \(IT tests\) are given more importance when an environment needs to evolve fast. Design processes often require iterations, and unit tests sometimes seriously impact our ability to refactor, while higher level testing is less impacted comparatively.

集成测试与单元测试同样重要。 它们从更高级别验证功能，并且同时涉及更多的组件或层。 当环境需要快速发展时，集成测试（IT测试）更加重要。 设计过程通常需要迭代，单元测试有时会严重影响我们重构的能力，而较高级别的测试则较少受到影响。

# Getting ready

This recipe shows how to develop automated IT tests that focus on Spring MVC web services. Such IT Tests are not behavioral tests as they don't assess the user interface at all. To test behaviors, an even higher testing level would be necessary, simulating the User journey through the application interface.

We will configure the Cargo Maven Plugin to stand up a test environment as part of the preintegration-test Maven phase. On the integration-test phase, we will get the Maven failsafe plugin to execute our IT Tests. Those IT Tests will make use of the REST-assured library to run HTTP requests against the test environment and assert the HTTP responses.

这个配方展示了如何开发自动化IT测试，专注于Spring MVC Web服务。 这样的IT测试不是行为测试，因为它们根本不评估用户界面。 为了测试行为，需要更高的测试级别，模拟用户通过应用程序界面的旅程。

我们将配置Cargo Maven插件来架起一个测试环境作为preintegration-test Maven阶段的一部分。 在集成测试阶段，我们将获得Maven failsafe plugin来执行我们的IT测试。 这些IT测试将利用REST-assured库对测试环境运行HTTP请求，并断言HTTP响应。

# How to do it…

1. We have designed Integration tests in the cloudstreetmarket-api module. These tests are intended to test the API controller methods.

1.我们在cloudstreetmarket-api模块中设计了集成测试。 这些测试旨在测试API控制器方法。



![](/assets/157.png)  


2. The great Rest-assured library comes with the following Maven dependency:

2.丰富的Rest-sure库提供了以下Maven依赖：

```java
<dependency>
    <groupId>com.jayway.restassured</groupId>
    <artifactId>rest-assured</artifactId>
    <version>2.7.0</version>
</dependency>
```

3. A typical example of an IT Test using REST-assured would be the following `UserControllerIT.createUserBasicAuth()`:

3.使用REST-assured 的IT测试的典型示例是以下`UserControllerIT.createUserBasicAuth()`：

```java
public class UserControllerIT extends AbstractCommonTestUser{

    private static User userA;
    
    @Before
    public void before(){
        userA = new User.Builder()
                .withId(generateUserName())
                .withEmail(generateEmail())
                .withCurrency(SupportedCurrency.USD)
                .withPassword(generatePassword())
                .withLanguage(SupportedLanguage.EN)
                .withProfileImg(DEFAULT_IMG_PATH)
                .build();
    }
    
    @Test
    public void createUserBasicAuth(){
    
        Response responseCreateUser = given().contentType("application/json;charset=UTF-8")
                    .accept("application/json"")
                    .body(userA)
                    .expect
                    .when()
                    .post(getHost() + CONTEXT_PATH + "/users");
                    
        String location = responseCreateUser.getHeader("Location");
        assertNotNull(location);
        Response responseGetUser = given().expect()
                    .log().ifError()
                    .statusCode(HttpStatus.SC_OK)
                    .when()
                    .get(getHost() + CONTEXT_PATH + location + JSON_SUFFIX);
                    
        UserDTO userADTO = deserialize(responseGetUser.getBody().asString());
        assertEquals(userA.getId(), userADTO.getId());
        assertEquals(userA.getLanguage().name(), userADTO.getLanguage());
        assertEquals(HIDDEN_FIELD, userADTO.getEmail());
        assertEquals(HIDDEN_FIELD, userADTO.getPassword());
        assertNull(userA.getBalance());
    }
}
```

4. Because they take longer to execute, we wanted to decouple the IT Tests execution from the main Maven life cycle. We have associated those IT Tests to a Maven profile named integration.

4.因为它们需要更长的执行时间，我们希望将IT测试执行与主Maven生命周期解耦。 我们将这些IT测试关联到一个名为集成的Maven配置文件。

> Maven profiles offer the possibility to optionally enrich a Maven build with extra life cycle bindings. For instance, our integration profile is activated passing this profile id as Profile argument in the usual command:
>
> Maven profiles offer提供有选择地充实Maven构建额外的生命周期绑定的可能性。例如，我们的集成配置文件被激活，在常规命令中将此配置文件id作为Profile参数传递：
>
> $ mvn clean install -P integration

5. For our API IT tests, we have located the profile-specific configuration in the cloudstreetmarket-api pom.xml file:

5.对于我们的API IT测试，我们在cloudstreetmarket-api pom.xml文件中找到了profile-specific 配置文件的配置：

```java
<profiles>
    <profile>
    <id>integration</id>
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-failsafe-plugin</artifactId>
                <version>2.12.4</version>
                <configuration>
                    <includes>
                        <include>**/*IT.java</include>
                    </includes>
                    <excludes>
                        <exclude>**/*Test.java</exclude>
                    </excludes>
                </configuration>
                <executions>
                    <execution>
                        <id>integration-test</id>
                        <goals>
                            <goal>integration-test</goal>
                        </goals>
                    </execution>
                    <execution>
                        <id>verify</id>
                        <goals><goal>verify</goal></goals>
                    </execution>
                </executions>
            </plugin>
                <plugin>
                    <groupId>org.codehaus.cargo</groupId>
                    <artifactId>cargo-maven2-plugin</artifactId>
                    <version>1.4.16</version>
                <configuration>
                    <wait>false</wait>
                    <container>
                        <containerId>tomcat8x</containerId>
                        <home>${CATALINA_HOME}</home>
                        <logLevel>warn</logLevel>
                    </container>
                    <deployer/>
                    <type>existing</type>
                    <deployables>
                        <deployable>
                            <groupId>edu.zc.csm</groupId>
                            <artifactId>cloudstreetmarket-api</artifactId>
                            <type>war</type>
                            <properties>
                                <context>api</context>
                            </properties>
                        </deployable>
                    </deployables>
                </configuration>
                <executions>
                    <execution>
                        <id>start-container</id>
                        <phase>pre-integration-test</phase>
                        <goals>
                            <goal>start</goal>
                            <goal>deploy</goal>
                        </goals>
                    </execution>
                <execution>
                    <id>stop-container</id>
                    <phase>post-integration-test</phase>
                    <goals>
                        <goal>undeploy</goal>
                        <goal>stop</goal>
                    </goals>
                </execution>
            </executions>
            </plugin>
        </plugins>
    </build>
    </profile>
</profiles>
```

6. Before attempting to run them on your machine, check that you have a **CATALINA\_HOME** environment variable pointing to your Tomcat directory. If not, you must create it. The variable to set should be the following \(if you have followed Chapter 1, Setup Routine for an Enterprise Spring Application\):

* [ ] C:\tomcat8: on MS Windows

* [ ]  /home/usr/{system.username}/tomcat8: on Linux

* [ ]  /Users/{system.username}/tomcat8: on Mac OS X

7. Also, ensure that Apache HTTP, Redis, and MySQL are up and running on your local machine \(see previous chapter if you have skipped it\).

8. When ready:

* [ ] either execute the following Maven command in your Terminal \(if you have the Maven directory in your path\):

**`mvn clean verify -P integration`**

* [ ] or create a shortcut for this custom build in your Eclipse IDE from the **Run \| Run Configurations…** menu. The Build configuration to create is the following: 

6.在尝试在计算机上运行它们之前，请检查是否有一个指向Tomcat目录的**CATALINA\_HOME**环境变量。 如果没有，您必须创建它。 要设置的变量应该如下（如果您已按照第1章）：

7.另外，确保Apache HTTP，Redis和MySQL在本地机器上启动并运行（如果您跳过它，请参见上一章）。

8.准备就绪：

* [ ] 在您的终端中执行以下Maven命令（如果您的路径中有Maven目录）：**`mvn clean verify -P integration`**

* [ ] 或者在Eclipse IDE中从**Run \| Run Configurations…**菜单。 要创建的构建配置如下：

![](/assets/158.png)

9. Running this command \(or shortcut\) should:

1. deploy the** api.war** to the local Tomcat Server
2. start the local Tomcat

3. execute the test classes matching the \*\*/\*IT.java pattern 

If all the tests pass, you should see the \[INFO\] BUILD SUCCESS message.

9.运行此命令（或快捷方式）应该：

1. 将**api.war**部署到本地Tomcat服务器
2. 启动本地Tomcat

3. 执行匹配\*\* / \* IT.java模式的测试类

如果所有测试通过，您应该看到\[INFO\] BUILD SUCCESS消息。

10. In between, when the build comes to the API, you should see the following bit of stack trace suggesting the successful execution of our IT tests:

10.期间，当构建到达API时，您应该看到以下位的堆栈跟踪建议我们的IT测试的成功执行：

![](/assets/159.png)

# How it works...

We will explain in this section why we have introduced the Maven failsafe plugin, how the Cargo Plugin configuration satisfies our needs, how we have used REST-assured, and how useful this REST-assured library is.

我们将在本节中解释为什么我们介绍了Maven failsafe plugin，Cargo Plugin配置如何满足我们的需求，我们如何使用REST-assured，以及这个REST-assured库是多么有用。

## Maven Failsafe versus Maven Surefire

We are using Maven failsafe to run Integration tests and Maven Surefire for unit tests. This is a standard way of using these plugins. The following table reflects this point, with the Plugins' default naming patterns for test classes:

我们使用Maven failsafe运行集成测试和Maven Surefire进行单元测试。 这是使用这些插件的标准方式。 下表反映了这一点，以及测试类的插件的默认命名模式：

![](/assets/160.png)

For Maven Failsafe, you can see that our overridden pattern inclusion/exclusion was optional. About the binding to Maven build phases, we have chosen to trigger the execution of our integration tests on the integration-test and verify phases.

对于Maven Failsafe，您可以看到我们的覆盖模式inclusion/exclusion是可选的。 关于Maven构建阶段的绑定，我们选择在集成测试和验证阶段触发集成测试的执行。

## Code Cargo

Cargo is a lightweight library that offers standard API for operating several supported containers \(Servlet and JEE containers\). Examples of covered API operations are artifacts' deployments, remote deployments and container start/stop. When used through Maven, Ant, or Gradle, it is mostly used for its ability to provide support to Integration Tests but can also serve other scopes.

Cargo是一个轻量级库，提供用于操作多个支持的容器（Servlet和JEE容器）的标准API。 覆盖的API操作的示例是工件的部署，远程部署和容器start/stop。 当通过Maven，Ant或Gradle使用时，它主要用于为集成测试提供支持，但也可以为其他范围提供服务。

## Cargo Maven Plugin

We have used Cargo through its Maven plugin org.codehaus.cargo:cargo-maven2-plugin to automatically prepare an integration environment that we can run integration tests against. After the integration tests, we expect this environment to shut down.

我们使用Cargo通过其Maven插件org.codehaus.cargo：cargo-maven2-plugin自动准备一个集成环境，我们可以运行集成测试。 在集成测试之后，我们预计这个环境会关闭。

## Binding to Maven phases

The following executions have been declared as part of the cargo-maven2-plugin configuration:

以下执行已被声明为cargo-maven2-plugin配置的一部分：

```java
<executions>
    <execution>
        <id>start-container</id>
        <phase>pre-integration-test</phase>
        <goals>
            <goal>start</goal>
            <goal>deploy</goal>
        </goals>
    </execution>
    <execution>
        <id>stop-container</id>
        <phase>post-integration-test</phase>
        <goals>
            <goal>undeploy</goal>
            <goal>stop</goal>
        </goals>
    </execution>
</executions>
```

Let's visit what happens when the mvn install command is executed.

The install is a phase of the default Maven life cycle. As explained in Chapter 1, Setup Routine for an Enterprise Spring Application, the default life cycle has 23 build phases from validate to deploy. The install phase is the 22nd, so 22 phases are checked to see whether there are plugin goals that could be attached to them.

让我们来看看执行mvn install命令时会发生什么。

install 是默认Maven生命周期的一个阶段。 如第1章“安装企业版Spring Application”所述，默认生命周期有23个从验证到部署的构建阶段。 安装阶段是22阶段，因此检查22个阶段以查看是否有可附加到它们的插件目标。

Here, the pre-integration-test phase \(that appears in the default life cycle between validate and install\) will trigger the processes that are located under the start and deploy goals of our maven Cargo plugin. It is the same logic with post-integration-test triggers the undeploy and stop goals.

Before the IT tests execution, we start and deploy the Tomcat server. These IT tests are processed with Maven failsafe in the integration-test phase. Finally, the Tomcat server is undeployed and stopped.

IT Tests can also be executed with the verify phase \(if the server is started out of the default Maven life cycle\).

这里，预集成测试阶段（出现在验证和安装之间的默认生命周期中）将触发位于我们的Maven Cargo插件的启动和部署目标下的进程。 它是同样的逻辑与后集成测试触发解除部署和停止目标。

在IT测试执行之前，我们开始并部署Tomcat服务器。 这些IT测试在集成测试阶段使用Maven failsafe处理。 最后，Tomcat服务器被取消部署并停止。

IT测试也可以使用验证阶段执行（如果服务器从默认Maven生命周期开始）。

## Using an existing Tomcat instance

In the Cargo Maven plugin configuration, we are targeting an existing instance of Tomcat. Our application is currently depending upon MySQL, Redis, Apache HTTP, and a custom session management. We have decided that the IT Tests execution will be required to be run in a proper integration environment.

Without all these dependencies, we would have got Cargo to download a Tomcat 8 instance.

使用现有的Tomcat实例

在Cargo Maven插plugin 配置中，我们将目标定位到Tomcat的现有实例。 我们的应用程序目前取决于MySQL，Redis，Apache HTTP和自定义会话管理。 已决定IT测试执行将被要求在一个适当的集成环境中运行。

没有所有这些依赖关系，我们会让Cargo下载一个Tomcat 8实例。

## Rest assured

REST-assured is an open source library licensed Apache v2 and supported by the company Jayway. It is written with Groovy and allows making HTTP requests and validating JSON or XML responses through its unique functional DSL that drastically simplify the tests of REST services.

REST-assured 是一个开源库，许可Apache v2并由Jayway公司支持。 它是用Groovy编写的，允许通过其独特的功能DSL来进行HTTP请求和验证JSON或XML响应，从而大大简化REST服务的测试。

## Static imports

To effectively use REST-assured, the documentation recommends adding static imports of the following packages:

为了有效地使用REST保证，文档建议添加以下包的静态导入：

* com.jayway.restassured.RestAssured.\*

* com.jayway.restassured.matcher.RestAssuredMatchers.\*

* org.hamcrest.Matchers.\* 

## A Given, When, Then approach

To understand the basics of the REST-assured DSL, let's consider one of our tests \(in UserControllerIT\) that provides a short overview of REST-assured usage:

为了理解REST-assured 的DSL的基础知识，让我们考虑我们的一个测试（在UserControllerIT中），它提供REST-assured使用的简要概述：

```java
@Test
public void createUserBasicAuthAjax(){

    Response response = given().header("X-Requested-With", "XMLHttpRequest")
                .contentType("application/json;charset=UTF-8")
                .accept("application/json\")
                .body(userA)
                .when()
                .post(getHost() + CONTEXT_PATH + "/users");
    assertNotNull(response.getHeader("Location"));
}
```

The given part of the statement is the HTTP Request specification. With REST-assured, some request headers like Content-Type or Accept can be defined in an intuitive way with `contentType(…)` and `accept(…)`. Other headers can be reached with the generic `.header(…)`. Request parameters and authentication can also be defined in a same fashion.

声明特定部分是HTTP请求规范。 通过REST-assured，一些请求头（如Content-Type或Accept）可以用`contentType(…)`和`accept(…)`以直观的方式定义。 可以使用通用的`.header(…)`访问其他headers 。 请求参数和认证也可以以相同的方式定义。

For POST and PUT requests, it is necessary to pass a body to the request. This body can either be plain JSON or XML or directly the Java object \(as we did here\). This body, as a Java object, will be converted by the library depending upon the content-type defined in the specification \(JSON or XML\).

对于POST和PUT请求，有必要向请求传递一个主体。 这个主体可以是纯JSON或XML或直接的Java对象（如我们在这里所做的）。 该主体作为Java对象，将由库根据规范中定义的内容类型（JSON或XML）进行转换。

After the HTTP Request specification, the `when()` statement provides information about the actual HTTP method and destination.

At this stage, the returned object allows us either to define expectations from a `then()` block or, as we did here, to retrieve the Response object from where constraints can be defined separately. In our test case, the Location header of the Response is expected to be filled.

在HTTP请求规范之后，`when()`声明提供有关实际HTTP方法和目标的信息。

在这个阶段，返回的对象允许我们从`then()`块中定义期望值，或者像我们这里所做的那样，检索响应对象，从那里可以单独定义约束。 在我们的测试用例中，Response的Location header 将被填充。

# There is more…

More information can be found at the following Cargo and REST-assured respective documentations:

更多信息可以在以下货物和REST保证的相关文档中找到：

## About Cargo

For more information about the product and its integration with third-party systems, refer to

有关产品及其与第三方系统集成的更多信息，请参阅

https://codehaus-cargo.github.io/cargo/Home.html

## More REST-assured examples

For more examples, the REST-assured online Wiki provides plenty:

更多的例子，REST保证在线Wiki提供了很多：

https://github.com/jayway/rest-assured/wiki/Usage

  


  
  


