In this recipe, we will focus on defining, with Maven, the project structure we need for our application.

在本文中，我们将重点介绍使用Maven定义我们的应用程序所需的项目结构。



**Getting ready**

We will initially create two Eclipse projects: one for the application and one for the components that ZipCloud as a company could share later on with other projects. Take a look at the following image which presents the project components that we are going to build:

**准备**

我们将首先创建两个`Eclipse`项目：一个用于应用程序，一个用于`ZipCloud`作为公司可以与其他项目共享的组件。 看看下面的图像，其中介绍了我们将要构建的项目组件：

![](/assets/4.png)



The application project **`cloudstreetmarket-parent`** will have three modules. Two of them will be packaged as web archives **\(war\)**: the main web application and the **REST API**. One of them will be packaged as a** jar **dependency \(cloudstreetmarket-core\).

The company-specific project **zipcloud-parent** will have only one submodule—**zipcloud-core**, which will be packaged as **jar**.

应用项目**cloudstreetmarket-parent**将有三个模块。 其中两个将被打包为**Web**存档**（war）**：主要的web应用程序和**REST API**。 其中一个将被打包为jar依赖（cloudstreetmarket-core）。

公司特定的项目**zipcloud-parent**将只有一个子模块-**zipcloud-core**，它将打包为**jar**。



**How to do it...**

The following steps will help us create a Maven parent project:

1. From Eclipse, navigate to File \| New \| Other.
2. A New wizard opens up wherein you can select the type of project within a hierarchy. Then, open the Maven category, select Maven Project, and click on Next.

      The New Maven Project wizard opens as shown in the following screenshot:

**怎么做...**

以下步骤将帮助我们创建一个Maven父项目：

1. 从Eclipse，导航到文件\| 新\| 其他。

2. 将打开一个新向导，您可以在其中选择层次结构中的项目类型。 然后，打开Maven类别，选择Maven Project，然后单击下一步。

      将打开New Maven项目向导，如以下屏幕截图所示：

![](/assets/5.png)



3.Make sure to check the** Create a simple project** option. Click on **Next**.

4. Fill up the next wizard as follows:

* [ ] edu.zipcloud.cloudstreetmarket as **Group Id  **
* [ ] cloudstreetmarket-parent as **Artifact Id  **

* [ ] 0.0.1-SNAPSHOT as **Version  **

* [ ] pom as **Packaging  **

* [ ] CloudStreetMarket Parent as **Name  **

* [ ] Then, click on the **Finish** button

The parent project must appear in the package explorer on the left-hand side of the dashboard.

We now have to tell m2eclipse which Java compiler version you plan to use in this project so that it automatically adds the right JRE system library to the submodules we are about to create. This is done through the pom.xml file.



3.确保选中创建简单项目选项。 单击下一步。

4.按照以下步骤填写下一个向导：

* [ ] edu.zipcloud.cloudstreetmarket作为组**Group Id**
* [ ] cloudstreetmarket-parent作为**Artifact Id  **

* [ ] 0.0.1-SNAPSHOT作为**Version**

* [ ] pom作为**Packaging  **

* [ ] CloudStreetMarket Parent作为**Name  **

* [ ] 然后，单击完成**Finish** 

父项目必须显示在仪表板左侧的包资源管理器中。

![](/assets/6.png)

我们现在必须告诉m2eclipse哪个Java编译器版本计划在这个项目中使用，以便它自动添加正确的`JRE`系统库到我们即将创建的子模块。 这是通过pom.xml文件完成的。



5. Edit pom.xml file to specify the Java compiler version:

* [ ]  Double-click on the **pom.xml** file. The **m2eclipse Overview** tab shows up by default. You have to click on the last tab,** pom.xml**, to access the full XML definition.
* [ ] In this definition, add the following block at the end but still as part of the &lt;project&gt; node. \(You can also copy/paste this piece of code from the cloudstreetmarket-parent's pom.xml of the chapter\_1 source code\):

&lt;build&gt;

   &lt;plugins&gt;

    &lt;plugin&gt;

     &lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;

     &lt;artifactId&gt;maven-compiler-plugin&lt;/artifactId&gt;

     &lt;version&gt;3.1&lt;/version&gt;

    &lt;configuration&gt;

      &lt;source&gt;1.8&lt;/source&gt;

      &lt;target&gt;1.8&lt;/target&gt;

      &lt;verbose&gt;true&lt;/verbose&gt;

      &lt;fork&gt;true&lt;/fork&gt;

     &lt;executable&gt;${JAVA\_HOME}/bin/javac&lt;/executable&gt;

      &lt;compilerVersion&gt;1.8&lt;/compilerVersion&gt;

    &lt;/configuration&gt;

   &lt;/plugin&gt;

   &lt;plugin&gt;

       &lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;

       &lt;artifactId&gt;maven-surefire-plugin&lt;/artifactId&gt;

      &lt;version&gt;2.4.2&lt;/version&gt;

   &lt;configuration&gt;

   &lt;jvm&gt;${JAVA\_HOME}/bin/java&lt;/jvm&gt;

&lt;forkMode&gt;once&lt;/forkMode&gt;

&lt;/configuration&gt;

     &lt;/plugin&gt;

   &lt;/plugins&gt;

&lt;/build&gt;



> You have probably noticed the **maven-surefire-plugin** declaration as well. We will review it soon; it allows us to run unit tests during.



5.编辑pom.xml文件以指定Java编译器版本：

* [ ]  双击**pom.xml**文件。 默认情况下，**m2eclipse Overview**选项卡显示。 您必须单击最后一个选项卡**pom.xml**才能访问完整的XML定义。
* [ ] 在此定义中，在结尾添加以下块，但仍然作为**&lt;project&gt;**节点的一部分。  （您还可以从cloudstreetmarket  -  parent的chapter\_1源代码的pom.xml复制/粘贴此代码段）：

&lt;build&gt;

   &lt;plugins&gt;

    &lt;plugin&gt;

     &lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;

     &lt;artifactId&gt;maven-compiler-plugin&lt;/artifactId&gt;

     &lt;version&gt;3.1&lt;/version&gt;

    &lt;configuration&gt;

      &lt;source&gt;1.8&lt;/source&gt;

      &lt;target&gt;1.8&lt;/target&gt;

      &lt;verbose&gt;true&lt;/verbose&gt;

      &lt;fork&gt;true&lt;/fork&gt;

     &lt;executable&gt;${JAVA\_HOME}/bin/javac&lt;/executable&gt;

      &lt;compilerVersion&gt;1.8&lt;/compilerVersion&gt;

    &lt;/configuration&gt;

   &lt;/plugin&gt;

   &lt;plugin&gt;

       &lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;

       &lt;artifactId&gt;maven-surefire-plugin&lt;/artifactId&gt;

      &lt;version&gt;2.4.2&lt;/version&gt;

   &lt;configuration&gt;

   &lt;jvm&gt;${JAVA\_HOME}/bin/java&lt;/jvm&gt;

&lt;forkMode&gt;once&lt;/forkMode&gt;

&lt;/configuration&gt;

     &lt;/plugin&gt;

   &lt;/plugins&gt;

&lt;/build&gt;



> 你可能注意到了maven-surefire-plugin声明以及。 我们会尽快进行审查; 它允许我们在运行单元测试构建。



   6. Now, we will create submodules:

      As submodules of the Parent project, we have seen that we needed one web module to handle and render the site's screens, one web module for the REST API, and one other module that will be used to package all the business logic \(services, data access, and so on.\) specific to the first product cloudstreetmarket.com:

1.    From the main Webapp module:in Eclipse, navigate to **File \| New \| Other**. A **New** wizard opens up through which you can select the type of project within a hierarchy. Open the Maven category, select Maven Module, and click on Next.
2. The New Maven Module wizard opens up after this; fill it up as follows:

   Check Create a simple project.

   Enter cloudstreetmarket-webapp as Module Name.

   Enter cloudstreetmarket-parent as Parent project.

   ![](/assets/7.png)

3. Click on the **Next **button after which the next step shows up.Enter the following entries in that new window:

   Enter edu.zipcloud.cloudstreetmarket as **Group Id.   **

   Enter 0.0.1-SNAPSHOT as **Version**.

   Select **war** as **Packaging**.

   Enter CloudStreetMarket Webapp as **Name**.

   Then click on the **Finish **button.



6.现在，我们将创建子模块：

    作为Parent项目的子模块，我们已经看到，我们需要一个Web模块来处理和渲染网站的屏幕，REST API的一个Web模块，以及另一个用于打包所有业务逻辑（服务，数据 访问等）特定于第一个产品cloudstreetmarket.com：

1. 从主Webapp模块：在Eclipse中，导航到**File \| New \| Other**。 将打开一个**new**，通过该向导可以选择层次结构中项目的类型。 打开**Maven**类别，选择**Maven Module**，然后单击**Next**。

2. .此后将打开New Maven模块向导; 填写如下：

           检查创建一个简单的项目。

           输入cloudstreetmarket-webapp作为模块名称。

           输入cloudstreetmarket-parent作为父项目。

3. 单击N**ext**按钮，之后将显示下一步。在该新窗口中输入以下条目：

       输入edu.zipcloud.cloudstreetmarket作为**Group Id**。

       输入0.0.1-SNAPSHOT作为**Version**。

       选择**war**作为**Packaging**。

       输入CloudStreetMarket Webapp作为**Name**。

       然后单击**Finish **按钮。

    

7.Now we will go ahead to create and REST API module:

  We are going to repeat the previous operation with different parameters.

1.     From Eclipse, navigate to **File \| New \| Other**. The selection wizard pops up when you go there. After this, open the **Maven **category, select **Maven Module**, and click on **Next**.
2. In the **New Maven Module** wizard, enter the following entries:

          Check the **Create a simple project** option.

          Enter cloudstreetmarket-api as **Module Name**.

          Enter cloudstreetmarket-parent as **Parent project**.

3. Click on the **Next **button to proceed to the next step. Enter the following entries in that window:

          Enter edu.zipcloud.cloudstreetmarket as **Group Id**.

          Enter 0.0.1-SNAPSHOT as **Version**.

          Select** war **as** Packaging**.

          Enter CloudStreetMarket API as** Name**.

          Then click on the **Finish **button.

    

7.现在我们将继续创建和REST API模块：

   我们将使用不同的参数重复前面的操作。

1.    从Eclipse，导航到**File \| New \| Other**。 当你去那里时，选择向导弹出。 之后，打开**Maven 类型**，选择**Maven Module**，然后单击**Next**。
2. 在**New Maven Module**向导中，输入以下条目：

           选中**Create a simple project**选项。

           输入cloudstreetmarket-api作为**Module Name**。

           输入cloudstreetmarket-parent作为**Parent project**。

3. 单击**Next **按钮继续下一步。 在该窗口中输入以下条目：

           输入edu.zipcloud.cloudstreetmarket作为**Group Id**。

           输入0.0.1-SNAPSHOT作为**Version**。

           选择**war**作为**Packaging**。

           输入CloudStreetMarket API作为**Name**。

           然后单击**Finish **按钮。

   

8. Now, we will create the core module:

  For this, navigate to** File \| New \| Other**. The selection wizard pops up when you do so. Open the **Maven **category, select **Maven Module**, and click on **Next**.

1. In the **New Maven Module** wizard, enter the following entries:

         Check the **Create a simple project** option.

         Enter cloudstreetmarket-core as **Module Name**.

         Enter cloudstreetmarket-parent as **Parent project**.

  2. Click on the **Next **button to go to the next step. Fill in the fields with the following:

       Enter edu.zipcloud.cloudstreetmarket as **Group Id**.

        Enter 0.0.1-SNAPSHOT as **Version**.

        This time, select **jar **as **Packaging**.

       Enter CloudStreetMarket Core as **Name**.

       Then click on the **Finish **button.

8.现在，我们将创建核心模块：

   为此，导航到**File \| New \| Other**。 当您这样做时，选择向导弹出。 打开**Maven**类别，选择**Maven Module**，然后单击**Next**。

   1.在**New Maven Module**向导中，输入以下条目：

     选中**Create a simple project**选项。

     输入cloudstreetmarket-core作为**Module Name**。

     输入cloudstreetmarket-parent作为**Parent project**。

   2.单击**Next**按钮转到下一步。 在字段中填写以下内容：

     输入edu.zipcloud.cloudstreetmarket作为**Group Id**。

     输入0.0.1-SNAPSHOT作为**Version**。

     这一次，选择**jar**作为**Packaging**。

     输入CloudStreetMarket Core为**Name**。

     然后单击**Finish **按钮。



If you have the Java perspective activated \(in the top-right corner\), you should see the overall created structure matching the screenshot here:

如果你激活了Java透视图（在右上角），你应该看到整个创建的结构与此处的截图相符：





![](/assets/8.png)  


9. Now, we will create a company-specific project and its module\(s\):

Let's assume that many different categories of dependencies \(core, messaging, reporting, and so on…\) will be part of the company-business project later.

1.   We need a parent project, so from Eclipse, navigate to **File \| New \| Other**.The selection wizard pops up. Open the Maven category, select Maven Project, and    click on Next.
2. In the first step of the New Maven Project wizard, as for the Parent project we created earlier, only check the **Create a simple Project** and **Use default** **workspace location** options.

3. Click on the **Next** button and fill in the next wizard as follows:

        Enter edu.zipcloud as **Group Id**.

        Enter zipcloud-parent as **Artifact Id**.

         Enter 0.0.1-SNAPSHOT as **Version**.

         Select **pom **as Packaging.

        Enter ZipCloud Factory Business Parent as **Name**.



9.现在，我们将创建一个公司特定的项目及其模块：

   让我们假设许多不同类型的依赖（核心，消息传递，报告等等）将成为公司业务项目的一部分。

1.    我们需要一个父项目，所以从Eclipse，导航到**File \| New \| Other**。选择向导弹出。 打开**Maven**类别，选择**Maven Project**和   单击**Next**。

2.在New Maven Project向导的第一步中，对于我们之前创建的`Parent`项目，选中**Create a simple Project** 和 **Use default** **workspace location**选项。

3.单击**Next**按钮并填写下一个向导，如下所示：

      输入edu.zipcloud作为**Group Id**。

       输入zipcloud-parent作为**Artifact Id**。

      输入0.0.1-SNAPSHOT作为**Version**。

     选择**pom**作为**Packaging**。

     输入ZipCloud Factory Business Parent为**Name**。



Again, in the created `pom.xml` file, add the following block inside the` <project> `node to create the underlying modules properly and to enable automatic test execution. \(You can also copy/paste this piece of code from the zipcloud-parent's pom.xml file of the chapter\_1 source code\):

再次，在创建的`pom.xml`文件中，在`<project>`节点中添加以下块，以正确创建底层模块并启用自动测试执行。  （您也可以从ziplipse-parent的chapter\_1源代码的pom.xml文件中复制/粘贴这段代码）：

&lt;build&gt;

   &lt;plugins&gt;

    &lt;plugin&gt;

     &lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;

     &lt;artifactId&gt;maven-compiler-plugin&lt;/artifactId&gt;

     &lt;version&gt;3.1&lt;/version&gt;

    &lt;configuration&gt;

      &lt;source&gt;1.8&lt;/source&gt;

      &lt;target&gt;1.8&lt;/target&gt;

      &lt;verbose&gt;true&lt;/verbose&gt;

      &lt;fork&gt;true&lt;/fork&gt;

     &lt;executable&gt;${JAVA\_HOME}/bin/javac&lt;/executable&gt;

      &lt;compilerVersion&gt;1.8&lt;/compilerVersion&gt;

    &lt;/configuration&gt;

   &lt;/plugin&gt;

   &lt;plugin&gt;

       &lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;

       &lt;artifactId&gt;maven-surefire-plugin&lt;/artifactId&gt;

      &lt;version&gt;2.4.2&lt;/version&gt;

   &lt;configuration&gt;

   &lt;jvm&gt;${JAVA\_HOME}/bin/java&lt;/jvm&gt;

&lt;forkMode&gt;once&lt;/forkMode&gt;

&lt;/configuration&gt;

     &lt;/plugin&gt;

   &lt;/plugins&gt;

&lt;/build&gt;



Now, we are going to create one company-business core module, which will be a sub module of the parent project we just created.

For this, navigate to **File \| New \| Othe**r. The selection wizard pops up. Open the

**Maven** category, select **Maven Module**, and click on **Next**.

1. In the **New Maven Module** wizard, enter the following details:

     Check the **Create a simple project** option.

     Enter zipcloud-core as **Module Name**.

     Enter zipcloud-parent as **Parent project**.

 2. Click on the **Next** button and go to the next step. Here, enter the following details:

    Enter edu.zipcloud as **Group Id**.

   Enter 0.0.1-SNAPSHOT as **Version**.

   Select** jar** as **Packaging**.

   Select ZipCloud Factory Core Business as **Name**.



现在，我们将创建一个公司 - 业务核心模块，它将是我们刚刚创建的父项目的子模块。

为此，导航到**File \| New \| Othe**r。 将弹出选择向导。 打开**Maven**类别，选择**Maven Module**，然后单击**Next**。

   1.在**New Maven Module**向导中，输入以下详细信息：

    选中**Create a simple project**选项。

    输入zipcloud-core作为**Module Name**。

    输入zipcloud-parent作为**Parent project**。

  2.单击**Next**按钮并转到下一步。 在这里，输入以下详细信息：

   输入edu.zipcloud作为**Group Id**。

   输入0.0.1-SNAPSHOT作为**Version**。

   选择**jar**作为**Packaging**。

   选择ZipCloud FactoryCore Business作为**Name**。



10. Now, build the two projects:

  If the structure is correct, the following Maven command could be successfully run: mvn clean install

10.现在，构建这两个项目：

   如果结构正确，可以成功运行以下Maven命令：`mvn clean install`



> This command can be launched in the terminal if Maven is installed on the development machine.
>
> 如果Maven安装在开发机上，此命令可以在终端中启动



In our study case, we will, for now, launch it using the m2eclipse modified Run As menu: Right click on the zipcloud-parent project and click on Run As \| Maven Clean.

在我们的研究案例中，我们将使用m2eclipse修改的运行方式菜单启动它：右键单击zipcloud-parent，然后单击**Run As\|  Maven Clean**。



> In the Maven console, you should now see this beautiful line at the bottom:
>
> 在Maven控制台中，您现在应该看到这个美丽的线在底部：
>
> \[INFO\] BUILD SUCCESS

Now, repeat the operation for the install build phase. You should now see thefollowing output in the console:

现在，重复安装构建阶段的操作。 您现在应该在控制台中看到以下输出：

`[INFO] ZipCloud Parent .......................SUCCESS [ 0.313 s]`

`[INFO] ZipCloud Core .........................SUCCESS [ 1.100 s]`

`[INFO] ----------------------------------------------------------`

`[INFO] BUILD SUCCESS`

`[INFO] ----------------------------------------------------------`



Ok, now you should be able to build cloudstreetmarket-parent as well.

For this, right-click on the **cloudstreetmarket -parent** project and click on **Run As \| Maven Clean**. The Maven console should print the following after this step:

`[INFO] BUILD SUCCESS`

OK，现在你应该能够建立cloudstreetmarket-parent。

右键单击**cloudstreetmarket -parent**项目并单击**Run As \|  Maven Clean**。 在此步骤后，Maven控制台应打印以下内容：

`INFO BUILD SUCCESS`

Again, right-click on the **cloudstreetmarket -parent** project and click on **Run As \| Maven Install**. The Maven console should now print the following:

再次右键单击**cloudstreetmarket -parent**项目，然后单击**Run As \|  Maven Install**。  Maven控制台现在应该打印以下内容：

`[INFO] CloudStreetMarket Parent ..............SUCCESS [ 0.313 s]`

`[INFO] CloudStreetMarket Webapp ..............SUCCESS [ 6.129 s]`

`[INFO] CloudStreetMarket Core ................SUCCESS [ 0.922 s]`

`[INFO] CloudStreetMarket API .................SUCCESS [ 7.163 s]`

`[INFO] ----------------------------------------------------------`

`[INFO] BUILD SUCCESS`

`[INFO] ----------------------------------------------------------`



Scrolling up a bit should display the following trace:

向上滚动应显示以下跟踪：

`-------------------------------------------------------`

`T E S T S`

`-------------------------------------------------------`

`There are no tests to run.`

`Results :`

`Tests run: 0, Failures: 0, Errors: 0, Skipped: 0`



> Maven here, with the help of the maven-surefire-plugin, which we manually added, parses all the classes encountered in the src/test/java directories. Again, this path can be customized.
>
> In the detected test classes, Maven will also run the methods annotated with the JUnit @Test annotation. A JUnit dependency is required in the project.
>
> Maven在这里，在maven-surefire插件的帮助下，我们手动添加，解析src / test / java目录中遇到的所有类。 同样，这个路径可以定制。
>
> 在检测到的测试类中，Maven还将运行使用JUnit @Test注释的方法。 项目中需要JUnit依赖项。



**How it works...**

In this section, we are going through quite a few concepts about Maven so that you can better understand its standards.

**怎么运行的...**

在本节中，我们将讨论Maven的一些概念，以便您能够更好地理解它的标准。





**New Maven project, new Maven module**

The project creation screens we just went through also come from the m2eclipse plugin.

These screens are used to initialize a Java project with a preconfigured pom.xml file and a standard directory structure.

The m2eclipse plugin also provides a set of shortcuts to run Maven build phases and some handy tabs \(already seen\) to manage project dependencies and visualize the pom.xml configuration.

**新的Maven项目，新的Maven模块**

我们刚刚通过的项目`creation screens`也来自m2eclipse插件。

这些`screens`用于使用预配置的`pom.xml`文件和初始化`Java`项目标准目录结构。

m2eclipse插件还提供了一组快捷方式来运行Maven构建阶段和一些方便的选项卡（已经看到）来管理项目依赖项并可视化`pom.xml`组态。



**The standard project hierarchy**

Navigating through the created projects, you should be able to notice a recurring hierarchy made of the following directories: src/main/java, src/main/resource, src/test/java, and src/test/resource. This structure is the default structure that Maven drives us through. This model has become a standard nowadays. But, we can still override it \(in the pom.xml files\) and create our own hierarchy.

**标准项目层次结构**

浏览已创建的项目，您应该能够注意到一个循环层次结构由以下目录组成：src / main / java，src / main / resource，src / test /java和src / test / resource。 这个结构是我们通过Maven驱动的默认结构。 这种模式已经成为当今的标准。 但是，我们仍然可以覆盖它（在`pom.xml`文件）并创建我们自己的层次结构。



If you remember the **maven-compiler-plugin** definition added in the pom.xml files of the parent projects, there were the following four lines of code that we used:

如果你还记得在父项目的`pom.xml`文件中添加的maven-compiler-plugin定义，我们使用了以下四行代码：

`<verbose>true</verbose>`

`<fork>true</fork>`

`<executable>${JAVA_HOME}/bin/javac</executable>`

`<compilerVersion>1.8</compilerVersion>`

These lines allow Maven to use an external JDK for the compiler. It is better to have control over which compiler Maven uses, especially when managing different environments.Also, there were the following two lines that might look like an over configuration:

这些行允许Maven使用外部`JDK`作为编译器。 最好有控制Maven使用的编译器，尤其是在管理不同环境时。此外，以下两行可能看起来像一个过度配置：

`<source>1.8</source>`

`<target>1.8</target>`

From a strict Maven point of view, these lines are optional when an external JDK is defined with a specified compilerVersion. Initially, with these two lines, we can control which Java version we want the default code to be compiled in. When maintaining older systems, the existing code might still compile in a previous version of Java.

Actually, m2eclipse specifically expects these two lines in order to add JRE System Library \[JavaSE-1.8\] to the build path of the jar and war modules. Now, with these lines, Eclipse compiles these projects in the same way Maven does: in Java SE 8.

从严格的Maven角度来看，当定义外部`JDK`时，这些行是可选的与指定的compilerVersion。 最初，用这两行，我们可以控制哪个Java版本，我们希望编译默认代码。在维护旧系统时现有代码可能仍然在以前的Java版本中编译。

实际上，m2eclipse专门期望这两行，以便添加JRE System库JavaSE-1.8到jar和war模块的构建路径。 现在，用这些线，Eclipse以与Maven相同的方式编译这些项目：在Java SE 8中。



> If this dependency still shows up as a different version of Java,you may need to right-click on the module and then navigate to** Maven \| Update Project**.
>
> 如果此依赖项仍显示为不同版本的Java，您可能需要右键单击模块，然后导航到**Maven \|** **Update Project**。



**The project's structure in the IDE**

About the parent projects in the Eclipse project hierarchy; did you notice that the created submodules seem duplicated as standalone projects and as direct children of the parent? This is due to the fact that Eclipse doesn't handle hierarchies of projects yet in Luna. For this reason, the modules appear as separated projects. It might be slightly confusing because the source code appears to be located beside the parent projects. This is not the case in reality, it is only the way they are rendered, so we can have all the tools normally bound to the project level.

**IDE中的项目结构**

关于Eclipse项目层次结构中的父项目; 你注意到创建的子模块似乎重复为独立的项目和父的直接子？ 这是因为Eclipse在Luna中没有处理项目的层次结构。 因此，模块显示为单独的项目。 它可能有点混乱，因为源代码似乎位于父项目旁边。 在现实中不是这样，它只是它们被渲染的方式，所以我们可以拥有通常绑定到项目级的所有工具。



> At this time, JetBRAINS IntelliJ IDEA already supports visualhierarchies of the projects.
>
> 此时，JetBRAINS IntelliJ IDEA已经支持项目的视觉层次。



Finally, if you open a parent project's` pom.xml` file, you should see the `<modules>` node populated with the created submodules. This has been done automatically as well by m2eclipse. We recommend that you keep an eye on this feature because m2eclipse doesn't always update these` <modules>` nodes depending on which way you alter the project hierarchy.

最后，如果打开父项目的`pom.xml`文件，您应该看到用创建的子模块填充的`<modules>`节点。 这已经由m2eclipse自动完成。 我们建议您关注此功能，因为m2eclipse不会总是更新这些`<modules>`节点，具体取决于您更改项目层次结构的方式。



**Maven's build life cycles**

A build life cycle in Maven is a specific sequence \(and a group\) of predefined operations called phases. There are three existing life cycles in Maven: default, clean, and site.

Let's have a look at all the phases that include the default and clean life cycles \(probably the life cycles the most commonly used by developers\).

**Maven的构建生命周期**

Maven中的构建生命周期是称为阶段的预定义操作的特定序列（和一组）。  Maven中有三个现有的生命周期：default，clean和site。

让我们来看看所有的阶段，包括default和clean的生命周期（可能是开发人员最常使用的生命周期）。



**The clean life cycle**

The Maven clean phase plays a central role. It resets a project build from Maven's perspective. It is usually about deleting the target directory that is created by Maven during the build process. Here are some details about the phases included in the clean life cycle.These details come from the Maven documentation:

**clean 的生命周期**

Maven的clean 阶段起着核心作用。 它从Maven的角度重置项目构建。 它通常是删除关于在构建过程中由Maven创建的目标目录。 这里有一些关于clean 生命周期中包括的阶段的细节。这些细节来自Maven文档：

| Phases | Description |
| :--- | :--- |
| pre-clean | This executes processes that are needed prior to the actual project cleaning |
| clean | This removes all files generated by the previous buildpost-clean |   |
| post-clean | This executes processes that are needed to finalize the project cleaning |



**The default life cycle**

In the default life cycle, you can find the most interesting build phases that deal with source generation, compilation, resource handling, tests, integration tests, and artefact deployment.Here are some details about the phases included in the default life cycle:

**默认生命周期**

在默认生命周期中，您可以找到处理源代码生成，编译，资源处理，测试，集成测试和工件部署的最有趣的构建阶段。有关缺省生命周期中包括的阶段的一些详细信息：

![](/assets/9.png)

![](/assets/10.png)



**Built-in life cycle bindings**

Now that we have seen the purpose of each phase in the presented two life cycles, we must say that, for the default life cycle, depending upon which module packaging type we are choosing, only some of these phases are potentially activated for goal execution.

Let's see the phases that we skipped in the default life cycle for different packaging types:

**内置生命周期绑定**

现在我们已经看到了在所给出的两个生命周期中的每个阶段的目的，我们必须说，对于默认生命周期，取决于我们所选择的模块封装类型，只有这些阶段中的一些可能被激活用于目标执行。

让我们看看我们在不同包装类型的默认生命周期中跳过的阶段：

![](/assets/11.png)

> In Chapter 9, Testing and Troubleshooting, we will practically bindexternal plugins goals to identified build phases.
>
> 在第9章“测试和故障排除”中，我们将几乎将外部插件目标绑定到已识别的构建阶段。

In summary, calling: mvn clean install on a jar packaged-module will result in executing the following phases: clean, process-resources, compile, process-test-resources, test-compile,test, package, and install.

总之，调用：`mvn clean`安装在jar包模块将导致执行以下阶段：clean，process-resources，compile，process-test-resources，test-compile，test，package和install。



**About Maven commands**

When Maven is told to execute one or more phases targeting a specific project's pom.xml file,it will execute the requested phase\(s\) for each of its modules.

Then, for every single requested phase, Maven will do the following:

* Identify which life cycle the phase belongs to
* Look for the packaging of the current module and identify the right life cycle binding

* Execute all the phases in the hierarchy of the identified life cycle bindings, which are located before the requested phase in the hierarchy

**关于Maven命令**

当Maven被告知要执行一个或多个目标为特定项目的pom.xml文件的阶段时，它将为其每个模块执行请求的阶段。

然后，对于每个单独的请求阶段，Maven将执行以下操作：

* 确定阶段所属的生命周期
* 查找当前模块的packaging 并识别正确的生命周期绑定

* 执行标识的生命周期绑定的层次结构中位于层次结构中请求的阶段之前的所有阶段



> By the term execute all the phases, we mean execute all the underlying detected and attached plugin goals \(native plugins or not\).
>
> 执行所有阶段，我们的意思是执行所有底层检测和附加的插件目标（不是本地插件）。



In summary, calling mvn clean install on a jar packaged module will execute the following phases: clean, process-resources, compile, process-test-resources,test-compile, test, package, and install.

总而言之，在jar包模块上调用`mvn clean install`将执行以下阶段：clean，process-resources，compile，process-test-resources，test-compile，test，package和install。



**There's more...**

You may wonder why we have created these projects and modules in regard to our application.

**还有更多...**

你可能想知道为什么我们已经创建了这些项目和模块在我们的应用程序。



**How did we choose the jar module's name?**

About the Maven structure, the best names for nondeployable modules often emphasize a functional purpose, a specific concept created by the business, or are driven by the product \(cloudstreetmarket-chat, cloudstreetmarket-reporting, cloudstreetmarket-user-management,and so on.\). This strategy makes the dependency management easier because we can infer whether a new module requires another module or not. Thinking about controllers, services,and DAO layers at a macro scale doesn't really make sense at this stage, and it could lead to design interference or circular dependencies. These technical subcomponents \(service, DAO,and so on\) will be present or not, as needed, in each functional module as Java packages but not as JAR-packaged dependencies.

**我们如何选择jar模块的名称？**

关于Maven结构，不可部署模块的最佳名称通常强调功能目的，由业务创建的特定概念或由产品驱动的（cloudstreetmarket-chat, cloudstreetmarket-reporting, cloudstreetmarket-user-management等等。  ）。 这个策略使得依赖关系管理更容易，因为我们可以推断新模块是否需要另一个模块。 在宏观层面思考控制器，service层和DAO层在这个阶段没有真正意义，它可能导致设计干扰或循环依赖。 这些技术子组件（service，DAO等）将根据需要在每个功能模块中作为Java包而不是作为JAR打包依赖存在。



**How did we choose the names for deployable modules?**

Choosing a name for a deployable module \(war\) is a bit different different from choosing a name for a JAR-packaged module. The deployable archive must be thought of as scalable and potentially load balanced. It is fair to assume that the requests that will target the application to retrieve HTML contents can be distinguished from the ones that will return REST contents. 

With this assumption, in our case it has been our wish to split the war into two. Doing so may raise the question of how the web sessions are maintained between the two webapps. We will answer this point later on.

**我们如何选择可部署模块的名称？**

选择可部署模块的名称（war）与为JAR打包的模块选择名称有点不同。 可部署的存档必须被认为是可扩展的，并且可能负载平衡。 可以公平地假定将针对应用程序检索HTML内容的请求与将返回REST内容的请求区分开。

有了这个假设，在我们的情况下，我们一直希望把war分为两个。 这样做可能会提出如何在两个Web应用程序之间维护Web会话的问题。 我们稍后会回答这一点。



**Why did we create core modules?**

We created the core modules, firstly, because it is certain that, in the cloudstreetmarket application and also in the company-shared project, we will have POJOs, exceptions,constants, enums, and some services that will be used horizontally by almost all the modules or applications. If a concept is specific to a created functional module, it must not be part of core modules.

Then, it is probably better to start big grained to refine later rather than thinking about modules that may be implemented differently or even not implemented at all. In our case, we are a start-up, and it is not silly to say that the 5 to 10 features we are going to implement can constitute the core business of this application.

**为什么要创建核心模块？**

我们创建了核心模块，首先，因为在cloudstreetmarket应用程序和公司共享项目中，我们将有POJOs，exceptions, constants, enums和一些services ，将被几乎所有的模块或应用程平衡使用。 如果一个概念特定于所创建的功能模块，它不能是核心模块的一部分。

然后，可能更好的开始大粒度以后再细化，而不是考虑可能实现不同或甚至没有实现的模块。 在我们的案例中，我们是一个初创公司，不是说，我们将要实现的5到10功能可以构成这个应用程序的核心业务。



**See also...**

*   We also recommend that you install Code Style Formatters. Triggered from the Save Event, we have, with these formatters, the ability to restyle our code automatically with a uniform predefinition. Having such formatters in a team is much appreciated since it guarantees the same rendering while comparing two files with a versioning tool.

**也可以看看...**

*    我们还建议您安装Code Style Formatters。 从保存事件触发，我们有了这些格式化，能够用统一的预定义自动重新编译我们的代码。 在团队中有这样的格式化程序是非常感谢，因为它保证相同的呈现，同时比较两个文件与版本控制工具。



