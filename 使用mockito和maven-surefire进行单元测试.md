Unit Tests are useful to keep an eye on the components' implementation. The legacy philosophy of Spring promotes reusable components application-wide. The core implementations of these components may either alter states \(states of transitory objects\) or trigger interactions with other components.

Using Mocks in Unit Tests specifically assesses the behavior of component's methods in regard to other components. When the developer gets used to Mocks, it is amazing to see how much the design becomes influenced toward the use of different layers and logic externalization. Similarly, object names and method names are given more importance. Because they summarize something that is happening elsewhere, Mocks save the energy of the next developer that will have to operate in the area of code.

单元测试有助于关注组件的实现。 Spring的传统理念促进了可重用组件的应用范围。 这些组件的核心实现可以改变状态（临时对象的状态）或触发与其他组件的交互。

在单元测试中使用Mocks 模拟测试评估组件的方法对于其他组件的行为。 当开发者习惯了Mocks时，看到设计对于使用不同层和逻辑外部化的影响是多么惊人。 类似地，对象名称和方法名称更重要。 因为他们总结了其他地方发生的事情，Mocks节省了下一个需要在代码领域运行的开发人员的精力。

Developing Unit Tests is by definition an Enterprise policy. As the percentage of code covered by tests can easily reflect the maturity of a product, this code-coverage rate is also becoming a standard reference to assess companies in regard to their products. It must also be noted that companies practicing code reviews as a development process find valuable insights from Pull Requests. When Pull Requests highlight behavioral changes through tests, the impact of potential changes becomes clear faster.

开发单元测试是通过定义一个企业的政策。 由于测试覆盖的代码百分比可以轻松反映产品的成熟度，因此该代码覆盖率也成为评估公司产品的标准参考。 还必须注意，作为开发流程进行代码审查的公司会从Pull Requests中找到有价值的见解。 当Pull Requests通过测试强调行为变化时，潜在变化的影响变得更快。

# How to do it…

1.Rerun a Maven Install on the cloudstreetmarket-parent project as in the previous recipe. When the build process comes to build the core module, you should see the following logs that suggest the execution of unit tests during the **test** phase \(between **compile** and **package**\):

1.在cloudstreetmarket父项目上重新运行Maven Install ，如上一个配方中所示。 当构建过程构建核心模块时，您应该看到以下日志，它们建议在**test** 阶段（**compile** 和**package**之间）执行单元测试：

![](/assets/154.png)

2.Those tests can be found in the cloudstreetmarket-core module, specifically in the src/test/java source folder:

2.这些测试可以在cloudstreetmarket-core模块中找到，特别是在src/test/java源文件夹中：

![](/assets/155.png)

Both unit tests and integration tests use JUnit:

单元测试和集成测试都使用JUnit：

```java
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.9</version>
</dependency>
```

3.JUnit is natively supported by Eclipse IDE, and this last one offers handles to **Run **and **Debug **tests from a class or a method outside Maven:

3. Eclipse IDE本身支持JUnit，最后一个提供了从类或Maven外部方法**Run **和**Debug **测试的句柄：

![](/assets/156.png)  


4.A very simple JUnit test class is IdentifiableToIdConverterTest \(see the following code\). This class asserts that all the registered Entities can be converted by IdentifiableToIdConverter for being Identifiable implementations \(remember HATEOAS:\):

4.一个非常简单的JUnit测试类是IdentifiableToIdConverterTest（见下面的代码）。 这个类声明所有注册的实体可以通过IdentifiableToIdConverter转换为可识别的实现（记住HATEOAS :\)：

```java
import static org.junit.Assert.*;
import org.junit.Test;
import edu.zipcloud.cloudstreetmarket.core.entities.*;

public class IdentifiableToIdConverterTest {

    private IdentifiableToIdConverter converter;
    
    @Test
    public void canConvertChartStock(){
        converter = new IdentifiableToIdConverter(ChartStock.class);
        assertTrue(converter.canConvert(ChartStock.class));
    }
    
    @Test
    public void canConvertAction(){
        converter = new IdentifiableToIdConverter(Action.class);
        assertTrue(converter.canConvert(Action.class));
    }
}
```

5.More advanced unit tests use the Mockito library.For instance, in the following YahooQuoteToCurrencyExchangeConverterTest:

5.更高级的单元测试使用Mockito库。例如，在以下YahooQuoteToCurrencyExchangeConverterTest中：

```java
@RunWith(MockitoJUnitRunner.class)
public class YahooQuoteToCurrencyExchangeConverterTest {

    @InjectMocks
    private YahooQuoteToCurrencyExchangeConverter converter;
    @Mock
    private CurrencyExchangeRepository currencyExchangeRepository;
    
    @Test
    public void transferCriticalData(){
    
        when(currencyExchangeRepository.findOne(any(String.class))).thenReturn(new CurrencyExchange("WHATEVER_ID""));
        CurrencyExchange currencyExchange = converter.convert(buildYahooQuoteInstance());
        assertEquals("WHATEVER_ID"",currencyExchange.getId());
        assertEquals("USDGBP=X"", currencyExchange.getName());
        assertEquals(BigDecimal.valueOf(10),
        currencyExchange.getBid());
        ...
        assertEquals(BigDecimal.valueOf(17), currencyExchange.getOpen());
        verify(currencyExchangeRepository, times(1)).findOne(any(String.class));
    }
    ...
}
```

Here, the highlighted `transferCriticalData()`test gets an instance of `YahooQuoteToCurrencyExchangeConverter` that is not initialized with a real `@Autowired` CurrencyExchangeRepository but instead with a **Mock**. The converter gets its `convert()` method invoked with a YahooQuote instance.

这里，突出显示的`transferCriticalData()`测试获取`YahooQuoteToCurrencyExchangeConverter`的实例，该实例未使用真实的`@Autowired` CurrencyExchangeRepository初始化，而是使用**Mock**初始化。 转换器获取使用YahooQuote实例调用的`convert()`方法。

> The Mock is told to return a specific CurrencyExchange instance when its` findOne(String s)` method is called inside `convert()`. Then, the returned currencyExchange object is assessed field by field to ensure they are matching their individual expectations.
>
> 当在`convert()`中调用其`findOne(String s)`方法时，Mock会被告知返回一个特定的CurrencyExchange实例。 然后，按字段逐个评估返回的currencyExchange对象，以确保它们与其各自的期望相匹配。

6.The following Maven dependency to Mockito has been added across the different modules:

6.在不同的模块中添加了以下Maven对Mockito的依赖：

```java
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-all</artifactId>
    <version>1.9.5<version>
</dependency>
```

7.A more extended use of Mockito for unit tests can be found in CommunityServiceImplTest. For example, in the following example, the registerUser\_generatePasswordAndEncodeIt test makes use of the ArgumentCaptor:

7.可以在CommunityServiceImplTest中找到更广泛地使用Mockito进行单元测试。 例如，在以下示例中，registerUser\_generatePasswordAndEncodeIt测试使用ArgumentCaptor：

```java
@Test
public void registerUser_generatesPasswordAndEncodesIt() {

    when(communityServiceHelper.generatePassword()).thenReturn("newPassword");
    when(passwordEncoder.encode("newPassword")).thenReturn("newPasswordEncoded");
    ArgumentCaptor<User>userArgumentCaptor = ArgumentCaptor.forClass(User.class);
    userA.setPassword(null);
    communityServiceImpl.registerUser(userA);
    verify(userRepository, times(1)).save(userArgumentCaptor.capture());
    verify(passwordEncoder, times(1)).encode("newPassword");
    String capturedGeneratedPassword = userArgumentCaptor.getValue().getPassword();
    assertEquals("newPasswordEncoded", capturedGeneratedPassword);
}
```

# How it works...

## @Test annotation

The `@Test` annotation must be placed on public void methods so that JUnit considers them as test cases. An exception thrown within one of these methods will be considered as a test failure. Consequently, an execution without any exception thrown represents a success.

The` @Test `annotation can be customized, passing the following two optional arguments.

`@Test`注释必须放在public void方法上，以便JUnit将它们视为测试用例。 在这些方法之一中抛出的异常将被视为测试失败。 因此，没有任何异常抛出的执行代表成功。

`@Test`注释可以定制，传递以下两个可选参数。

##  The expected and timeout arguments

An expected parameter on an `@Test` annotation specifies that the test is expected to throw a specific type of exception to be successful. When a different Type of exception is thrown or when no exception is thrown at all, JUnit must consider the execution as a failure. When a test case is provided a timeout parameter in its `@Test` annotation, this test will fail when the execution lasts more than the indicated time.

预期和超时参数

`@Test`注释的期望参数指定测试应该抛出特定类型的异常以成功。 当抛出一个不同类型的异常或根本没有抛出异常时，JUnit必须将执行视为失败。 当测试用例在其`@Test`注释中提供了超时参数时，如果执行持续超过指示的时间，则此测试将失败。

## The @RunWith annotation

As introduced in the recipe, the `@RunWith` annotation permits the use of external test runners \(instead of the default BlockJUnit4ClassRunner coming with JUnit\). By the way, a declarative technique for specifying the default JUnit runner could be to get `@RunWith` targeting JUnit4.class like so: `@RunWith(JUnit4.class)`.

A runner runs tests and notifies a RunNotifier of significant events as it does so.

A custom Runner must implement abstract methods from `org.junit.runner.Runner` such as` run(RunNotifier notifier)` and `getDescription()`. It must also follow up on core JUnit functions, driving for example, the test execution flow. JUnit has a set of annotations such as `@BeforeClass`, `@Before`, `@After`, and `@AfterClass` natively handled by org.junit.runner.ParentRunner. We are going to visit these annotations next.

如配方中所述，`@RunWith`注释允许使用外部测试运行器（而不是与JUnit一起提供的默认BlockJUnit4ClassRunner）。 顺便说一下，用于指定默认JUnit运行程序的声明性技术可以获得`@RunWith`以JUnit4.class为目标：`@RunWith(JUnit4.class)`。

运行器运行测试并通知RunNotifier重要事件这么做。

自定义Runner必须从`org.junit.runner.Runner`实现抽象方法，例如`run(RunNotifier notifier)`和`getDescription()`。 它还必须跟进核心JUnit函数，例如，驱动测试执行流程。 JUnit有一组注释，如`@BeforeClass`，`@Before`，`@After`和`@AfterClass`由`org.junit.runner.ParentRunner`本地处理。 接下来我们要访问这些注释。

## @Before and @After annotations

In test classes that contains several test cases, it is a good practice to try making the test logic as clear as possible. From this perspective, variable initialization and context reinitialization are operations that people often attempt to externalize for reusability. `@Before` annotations can be defined on `public void` methods to get them executed by the Runner before every single test. Similarly, `@After` annotations mark the `public void` method again to be executed after each test \(usually for cleanup resources or destroying a context\).

For information, on inheritance, `@Before` methods of parent classes will be run before those of the current class. Similarly, `@After` methods declared in superclasses will be run after those of the current class.

Another interesting point from the Javadoc specifies that all `@After` methods are guaranteed to run, even if a `@Before` or a `@Test` annotated method throws an exception.

在包含多组测试测试类，它是一个很好的做法，尝试使测试逻辑尽可能清晰。 从这个角度看，变量的初始化和上下文重新初始化是人们常常试图对外部化操作的可重用性。 `@Before`之前，可以在`public void`方法上定义注释，以便在每次测试之前让Runner执行注释。 类似地，`@After`注释标记`public void`方法，以便在每次测试后执行（通常用于清理资源或破坏上下文）。

有关继承的信息，父类的`@Before`方法将在当前类的那些方法之前运行。 类似地，在超类中声明的`@After`方法将在当前类的方法之后运行。

从Javadoc中另一个有趣的规定，所有的`@After`方法都保证运行，即使一个`@Before`或者`@Test`注释的方法抛出一个异常。

## @BeforeClass and @AfterClass annotations

The `@BeforeClass` and `@AfterClass` annotations can be applied to **public static void** methods. `@BeforeClass` causes a method to be run **once** in the test life cycle. The method will be run before any other `@Test` or `@Before` annotated methods.

A method annotated `@AfterClass` is guaranteed to be run **once **after all tests and also after all `@BeforeClass`, `@Before`, or `@After` annotated methods even if one of them throws an exception.

`@BeforeClass` and `@AfterClass` are valuable tools for handling performance-consuming operations related to the preparation of test context \(database connection management and pre/post business treatments\).

For information, on inheritance, `@BeforeClass` annotated methods in superclasses will be executed before the ones of the current class, and `@AfterClass` annotated methods in the superclasses will be executed after those of the current class.

`@BeforeClass`和`@AfterClass`注释可以应用于**public static void**方法。 `@BeforeClass`使得方法在测试生命周期中运行**一次**。 该方法将在任何其他`@Test`或`@Before`注释方法之前运行。

注释`@AfterClass`的方法保证在所有测试之后运行**一次**，并且在所有`@BeforeClass`，`@Before`或`@After`注释方法之后运行，即使其中一个抛出异常。

`@BeforeClass`和`@AfterClass`是处理与准备测试上下文（数据库连接管理和前/后业务处理）相关的性能消耗操作的有价值工具。

有关信息，在继承方面，`@BeforeClass`在超类中的注释方法将在当前类的方法之前执行，而`@AfterClass`在超类中的注释方法将在当前类的方法之后执行。

## Using Mockito

Mockito is an Open Source testing framework that supports Test-Driven Developments and Behavior-Driven developments. It permits the creation of double objects \(Mock objects\) and helps in isolating the system under test.

Mockito是一个开源测试框架，支持测试驱动开发和行为驱动开发。 它允许创建双对象（Mock对象），并有助于隔离被测系统。

## MockitoJUnitRunner

We have been talking about custom runners. The MockitoJUnitRunner is a bit particular in the way that it implements a decoration pattern around the default JUnitRunner.

Such design makes optional the use of this runner \(all the provided services could also be implemented declaratively with Mockito\).

The MockitoJUnitRunner automatically initializes `@Mock` annotated dependencies \(this saves us a call to `MockitoAnnotations.initMocks(this)`, in a `@Before` annotated method for example\).

我们一直在讨论custom runners。 该MockitoJUnitRunner有点特别，因为它实现了围绕默认JUnitRunner的装饰方式。

这样的设计使得可选择使用这个runner（所有提供的服务也可以用Mockito声明地实现）。

MockitoJUnitRunner自动初始化`@Mock`注解的依赖性（例如，在`@Before`注释的方法中，这将为我们调用`MockitoAnnotations.initMocks(this)`）。

```java
initMocks(java.lang.Object testClass)
```

_Initializes objects annotated with Mockito annotations for given testClass: `@Mock`_

_与初始化的Mockito注解对象在给定的testClass: `@Mock`_

The MockitoJUnitRunner also validates the way we implement the framework, after each test method, by invoking `Mockito.validateMockitoUsage()`.This validation assertively gets us to make an optimal use of the library with the help of explicit error outputs.

MockitoJUnitRunner还通过调用`Mockito.validateMockitoUsage()`来验证我们实现框架的方式，在每个测试方法之后，

这个验证肯定会让我们在明确错误输出的帮助下得到最佳利用。

## The transferCriticalData example

The system under test is the YahooQuoteToCurrencyExchangeConverter. The `@InjectMocks` annotation tells Mockito to perform injection of dependencies \(constructor injection, property setter, or field injection\) on the targeted converter using initialized Mocks before each test.

The `Mockito.when(T methodCall)` method, coupled with `thenReturn(T value)` allows the definition of a fake CurrencyExchange returned object when a call to `currencyExchangeRepository.findOne` will actually be made inside the `converter. convert(...)` tested method.

The Mockito verify method with `verify(currencyExchangeRepository, times(1)).findOne(any(String.class))` tells Mockito to validate how the tested convert method has interacted with the Mock\(s\). In the following example, we want the `convert `method to have called the repository only once.

被测系统是YahooQuoteToCurrencyExchangeConverter。` @InjectMocks`注释告诉Mockito在每次测试之前使用初始化的Mocks在目标转换器上执行依赖性注入（构造函数注入，属性设置器或字段注入）。

`Mockito.when(T methodCall)`方法，加上`thenReturn(T value)`允许定义一个假的CurrencyExchange返回对象，当调用`currencyExchangeRepository.findOne`时实际上是在`converter. convert(...)`测试方法。

Mockito验证方法与`verify(currencyExchangeRepository, times(1)).findOne(any(String.class))`告诉Mockito验证测试的转换方法如何与Mock交互。 在以下示例中，我们希望`convert`方法只调用存储库一次。

## The registerUser example

More specifically in the registerUser\_generatesPasswordAndEncodesIt test, we make use of a MockitoArgumentCaptor to manually perform deeper analyses on the object that a mocked method has been called with.

A MockitoArgumentCaptor is useful when we don't have an intermediate layer and when results are reused to invoke other methods.

More introspection tools than the superficial \(but still very useful\) Type checking can be required \(for example,` any(String.class)`\). An ArgumentCaptor as a solution is used with extra local variables in test methods.

更具体地说，在registerUser\_generatesPasswordAndEncodesIt测试中，我们使用MockitoArgumentCaptor来对已经调用了模拟方法的对象手动执行更深入的分析。

当我们没有中间层并且结果被重用来调用其他方法时，MockitoArgumentCaptor是有用的。

更多内省工具不是肤浅的（但仍然非常有用）类型检查要求（例如，`any(String.class)`）。 作为解决方案的ArgumentCaptor在测试方法中与额外的局部变量一起使用。

> Remember that local variables and transitory states in implementation methods will always increase the complexity of their related tests. Shorter, explicit, and cohesive methods are always better options.
>
> 记住，实现方法中的局部变量和瞬态状态将总是增加它们相关测试的复杂性。 简要，明确的和有凝聚力的方法始终是更好的选择。

# There is more…

## About Mockito

We advise the Mockito's Javadoc that is very well done and full of practical examples

我们建议Mockito的Javadoc是做得很好，充满了实际的例子

http://docs.mockito.googlecode.com/hg/org/mockito/Mockito.html

JUnit Rules

We didn't cover JUnit Rules in any way so far. JUnit offers `@Rule` annotations that can be applied on test-class fields to abstract recurring business-specific preparations. It is often used to prepare test context objects \(fixtures\).

我们迄今为止没有以任何方式涵盖JUnit规则。 JUnit提供`@Rule`注释，可以应用于测试类字段，以抽象定期的业务特定准备。 它通常用于准备测试上下文objects \(fixtures\)。

  
http://www.codeaffine.com/2012/09/24/junit-rules

http://junit.org/javadoc/latest/org/junit/Rule.html

  


  


