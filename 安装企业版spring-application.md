本章涵盖的主题对应于这个四步程序：

* 为JEE开发人员和Java SE 8安装Eclipse
* Eclipse配置Java SE 8，Maven 3和Tomcat 8
* 使用Maven定义项目结构
* 安装Spring，Spring MVC和Web结构 

### 简介

在我们深入这个例程来初始化开发之前，我们将回答介绍几个问题，应该有助于你更好地了解日常生活。

记住，本章的结果也将构成所有人的最小起点其他章节。

## Why such a routine?

Whatever your initial objectives may be, it is necessary to make sure that the design will not suffer from early stage failures. This routine should cover you against this risk.

The idea beyond the routine itself is to share a bootstrap methodology to kick off the project base that you need now and that will support your needs tomorrow. The routine is also a key to drive your product thoughts toward a sustainable architecture which will be easy to refactor and to maintain.

Setting up a new project for an enterprise-level architecture will not kill the excitement and creativity!

为什么是这样的程序？

无论你的初始目标是什么，都必要确保设计不会遭受早期失败。 这个程序应该涵盖这种风险。

超越常规本身的想法是共享引导方法来启动您现在需要的项目基础，并将支持您明天的需求。 该程序也是推动您的产品思想朝着可持续建筑的关键，这将是容易重构和维护。

为企业级架构设置新项目不会磨灭激情和创造力！

## Why making use of the Eclipse IDE?

There is competition in the domain, but Eclipse is popular among the Java community for being an active open source solution; it is consequently accessible online to anyone with no restrictions. It also provides, among other usages, a very good support for web implementations and particularly to MVC web implementations.

为什么要使用Eclipse IDE？

在域中存在竞争，但Eclipse是Java社区中流行的一个积极的开源解决方案; 因此它可以在任何人没有限制的在线访问。 还提供了其他用途，非常好的支持Web实现，特别是MVC Web实现。

## Why making use of Maven?

Maven is a software project management and comprehension tool. It is an open source project supported by the Apache community and the Apache Software Foundation. For nearly 10 years, Maven has given massive benefits. It has also shaped a standard structure for Java projects. With its Project Object Model\(POM\) approach, it provides, to anyone and potentially to any third-party software, a uniform and radical way of understanding and building a Java project hierarchy with all its dependencies.

In early stage architectures, it is critical to consider the following decisions:

* Opening the project definition to potentially different development environments and continuous integration tools

* Monitoring the dependencies and maybe securing their access

* Imposing a uniform directory structure within the project hierarchy

* Building a self-tested software with self-tested components

Choosing Maven secures these points and fulfills our project's need to make our project reusable, secure, and testable \(under automation\).

为什么要使用Maven？    **      
**

Maven是一个软件项目管理和理解工具。 它是一个由Apache社区和Apache软件基金会支持的开源项目。 近10年来，Maven给了巨大的利益。 它还为Java项目塑造了一个标准结构。 通过它的项目对象模型（POM）方法，向任何人和潜在地向任何第三方软件提供一种统一和彻底的理解和构建具有所有依赖性的Java项目层次结构的方式。

在早期架构中，考虑以下决定是至关重要的：

* 打开项目定义到潜在不同的开发环境和持续集成工具

* 监视依赖关系并可能确保其访问

* 在项目层次结构中强制使用统一的目录结构

* 构建具有自测试组件的自测试软件

选择Maven保护这些点并满足我们的项目的需要，使我们的项目可重用，安全和可测试（在自动化下）。

## What does the Spring Framework bring?

The Spring Framework and its community have contributed to pulling forward the Java platform for more than a decade. Presenting the whole framework in detail would require us to write more than a book. However, the core functionality based on the principles of **Inversion of Control\(IOC\)** and **Dependency Injection\(DI\)** through a performant access to the bean repository allows considerable reusability. Staying lightweight, it secures great scaling capabilities and could probably suit all modern architectures.

Spring Framework带来了什么？

Spring框架及其社区已经推动了Java平台的十多年。 详细介绍整个框架将需要我们写一本书以外的书。 然而，通过对bean存储库的执行访问，基于**控制反转（IOC）**和**依赖注入（DI）**的原理的核心功能允许相当大的可重用性。 保持轻量级，保证巨大的缩放能力，可能适合所有现代建筑。

