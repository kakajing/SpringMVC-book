The following recipe is about downloading and installing the Eclipse IDE for JEE developers and downloading and installing JDK 8 Oracle Hotspot.

以下配方是关于为JEE开发人员下载和安装Eclipse IDE，以及下载和安装JDK 8 Oracle Hotspot。

## Getting ready    ** **

This first recipe could appear redundant or unnecessary in regard to your education or experience. However, having a uniform configuration all along this book will provide you with many benefits.

For instance, you will certainly avoid unidentified bugs \(integration or development\). You will also experience the same interfaces as seen in the presented screenshots. Also, because the third-party products are living, you will not have the surprise of encountering unexpected screens or windows.

这第一个配方可能看起来多余或不必要的教育或经验。 但是，本书中的统一配置将为您带来许多好处。

例如，你一定会避免不明的错误（集成或开发）。 您还将体验与所提供的屏幕截图中所示的相同的界面。 此外，因为第三方产品是活的，你不会有遇到意外的屏幕或窗口的惊喜。

## **How to do it...    **

The whole first chapter in general requires a step by step cooperation. From the next chapter, we will be using GIT and your active involvement will be lightened.

1. Download a distribution of the Eclipse IDE for Java EE developers:

2. [ ]  We will be using an Eclipse Luna distribution in this book. We recommend that you install this version in order to match our guidelines and screenshots completely. Download a Luna distribution for the OS and environment of your choice from [https://www.eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunasr1](https://www.eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunasr1).

The product to download is not a compiled installer but a zip archive.

* [ ] If you feel confident enough to use another version \(more recent\) of the Eclipse IDE for Java EE Developers, all of them can be found at [https://www.eclipse.org/downloads](https://www.eclipse.org/downloads).** **

整个第一章一般需要一步一步的合作。 从下一章，我们将使用GIT，您的积极参与将被减轻。

1. 为Java EE开发人员下载Eclipse IDE的发行版：

2. [ ] 我们将在本书中使用Eclipse Luna发行版。 我们建议您安装此版本，以便完全符合我们的指南和屏幕截图。 从https//www.eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunasr1下载您选择的操作系统和环境的Luna发行版。

要下载的产品不是已编译的安装程序，而是zip存档。

* [ ] 如果你有足够的信心为Java EE开发人员使用另一个版本（更新版本）的Eclipse IDE，可以在https//www.eclipse.org/downloads找到它们。

> For upcoming installations, on Windows, a few target locations are suggested to be in the root directory \(C:\\). To avoid permissionrelated issues, it would be better if your Windows user is configured to be a Local Administrator. If you can't be part of this group, feel free to target installation directories you have write access to.
>
> 对于即将到来的安装，在Windows上，建议将几个目标位置位于根目录（C：\）中。 为了避免与权限相关的问题，最好是将Windows用户配置为本地管理员。 如果您不能成为此组的一部分，请随意指定您有写访问权限的安装目录。

1. Extract the downloaded archive into an eclipse directory from the steps:

2. [ ]  C:\Users{system.username}\eclipse: Extract it here if you are on Windows

3. [ ]  /home/usr/{system.username}/eclipse: Extract it here if you are on Linux

4. [ ]  /Users/{system.username}/eclipse: Extract it here if you are on Mac OS X

2.将下载的归档文件解压缩到eclipse目录中：

* [ ] C：\ Users \ system.username \ eclipse：如果您在Windows上，请在此处解压缩

* [ ] /home/usr/system.username/eclipse：如果你在Linux上，在这里提取

* [ ] /Users/system.username/eclipse：如果您在Mac OS X上，请在此处解压缩

1. Select and download JDK 8:
2. [ ]  We suggest that you download the Oracle Hotspot JDK. Hotspot is a performant JVM implementation originally built by Sun Microsystems. Now owned by Oracle, the Hotspot JRE and JDK are downloadable for free.

3. [ ]  Then, choose the product corresponding to your machine through the Oracle website's link [http://www.oracle.com/technetwork/java/javase/](http://www.oracle.com/technetwork/java/javase/) downloads/jdk8-downloads-2133151.html.

3.选择并下载JDK 8：

* [ ] 我们建议您下载Oracle热点JDK。  Hotspot是最初由Sun Microsystems构建的高性能JVM实现。 现在由Oracle拥有，热点JRE和JDK可免费下载。

* [ ] 然后，通过Oracle网站的链接http//www.oracle.com/technetwork/java/javase/ downloads / jdk8-downloads-2133151.html选择与您的计算机对应的产品。

> To avoid a compatibility issue later on, do stay consistent with the architecture choice \(32 or 64 bits\) that you have made earlier for the Eclipse archive.   
> 为了避免以后发生兼容性问题，请保持与先前为Eclipse归档所做的架构选择（32或64位）一致。

1. Install JDK 8 on the operating system of your choice using the following instructions:

   On Windows, this is a monitored installation initiated with an executable file:

   1. Execute the downloaded file and wait until you reach the next installation step

   2. On the installation-step window, pay attention to the destination directory and change it to C:\java\jdk1.8.X\_XX \(X\_XX refers to the latest current version here. We will be using jdk1.8.0\_25 in this book. Also, it won't be necessary to install an external JRE, so uncheck the public JRE feature.\)

On Linux/Mac, perform the following steps:

1. Download the tar.gz archive corresponding to your environment

2. Change the current directory to where you want to install Java. For easier instructions, let's agree on the /usr/java directory

3. Move the downloaded tar.gz archive to this current directory

4. Unpack the archive with the following command line targeting the name ofyour archive: tar zxvf jdk-8u25-linux-i586.tar.gz \(this example isfor a binary archive corresponding to a Linux x86 machine\)

You must end up with the /usr/java/jdk1.8.0\_25 directory structure that contains the /bin, /db, /jre, /include subdirectories.

4.使用以下指示信息在您选择的操作系统上安装JDK 8：

在Windows上，这是使用可执行文件启动的受监控安装：

```
    1.执行下载的文件并等待，直到到达下一个安装步骤

    2.在安装步骤窗口中，注意目标目录并将其更改为C：\ java \ jdk1.8.X\_XX（X\_XX是指此处的最新版本，我们将在本书中使用jdk1.8.0\_25  。此外，不需要安装外部JRE，因此取消选中公共JRE功能。）
```

在Linux / Mac上，执行以下步骤：

```
   1.下载与您的环境相对应的tar.gz归档文件

   2.将当前目录更改为要安装Java的位置。 为了更容易的说明，让我们同意/ usr / java目录

   3.将下载的tar.gz归档文件移动到此当前目录

  4.使用以下命令行解压缩存档，目标为您的存档名称：tar zxvf jdk-8u25-linux-i586.tar.gz（此示例适用于对应于Linux x86机器的二进制存档）
```

您必须使用包含/ bin，/ db，/ jre，/ include子目录的/usr/java/jdk1.8.0\_25目录结构。

## How it works…

In this section we are going to provide more insights about the version of Eclipse we used and  
 about how we chose this specific version of JVM.

### Eclipse for Java EE developers

We have successfully installed the Eclipse IDE for Java EE developers here. Comparatively  
 to Eclipse IDE for Java Developers, there are some additional packages coming along such  
 as Java EE Developer Tools, Data Tools Platform, and JavaScript Development Tools. This  
 version is appreciated for its ability to manage development servers as part of the IDE itself,  
 its capability to customize project facets, and its ability to support JPA. The Luna version is  
 officially Java SE 8 compatible; this has been a decisive factor at the time of writing.

在本节中，我们将提供关于我们使用的Eclipse版本的更多见解  
关于我们如何选择这个特定版本的JVM。

**Eclipse for Java EE开发人员**

我们已经为Java EE开发人员成功安装了Eclipse IDE。 比较  
到Eclipse IDE for Java Developers，还有一些额外的软件包  
作为Java EE开发工具，数据工具平台和JavaScript开发工具。 这个  
版本是欣赏作为IDE自身的一部分管理开发服务器的能力，  
其定制项目方面的能力，以及其支持JPA的能力。  Luna版本是  
官方Java SE 8兼容; 这在写作时是一个决定性的因素。

### Choosing a JVM

The choice of the JVM implementation could be discussed over performance, memory management, garbage collection, and optimization capabilities.

There are lots of different JVM implementations, including couple of open source solutions such as OpenJDK and IcedTea \(RedHat\). The choice of JVM really depends on the application's requirements. We have chosen Oracle Hotspot from experience and from reference implementations deployed in production; this JVM implementation can be trusted for a wide range of generic purposes. Hotspot also behaves very well if you have to run Java UI applications. Eclipse is one of them.

可以在性能，内存管理，垃圾回收和优化功能方面讨论JVM实现的选择。

有很多不同的JVM实现，包括一些开源解决方案，如OpenJDK和IcedTea（RedHat）。  JVM的选择真的取决于应用程序的要求。 我们从经验和从生产部署的参考实现中选择了Oracle热点; 这个JVM实现可以被广泛的通用目的信任。 如果您必须运行Java UI应用程序，热点也表现得非常好。  Eclipse是其中之一。

### **Java SE 8**

If you haven't already played with Scala or Clojure, it is time that you took the functional programming train with Java With Java SE 8, Lambda expressions reduce the amount of code dramatically providing improved readability and maintainability. We won't implement this Java 8 feature, but since it is probably the most popular, it must be highlighted as it has given a massive credit to the paradigm change. It is important, nowadays, to be familiar with these patterns.

如果你还没有玩过Scala或者Clojure，那么你就可以使用Java的函数式编程了。使用Java SE 8，Lambda表达式可以减少代码量，显着提高可读性和可维护性。 我们不会实现这个Java 8功能，但由于它可能是最受欢迎的，它必须突出显示，因为它给了范式变化的大量信誉。 现在重要的是熟悉这些模式。

