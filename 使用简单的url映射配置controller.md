This recipe introduces the Spring MVC controller with its simplest implementation.

这个配方介绍了Spring MVC控制器及其最简单的实现。

## Getting ready    **                          **

We will discover later on, and especially in Chapter 3, Working with Java Persistence and Entities, that Spring MVC is a great tool to build a REST API. Here, we will focus on how to create a controller that prints some content in the response.

Starting with this recipe, we will be using GIT to follow each iteration that has been made to develop the cloudstreetmarket application. After the initial setup, you will appreciate how smoothly you can upgrade.

我们将在以后发现，尤其是在第3章使用Java持久性和实体，Spring MVC是构建REST API的一个很好的工具。 在这里，我们将重点介绍如何创建在响应中打印某些内容的控制器。

从这个配方开始，我们将使用GIT跟踪已经开发cloudstreetmarket应用程序的每个迭代。 在初始设置后，你会欣赏到如何可以轻松地升级。

## How to do it...  **                            **

This recipe comes with two initial sections for installing and configuring GIT.**                       **

该配方附有两个初始部分，用于安装和配置GIT。

### Downloading and installing GIT      **                        **

1. To download GIT, go to the GIT download page at [https://git-scm.com/download](https://git-scm.com/download). Select the right product corresponding to your environment \(Mac OS X, Windows, Linux, or Solaris\).

2. To install GIT for Linux and Solaris, execute the suggested installation commands using the system's native package manager.

下载和安装GIT  **                            **

1. 要下载GIT，请转到GIT下载页面，网址为[https://git-scm.com/download。](https://git-scm.com/download。) 根据你的环境（Mac OS X，Windows，Linux或Solaris）选择正确的产品。

2. 要为Linux和Solaris安装GIT，请使用系统的本机包管理器执行建议的安装命令。

For Mac OS X, double-click on the downloaded dmg file to extract the package on your hard drive. Navigate to the extracted directory and double-click on the pkg file. Select all the default options, one step after an other, up to the Successful Installation screen. Close the screen.

For Windows, execute the downloaded program and follow the default options for every step up to these screens:

* **Adjusting your PATH environment**: Select the **Use Git from the Windows Command Prompt** option

* **Choosing the SSH executable**: Select the **Use OpenSSH** option

* **Configuring the line endings conversions**: Select the C**heckout Windows-style** and **commit Unix-style line endings** options

* **Configuring the terminal emulator to use Git Bash**: Select **Use Windows' default console window**

* **Configuring experimental performance tweaks**: Don't tick the **Enable file system caching** checkbox

对于Mac OS X，双击下载的dmg文件以解压缩在硬盘驱动器上的包。 导航到提取的目录并双击pkg文件。 选择所有默认选项，一个接一个，直到成功安装屏幕。 关闭屏幕。

对于Windows，执行下载的程序，并遵循每个步骤到这些屏幕的默认选项：

* **调整PATH环境**：从Windows命令中选择**Use Git from the Windows Command Prompt**选项

* 选择**SSH executable**：选择使用**Use OpenSSH**选项

* 配置行结束转换：选择**Checkout Windows-style**并**commit Unix-style line endings** 选项

* 配置**终端仿真器**以使用Git Bash：选择**Windows' default console window**

* 配置**实验性能调整**：不要勾选**Enable file system caching**复选框

Let the installation finish and click on the **Finish **button.

For verification, open your terminal and enter the following command:

`git –version`

This command should display the installed version. The presented installation guidelines were associated with GIT 2.6.3.

安装完成后单击**Finish **按钮。

要进行验证，请打开终端并输入以下命令：

`git -version`

此命令应显示已安装的版本。 所提供的安装指南与GIT 2.6.3相关联。

### Configuring GIT in Eclipse    **                          **

1. We will first initialize the local repository from the terminal. Go to your workspace location: cd &lt;home-directory&gt;/workspace.

   Replace`<home-directory>`with your own home path.

2. Enter the following command to create a local Git repository at this location:

   `git init`

3. Enter the following command:

   `git remote add origin https://github.com/alex-bretet/ cloudstreetmarket.com`

4. Then, enter the git fetch command.

5. Select both your parent projects and right-click on one of them. Go to Team \| Add to index:

6. From the top-right panel, click on the Git perspective:Add this perspective with the button if you don't have it yet.

7. From the left hierarchy \(the **Git **perspective\), select **Add an existing local Git repository**.

8. A contextual window opens. Target the location of the local Git repository we just created \(it should be the current workspace directory\).

9. A new repository should now appear in **Git** perspective.

10. As shown in the following screenshot, right-click and select Checkout to see the latest version of the branch origin/v1.x.x.

11. When prompted, Checkout as New Local Branch:

12. The actual workspace should now be synchronized with the branch v1.x.x. This branch reflects the state of the environment at the end of Chapter 1, Setup Routine for an Enterprise Spring Application.

13. Right-click on **zipcloud-parent** to execute **Run as \| Maven clean** and** Run as \| Maven install**. Then, do the same operation on cloudstreetmarket-parent. You will observe BUILD SUCCESS each time.

14. Finally, right-click on one project and go to **Maven \| Update Project**. Select all the projects of the workspace and click on **OK**.

15. If you still have a red warning in one of your projects \(as shown in the previous screenshot\), you will probably have to reattach a target runtime environment to **cloudstreetmarket-api** and **cloustreetmarket-webapp** \(as per Chapter 1 , Setup Routine for an Enterprise Spring Application, 2nd recipe, 7th step\).

16. From the terminal, go to the local GIT repository:

    `cd <home-directory>/workspace`

17. Enter the following command:

    `git pull origin v2.2.1`

18. Reiterate steps 13 and 14. \(Be prepared to repeat these two steps every time after pulling new changes.\)

19. In the **cloudstreetmarket-webapp** module, a new package is now present:

    `edu.zipcloud.cloudstreetmarket.portal.controllers.`

20. Inside this package, an InfoTagController class has been created:

```java
@Controller

@RequestMapping("/info")

public class InfoTagController {

    @RequestMapping("/helloHandler")

    @ResponseBody

    public String helloController(){

        return "hello";

    }

}
```

1. Make sure the two wars are deployed in the Tomcat server. Start the Tomcat server and access the [http://localhost:8080/portal/info/helloHandler](http://localhost:8080/portal/info/helloHandler)  URL with your browser.

   > You should see a simple hello displayed as HTML content.

2. In the cloudstreetmarket-webapp/src/main/webapp/WEB-INF/dispatcher-context.xml file, the following bean definition is added:

```java
<bean id="webAppVersion" class="java.lang.String">

    <constructor-arg value="1.0.0"/>

</bean>
```

1. The following method and members in the InfoTagController class are also added:

```java
@Autowired
private WebApplicationContext webAppContext;

private final static LocalDateTime startDateTime = LocalDateTime.now();

private final static DateTimeFormatter DT_FORMATTER = DateTimeFormatter.ofPattern("EEE, d MMM yyyy h:mm a");


@RequestMapping("/server")

@ResponseBody

public String infoTagServer(){

    return new StringJoiner("<br>")

        .add("-------------------------------------")

        .add(" Server: " + webAppContext.getServletContext().getServerInfo())

        .add(" Start date: " + startDateTime.format(DT_FORMATTER))

        .add(" Version: " + webAppContext.getBean("webAppVersion"))

        .add("--------------------------------------")

        .toString();

}
```

1. Now, access the [http://localhost:8080/portal/info/server](http://localhost:8080/portal/info/server) URL with your browser.

在Eclipse中配置GIT  **                            **

1. 我们将首先从终端初始化本地存储库。 转到你的工作空间位置：`cd <home-directory> / workspace`。

   将`<home-directory>`替换为你自己的主路径。

2. 输入以下命令以在此位置创建本地Git存储库：

   `git init`

3. 输入以下命令：

   `git remote add origin `[`https://github.com/alex-bretet/`](https://github.com/alex-bretet/)`cloudstreetmarket.com`

4. 然后，输入`git fetch`命令。

5. 选择你的父项目，然后右键单击其中一个。 转到Team \| 添加到索引：

   ![](/assets/18.png)

6. 从右上方面板中，单击Git透视图：

   ![](/assets/19.png)

   如果你还没有添加这个![](/assets/20.png)角度与按钮。

7. 从左侧层次（Git透视图），选择**Add an existing local Git repository**。

8. 将打开上下文窗口。 目标我们刚刚创建的本地Git存储库的位置（它应该是当前的工作空间目录）。

9. 新的存储库现在应该出现在**Git**透视图中。

10. 如以下屏幕截图所示，右键单击并选择Checkout以查看分支源/ v1.x.x的最新版本。

    ![](/assets/21.png)

11. 出现提示时，Checkout为新的本地分支：

    ![](/assets/22.png)

12. 实际工作区现在应该与分支v1.x.x同步。 此分支反映了第1章“企业弹簧应用程序的安装例程”结束时的环境状态。

    ![](/assets/23.png)

13. 右键单击**zipcloud-parent**以执行**Run as \| Maven clean** 和**Run as \| Maven install**。 然后，在**cloudstreetmarket-parent**上执行相同的操作。 你每次都会看到`BUILD SUCCESS`。

14. 最后，右键单击一个项目并转到**Maven \| Update Project**。 选择工作区的所有项目，然后单击**OK**。

15. 如果您的一个项目中仍然有红色警告（如上一个屏幕截图所示），则可能需要将目标运行时环境重新挂接到**cloudstreetmarket-api**和**cloustreetmarket-webapp**（根据第1章，安装企业版Spring Application，第二配方，第七步）。

16. 从终端，转到本地GIT存储库：

    `cd <home-directory> / workspace`

17. 输入以下命令：

    `git pull origin v2.2.1`

18. 重申步骤13和14.（准备在拉取新更改后每次都重复这两个步骤。）

19. 在**cloudstreetmarket-webapp**模块中，现在有一个新包：

    `edu.zipcloud.cloudstreetmarket.portal.controllers`。

20. 在这个包中，创建了一个InfoTagController类：

```java
@Controller

@RequestMapping("/info")

public class InfoTagController {

    @RequestMapping("/helloHandler")

    @ResponseBody

    public String helloController(){

        return "hello";

    }

}
```

1. 确保两个war都部署在Tomcat服务器中。 启动Tomcat服务器，并使用浏览器访问http：// localhost：8080 / portal / info / helloHandler URL。

   > 你应该看到一个简单的hello显示为HTML内容。

2. 在cloudstreetmarket-webapp / src / main / webapp / WEB-INF / dispatcher-context.xml文件中，添加了以下bean定义：

   ```java
   <bean id =“webAppVersion” class =“java.lang.String”>
       <constructor-arg value =“1.0.0”/>
   </ bean>
   ```

3. 还添加了InfoTagController类中的以下方法和成员：

```java
@Autowired
private WebApplicationContext webAppContext;

private final static LocalDateTime startDateTime = LocalDateTime.now();

private final static DateTimeFormatter DT_FORMATTER = DateTimeFormatter.ofPattern("EEE, d MMM yyyy h:mm a");


@RequestMapping("/server")

@ResponseBody

public String infoTagServer(){

    return new StringJoiner("<br>")

        .add("-------------------------------------")

        .add(" Server: " + webAppContext.getServletContext().getServerInfo())

        .add(" Start date: " + startDateTime.format(DT_FORMATTER))

        .add(" Version: " + webAppContext.getBean("webAppVersion"))

        .add("--------------------------------------")

        .toString();
}
```

1. 现在，使用浏览器访问http：// localhost：8080 / portal / info / server URL。

> You should see the following content rendered as an HTML document:
>
> 您应该看到以下内容呈现为HTML文档：
>
> ---
>
> Server: Apache Tomcat/8.0.14
>
> Start date: Sun, 16 Nov 2014 12:10 AM
>
> Version: 1.0.0
>
> ---

## How it works...

We are going to draft an overview of Spring MVC as a Framework. We will then review how a Controller is configured from the DispatcherServlet, the controller-level annotations, and from the method-handler signatures.**          **

我们将初步概述Spring MVC作为一个框架。 然后回顾如何从DispatcherServlet，controller-level注解和method-handler 签名配置控制器。

### Spring MVC overview **               **

Spring MVC implements two common design patterns: the front controller design pattern and the MVC design pattern.

Spring MVC概述**  **              

Spring MVC实现两种常见的设计模式：前端控制器设计模式和MVC设计模式。

### Front controller**                **

A system designed as a **Front controller **exposes a single entry point for all incoming requests. In Java Web environments, this entry point is usually a servlet—a unique servlet that dispatches and delegates to other components.

前端控制器  **              **

设计为**Front controller **的系统为所有传入请求公开一个入口点。 在Java Web环境中，此入口点通常是一个servlet — 一个唯一的servlet，它分派并委派给其他组件。



> In the case of Spring MVC, this unique servlet is the DispatcherServlet.
>
> 在Spring MVC的情况下，这个独特的servlet是DispatcherServlet。



Servlets are standards in the Java web. They are associated to predefined URL paths and are registered in deployment descriptors \(the web.xml files\). Parsing deployment descriptors, the servlet-container \(such as Apache Tomcat\) identifies the declared servlets and their URL mapping. At runtime, the servlet-container intercepts every HTTP client request and creates a new Thread for each one of them. Those Threads will call the matching relevant servlets with Java-converted request and response objects.

Servlet是Java Web中的标准。 它们与预定义的URL路径相关联，并在部署描述符（web.xml文件）中注册。 解析部署描述符，servlet容器（例如Apache Tomcat）标识已声明的servlet及其URL映射。 在运行时，servlet-container拦截每个HTTP客户端请求，并为其中的每一个创建一个新的Thread。 这些线程将使用Java转换的请求和响应对象调用匹配的相关servlet。

### MVC design pattern  **              **

The MVC design pattern is more of an architectural style. It describes the application as a whole. It encourages a clear separation of concerns between three different layers that the request thread has to pass through: the Model, the View, and the Controller—the Controller, the Model, and then the View to be accurate.

MVC设计模式  **              **

MVC设计模式更多是一种建筑风格。 它描述了整个应用程序。 它鼓励在请求线程必须经过的三个不同层之间清楚地分离问题：模型，视图和控制器 - 控制器，模型，然后视图是准确的。

![](/assets/24.png)

When a client request is intercepted by the servlet-container, it is routed to the DispatcherServlet. The DispatcherServlet sends the request to one Controller \(one controller method-handler\), which has a configuration matching the request state \(if a match is found\).

The Controller orchestrates the business logic, the model generation and ultimately chooses a View for the model and the response. In this perspective, the model represents a populated data structure handled by the controller and given to the view for visualization purposes.

But the three components \(Model, View, and Controller\) can also be visualized at a Macro scale as independent static layers. Each of these components is a layer and a placeholder for every individual constituent, part of the category. The **Controller layer **contains all the registered controllers as well as the Web Interceptors and converters; the **Model generation** **layer** \(and Business logic layer\) contains the business services and data access components. The **View layer** encloses the templates \(JSPs for example\) and other web client-side components.

当客户端请求被servlet-container拦截时，它被路由到DispatcherServlet。 DispatcherServlet将请求发送到一个Controller（一个控制器方法处理程序），该控制器具有与请求状态匹配的配置（如果找到匹配项）。

Controller编排业务逻辑，模型生成，并最终选择一个用于模型和响应的视图。在这个透视图中，模型表示由控制器处理并且被提供给视图用于可视化目的的填充数据结构。

但是，这三个组件（模型，视图和控制器）也可以作为独立的静态图层在宏标度下可视化。 这些组件中的每一个都是每个组成部分（类别的一部分）的层和占位符。 **Controller层**包含所有注册的控制器以及Web拦截器和转换器; **Model generation层**（和业务逻辑层）包含业务服务和数据访问组件。** View层**包含模板（例如JSP）和其他Web客户端组件。

### Spring MVC flow   **             **

The Spring MVC flow can be represented with the following diagram:

Spring MVC流    **            **

Spring MVC流程可以用下面的图表示：

![](/assets/25.png)

We previously mentioned that Spring MVC implements a front controller pattern. The entry point is the DispatcherServlet. This DispatcherServlet relies on a HandlerMapping implementation. With different strategies and specificities, the HandlerMapping resolves a Controller method-handler for the request.

Once the DispatcherServlet has a Controller method-handler, it dispatches the request to it. The method-handler returns a View name \(or directly the View itself\) and also the populated model object to the DispatcherServlet.

我们前面提到Spring MVC实现了一个前端控制器模式。 入口点是DispatcherServlet。 这个DispatcherServlet依赖于HandlerMapping实现。 使用不同的策略和特殊性，HandlerMapping解析请求的Controller方法处理程序。

一旦DispatcherServlet有一个Controller方法处理程序，它会分派请求到它。 方法处理程序返回一个视图名称（或直接视图本身）以及填充模型对象到DispatcherServlet。

With a View name, the DispatcherServlet asks a ViewResolver implementation to find and select a View.

With the request, a View, and a Model, the DispatcherServlet has everything to build the client response. The view is processed with all these elements and the response is finally returned to the servlet-container.

使用视图名称，DispatcherServlet会要求ViewResolver实现来查找和选择视图。

通过请求，视图和模型，DispatcherServlet具有构建客户机响应的一切。 使用所有这些元素处理视图，并且响应最终返回到servlet容器。

### DispatcherServlet – the Spring MVC entrypoint    **            **

As explained, the DispatcherServlet is quite a central piece in Spring MVC. It intercepts the client requests that target predefined URL paths for the application. It maps them to handlers that belong to business logic operators \(Controllers, Interceptors, Filters, and so  
 on\). It also provides a set of tools, available as beans for solving recurring web development issues and techniques such as serving a centralized and modular **View layer, handling internationalisation, themes, handing exceptions**, and so on.

Before everything, the DispatcherServlet is a servlet and is defined as such in the web. xml file with a servlet configuration and its servlet-mapping. The code is as follows:**          **

如前所述，DispatcherServlet是Spring MVC中的一个中心部分。 它拦截针对应用程序预定义的URL路径的客户端请求。 将它们映射到属于业务逻辑运算符（控制器，拦截器，过滤器等）的处理程序。 它还提供了一组工具，可用作解决循环Web开发问题和技术的bean，如提供集中和模块化**View层，处理国际化，主题，处理异常**等等。

在一切之前，DispatcherServlet是一个servlet，并在Web中定义。 xml文件与servlet配置及其servlet映射。 代码如下：

```java
<servlet>
    <servlet-name>spring</servlet-name>    
    <servlet-class>
        org.springframework.web.servlet.DispatcherServlet
    </servlet-class>

    <load-on-startup>1</load-on-startup>
</servlet>

<servlet-mapping>
    <servlet-name>spring</servlet-name>
    <url-pattern>/*</url-pattern>
</servlet-mapping>
```

In our application, in the cloudstreetmarket-webapp, the DispatcherServlet is named spring and covers the full context-path of the application: /\*.

We have already seen that each DispatcherServlet has a restricted-scope WebApplicationContext that inherits the beans from the root ApplicationContext.

By default, for the WebApplicationContext, Spring MVC looks in the /WEB-INF directory for a configuration file named {servletName}-servlet.xml. We have, however, overridden this default name and location through the initialization parameter contextConfigLocation:

在我们的应用程序中，在**cloudstreetmarket-webapp**中，DispatcherServlet被命名为spring并覆盖应用程序的完整上下文路径：**/ \***。

我们已经看到每个DispatcherServlet都有一个限制范围的WebApplicationContext，它从根ApplicationContext继承bean。

默认情况下，对于WebApplicationContext，Spring MVC在/ WEB-INF目录中查找名为`{servletName} -servlet.xml`的配置文件。 但是，我们通过初始化参数**contextConfigLocation**覆盖了此默认名称和位置：

```java
<servlet>

    <servlet-name>spring</servlet-name>

    <servlet-class>

        org.springframework.web.servlet.DispatcherServlet

    </servlet-class>

    <init-param>

        <param-name>contextConfigLocation</param-name>

        <param-value>/WEB-INF/dispatcher-context.xml</param-value>

    </init-param>

    <load-on-startup>1</load-on-startup>

</servlet>

<servlet-mapping>

    <servlet-name>spring</servlet-name>

    <url-pattern>/*</url-pattern>

</servlet-mapping>
```

Still in the web.xml, you can see that the root application context \(classpath\*:/METAINF/spring/\*-config.xml\) starts with the ContextLoaderListener:

仍然在`web.xml`中，你可以看到根应用程序上下文（`classpath *：/ METAINF / spring / * - config.xml`）ContextLoaderListener开头：

```java
<listener>

    <listener-class>
        org.springframework.web.context.ContextLoaderListener
    </listener-class>

</listener>
```

## Annotation-defined controllers **   **

Spring MVC controllers are the place where client requests really start to be processed by the business-specific code. Since Spring 2.5, we have been able to use annotations on controllers so we don't have to explicitly declare them as beans in configuration. This makes their implementation much easier to extend and understand.

注解定义的控制器    

Spring MVC控制器是客户端请求真正开始由特定于业务的代码处理的地方。 自从Spring 2.5以来，已经能够在控制器上使用注解，所以我们不必在配置中显式声明它们。 这使得它们的实现更容易扩展和理解。

### **@Controller     **

A `@Controller` annotation tags a class as a Web controller. It remains a Spring Stereotype for presentation layers. The main purpose of defining a Spring Stereotype is to make a target type or method discoverable during the Spring classpath scanning which is activated by package with the following command:

`<context:component-scan base-package="edu.zipcloud.cloudstreetmarket.portal"/>`

There is not much custom logic related to this annotation. We could run a Controller with other Stereotype annotations \(`@Component` or `@Service`\) if we don't bother making the application a cleaner place. ** **

`@Controller注解`将类标记为Web控制器。 它仍然是表示层的Spring构造型。 定义Spring构造型的主要目的是使一个目标类型或方法在Spring类路径扫描期间是可发现的，它由包使用以下命令激活：

`<context:component-scan base-package="edu.zipcloud.cloudstreetmarket.portal"/>`

没有很多与此注解相关的自定义逻辑。 我们可以运行一个控制器与其他Stereotype注解（`@Component`或`@Service`），如果我们不制造麻烦使应用程序更一目了然。

### @RequestMapping   ** **

The @RequestMapping annotations define handlers onto Controller classes and/or onto controller methods. These annotations are looked-up among stereotyped classes by the DispatcherServlet. The main idea behind the @RequestMapping annotations is to define a primary path mapping on the class-level and to narrow HTTP request methods, headers, parameters, and media-types on the methods.

To implement this narrowing, the @RequestMapping annotation accepts comma-separated parameters within parentheses.

Consider the following example:

`@RequestMapping(value="/server", method=RequestMethod.GET)`

Available parameters for @RequestMapping are summarized in the following table:

`@RequestMapping`注解定义处理程序到Controller类和/或控制器方法。 这些注解由DispatcherServlet在构造型类中查找。  `@RequestMapping`注解的主要思想是在类级别上定义主路径映射，并缩小HTTP请求方法，头，参数和方法上的媒体类型。

要实现此缩小，`@RequestMapping`注解在括号中接受逗号分隔的参数。

请考虑以下示例：

`@RequestMapping(value="/server", method=RequestMethod.GET)`

`@RequestMapping`的可用参数总结在下表中：

![](/assets/26.png)

All these parameters can be used both at the type and method level. When used at the type level, all method-level parameters inherit the parent-level narrowing.

所有这些参数都可以在类型和方法级别使用。 当在类型级别使用时，所有方法级别参数都继承父级级别限制。

### Controller method-handler signatures

Several constituents make a Controller method-handler. Here's another example of such a handler with Spring MVC:

```java
@RequestMapping(value="/index")
public ModelAndView getRequestExample(ServletRequest request){

    ModelAndView mav = new ModelAndView();

    mav.setViewName("index");
    mav.addObject("variable1", new ArrayList<String>());
    return mav;
}
```

We have just talked about how to use the `@RequestMapping` annotation. With regard to the method signature, this annotation can only be placed before the return-type.

几个组成部分构成一个Controller方法处理程序。 这里是Spring MVC这样的处理程序的另一个例子：

```java
@RequestMapping(value="/index")
public ModelAndView getRequestExample(ServletRequest request){

    ModelAndView mav = new ModelAndView();

    mav.setViewName("index");
    mav.addObject("variable1", new ArrayList<String>());
    return mav;
}
```

我们刚刚谈到了如何使用`@RequestMapping`注解。 关于方法签名，此注解只能放在返回类型之前。

### Supported method arguments types  

Declaring specific types of arguments for handler methods can get Spring to automatically inject in them references to external objects. Objects related to the request lifecycle, the session, or to the application configuration. With the benefit of being scoped for the method, those argument types are presented in the following table:

支持的方法参数类型  

为处理程序方法声明特定类型的参数可以使Spring自动注入对外部对象的引用。 与请求生命周期，会话或应用程序配置相关的对象。 有了为方法设置作用域的优点，这些参数类型在下表中显示：

![](/assets/27.png)

![](/assets/28.png)

### Supported annotations for method arguments  

A set of native annotations for method-handler arguments has been designed. They must be seen as handles that configure the web behavior of controller methods in regard to incoming requests or the response yet to be built.

They identify abstractions for handy Spring MVC functions such as request parameter binding, URI path variable binding, injection-to-argument of request payloads, HTML form-parameter binding, and so on.

支持方法参数的注解

已设计了一组方法处理程序参数的本机注解。 它们必须被看作是配置控制器方法的web行为的入口请求或响应尚未建立的句柄。

它们识别用于方便的Spring MVC函数的抽象，例如请求参数绑定，URI路径变量绑定，请求有效载荷的注入到参数，HTML表单参数绑定等。

![](/assets/29.png)

These annotations have to be placed just before the method argument to be populated:

这些注解必须放在要填充的方法参数之前：

```java
@RequestMapping(value="/index")
public ModelAndView getRequestExample(@RequestParam("exP1") String exP1){
    ModelAndView mav = new ModelAndView();
    mav.setViewName("index");
    mav.addObject("exP1", exP1);
    return mav;
}
```

### Supported return Types

Spring MVC, with different possible controller method return Types, allows us to specify either the response sent back to the client or the necessary configuration for targeting or populating with variables an intermediate View layer. Depending upon what we want to do or the actual application state, we have the choice among the following:

支持的返回类型

Spring MVC，使用不同的可能的控制器方法返回类型，允许我们指定发送回客户端的响应，或者指定或填充变量中间View层的必要配置。 根据我们想要做什么或实际应用程序状态，我们有以下选择：

![](/assets/30.png)

![](/assets/31.png)

## There's more...

In the `InfoTagController.infoTagServer()` method-handler, we have used the `@ResponseBody` annotation before the return Type. This annotation has been borrowed from the REST-specific tools. When you don't need to process a View, the `@ResponseBody `directive will use the registered Spring converters to marshal the returned object into the expected format \(XML, JSON, and so on\). It will then write the marshalled content to the Response body \(as the Response payload\).

In the case of a String object with no more configurations, it is printed out as such in the Response body. We could have used the ResponseEntity&lt;String&gt; return Type to achieve the same goal.

在`InfoTagController.infoTagServer()`方法处理程序中，我们在返回类型之前使用了`@ResponseBody`注解。 此注解已从REST特定工具借用。 当你不需要处理一个视图时，`@ResponseBody`指令将使用注册的Spring转换器将返回的对象编译成预期的格式（XML，JSON等等）。 然后它将编组的内容写入响应主体（作为响应有效负载）。

在没有更多配置的String对象的情况下，它将在响应主体中打印出来。 我们可以使用`ResponseEntity <String>`返回类型来实现相同的目标。

