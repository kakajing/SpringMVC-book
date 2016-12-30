In this recipe, we will add third-party dependencies to our pom.xml files using inheritance.

We will load Spring application contexts and create the first controller of our application. Finally, we will deploy and start the web app in Tomcat.

在这个谱方中，我们将使用继承向我们的pom.xml文件中添加第三方依赖项。

我们将加载Spring application contexts并创建我们的应用程序的第一个控制器。 最后，我们将在Tomcat中部署和启动Web应用程序。



**Getting ready**

Now that we have Eclipse ready and Maven configured properly, the fun can begin. We need to specify all the necessary Spring dependencies in our pom.xml files, and we need to set up Spring so that it loads and retrieves its context for every module.

We also need to organize and optionally expose web resources, such as JSPs, JavaScript files,CSS files, and so on. If you've completed this configuration, we should end up with a static welcome page provided by the Tomcat server, started without exceptions!

**准备**

现在我们已经准备好Eclipse并且Maven configured properly，可以开始。 我们需要在我们的pom.xml文件中指定所有必需的Spring依赖关系，我们需要设置Spring，以便加载和检索每个模块的上下文。

我们还需要组织并可选地公开Web资源，例如JSP，JavaScript文件，CSS文件等。 如果你已经完成了这个配置，我们应该得到一个由Tomcat服务器提供的静态欢迎页面，开始时没有例外！



**How to do it...**

Our first set of changes relate to the parent projects:

1. We will define dependencies and build options for those parent projects. Let’s do it with the following steps:

1. Open the cloudstreetmarket-parent pom.xml from the chapter\_1 source code directory and select the pom.xml tab \(underneath the main window\).

   Copy and paste into the cloudstreetmarket-parent's pom.xml file the &lt;properties&gt;, &lt;dependencyManagement&gt;, and &lt;build&gt; blocks.

   Now, repeat the operation for zipcloud-parent.

2. Open the zipcloud-parent's pom.xml file from the chapter\_1 source code and click on the pom.xml tab.

3. Copy and paste into your zipcloud-parent's pom.xml the &lt;properties&gt; and &lt;dependencyManagement&gt; blocks. You should already have copied over the &lt;build&gt; section in the third recipe.

**怎么做...**

我们的第一组更改与父项目有关：

1.我们将为这些父项目定义依赖项和构建选项。 让我们通过以下步骤：

1. 从chapter\_1源代码目录中打开cloudstreetmarket-parent pom.xml，然后选择pom.xml选项卡（主窗口下）。

   将`<properties>`，`<dependencyManagement>`和`<build>`块复制并粘贴到cloudstreetmarket -parent的pom.xml文件中。

   现在，重复zipcloud-parent的操作。

2. 从chapter\_1源代码打开zipcloud-parent的pom.xml文件，然后单击pom.xml选项卡。

3. 复制并粘贴到您的zipcloud-parent的pom.xml `<properties>`和`<dependencyManagement>`块中。 您应该已经复制在第三个配方中的`<build>`部分。



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

2. 现在，对cloudstreetmarket-api执行相同的操作。 从chapter\_1源代码复制并粘贴整个`src / main / webapp / * `分支到cloudstreetmarket-api项目。 你最终需要得到与chapter\_1源代码相同的webapp node 和children：

   ![](/assets/13.png)



5. Now, we target a runtime for the web modules:

1. In Eclipse, right-click on the cloudmarket-api project.

2. Select the Properties menu.

3. On the navigation panel, select **Targeted Runtimes**.

4. On the central window, check the Server **Apache Tomcat v8.0** option.

5. Click on **OK **and repeat the fifth operation on cloudstreetmarket-webapp.

5.现在，我们定位Web模块的运行时：

1. 在Eclipse中，右键单击cloudmarket-api项目。

2. 选择**Properties **菜单。

3. 在导航面板上，选择**Targeted Runtimes**。

4. 在中央窗口中，检查服务器**Apache Tomcat v8.0**选项。

5. 单击**OK **，并对**cloudstreetmarket-webapp**重复第五个操作。



> A few Eclipse warnings in the index.jsp files must have disappeared after this.
>
> index.jsp文件中的一些Eclipse警告必须在此之后消失。



If you still have Warnings in the project, your Eclipse Maven configuration may be out of synchronization with the local repository.

如果项目中仍有警告，则您的Eclipse Maven配置可能与本地存储库不同步。



6. This step should clean your existing project warnings \(if any\):

    In this case, perform the following steps:

1. Select all the projects in the project hierarchy, except the servers, as follows:

   ![](/assets/14.png)

2. Right-click somewhere in the selection and click on **Update Project** under **Maven**. The **Warnings **window at this stage should disappear!

6.此步骤应清除现有项目警告（如果有）：

   在这种情况下，请执行以下步骤：

1. 选择项目层次结构中除服务器之外的所有项目，如下所示：

2. 右键单击选择中的某处，然后单击**Maven**下的**Update Project**。 在这个阶段的**Warnings **窗口应该消失！



7. Let's deploy the wars and start Tomcat:

   Add the **servers **view in Eclipse. To do so, perform the following operations:

1. Navigate to **Window \| Show view \| Other**.

2. Open the **Server **directory and select servers. You should see the following tab created on your dashboard:

7.让我们部署战争并启动Tomcat：

   在Eclipse中添加**servers **视图。 为此，请执行以下操作：

1. 导航到**Window \| Show view \| Other**。

2. 打开**Server **目录并选择服务器。 您应该会在信息中心上看到以下选项卡：

   ![](/assets/15.png)



8. To deploy the web archives, go through the following operations:

1. Inside the view we just created, right-click on the **Tomcat v8.0 Server** at **localhost **server and select **Add and Remove….   **

2. In the next step, which is the **Add and Remove** window, select the two available archives and click on **Add **and then on **Finish**.

8.要部署Web归档，请执行以下操作：

1. 在刚刚创建的视图中，右键单击**localhost**服务器上的**Tomcat v8.0Server** ，然后选择**Add and Remove**...。

2. 在下一步（即**Add and Remove**）窗口中，选择两个可用的归档，然后单击**Add**，然后单击**Finish**。



9. To start the application in Tomcat, we need to complete these steps:

1. In the **Servers **view, right-click on the **Tomcat v8.0 Server at localhost** server and click on **Start**.

2. In the **Console **view, you should have the following at the end:

9.要在Tomcat中启动应用程序，我们需要完成以下步骤：

1. 在服务器视图中，右键单击**localhost**服务器上的**Tomcat v8.0 Server **，然后单击**Start**。

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





