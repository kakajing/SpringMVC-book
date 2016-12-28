```
This recipe entails configuration technics to develop efficiently on Eclipse with Java, Maven, and Tomcat.
```

这个配方需要配置技术，在Eclipse上使用Java，Maven和Tomcat高效开发。

**Getting ready                          
**

Once the different products are installed, there are a couple of steps that we need to follow, mainly to make Eclipse work properly with Java SE 8, Maven 3, and Tomcat 8. In this recipe, we will also look at how to customize the Eclipse configuration file \(Eclipse.ini\) in order to make the most of the platform that runs Java and to make sure that it will cope with any significant growth of the application.

**准备                          
**

一旦安装了不同的产品，我们需要遵循几个步骤，主要是让Eclipse使用Java SE 8，Maven 3和Tomcat 8正常工作。在这个配方中，我们还将讨论如何自定义 Eclipse配置文件（Eclipse.ini），以便充分利用运行Java的平台，并确保它将处理应用程序的任何显着增长。

**How to do it...       怎么做...**

Let's take a look at the following steps to configure Eclipse on your desktop:

1. You can start by creating a shortcut on your desktop to point to the Eclipse executable:

   On Windows, the executable file is Eclipse.exe and is located at the eclipse directory root

   On Linux/Mac, the file is named Eclipse and is also is located at the eclipse directory root

让我们来看看在桌面上配置Eclipse的以下步:

```
1.您可以从桌面上创建一个指向Eclipse的快捷方式：
```

1. [ ] 在Windows上，可执行文件是Eclipse.exe，位于eclipse目录根

2. [ ] 在Linux / Mac上，文件名为Eclipse，也位于eclipse目录根

   2.Then, we need to customize the eclipse.ini file:  
   In the Eclipse directory, where you have previously extracted the Eclipse archive, you can find the eclipse.ini file. It is a text file that contains a few command-line options in order to control the Eclipse startup.

3. [ ] The Eclipse community recommends to specify the path to our JVM here.  
   Hence, depending on your system, add the following two lines at the top of the file:

For Windows, add the following:  
  -vm  
  C:\java\jdk1.8.0\_25\jre\bin\server\jvm.dll  
  For Linux/Mac, add this:  
 -vm  
 /usr/java/jdk1.8.0\_25/jre/lib/{your.architecture}/server/libjvm.so  
The following is an optional setting that you can consider:

* [ ]  If your development machine has at least 2 GB of RAM, you can enter the
  following options to make Eclipse run faster than the default settings. This section is optional because Eclipse's default settings are already optimized to suit most users' environment:

-Xmx512m

-Xverify:none

-Dosgi.requiredJavaVersion=1.6

-XX:MaxGCPauseMillis=10

-XX:MaxHeapFreeRatio=70

-XX:+UseConcMarkSweepGC

-XX:+CMSIncrementalMode

-XX:+CMSIncrementalPacing

1. 然后，我们需要定制eclipse.ini文件：  
   在Eclipse目录中，您先前已提取Eclipse归档，您可以找到eclipse.ini文件。 它是一个文本文件，包含几个命令行选项，以控制Eclipse启动。

2.Eclipse社区建议在此处指定我们的JVM的路径。因此，根据您的系统，在文件的顶部添加以下两行：

对于Windows，请添加以下内容：  
-vm  
C：\ java \ jdk1.8.0\_25 \ jre \ bin \ server \ jvm.dll

对于Linux / Mac，添加：  
-vm  
/usr/java/jdk1.8.0\_25/jre/lib/{your.architecture}/server/libjvm.so  
以下是您可以考虑的可选设置：

* [ ] 如果您的开发机器至少有2 GB的RAM，可以输入
  以下选项使Eclipse运行速度比默认设置快。 此部分是可选的，因为Eclipse的默认设置已针对大多数用户的环境进行了优化：

-Xmx512m  
-Xverify:none  
-Dosgi.requiredJavaVersion=1.6  
-XX:MaxGCPauseMillis=10  
-XX:MaxHeapFreeRatio=70  
-XX:+UseConcMarkSweepGC  
-XX:+CMSIncrementalMode  
-XX:+CMSIncrementalPacing

If your machine has less than 2 GB of RAM, you can still enter this set of options without overriding the default –Xms and –Xmx arguments.

如果您的机器具有少于2 GB的RAM，您仍然可以输入此组选项  
而不覆盖缺省的-Xms和-Xmx参数。

> All the options under -vmargs are arguments that will be passed to the JVM at startup. It is important not to mess up the Eclipse options \(the top part of the file\) with the VM arguments \(the bottom part\).
>
> -vmargs下的所有选项都是将在启动时传递到JVM的参数。 重要的是不要使用VM参数（底部）弄乱Eclipse选项（文件的顶部）。

3.After this we will go through the following steps to start Eclipse and set the workspace:

Launch the executable described in the Step 2.

1. [ ] For our project, specify the path: &lt;home-directory&gt;/workspace This path is different for each Operating System:

2. [ ] C:\Users{system.username}\workspace: This is the path on Windows

3. [ ] /home/usr/{system.username}/workspace: This is on Linux

4. [ ] /Users/{system.username}/workspace: This is on Mac OS

5. [ ] Click on OK and let the Eclipse program start

3.之后，我们将通过以下步骤启动Eclipse并设置工作空间：

启动步骤2中描述的可执行文件。

* [ ] 对于我们的项目，指定路径：&lt;home-directory&gt; / workspace此路径对于每个操作系统是不同的：

* [ ] C：\ Users \ system.username \ workspace：这是Windows上的路径

* [ ] /home/usr/system.username/workspace：这是在Linux上

* [ ] /Users/system.username/workspace：这是在Mac OS

* [ ] 单击确定，让Eclipse程序启动

> The workspace is the place from where you manage your Java projects. It can be specific to one application, but not necessarily.
>
> 工作区是从中管理Java项目的地方。 它可以是特定于一个应用程序，但不一定。

4.Then, we need to check the JRE definitions:  
  Here, a couple of settings need to be verified in Eclipse:

1. Open the Preferences menu under Window \(on Mac OS X the Preference menu is under the Eclipse menu\).
2. In the navigation panel on the left-hand side, open the Java hierarchy and click on **Installed JREs** under **Java.**
3. On the central screen, remove any existing JREs that you may already have.

4. Click on the** Add…** button to add a standard JVM.

5. Enter C:\java\jdk1.8.0\_25 \(or /usr/java/...\) as **JRE home**.

6. And enter jdk1.8.0\_25 as **JRE name.**

7. Enter C:\java\jdk1.8.0\_25 \(or /usr/java/...\) as JR

> We tell Eclipse to use the Java Runtime Environment of JDK 8.

1. 然后，我们需要检查JRE定义：

   这里，需要在Eclipse中验证几个设置：

   1.打开“窗口”下的“首选项”菜单（在Mac OS X上，“首选项”菜单位于Eclipse菜单下）。

   2.在左侧的导航面板中，打开Java层次结构，然后单击Java下的已安装JRE。

   3.在中央屏幕上，删除您可能已经拥有的任何现有JRE。

   4.单击添加...按钮添加标准JVM。

   5.输入C：\ java \ jdk1.8.0\_25（或/ usr / java / ...）作为JRE主目录。

   6.然后输入jdk1.8.0\_25作为JRE名称。

> 我们告诉Eclipse使用JDK 8的Java运行时环境。

![](/assets/1.png)

5.Now, we will check the compiler compliance level:

1. In the navigation panel, click on Compiler under Java.

2. Check that the Compiler compliance level is set to 1.8 in the drop-down list.

5.现在，我们将检查编译器合规级别：

1.在导航面板中，单击Java下的编译器。

2.检查下拉列表中的编译器合规性级别是否设置为1.8。

6.After this, we need to check the Maven configuration:

1. Still in the navigation panel of the Preferences menu, open the Maven hierarchy and navigate to Maven \| Installations.

2. We will specify here which Maven installation we plan to use. For the purpose of this book, the embedded Maven will be perfect.

3. Back in the navigation panel, go to Maven \| User Settings.

4. Set the local repository to &lt;home-directory&gt;/.m2/repository.

> In this local repository, our local cached versions of the required artefacts will reside. It will prevent our environment from having to download them on each build.

1. For the User Settings field, create a settings.xml file in the .m2 directory: &lt;home-directory&gt;/.m2/settings.xml.

   5.对于“用户设置”字段，在.m2目录中创建settings.xml文件：&lt;home-directory&gt; /。m2 / settings.xml。

6.之后，我们需要检查Maven配置：

1.仍然在首选项菜单的导航面板中，打开Maven层次结构并导航到Maven \| 安装。

2.我们将在这里指定我们计划使用哪个Maven安装。 为了本书的目的，嵌入式Maven将是完美的。

3.返回导航面板，转到Maven \| 用户设置。

4.将本地存储库设置为&lt;home-directory&gt; /。m2 / repository。

> 在本地存储库中，我们的本地缓存版本的必需的工件将驻留。 它将防止我们的环境不得不在每次构建上下载它们。

5.For the User Settings field, create a settings.xml file in the .m2 directory: &lt;home-directory&gt;/.m2/settings.xml.

1. 对于“用户设置”字段，在.m2目录中创建settings.xml文件：&lt;home-directory&gt; /。m2 / settings.xml。

   6.Edit the settings.xml file and add the following block:

\(You can also copy/paste it from the chapter\_1/source\_code/.m2 directory\):

&lt;settings xmlns="[http://maven.apache.org/SETTINGS/1.1.0](http://maven.apache.org/SETTINGS/1.1.0)"

xmlns:xsi="[http://www.w3.org/2001/XMLSchema-instance](http://www.w3.org/2001/XMLSchema-instance)"

xsi:schemaLocation="[http://maven.apache.org/SETTINGS/1.1.0](http://maven.apache.org/SETTINGS/1.1.0)

[http://maven.apache.org/xsd/settings-1.1.0.xsd"&gt;](http://maven.apache.org/xsd/settings-1.1.0.xsd"&gt);

&lt;profiles&gt;

&lt;profile&gt;

&lt;id&gt;compiler&lt;/id&gt;

&lt;properties&gt;

&lt;JAVA\_HOME&gt;C:\java\jdk1.8.0\_25&lt;/JAVA\_HOME&gt;

&lt;/properties&gt;

&lt;/profile&gt;

&lt;/profiles&gt;

&lt;activeProfiles&gt;

&lt;activeProfile&gt;compiler&lt;/activeProfile&gt;

&lt;/activeProfiles&gt;

&lt;/settings&gt;

If you are not on a Windows machine, change JAVA\_HOME in this file to your JDK installation directory \(/usr/java/jdk1.8.0\_25\).

6.编辑settings.xml文件并添加以下块：

（您也可以从chapter\_1 / source\_code / .m2目录复制/粘贴它）：

&lt;settings xmlns =“http//maven.apache.org/SETTINGS/1.1.0”

xmlns：xsi =“http//www.w3.org/2001/XMLSchema-instance”

xsi：schemaLocation =“http//maven.apache.org/SETTINGS/1.1.0

http//maven.apache.org/xsd/settings-1.1.0.xsd“&gt;

&lt;profiles&gt;

&lt;profile&gt;

&lt;id&gt;编译器&lt;/ id&gt;

&lt;properties&gt;

&lt;JAVA\_HOME&gt; C：\ java \ jdk1.8.0\_25 &lt;/ JAVA\_HOME&gt;

&lt;/ properties&gt;

&lt;/ profile&gt;

&lt;/ profiles&gt;

&lt;activeProfiles&gt;

&lt;activeProfile&gt;编译器&lt;/ activeProfile&gt;

&lt;/ activeProfiles&gt;

&lt;/ settings&gt;

如果不在Windows机器上，请将此文件中的JAVA\_HOME更改为JDK安装目录（/usr/java/jdk1.8.0\_25）。

7.Go back to the navigation panel and click on Maven. Follow the configuration given in this screenshot:

7.回到导航面板并单击Maven。 按照此屏幕截图中给出的配置：

![](/assets/2.png)

8.Click on OK to save these configuration changes.

1. 单击确定保存这些配置更改。

7.Now we will install Tomcat 8 in the Eclipse IDE. For this, go through these steps:

1. Download a ZIP archive for the latest Core version of Tomcat8 from the Tomcat website:

   [http://tomcat.apache.org/download-80.cgi](http://tomcat.apache.org/download-80.cgi)

.  2. Extract the downloaded archive to the following directory:

* [ ] On Windows, extract the archive at C:\tomcat8

* [ ] On Linux, extract the archive at /home/usr/{system.username}/tomcat8

* [ ] On Mac OS X, extract the archive at /Users/{system.username}/tomcat8

> Depending on your system, you must be able to access the bin directory from the hierarchy: C:\tomcat8\bin, /home/usr/{system.username}/tomcat8/bin or /Users/{system.username}/tomcat8/bi

```
3.In Eclipse, select the **Preferences** menu under **Windows**, and in thenavigation panel on the left-hand side, open the **Server** hierarchy and then select **Runtime Environment**
4.On the central window, click on the** Add…** button.
5.In the next step \(the **New Server** environment window\), navigate to Apache \| Apache Tomcat v8.0
```

.   6. Also, check this option: **Create a New Local Server**

```
7.Click on the **Next** button.

8.Fill in the details in the window as shown in the following screenshot:
```

7.现在我们将在Eclipse IDE中安装Tomcat 8。 为此，请执行以下步骤：

1.从Tomcat网站下载最新Core版本的Tomcat8的ZIP存档：http//tomcat.apache.org/download-80.cgi  
   2.将下载的归档解压缩到以下目录：  在Windows上，解压缩归档文件在C：\ tomcat8

* [ ] 在Linux上，在/home/usr/system.username/tomcat8处解压缩归档

* [ ] 在Mac OS X上，将归档文件解压缩到/Users/system.username/tomcat8

> 根据您的系统，您必须能够从层次结构访问bin目录：C：\ tomcat8 \ bin，/home/usr/system.username/tomcat8/bin或/Users/system.username/tomcat8  / bin

3.在Eclipse中，选择**Windows**下的**Preferences**菜单，然后在左侧的thenavigation面板中打开**Server**层次结构，然后选择**Runtime Environments**

4.在中央窗口上，单击**Add...**按钮。

5.在下一步（the **New Server** environment window）中，导航到**Apache \|  Apache Tomcat v8.0**

6.此外，选中此选项：**Create a New Local Server**

7.单击**Next**按钮。

8.在窗口中填写详细信息，如以下屏幕截图所示：

![](/assets/3.png)

> If you are on Linux \(or Mac OS X\), replace C:\tomcat8 with your Tomcat installation directory.
>
> 如果您在Linux（或Mac OS X）上，请将Tomcat安装目录替换为C：\ tomcat8。

**How it works...    **

We are going to review in this section the different elements and concepts that this recipe took us through.

**The eclipse.ini file    **

As we've already seen, the eclipse.ini file controls the Eclipse startup. It is an extra component that makes the Eclipse platform very configurable. You can find the list of command-line arguments that can be used in their documentation at

[http://help.eclipse.org/luna/topic/org.eclipse.platform.doc.isv/](http://help.eclipse.org/luna/topic/org.eclipse.platform.doc.isv/)  
reference/misc/runtime-options.html

It is important to acknowledge the following warnings that this documentation mentions:

* All lines after -vmargs are passed as arguments to the JVM; all arguments and options for Eclipse must be specified before -vmargs \(just like when you use arguments on the command line\)
* Any use of -vmargs on the command line replaces all -vmargs settings in the .ini file unless --launcher.appendVmargs is specified either in the .ini file or on the command line

**怎么运行的...    **

我们将在本节中回顾这个配置带给我们的不同元素和概念。

**eclipse.ini文件    **

正如我们已经看到的，eclipse.ini文件控制Eclipse启动。 它是一个额外的组件，使Eclipse平台非常可配置。 您可以在其文档中找到可用于其中的命令行参数列表  
http//help.eclipse.org/luna/topic/org.eclipse.platform.doc.isv/reference / misc / runtime-options.html  
重要的是要承认本文档提到的以下警告：

* -vmargs之后的所有行作为参数传递给JVM;  Eclipse的所有参数和选项必须在-vmargs之前指定（就像在命令行中使用参数一样）

> This explains why we have inserted the –vm option at the top of the file.
>
> 这解释了为什么我们在文件的顶部插入了-vm选项。

* 在命令行中使用-vmargs将替换.ini文件中的所有-vmargs设置，除非--launcher.appendVmargs在.ini文件中或在命令行中指定

**Setting the –vm option        
**

Setting the -vm option allows us to be sure of the JVM implementation on which Eclipse runs as a program. You might have noticed that we've targeted the JVM as a library \(\*.dll /\*.so\). It has better performance on startup and also identifies the program process as the Eclipse executable and not just as the Java executable.

If you wonder which JVM Eclipse uses when a –vm option is not set, be aware that Eclipse DOES NOT consult the JAVA\_HOME environment variable. \(Eclipse wiki\).

Instead, Eclipse executes the Java command that parses your path environment variable.

**设置-vm选项        
**

设置-vm选项允许我们确定Eclipse作为程序运行的JVM实现。 您可能已经注意到，我们已将JVM作为库（\* .dll /\*.so）。 它在启动时具有更好的性能，还将程序进程标识为Eclipse可执行文件，而不仅仅是Java可执行文件。

如果您想知道当未设置-vm选项时，Eclipse使用哪个JVM，请注意，Eclipse不会查询JAVA\_HOME环境变量。  （Eclipse wiki）。

相反，Eclipse会执行解析路径环境变量的Java命令。

**Customizing JVM arguments        
**

The suggested JVM argument list comes from Piotr Gabryanczyk's work on the Java memory management model. Initially, for JetBRAINS IntelliJ settings, this configuration is also useful for an Eclipse environment. It helps in the following tasks:

* Preventing the garbage collector from pausing the application for more than 10 ms \(-XX:MaxGCPauseMillis=10\)

* Lowering the level from which the garbage collector starts to 30% of the occupied memory \(-XX:MaxHeapFreeRatio=70\)

* Imposing the garbage collector to run as a parallel thread, lowering its interference with the application \(-XX:+UseConcMarkSweepGC\)

* Choosing the incremental pacing mode for the garbage collector, which generates breaks in the GC job so that the application can definitely stop freezing \(–XX:+CMSIncrementalPacing\)

**自定义JVM参数        
**

建议的JVM参数列表来自Piotr Gabryanczyk关于Java内存管理模型的工作。 最初，对于JetBRAINS IntelliJ设置，此配置对于Eclipse环境也很有用。 它有助于以下任务：

* 防止垃圾回收器暂停应用程序超过10毫秒（-XX：MaxGCPauseMillis = 10）

* 将垃圾收集器启动的级别降低到占用内存的30％（-XX：MaxHeapFreeRatio = 70）

* 强制垃圾收集器作为并行线程运行，降低其对应用程序的干扰（-XX：UseConcMarkSweepGC）

* 为垃圾收集器选择增量节奏模式，在GC作业中生成中断，以便应用程序可以明确地停止冻结（-XX：CMSIncrementalPacing）

整个程

The instantiated objects throughout the program's life cycle are stored in the Heap memory.

The suggested parameters define a JVM startup Heap space of 128 mb \(-Xms\) and an  
 overall 512 mb maximum heap space \(–Xmx\). The heap is divided in two subspaces, which are as follows:

* **Young generation**: New objects are stored in this area. For the leading Hotspot or OpenJDK JVMs, the young memory space is divided in two:

* [ ]   Eden: New objects are stored in this subdivision area. Objects with short lives will be deallocated from here.

* [ ]  Survivor: This is a buffer between the young and old generation. The survivor space is smaller than the Eden and it is also divided in two \(the FROM and TO areas\). You can adjust the ratio between Eden and Survivor objects with -XX:SurvivorRatio \(here, -XX: SurvivorRatio=10 means YOUNG = 12, EDEN = 10, FROM = 1 and TO =1\).

生命周期中的实例化对象存储在堆内存中。

建议的参数定义JVM启动Heap空间128 mb（-Xms）和一个  
总共512 MB的最大堆空间（-Xmx）。 堆被分为两个子空间，如下所示：

* **Young generation**：新对象存储在这个区域。 对于领先的Hotspot或OpenJDK JVM，年轻的内存空间分为两部分：

* [ ] Eden：新对象存储在此细分区域中。 短暂的对象将从这里被释放。

* [ ] Survivor：这是新对象和旧对象之间的缓冲区。 幸存者空间小于Eden，它也分为两个（FROM和TO区域）。 您可以使用-XX：SurvivorRatio（这里，-XX：SurvivorRatio = 10表示YOUNG = 12，EDEN = 10，FROM = 1和TO = 1）调整Eden和Survivor对象之间的比率。

> The minimum size of the young area can be adjusted with -XX:NewSize.
>
> The maximum size can be adjusted with -XX:MaxNewSize.
>
> Young区域的最小尺寸可以用调整 -XX：NewSize。
>
> 最大尺寸可以用调整 -XX：MaxNewSize。

* Old generation: When objects in Eden or Survivor spaces are still referenced after enough garbage collections, they are moved here. It is possible to set the Young area size as a ratio of the Old area size with -XX:NewRatio. \(That is, -XX:NewRatio=2 means HEAP = 3, YOUNG = 1 and OLD =2\).

* Old generation：当Eden或Survivor空间中的对象在足够的垃圾回收后仍被引用时，它们会移动到这里。 可以将Young区域大小设置为旧区域大小与-XX：NewRatio的比率。 （即，-XX：NewRatio = 2意味着HEAP = 3，YOUNG = 1和OLD = 2）。

> The maximum size for the new generation space -XX:MaxNewSize  
>  must always remain smaller than half the heap space \(-Xmx/2\) because the garbage collector may move all the Young space to the  
>  Old space.
>
> 新生成空间的最大大小 -XX：MaxNewSize  
> 必须始终保持小于堆空间（-Xmx / 2）的一半，因为垃圾收集器可能将所有Young空间移动到  
> 老空间。

With Hotspot or OpenJDK, the permanent generation space was used to store information related to the classes' definition \(structure, fields, methods, and so on.\). You may have already encountered a PermGen space OutOfMemoryError exception when the loaded structure becomes too big. In this situation, the solution is to increase the -XX:MaxPermSize argument. It is no longer necessary with JDK8.

For this purpose, the **Permanent Generation \(PermGen\)** space has been replaced by a metadata space that is not part of the heap but of the native memory. The default maximum size of this space is unlimited. However, we can still restrict it with -XX:MetaspaceSize or -XX:MaxMetaspaceSize.

使用Hotspot或OpenJDK，永久生成空间用于存储与类的定义（结构，字段，方法等）相关的信息。 当加载的结构变得太大时，您可能已经遇到PermGen空间OutOfMemoryError异常。 在这种情况下，解决方案是增加-XX：MaxPermSize参数。 它不再需要与JDK8。

为此，**永久代（PermGen）**空间已被元数据空间替换，该元数据空间不是堆的一部分，而是本机内存的一部分。 此空间的默认最大大小不受限制。 但是，我们仍然可以使用-XX：MetaspaceSize或-XX：MaxMetaspaceSize限制它。

**Changing the JDK compliance level**

Downgrading a compliance level allows us to run a lower version of a Java compiler than the one the JDK is natively identified to. It impacts the Eclipse builds, errors, and warnings and also the JavaDocs. It is obviously not possible to set a higher compilation version than the native version of a compiler.

**更改JDK合规性级别**

降低合规性级别允许我们运行比本地标识的JDK更低版本的Java编译器。 它影响Eclipse构建，错误和警告以及JavaDocs。 显然不可能设置比编译器的本机版本更高的编译版本。

**Configuring Maven**

Inside Eclipse, most of the Maven configuration comes from the m2eclipse plugin \(also called Maven integration for Eclipse\). This plugin is included, by default, in Eclipse Luna. It is then not necessary to download it manually. After the Maven configuration that we went through, m2eclipse is also very helpful to trigger Maven operations from the IDE context and to provide assistance to create Java Maven projects. You will learn more about m2eclipse in the next section.

**配置Maven**

在Eclipse中，大多数Maven配置来自m2eclipse插件（也称为Eclipse的Maven集成）。 默认情况下，此插件包含在Eclipse Luna中。 然后无需手动下载。 在我们经历的Maven配置之后，m2eclipse也非常有助于从IDE上下文触发Maven操作，并提供帮助来创建Java Maven项目。 您将在下一节中了解有关m2eclipse的更多信息。

We then installed a basic settings.xml file. This file is used to configure Maven without being bound directly to any projects. The most common uses of settings.xml are probably profile definition and credential storage to access the repository manager\(s\).

With Maven profiles, you have the possibility to run a build for a specific environment and to match a specific configuration \(variable values, set of dependencies, and so on.\). Maven profiles can be cumulated with each other. They can be activated through a command line, declaratively in the Maven settings or from the environment configuration such as files being present or missing on the filesystem, the used JDK, and so on.

然后我们安装了一个基本的settings.xml文件。 此文件用于配置Maven，而不直接绑定到任何项目。  settings.xml的最常见用法可能是配置文件定义和凭据存储，以访问存储库管理器。

使用Maven概要文件，您可以运行针对特定环境的构建，并匹配特定的配置（变量值，依赖关系集等）。  Maven配置文件可以相互累积。 它们可以通过命令行，在Maven设置中声明性地或从环境配置（例如文件系统中存在或缺少的文件，所使用的JDK等）来激活。

> In our settings.xml file, we have defined a compiler profile with its own JAVA\_HOME property. The compiler profile is activated by default to be declaratively defined in the &lt;activeProfiles&gt; section.  
>  Maven will consult the settings.xml file before looking up the system variables.
>
> 在我们的settings.xml文件中，我们定义了一个具有自己的JAVA\_HOME属性的编译器配置文件。 缺省情况下，激活编译器配置文件以在&lt;activeProfiles&gt;部分中声明性地定义。  
> Maven将在查找系统变量之前查询settings.xml文件。

**A repository manager**

A repository manager is a third-party application that manages all the required binaries and dependencies that a developed application may need. Acting as a buffering proxy between development environments and public repositories, a repository manager provides control of critical parameters such as build time, availability of dependencies, visibility and access restriction, and so on.

Famous solutions include Apache Archiva, Artifactory, Sonatype Nexus. In the context of our application, we won't make use of a repository manager.

**存储库管理器**

存储库管理器是管理开发的应用程序可能需要的所有所需的二进制文件和依赖关系的第三方应用程序。 作为开发环境和公共存储库之间的缓冲代理，存储库管理器提供控制的关键参数，如构建时间，依赖性的可用性，可见性和访问限制，等等。

著名的解决方案包括Apache Archiva，Artifactory，Sonatype Nexus。 在我们应用程序的情况下，我们不会使用存储库管理器。

**Tomcat 8 inside Eclipse**

Eclipse for JEE developers allows the integration of Tomcat with other application servers within the development environment. This is made possible through the provided **Web Tools Platform \(WTP\)** plugins that can manage web artefacts, their compilation, and their deployment into the web server.

In the servers tab \(made visible earlier\), double-clicking on the created Tomcat v8.0 server, opens a configuration window and enables the possibility of setting up parameters that are normally defined in the server.xml Tomcat file, which is located in the tomcat8\conf directory.

By default, WTP abstracts this configuration and doesn't impact the genuine server.xml file. This behavior can be changed by activating the **Publish module contexts to separate XML files** option in the **Server configuration** window.

**Tomcat 8在Eclipse中**

用于JEE开发人员的Eclipse允许将Tomcat与其他应用程序服务器集成在开发环境中。 这是通过提供的**Web Tools Platform（WTP）**插件，可以管理Web工件，其编译及其部署到Web服务器中。

在服务器选项卡（早期可见）中，双击创建的Tomcat v8.0服务器，打开配置窗口并启用设置参数的可能性通常在server.xml中定义Tomcat文件，它位于tomcat8 \ conf目录。

默认情况下，WTP抽象此配置，并不影响正版server.xml文件。可以通过在**Server configuration**窗口激活**Publish module contexts to separate XML files**来更改。

**There's more...**

* Find out more about the Eclipse installation at [http://wiki.eclipse.org/](http://wiki.eclipse.org/)Eclipse/Installation
* Learn more about the Eclipse.ini file at [http://wiki.eclipse.org/](http://wiki.eclipse.org/)Eclipse.ini

* Learn more about the m2eclipse plugin at [https://maven.apache.org/](https://maven.apache.org/)plugins/maven-eclipse-plugin/

* To understand how to use a repository manager, refer to [http://maven.apache](http://maven.apache).org/repository-management.html

* The Piotr Gabryanczyk article about the garbage collection optimization for IDEs can be found at [http://piotrga.wordpress.com/2006/12/12/intellij-andgarbage-collection](http://piotrga.wordpress.com/2006/12/12/intellij-andgarbage-collection)

* You can know more about memory optimization in general at [http://pubs](http://pubs).vmware.com/vfabric52/topic/com.vmware.vfabric.em4j.1.2/em4j/conf-heap-management.html and [https://blog.codecentric.de/](https://blog.codecentric.de/)en/2012/08/useful-jvm-flags-part-5-young-generation-garbagecollection

**还有更多...**

* 有关Eclipse安装的更多信息，请访问http//wiki.eclipse.org/Eclipse /安装
* 有关Eclipse.ini文件的详细信息，请访问http//wiki.eclipse.org/Eclipse.ini

* 有关m2eclipse插件的更多信息，请访问https//maven.apache.org/plugins / maven-eclipse-plugin /

* 要了解如何使用存储库管理器，请参阅http：//maven.apache.org / repository-management.html

* 关于IDE的垃圾收集优化的Piotr Gabryanczyk文章可以在http//piotrga.wordpress.com/2006/12/12/intellij-andgarbage-collection找到

* 您可以在http：// pubs.vmware.com/vfabric52/topic/com.vmware.vfabric.em4j.1.2/em4j/  
  conf-heap-management.html和https//blog.codecentric.de/en / 2012/08 / useful-jvm-flags-part-5-young-generation-garbagecollection上了解更多关于内存优化的息。



