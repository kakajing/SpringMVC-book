This recipe entails configuration technics to develop efficiently on Eclipse with Java, Maven, and Tomcat.

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

 让我们来看看在桌面上配置Eclipse的以下步骤：

    1. 您可以从桌面上创建一个指向Eclipse的快捷方式可执行文件：

* [ ]  在Windows上，可执行文件是Eclipse.exe，位于  eclipse目录根

* [ ]  在Linux / Mac上，文件名为Eclipse，也位于  eclipse目录根

   2. Then, we need to customize the eclipse.ini file:
       In the Eclipse directory, where you have previously extracted the Eclipse archive, you can find the eclipse.ini file. It is a text file that contains a few command-line options in order to control the Eclipse startup. 
* [ ] The Eclipse community recommends to specify the path to our JVM here.   Hence, depending on your system, add the following two lines at the top of the file:


  For Windows, add the following:
  -vm
  C:\java\jdk1.8.0\_25\jre\bin\server\jvm.dll
  For Linux/Mac, add this:
 -vm
 /usr/java/jdk1.8.0\_25/jre/lib/{your.architecture}/server/libjvm.so
The following is an optional setting that you can consider:
* [ ]  If your development machine has at least 2 GB of RAM, you can enter the   following options to make Eclipse run faster than the default settings. This section is optional because Eclipse's default settings are already optimized to suit most users' environment: 

-Xmx512m

-Xverify:none

-Dosgi.requiredJavaVersion=1.6

-XX:MaxGCPauseMillis=10

-XX:MaxHeapFreeRatio=70

-XX:+UseConcMarkSweepGC

-XX:+CMSIncrementalMode

-XX:+CMSIncrementalPacing


然后，我们需要定制eclipse.ini文件：
在Eclipse目录中，您先前已提取Eclipse归档，您可以找到eclipse.ini文件。 它是一个文本文件，包含几个命令行选项，以控制Eclipse启动。
* [ ]  Eclipse社区建议在此处指定我们的JVM的路径。  因此，根据您的系统，在文件的顶部添加以下两行：

对于Windows，请添加以下内容：
-vm
C：\ java \ jdk1.8.0\_25 \ jre \ bin \ server \ jvm.dll

对于Linux / Mac，添加：
-vm
/usr/java/jdk1.8.0\_25/jre/lib/{your.architecture}/server/libjvm.so
以下是您可以考虑的可选设置：
* [ ]  如果您的开发机器至少有2 GB的RAM，可以输入  以下选项使Eclipse运行速度比默认设置快。 此部分是可选的，因为Eclipse的默认设置已针对大多数用户的环境进行了优化：

-Xmx512m
-Xverify:none
-Dosgi.requiredJavaVersion=1.6
-XX:MaxGCPauseMillis=10
-XX:MaxHeapFreeRatio=70
-XX:+UseConcMarkSweepGC
-XX:+CMSIncrementalMode
-XX:+CMSIncrementalPacing 


If your machine has less than 2 GB of RAM, you can still enter this set of options without overriding the default –Xms and –Xmx arguments.

如果您的机器具有少于2 GB的RAM，您仍然可以输入此组选项而不覆盖缺省的-Xms和-Xmx参数。

> All the options under -vmargs are arguments that will be passed to the JVM at startup. It is important not to mess up the Eclipse options \(the top part of the file\) with the VM arguments \(the bottom part\).
>
> -vmargs下的所有选项都是将在启动时传递到JVM的参数。 重要的是不要使用VM参数（底部）弄乱Eclipse选项（文件的顶部）。





  




