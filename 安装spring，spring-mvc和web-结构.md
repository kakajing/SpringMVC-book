In this recipe, we will add third-party dependencies to our pom.xml files using inheritance.

We will load Spring application contexts and create the first controller of our application. Finally, we will deploy and start the web app in Tomcat.

在这个谱方中，我们将使用继承向我们的pom.xml文件中添加第三方依赖项。

我们将加载Spring application contexts并创建我们的应用程序的第一个控制器。 最后，我们将在Tomcat中部署和启动Web应用程序。


## Getting ready

Now that we have Eclipse ready and Maven configured properly, the fun can begin. We need to specify all the necessary Spring dependencies in our pom.xml files, and we need to set up Spring so that it loads and retrieves its context for every module.

We also need to organize and optionally expose web resources, such as JSPs, JavaScript files,CSS files, and so on. If you've completed this configuration, we should end up with a static welcome page provided by the Tomcat server, started without exceptions!


现在我们已经准备好Eclipse并且Maven配置正确，可以开始了，需要在我们的pom.xml文件中指定所有必需的Spring依赖关系，我们需要设置Spring，以便加载和检索每个模块的上下文。

我们还需要组织并可选地公开Web资源，例如JSP，JavaScript文件，CSS文件等。 如果你已经完成了这个配置，我们应该得到一个由Tomcat服务器提供的静态欢迎页面，无例外开始！


## How to do it...

Our first set of changes relate to the parent projects:

1. We will define dependencies and build options for those parent projects. Let’s do it with the following steps:

1. Open the cloudstreetmarket-parent pom.xml from the chapter\_1 source code directory and select the pom.xml tab \(underneath the main window\).

   Copy and paste into the cloudstreetmarket-parent's pom.xml file the &lt;properties&gt;, &lt;dependencyManagement&gt;, and &lt;build&gt; blocks.

   Now, repeat the operation for zipcloud-parent.

2. Open the zipcloud-parent's pom.xml file from the chapter\_1 source code and click on the pom.xml tab.

3. Copy and paste into your zipcloud-parent's pom.xml the &lt;properties&gt; and &lt;dependencyManagement&gt; blocks. You should already have copied over the &lt;build&gt; section in the third recipe.


我们的第一组更改与父项目有关：

1.我们将为这些父项目定义依赖项和构建选项。 让我们通过以下步骤：

1. 从chapter\_1源代码目录中打开cloudstreetmarket-parent pom.xml，然后选择pom.xml选项卡（主窗口下）。

   将`<properties>`，`<dependencyManagement>`和`<build>`块复制并粘贴到cloudstreetmarket -parent的pom.xml文件中。

   现在，重复zipcloud-parent的操作。

2. 从chapter\_1源代码打开zipcloud-parent的pom.xml文件，然后单击pom.xml选项卡。

3. 复制并粘贴到你的zipcloud-parent的pom.xml `<properties>`和`<dependencyManagement>`块中。 您应该已经复制在第三个配方中的`<build>`部分。



2. Now, we will define dependencies and build options for web modules:

1. Open the cloudstreetmarket-api's pom.xml from the chapter\_1 source code and select the pom.xml tab.

2. Copy and paste into your cloudstreetmarket-api's pom.xml the &lt;build&gt; and &lt;dependencies&gt; blocks.

3. Now, repeat the operation for cloustreetmarket-webapp.

4. Open the cloudstreetmarket-webapp's pom.xml from the chapter\_1 source code directory and click on the pom.xml tab.

5. Copy and paste into your cloudstreetmarket-webapp's pom.xml file the &lt;build&gt; and &lt;dependencies&gt; blocks.

2.现在，我们将定义web模块的依赖项和构建选项：

1. 从chapter\_1源代码中打开cloudstreetmarket-api的pom.xml并选择pom.xml选项卡。

2. 复制并粘贴到cloudstreetmarket-api的pom.xml `<build>`和`<dependencies>`块。

3. 现在，重复cloustreetmarket-webapp的操作。

4. 从chapter\_1源代码目录中打开cloudstreetmarket-webapp的pom.xml，然后单击pom.xml选项卡。

5. 复制并粘贴到cloudstreetmarket-webapp的pom.xml文件中`<build>`和`<dependencies>`块。



3. After this, we define dependencies for jar modules:

1. Open the cloudstreetmarket-core's pom.xml from the chapter\_1 source    code and click on the pom.xml tab.

2. Copy and paste into your cloudstreetmarket-core's pom.xml the entire    &lt;dependencies&gt; block.

3.之后，我们定义**jar**模块的依赖关系：

1. 从chapter\_1源中打开cloudstreetmarket-core的pom.xml   代码并单击pom.xml选项卡。

2. 复制并粘贴到您的cloudstreetmarket-core的pom.xml中`   <dependencies>`块。



4. Then, we place the web resources:

1. From the chapter\_1 source code, copy and paste the entire src/main/webapp/\* directory into your cloudstreetmarket-webapp project. Youneed to end up with the same webapp directory structure as the chapter\_1source code:

2. Now, perform the same operation for cloudstreetmarket-api. Copy and paste from the chapter\_1 source code the entire src/main/webapp/\* branch into your cloudstreetmarket-api project. You need to end up with the same webapp node and children as the chapter\_1 source code:

4. 然后，我们放置web资源：

1. 从chapter\_1源代码中，将整个`src / main / webapp / *` 目录复制并粘贴到cloudstreetmarket-webapp项目中。  你最终需要得到与chapter\_1源代码相同的webapp目录结构：

   ![](/assets/12.png)

2. 现在，对cloudstreetmarket-api执行相同的操作。 从chapter\_1源代码复制并粘贴整个`src / main / webapp / * `分支到cloudstreetmarket-api项目。 你最终需要得到与chapter\_1源代码相同的webapp 节点和子节点：

   ![](/assets/13.png)



5. Now, we target a runtime for the web modules:

1. In Eclipse, right-click on the cloudmarket-api project.

2. Select the Properties menu.

3. On the navigation panel, select **Targeted Runtimes**.

4. On the central window, check the Server **Apache Tomcat v8.0** option.

5. Click on **OK **and repeat the fifth operation on cloudstreetmarket-webapp.

5.现在，我们定位Web模块的运行时：

1. 在Eclipse中，右键单击cloudmarket-api项目。

2. 选择**Properties**菜单。

3. 在导航面板上，选择**Targeted Runtimes**。

4. 在中央窗口中，检查服务器**Apache Tomcat v8.0**选项。

5. 单击**OK **，并对**cloudstreetmarket-webapp**重复第五个操作。



> A few Eclipse warnings in the index.jsp files must have disappeared after this.
>
> index.jsp文件中的一些Eclipse警告必须在此之后消失。



If you still have Warnings in the project, your Eclipse Maven configuration may be out of synchronization with the local repository.

如果项目中仍有警告，则你的Eclipse Maven配置可能与本地存储库不同步。



6. This step should clean your existing project warnings \(if any\):

    In this case, perform the following steps:

1. Select all the projects in the project hierarchy, except the servers, as follows:

   ![](/assets/14.png)

2. Right-click somewhere in the selection and click on **Update Project** under **Maven**. The **Warnings **window at this stage should disappear!

6.此步骤应清除现有项目警告（如果有）：

   在这种情况下，请执行以下步骤：

1. 选择项目层次结构中除服务器之外的所有项目，如下所示：

2. 右键单击选择中的某处，然后单击**Maven**下的**Update Project**。 在这个阶段的**Warnings**窗口应该消失！



7. Let's deploy the wars and start Tomcat:

   Add the **servers**view in Eclipse. To do so, perform the following operations:

1. Navigate to **Window \| Show view \| Other**.

2. Open the **Server**directory and select servers. You should see the following tab created on your dashboard:

7.让我们部署战争并启动Tomcat：

   在Eclipse中添加**servers**视图。 为此，请执行以下操作：

1. 导航到**Window \| Show view \| Other**。

2. 打开**Server**目录并选择服务器。 您应该会在信息中心上看到以下选项卡：

   ![](/assets/15.png)



8. To deploy the web archives, go through the following operations:

1. Inside the view we just created, right-click on the **Tomcat v8.0 Server** at **localhost** server and select **Add and Remove…**

2. In the next step, which is the **Add and Remove** window, select the two available archives and click on **Add** and then on **Finish**.

8.要部署Web归档，请执行以下操作：

1. 在刚刚创建的视图中，右键单击**localhost**服务器上的**Tomcat v8.0Server** ，然后选择**Add and Remove**...。

2. 在下一步（即**Add and Remove**）窗口中，选择两个可用的归档，然后单击**Add**，然后单击**Finish**。



9. To start the application in Tomcat, we need to complete these steps:

1. In the **Servers** view, right-click on the **Tomcat v8.0 Server at localhost** server and click on **Start**.

2. In the **Console** view, you should have the following at the end:

9.要在Tomcat中启动应用程序，我们需要完成以下步骤：

1. 在服务器视图中，右键单击**localhost**服务器上的**Tomcat v8.0 Server**，然后单击**Start**。

2. 在**控制台**视图中，最后应该有以下内容：

`INFO: Starting ProtocolHandler ["http-nio-8080"]`

`Oct 20, 2014 11:43:44 AM org.apache.coyote.AbstractProtocol start`

`INFO: Starting ProtocolHandler ["ajp-nio-8009"]`

`Oct 20, 2014 11:43:44 AM org.apache.catalina.startup.Cata.. start`

`INFO: Server startup in 6898 ms`



> If you scroll up through these logs, you shouldn't have any exceptions!
>
> 如果你向上滚动这些日志，你不应该有任何异常！



Finally, if you try to reach http://localhost:8080/portal/index.html with your browser, you should receive the following HTML content:

最后，如果您尝试使用浏览器访问http：// localhost：8080 / portal / index.html，您应该会收到以下HTML内容：

![](/assets/16.png)

> A static access to an HTML page remains a modest visual achievement for this chapter. All along this book, you will discover that we haven't diminished the importance of the environment and the context Spring MVC acts in.
>
> 静态访问HTML页面对于本章仍然是一个温和的视觉成就。 在本书中，你会发现我们没有减少环境的重要性和Spring MVC的行为。


## How it works...

Through this recipe, we have been moving across web resources and Maven dependencies related to Spring, Spring MVC, and the web environment. Now, we will go through the way that Maven dependency and plugin management are performed. We will then talk about the Spring web application context and finally about the organization and packaging of web resources.


通过这个配方，我们已经跨越了与Spring，Spring MVC和Web环境相关的Web资源和Maven依赖。 现在，我们将介绍执行Maven依赖和插件管理的方式。 然后我们将讨论Spring Web应用程序上下文，最后讨论Web资源的组织和包装。


### Inheritance of Maven dependencies

There are two strategies concerning the inheritance of dependencies between parent projects and children modules. They both are implemented from the parent project. On the one hand,we can choose to define these dependencies directly from the `<dependencies>` node,shaping a basic inheritance in this way. On the other hand, to set up a managed inheritance,we can define the `<dependencies>` node as a child node of `<dependencyManagement>`. Let's have a look at the differences between the two.

Maven依赖的继承

有两个关于父项目和子模块之间的依赖的继承的策略。 它们都是从父项目实现的。 一方面，我们可以选择直接从`<dependencies>`节点定义这些依赖关系，以这种方式塑造基本继承。 另一方面，要设置一个管理继承，我们可以定义`<dependencies>`节点作为`<dependencyManagement>`的子节点.让我们来看看两者之间的差异。


### Basic inheritance

With a basic inheritance, all the dependencies specified in the parent's pom.xml file are automatically inherited into the child module with the same attributes (scope, version, packaging type, and so on) unless you override them (redefining these dependencies with the same couple groupId/artifactId).

On the one hand, it provides the option of using the versions of the dependencies we want in the modules we want. On the other hand, we can end up with a very complex dependencies schema and huge pom.xml files in the children modules. Also, managing version conflicts with external transitive dependencies can be a pain.

基本继承

使用基本继承，父工程的pom.xml文件中指定的所有依赖关系都会自动继承到具有相同属性（scope, version, packaging类型等）的子模块中，除非你覆盖它们（使用相同的属性重新定义这些依赖关系groupId / artifactId）。

一方面，它提供了在我们想要的模块中使用我们想要的依赖的版本的选项。 另一方面，我们可以得到一个非常复杂的依赖模式和子模块中庞大的`pom.xml`文件。 此外，管理版本与外部传递依赖性的冲突可能是一种痛苦。


> A transitive dependency is a required dependency with the needed dependency. Transitive dependencies have been automatically imported since Maven 2.0.

> 传递依赖是所需依赖的必需依赖。 传递依赖性已从Maven 2.0自动导入。


There are no standards in this inheritance type for external dependencies.

在这个继承类型中没有用于外部依赖的标准。


### Managed inheritance

With the < dependencyManagement> mechanism, dependencies defined in the parent pom.xml are not automatically inherited in children modules. However, the dependency attributes (scope, version, packaging type, and so on) are pulled from the parent dependency's definition, and therefore, the redefinition of these attributes is made optional. 

This process drives us towards a centralized dependency definition where all the children modules use the same versions of dependencies unless a specific dependency requires a custom one.


管理继承

使用`<dependencyManagement>`机制，在父工程的`pom.xml`中定义的依赖关系不会在children模块中自动继承。 但是，依赖属性（scope, version, packaging类型等）是从父依赖关系的定义中提取的，因此，这些属性的重新定义是可选的。

这个过程驱动着我们走向一个集中的依赖关系定义，其中所有的children模块使用相同的依赖关系，除非一个特定的依赖关系需要一个自定义依赖。


### Including third-party dependencies

Among the dependencies copied over, you might have noticed a few Spring modules, some test, web, logging, and utility dependencies.

The idea has been to start with a basic web development tool box, which is enhanced with all the Spring modules. We will visit most of the dependencies actually included when we face a particular situation.

包括第三方依赖项

在复制的依赖项中，你可能已经注意到了一些Spring模块，一些test，Web，logging和实用程序依赖项。
这个想法是从一个基本的Web开发工具箱开始，它增强了所有的Spring模块。 我们将访问当我们面对特定情况时实际包括的大多数依赖。



### The Spring Framework dependency model

As presented in the following diagram taken from the spring.io website, these days, the Spring Framework is currently made of 20 modules that are grouped in different areas:

Spring框架依赖模型

如下图所示，从spring.io网站，这些天，Spring框架目前由20个模块组成不同的区域：

![](/assets/17.png)

These modules have been included in the parent POMs as managed dependencies. This will allow us, later on, to quickly cherry-pick the needed ones, narrowing down a selection for our wars.

这些模块已作为托管依赖关系包含在父工程的**POM**中。 这将允许我们以后可以快速选择所需的，缩小我们的**wars**选择。

### The Spring MVC dependency

The Spring MVC module is self-contained in the spring-webmvc jar. Spring MVC in a web application is a fundamental element, as it handles incoming client requests and smoothly monitors the business operations from controllers. It finally offers a number of tools and interfaces capable of preparing responses in the format the clients expect them in.

All this workflow comes along with the spring-webmvc jar output HTML content or web services.

Spring MVC is entirely integrated in the Spring Framework, and all its components are standard with regard to the Spring architecture choices.

Spring MVC依赖

Spring MVC模块在spring-webmvc **jar**中是自包含的。  Web应用程序中的Spring MVC是一个基本元素，因为它处理传入的客户端请求并自如地监视来自控制器的业务操作。 最终提供了许多工具和接口，能够以客户期望他们的格式准备响应。

所有这工作流都伴随着spring-webmvc **jar**输出的HTML内容或Web服务。

Spring MVC完全集成在Spring框架中，所有的组件都是Spring架构选择的标准。


### Using Maven properties

In each parent pom.xml file, we have defined a `<properties>` block as part of the `<project>` section. These properties are user-defined properties bound to a project, but we can also define such properties within a **Maven Profile** option. Like variables, properties are referenced in the POMs with their name surrounded by **${…}**.

There is a standard on defining property names using periods as word separators. More than a standard, it is a uniform notation to access both user-defined variables and attributes of objects that constitute the Maven model. The Maven model is the public interface of Maven and starts from the project level.

The POM **XML Schema Definition (xsd)** is generated from this Maven model. It can sound abstract but in the end, the Maven model is only a set of POJOs with getters and setters. Have a look at the JavaDoc of the Maven model from the URL below, to identify concepts, specific to pom.xml files (Build, Dependency, Plugin, and so on.):


使用Maven属性

在每个父工程的`pom.xml`文件中，我们定义了一个`<properties>`块作为`<project>`节的一部分。 这些属性是绑定到项目的用户定义的属性，但是我们也可以在**Maven Profile**选项中定义这些属性。 像变量一样，属性在POM中引用，其名称由**${…}**包围。

有一个使用句点作为字分隔符来定义属性名称的标准。 不止一个标准，它是一个统一符号来访问用户定义的变量和构成Maven模型的对象的属性。  Maven模型是Maven的公共接口，从项目级开始。

POM **XML Schema Definition（xsd）**是从此Maven模型生成的。 它可以听起来抽象，但最终，Maven模型只是一组具有getter和setter的POJO。 从下面的URL中查看Maven模型的JavaDoc，以识别pom.xml文件特有的概念（Build，Dependency，Plugin等。）：

http://maven.apache.org/ref/3.0.3/maven-model/apidocs/index.html


To summarize, we can retrieve a node value defined in a POM and navigate the Maven model hierarchy using a period-based expression language that targets the getters.

For example, ${project.name} references the current project.getName(), ${project.parent.groupId}, the current project.getParent().getGroupId(), and so on.

Defining user properties that match an existing path of the Maven model is a way of overriding its value. That's what we have done for project.build.sourceEncoding.

Maven also offers the possibility to reach properties defined in the `settings.xml` files such as `${settings.localRepository}`; but also environment variables such as `${env.JAVA_HOME}`; and Java System properties such as `${java.class.path}`, `${java.version}`, `${user.home}`, or `${user.name}`.

总而言之，我们可以检索在POM中定义的节点值，并使用基于周期的表达式语言来定位Maven模型层次结构。

例如，${project.name}引用当前project.getName()，${project.parent.groupId}，当前project.getParent().getGroupId()等。

定义与Maven模型的现有路径匹配的用户属性是覆盖其值的一种方法。 这就是我们为project.build.sourceEncoding做的。

Maven还提供了达到`settings.xml`文件中定义的属性的可能性，例如`${settings.localRepository}`; 还有环境变量，如`${env.JAVA_HOME}`; 和Java系统属性，例如`${java.class.path}`，`${java.version}`，`${user.home}`或`${user.name}`。

### The web resources

If you remember, we copied/pasted the entire src/main/webapp directory from the chapter_1 source code. The webapp directory name is a Maven standard. The webapp folder in Eclipse doesn't need to be tagged as a source folder for the build path, as it would create a complex and useless package hierarchy for static files. Preferably, it appears as a plain directory tree.


如果你还记得，我们从chapter_1源代码复制/粘贴了整个`src/main/webapp`目录。  webapp目录名称是Maven标准。  Eclipse中的webapp文件夹不需要标记为构建路径的源文件夹，因为它会为静态文件创建一个复杂和无用的包层次结构。 优选地，它看起来像普通目录树。


The webapp directory must be seen as the document root of the application and positioned at the root level of the WAR. The public static web resources under webapp, such as HTML files, **Javascript**, **CSS**, and **image** files, can be placed in the subdirectories and structure of our choice. However, as described in the Servlet 3.0 Specification, the WEB-INF directory is a special directory within the application hierarchy. All its contents can never be reached from outside the application; its content is accessible from the servlet code calling for getResource or getResourceAsStream on ServletContext. The specification also tells us that the content of a WEB-INF directory is made up of the following:

    The /WEB-INF/web.xml deployment descriptor.
  
    The /WEB-INF/classes/ directory for servlet and utility classes. The classes in this directory must be available to the application class loader.
  
    The /WEB-INF/lib/*.jar area for Java ARchive files. These files contain servlets, beans, static resources, and JSPs packaged in a JAR file and other utility classes useful to the web application. The web application class loader must be able to load classes from any of these archive files.
  
  webapp目录必须被视为应用程序的文档根目录，并且位于WAR的根级别。  webapp下的公共静态Web资源（如HTML文件，Javascript，CSS和图像文件）可以放置在我们选择的子目录和结构中。 但是，如Servlet 3.0规范中所述，WEB-INF目录是应用程序层次结构中的特殊目录。 它的所有内容永远不能从应用程序外面访问; 其内容可以从servlet代码调用`getResource`或`getResourceAsStream`在ServletContext访问。 规范还告诉我们，WEB-INF目录的内容由以下内容组成：
  
    /WEB-INF/web.xml部署描述符。

    servlet和实用程序类的/ WEB-INF / classes /目录。 此目录中的类必须可供应用程序类装入器使用。

    用于Java ARchive文件的/WEB-INF/lib/*.jar区域。 这些文件包含打包在JAR文件中的servlet，bean，静态资源和JSP以及对Web应用程序有用的其他实用程序类。  Web应用程序类装入器必须能够从任何这些归档文件加载类。


It is good practice to create a jsp directory inside the WEB-INF folder so that the jsp files cannot be directly targeted without passing through an explicitly defined controller.

JSP applications do exist, and by definition, they will not follow this practice. These type of applications may be suited to certain needs, but they also don't specifically promote the use of an MVC pattern nor a great separation of concerns.

To use JSPs in a web application, the feature must be enabled in web.xml with the definition of a servlet of the org.apache.jasper.servlet.JspServlet type mapped to the JSP files location.

最好在WEB-INF文件夹中创建一个jsp目录，这样jsp文件不能通过明确定义的控制器直接定向。

JSP应用程序确实存在，根据定义，他们不会遵循这种做法。 这些类型的应用程序可能适合于某些需求，但也不具体地促进MVC模式的使用或关注的很大分离。

要在Web应用程序中使用JSP，必须在web.xml中启用该功能，并将org.apache.jasper.servlet.JspServlet类型的servlet的定义映射到JSP文件位置。

### The target runtime environment

We have experienced warnings in the index.jsp files. We have sorted them out by adding a target runtime to our projects. We also saw that Tomcat comes with the Eclipse Compilator for Java as a JAR library. To perform the JSP compilation, the tomcat8\lib directory must include the following JAR libraries: **jsp-api**, **servlet-api** and **el-api**, and so on. Specifying a target runtime for a project in Eclipse emulates and anticipates situation where the application will be run from an external Tomcat container (setup with those libraries). This also explains why the **jsp-api** and **el-api** dependencies are defined in the parent POMs with a provided scope.

目标运行时环境

我们在index.jsp文件中熟习了警告。通过将目标运行时添加到我们的项目中来对它们进行排序。还看到Tomcat附带了用于Java的Eclipse Compilator作为**JAR**库。 要执行JSP编译，tomcat8 \ lib目录必须包含以下JAR库：**jsp-api**，**servlet-api**和**el-api**等。 在Eclipse中指定项目的目标运行时模拟并预测应用程序将从外部Tomcat容器（使用这些库设置）运行的情况。 这也解释了为什么**jsp-api**和**el-api**依赖关系在具有提供范围的父工程的POM中定义。


### The Spring web application context

In the web.xml files, we defined a special type of Servlet, the Spring MVC DispatcherServlet, and we named it spring. This servlet covers the widest /* URL pattern. We will revisit the DispatcherServlet in the next chapter.


在`web.xml`文件中，我们定义了一种特殊类型的Servlet，即**Spring MVC DispatcherServlet**，我们将其命名为**spring**。 这个servlet覆盖最宽**/***的URL模式。 我们将在下一章重新讨论**DispatcherServlet**。


A DispatcherServlet has its own discovery algorithm that builds up WebApplicationContext. An optional contextConfigLocation initialization parameter is provided that points to a dispatcher-context.xml file. This parameter overrides the default expected filename and path (/WEB-INF/{servletName}-servlet.xml) for the WebApplicationContext defined in the DispatcherServlet discovery logic.

**DispatcherServlet**有自己的算法，构建**WebApplicationContext**。 提供了一个可选的**contextConfigLocation**初始化参数，它指向`dispatcher-context.xml`文件。 此参数覆盖**DispatcherServlet**发现逻辑中定义的**WebApplicationContext**的缺省预期文件名和路径(**/WEB-INF/{servletName}-servlet.xml**)。

With the load-on-startup attribute set to 1, as soon as the servlet container gets ready, a new WebApplicationContext gets loaded and scoped only for the starting servlet. Now, we don't wait for the first client request to load WebApplicationContext.

A Spring WebApplicationContext file usually defines or overrides the configuration and beans that Spring MVC offers to the web application.

Still in the web.xml file, an org.sfw.web.context.ContextLoaderListener listener is set up. The purpose of this listener is to start and shut down another SpringApplicationContext, which will be the root one following the container's life cycle.

To load more than one spring context file easily, the trick here is to use the classpath notation (which is relative) and the star (*) character in the resource path:

将`load-on-startup`属性设置为1时，只要servlet容器就绪，就只为起始servlet加载和限定一个新的**WebApplicationContext**。 现在，我们不等待第一个客户端请求加载**WebApplicationContext**。

**Spring WebApplicationContext**文件通常定义或覆盖Spring MVC为Web application提供的配置和bean。

仍然在`web.xml`文件中，设置了一个`org.sfw.web.context.ContextLoaderListener`监听器。 这个监听器的目的是启动和关闭另一个**SpringApplicationContext**，它将是容器生命周期之后的根。

要轻松加载多个spring context文件，这里的技巧是在资源路径中使用classpath表示法（它是相对的）和星号**(*)**字符：



```
<context-param>
  <param-name>contextConfigLocation</param-name>
  <param-value>classpath*:/META-INF/spring/*-config.xml</param-value> 
</context-param>
```
Doing so allows us to load all the context files encountered in the classpath that match a standard notation and location. This approach is appreciated for the consistency it imposes but also for the way it targets context files in underlying jars.

The aggregation of all the matching context files creates an ApplicationContext root with a much broader scope, and the WebApplicationContext inherits it. The beans we define in the root context become visible to the WebApplicationContext context. We can override them if needed. However, the DispatcherServlet context's beans are not visible to the root context.

这样做允许我们加载在类路径中遇到的所有匹配标准符号和位置的context文件。 这种方法是赞赏它强加的一致性，但也为目标上下文文件在底层jars的方式。

所有匹配的上下文文件的聚合创建一个具有更宽范围的ApplicationContext根，并且WebApplicationContext继承它。 我们在根上下文中定义的bean变得对WebApplicationContext上下文可见。 如果需要，我们可以覆盖它们。 但是，DispatcherServlet上下文的bean对根上下文不可见。

### Plugins

Maven is, above all, a plugin's execution framework. Every task run by Maven corresponds to a plugin. A plugin has one or more goals that are associated individually to life cycle phases. Like the dependencies, the plugins are also identified by a **groupId**, an **artifactId**, and a **version**. When Maven encounters a plugin that is not in the local repository, it downloads it. Also, a specific version of Maven targets, by default, a number of plugins that match the life cycle phases. These plugins are frozen on fixed versions and therefore on a defined behavior—you need to override their definition to get a more recent version or to alter their default behavior.


Maven，首先，一个插件的执行框架。 Maven运行的每个任务都对应一个插件。 插件具有一个或多个目标，它们分别与生命周期阶段相关联。 与依赖关系类似，插件也由**groupId**，**artifactId**和**version**标识。 当Maven遇到不在本地存储库中的插件时，会下载它。 此外，一个特定版本的Maven目标，默认情况下，许多插件匹配的生命周期阶段。 这些插件在固定版本上冻结，因此根据定义的行为冻结 - 你需要重写其定义以获取更新的版本或更改其默认行为。


### The Maven compiler plugin

The maven-compiler-plugin is a Maven core plugin. The core plugins are named as such because their goals are triggered on Maven core phases (clean, compile, test, and so on.). Noncore plugins relate to packaging, reporting, utilities, and so on. It is good practice to redefine the maven-compiler-plugin to control which version of the compiler is to be used or to trigger some external tools' actions (the m2eclipse project management tool, actually).

As its name suggests, the maven compiler plugin compiles the Java sources. For that, it uses the javax.tools.JavaCompiler class and has two goals: compiler:**compile**(triggered as part of the compile phase to compile the java/main source classes) and compiler:**testCompile** (triggered as part of the test-compile phase to compile the java/test source classes).

Maven编译器插件

**maven-compiler-plugin**是一个Maven核心插件。 核心插件是这样命名的，因为它们的目标是在Maven核心阶段（clean, compile, test等）上触发的。 非核心插件与packaging, reporting, utilities等有关。 最好重新定义**maven-compiler-plugin**来控制要使用的编译器版本，或者触发一些外部工具的操作（实际上是m2eclipse项目管理工具）。

顾名思义，maven编译器插件编译Java源代码。 为此，它使用`javax.tools.JavaCompiler`类，并有两个目标：编译器：**compile**（编译阶段时触发编译java /main source classes）和编译器：**testCompile**（触发作为测试编译的一部分 阶段编译java /test source classes）。


### The Maven surefire plugin

The maven-surefire-plugin is also a Maven core plugin that has only one goal:

surefire:test. This is invoked as part of the default life cycle (the test phase) to run unit tests defined in the application. It generates reports (*.txt or *.xml), by default, under the ${basedir}/target/surefire-reports location.


maven-surefire插件也是一个Maven核心插件，只有一个目标：

surefire：test。 这被作为默认生命周期（测试阶段）的一部分来调用，以运行应用程序中定义的单元测试。 它在默认情况下在`${basedir}/target/surefire-reports`位置下生成报告（*.txt 或 *.xml）。


### The Maven enforcer plugin

The **maven-enforcer-plugin** is very useful to define environmental conditions as critical for the project. It has two goals: enforcer:**enforce** (bound, by default, to the validate phase,
where it executes each defined rule once per module) and enforcer:**display-info** (it displays the detected information on execution of the rules).

The most interesting standard rule is probably **DependencyConvergence**: it analyzes all the used dependencies (direct and transitive) for us. In case of divergence of a version, it highlights it and stops the build. When we face this kind of conflict, it is amazingly easy to decide between the following:

   Excluding the lowest version from the classpath

   Not upgrading the dependency

We also quickly talked about the <pluginManagement> section, which was associated to the maven-enforcer-plugin. In this case, this is because m2eclipse doesn't support this plugin. Thus, to avoid a warning in Eclipse, it is necessary to add this section so that m2eclipse skips the enforce goal.


**maven-enforcer-plugin**对于定义环境条件对于项目至关重要非常有用。它有两个目标：enforcer：**enforce**（默认绑定到验证阶段，每个模块执行每一次定义的规则）和enforcer：**display-info**（它显示检测到的关于规则执行的信息）。

最有趣的标准规则可能是**DependencyConvergence**：它分析所有使用的依赖关系（直接和传递）。在一个版本的分歧的情况下，突出显示它并停止构建。当我们面对这种冲突时，很容易决定以下因素：

   从类路径中排除最低版本

   不升级依赖关系

我们还很快谈到了`<pluginManagement>`部分，它与**maven-enforcer-plugin**相关联。在这种情况下，这是因为m2eclipse不支持此插件。因此，为了避免在Eclipse中出现警告，必须添加此部分，以便m2eclipse跳过强制目标。

### The Maven war plugin

Using the maven-war-plugin, we redefined in our web POMs. We have again overridden the default behavior of this plugin that is used to package web modules. This is definitely necessary if you have a non-Maven standard project structure.

We may want to package our web resources in a different way that how it is organized in our IDE. We may need, for some reason, to exclude some resources from the war packaging or we may even want to give a name to the built war so that it can be used by the servlet container that matches a specific context path in the application URLs (/api, /app, and so on). Filtering, moving web resources around, and managing the generated war is the purpose of this plugin.


使用**maven-war-plugin**，在我们的Web POM中重新定义。再次覆盖了用于封装Web模块的此插件的默认行为。 如果你有一个非Maven标准项目结构，这是绝对必要的。

我们可能想以不同的方式打包我们的Web资源，如何在我们的IDE中组织。 由于某种原因，我们可能需要从war包装中排除一些资源，或者甚至可能想要为构建的war命名，以便servlet容器可以使用它匹配应用程序URL中的特定上下文路径(/api, /app，等等)。 过滤，移动网络资源和管理生成的war是这个插件的目的。

> By default, the web resources are copied to the WAR root. To override the default destination directory, specify the target path *.

> 默认情况下，Web资源将复制到WAR根目录。 要覆盖默认目标目录，请指定目标路径*。


## ##There's more...

This has been quite a broad overview about concepts that naturally require deeper interest:

About the way Maven manages its dependencies, we would suggest you to go through the Maven documentation on this topic at:

  http://maven.apache.org/guides/introduction/introduction-todependency-mechanism.html

  The Sonatype ebook talks nicely about Maven properties. You can find this ebook at: https://books.sonatype.com/mvnref-book/reference/resourcefiltering-sect-properties.html#resource-filtering-sectsettings-properties

  The Maven model API documentation can again be found at:
http://maven.apache.org/ref/3.0.3/maven-model/apidocs/index.
html

  Concerning the servlet 3.0 specification that we mentioned earlier, more information can be found about the web.xml file definition and about the structure of a WebArchive at: http://download.oracle.com/otn-pub/jcp/servlet-3.0-
fr-eval-oth-JSpec/servlet-3_0-final-spec.pdf

  Finally, for more information about Maven plugins; we absolutely recommend you visit the Maven listing at http://maven.apache.org/plugins


这对于自然需要更深入的兴趣的概念已经进行了相当广泛的概述：
  关于Maven管理其依赖关系的方式，我们建议您阅读有关此主题的Maven文档：

  http://maven.apache.org/guides/introduction/introduction-todependency-mechanism.html

  Sonatype电子书很好地谈论了Maven属性。您可以在以下网址找到此电子书：https://books.sonatype.com/mvnref-book/reference/resourcefiltering-sect-properties.html#resource-filtering-sectsettings-properties

  可以再次找到Maven模型API文档：
http://maven.apache.org/ref/3.0.3/maven-model/apidocs/index。
html

  关于我们前面提到的servlet 3.0规范，有关web.xml文件定义和WebArchive结构的更多信息，请访问：http://download.oracle.com/otn-pub/jcp/servlet-3.0 - - 
fr-eval-oth-JSpec / servlet-3_0-final-spec.pdf

  最后，有关Maven插件的更多信息;我们绝对建议您访问http://maven.apache.org/plugins上的Maven列表


**See also**

  The spring.io website from Pivotal, and especially the Spring Framework overview page, can also refresh, or introduce a few key concepts. Follow the address:
http://docs.spring.io/spring-framework/docs/current/springframework-reference/html/overview.html## ### 

   来自Pivotal的spring.io网站，尤其是Spring Framework概述页面，还可以刷新或介绍几个关键概念。 按地址：
http://docs.spring.io/spring-framework/docs/current/springframework-reference/html/overview.ht
### The Maven checkstyle plugin

One other interesting enough plugin which could also be highlighted here is the mavencheckstyle-plugin. When a team is growing, we sometimes need to guarantee the maintenance of certain development practices or we may need to maintain specific securityrelated coding practices. Like the maven-enforcer-plugin, the maven-checkstyle-plugin makes our builds assertive against this type of violation.

Find out more about this plugin, again in the Maven documentation, at: http://maven.apache.org/plugins/maven-checkstyle-plugin.



另一个有趣的插件，也可以在这里突出显示的是**mavencheckstyle-plugin**。 当一个团队成长时，我们有时需要保证某些开发实践的维护，或者我们可能需要维护特定的安全相关的编码实践。 像**maven-enforcecer-plugin**一样，**maven-checkstyle-plugin**使我们的构建对这种类型的违例断言。

有关此插件的更多信息，请参阅Maven文档，网址为：
http://maven.apache.org/plugins/maven-checkstyle-plugin.


