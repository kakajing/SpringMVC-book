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

   1.您可以从桌面上创建一个指向Eclipse的快捷方式：

2. [ ] 在Windows上，可执行文件是Eclipse.exe，位于eclipse目录根

3. [ ] 在Linux / Mac上，文件名为Eclipse，也位于eclipse目录根

   2.Then, we need to customize the eclipse.ini file:  
   In the Eclipse directory, where you have previously extracted the Eclipse archive, you can find the eclipse.ini file. It is a text file that contains a few command-line options in order to control the Eclipse startup.

4. [ ] The Eclipse community recommends to specify the path to our JVM here.  
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

2.Eclipse社区建议在此处指定我们的JVM的路径。  
   因此，根据您的系统，在文件的顶部添加以下两行：

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

1. After this we will go through the following steps to start Eclipse and set the workspace:

   Launch the executable described in the Step 2.

2. [ ] For our project, specify the path: &lt;home-directory&gt;/workspace This path is different for each Operating System:

3. [ ]  C:\Users{system.username}\workspace: This is the path on Windows

4. [ ]  /home/usr/{system.username}/workspace: This is on Linux

5. [ ]  /Users/{system.username}/workspace: This is on Mac OS

6. [ ]  Click on OK and let the Eclipse program start

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

1. Then, we need to check the JRE definitions:

Here, a couple of settings need to be verified in Eclipse:

1. Open the Preferences menu under Window \(on Mac OS X the Preference menu is under the Eclipse menu\).

   1. In the navigation panel on the left-hand side, open the Java hierarchy and click on Installed JREs under Java.

   2. On the central screen, remove any existing JREs that you may already have.

   3. Click on the Add… button to add a standard JVM.

   4. Enter C:\java\jdk1.8.0\_25 \(or /usr/java/...\) as JRE home.

2. And enter jdk1.8.0\_25 as JRE name.

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

1. Now, we will check the compiler compliance level:

   1. In the navigation panel, click on Compiler under Java.

   2. Check that the Compiler compliance level is set to 1.8 in the drop-down list.

5.现在，我们将检查编译器合规级别：

1.在导航面板中，单击Java下的编译器。

2.检查下拉列表中的编译器合规性级别是否设置为1.8。

1. After this, we need to check the Maven configuration:

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

1. Edit the settings.xml file and add the following block:

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

1. Go back to the navigation panel and click on Maven. Follow the configuration given in this screenshot:

   7.回到导航面板并单击Maven。 按照此屏幕截图中给出的配置：

![](/assets/2.png)

1. Click on OK to save these configuration changes.

   8  
   .单击确定保存这些配置更改。

2. Now we will install Tomcat 8 in the Eclipse IDE. For this, go through these steps:

   1. Download a ZIP archive for the latest Core version of Tomcat8 from the Tomcat website:

      [http://tomcat.apache.org/download-80.cgi](http://tomcat.apache.org/download-80.cgi)

.  2. Extract the downloaded archive to the following directory:

On Windows, extract the archive at C:\tomcat8

On Linux, extract the archive at /home/usr/{system.username}/tomcat8

On Mac OS X, extract the archive at /Users/{system.username}/tomcat8

Depending on your system, you must be able to access the bin directory from the hierarchy: C:\tomcat8\bin, /home/usr/{system.username}/tomcat8/bin or /Users/{system.username}/tomcat8/bin

.   3. In Eclipse, select the **Preferences** menu under **Windows**, and in thenavigation panel on the left-hand side, open the **Server** hierarchy and then select **Runtime Environments**

.  4. On the central window, click on the** Add…** button.

1. In the next step \(the **New Server** environment window\), navigate to Apache \| Apache Tomcat v8.0

. 6. Also, check this option: **Create a New Local Server**

1. Click on the **Next** button.

2. Fill in the details in the window as shown in the following screenshot:

7.现在我们将在Eclipse IDE中安装Tomcat 8。 为此，请执行以下步骤：

1.从Tomcat网站下载最新Core版本的Tomcat8的ZIP存档：

http//tomcat.apache.org/download-80.cgi  
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





