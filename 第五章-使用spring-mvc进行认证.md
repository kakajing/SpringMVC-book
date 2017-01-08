This chapter covers the following recipes:

* Configuring Apache HTTP to proxy your Tomcat\(s\)

* Adapting users and roles to fit Spring Security

* Authenticating over a BASIC scheme

* Storing credentials in a REST environment

* Authenticating with a third-party OAuth2 scheme

* Authorizing on services and controllers

本章涵盖以下配方：

* 配置Apache HTTP以代理您的Tomcat

* 适应用户和角色以适应Spring Security

* 通过BASIC方案进行认证

* 在REST环境中存储凭据

* 使用第三方OAuth2方案进行认证

* 授权服务和控制器

# Introduction

In this chapter, developing the CloudStreetMarket application, we cover two ways of authenticating in a Spring environment.

We believe that only providing Security annotations to restrict controllers and services wouldn't be sufficient to give a big picture of Spring Authentication. It's clearly not possible to feel confident about the Security tools that can be used with Spring MVC, without a few key concepts such as the role of the Authentication object, the Spring Security filter-chain, the SecurityInterceptor workflow, and so on. As it is necessary for configuring OAuth, we will also show you how to set up an Apache HTTP proxy and a host alias on your machine to emulate the cloudstreetmarket.com domain locally.

  
在本章中，开发CloudStreetMarket应用程序，我们介绍了在Spring环境中的两种认证方式。

我们认为只提供安全注释来限制控制器和服务不足以提供Spring认证的大图片。 显然不可能对Spring MVC可以使用的安全工具感到自信，没有一些关键概念，例如Authentication对象，Spring Security过滤器链，SecurityInterceptor工作流等的作用。 由于配置OAuth非常必要，我们还将向您展示如何在您的计算机上设置Apache HTTP代理和主机别名，以便在本地模拟cloudstreetmarket.com域。



