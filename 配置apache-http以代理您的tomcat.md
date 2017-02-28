We are going to access the application using a local alias cloudstreetmarket.com \(on the port 80\) rather than the former localhost:8080. Implementing the configuration for that is sometimes a mandatory step, when developing third-party integrations. In our case, the thirdparty will be Yahoo! and its OAuth2 authentication servers.

我们将使用本地别名cloudstreetmarket.com（在端口80上）访问应用程序，而不是以前的本地主机：8080。 在开发第三方集成时，实施此配置有时是必须执行的步骤。 在我们的例子中，第三方将是Yahoo!及其OAuth2认证服务器。

## Getting ready

It will mostly be about configuration. We will install an Apache HTTP server and stick to the Apache Tomcat How-To. This will drive us to update our Tomcat connector and to create a virtual host in the Apache configuration file.

You will discover how this configuration can allow a great flexibility and simply serve web content to the customers with an advanced and scalable architecture.

它主要是关于配置。 我们将安装Apache HTTP服务器并坚持使用Apache Tomcat How-To。 这将驱使我们更新我们的Tomcat连接器，并在Apache配置文件中创建一个虚拟主机。

您将了解此配置如何允许极大的灵活性，并以高级和可扩展架构向客户提供Web内容。

## How to do it...

1.On MS Windows, download and install Apache HTTP Server.

1.在MS Windows上，下载并安装Apache HTTP Server。

* [ ] The easiest way is probably to download directly the binaries from an official distributor. Select and download the appropriated latest Zip archive from one of the following URLs:

最简单的方法是直接从官方经销商下载二进制文件。 从以下某个网址中选择并下载最新的Zip归档文件：

[http://www.apachelounge.com/download](http://www.apachelounge.com/download)

[http://www.apachehaus.com/cgi-bin/download.plx](http://www.apachehaus.com/cgi-bin/download.plx)

* [ ]  Create a directory C:\apache24 and unzip the downloaded archive into this location.

创建目录C：\ apache24并将下载的归档解压缩到此位置。

> You should be able to reach the bin directory through this form:C:\apache24\bin.
>
> 你应该能够通过这种形式到达bin目录：C：\ apache24 \ bin。

2.On Linux / Mac OS, download and install Apache HTTP Server.

2.在Linux / Mac OS上，下载并安装Apache HTTP Server。

1. Download the latest sources \(compressed in a tar.gz archive\) from the apache website:

   1.从apache网站下载最新的源文件（在tar.gz归档文件中压缩）：

[http://httpd.apache.org/download.cgi\\#apache24](http://httpd.apache.org/download.cgi\#apache24)

1. From the downloaded archive, extract the sources:

   2.从下载的存档中，提取源：

`$ tar –xvzf httpd-NN.tar.gz`

`$ cd httpd-NN`

The NN command being the current version of Apache HTTP.

NN命令是Apache HTTP的当前版本。

1. Autoconfigure the arborescence:
   3.自动配置树状结构：

`$ ./configure`

1. Compile the package:
   4.编译软件包：

`$ make`

1. Install the arborescence:  
   5.安装arborescence：

`$ make install`

3.On MS Windows, add a new alias in the hosts file.

3.在MS Windows上，在hosts文件中添加一个新别名。

1. Edit with Notepad the file that can be found at the following path:

   1.使用记事本编辑可在以下路径中找到的文件：

`%SystemRoot%\system32\drivers\etc\hosts`

This file has no extension, Notepad 'doesn't complain about that when you want to save the file.

此文件没有扩展名，“记事本”在保存文件时不抱怨。

1. Add the following entry at the end of the file:

   2.在文件末尾添加以下条目：

127.0.0.1 cloudstreetmarket.com

1. Save the modification.

3.保存修改。

4.On Linux/Mac OS, add a new alias in the hosts file.

4.在Linux / Mac OS上，在hosts文件中添加新别名。

1. Edit the file that can be found at the following path: /etc/hosts

   1.编辑可在以下路径中找到的文件：/ etc / hosts

2. Add the following entry at the end of the file:

   2.在文件末尾添加以下条目：

127.0.0.1 cloudstreetmarket.com

 3.Save the modification.

 3.保存修改。

5.For all Operation Systems, edit the httpd.conf Apache configuration file.

1. This file can either be found at C:\apache24\conf \(on Windows\) or at

/usr/local/apache2/conf \(on Linux or Mac\).

1. Uncomment the following two lines:

LoadModule proxy\_module modules/mod\_proxy.so

LoadModule proxy\_http\_module modules/mod\_proxy\_http.so

1. Add the following block at the very bottom of the file:

&lt;VirtualHost cloudstreetmarket.com:80&gt;

ProxyPass         /portal [http://localhost:8080/portal](http://localhost:8080/portal)

5.对于所有操作系统，编辑httpd.conf Apache配置文件。

1.这个文件可以在C：\ apache24 \ conf（在Windows上）或在

/ usr / local / apache2 / conf（在Linux或Mac上）。

2.取消注释以下两行：

LoadModule proxy\_module modules / mod\_proxy.so

LoadModule proxy\_http\_module modules / mod\_proxy\_http.so

3.在文件的最底部添加以下块：

```js
<VirtualHost cloudstreetmarket.com:80>
    ProxyPass /portal http://localhost:8080/portal
    ProxyPassReverse /portal http://localhost:8080/portal
    ProxyPass /api http://localhost:8080/api
    ProxyPassReverse /api http://localhost:8080/api
    RedirectMatch ^/$ /portal/index
</VirtualHost>
```

> A sample of a modified httpd.conf file \(for Apache HTTP 2.4.18\) can be found in the chapter\_5/source\_code/app/apache directory.
>
> 修改的httpd.conf文件（对于Apache HTTP 2.4.18）的示例可以在chapter\_5 / source\_code / app / apache目录中找到。

6.Edit the server.xml Tomcat configuration file.

1. This file can either be found at C:\tomcat8\conf \(on Windows\) or at /home/usr/{system.username}/tomcat8/conf \(on Linux or Mac\).

2. Find the &lt;Connector port"="8080"" protocol"="HTTP/1.1""...&gt; definition and edit it as follows:

6.编辑server.xml Tomcat配置文件。

1.此文件可以在C：\tomcat8\conf（在Windows上）或在/home/usr/system.username/tomcat8/conf（在Linux或Mac上）找到。

2.找到&lt;连接器端口&gt; =“8080”“协议”=“HTTP / 1.1”“...&gt;定义并编辑如下：

```js
<Connector port"="8080"" protocol"="HTTP/1.1""
connectionTimeout"="20000"
redirectPort"="8443""
proxyName"="cloudstreetmarket.com"" proxyPort"="80""/>
```

> A sample of a modified server.xml file \(for Apache Tomcat 8.0.30\) can be found in the chapter\_5/source\_code/ app/tomcat directory.
>
> 可以在chapter\_5 / source\_code / app / tomcat目录中找到修改的server.xml文件（对于Apache Tomcat 8.0.30）的示例。

7.On MS Windows, start the Apache HTTP server.

1. Open a command prompt window.

2. [ ]  Enter the following command:

7.在MS Windows上，启动Apache HTTP服务器。

1.打开命令提示符窗口。

* [ ] 输入以下命令：

`$ cd C:/apache24/bin`

1. Install an Apache service:

2.安装Apache服务：

`$ httpd.exe –k install`

 Start the server:

* [ ] 启动服务器：

`$ httpd.exe –k start`

1. On Linux/Mac OS, start the Apache HTTP server:

 8.在Linux / Mac OS上，启动Apache HTTP服务器：

Start the server:

* [ ] 启动服务器：

`$ sudo apachectl start`

Now start the Tomcat server and open your favorite web browser. Go to [http://cloudstreetmarket.com](http://cloudstreetmarket.com), you should obtain the following landing-page:

现在启动Tomcat服务器并打开您喜欢的Web浏览器。 转到http//cloudstreetmarket.com，您应该获得以下着陆页：

![](/assets/77.png)

## How it works...

The Apache HTTP configuration we made here is somehow a standard nowadays. It supplies an infinite level of customization on a network. It also allows us to initiate the scalability.

我们在这里做的Apache HTTP配置在某种程度上是现在的标准。 它在网络上提供无限级别的定制。 它还允许我们启动可扩展性。

### DNS configuration or host aliasing

Let's revisit how web browsers work. When we target a URL in the web browser, the final server is accessed from its IP, to establish a TCP connection on a specific port. The browser needs to resolve this IP for the specified name.

To do so, it queries a chain of Domain Name Servers \(on the Internet, the chain often starts with the user's Internet Service Provider \(ISP\). Each DNS basically works this way:

* It tries to resolve the IP by itself, looking-up in its database or its cache

* If unsuccessful, it asks another DNS and waits for the response to cache the result and sends it back to the caller

DNS配置或主机别名

让我们回顾一下Web浏览器是如何工作的。 当我们在Web浏览器中定位URL时，将从其IP访问最终服务器，以在特定端口上建立TCP连接。 浏览器需要为指定的名称解析此IP。

为此，它查询一系列域名服务器（在互联网上，链通常以用户的互联网服务提供商（ISP）开头），每个DNS基本上都是这样做的：

* 它尝试自己解析IP，在其数据库或其缓存中查找

* 如果不成功，它询问另一个DNS并等待响应缓存结果并将其发送回调用者

A DNS managing one specific domain is called a** Start Of Authority \(SOA\)**. Such DNS are usually provided by registrars, and we usually use their services to configure records \(and our server IP\) for a domain zone.  
Around the web, each DNS tries to resolve the ultimate SOA. The top hierarchy of DNS servers is called **root name servers**. There are hundreds of them bound to one specific **Top-Level Domain** \(TLD such as .com, .net, .org…\).

When the browser gets the IP, it tries to establish a TCP connection on the specified port \(it defaults to 80\). The remote server accepts the connection and the HTTP request is sent over the network.

管理一个特定域的DNS称为**Start Of Authority \(SOA\)**。 这种DNS通常由注册商提供，我们通常使用他们的服务来配置域区域的记录（和我们的服务器IP）。

在网络上，每个DNS都试图解决最终的SOA。  DNS服务器的顶层称为**root name servers**。 有几百个绑定到一个**Top-Level Domain**（TLD，如.com，.net，.org ...）。

当浏览器获取IP时，它尝试在指定的端口上建立TCP连接（默认为80）。 远程服务器接受连接，并通过网络发送HTTP请求。

### In production – editing DNS records

As soon as we approach the production stage, we need the real domain name to be configured for DNS records, online, with a domain-name provider. There are different types of records to edit. Each one serves a specific purpose or resource type: host, canonical names, mail-exchanger, name server, and others. Specific guidance can usually be found on the domain name provider website.

在生产 - 编辑DNS记录

一旦我们接近生产阶段，我们需要使用域名提供商为在线DNS记录配置真实的域名。 有不同类型的记录要编辑。 每个服务器都服务于特定目的或资源类型：主机，规范名称，邮件交换器，名称服务器和其他。 具体的指导通常可以在域名提供商网站上找到。

### An alias for the host

Before contacting any kind of DNS, the operating system may be able to resolve the IP by itself. For this purpose, the host file is a plain-text registry. Adding aliases to this registry defines proxies to whatever final server. Doing so is a common technique for development environments but isn't restricted to them.

Each line represents an IP address followed by one or more host names. Each field is separated by white space or tabs. Comments can be specified at the very beginning of a line with a \#character. Blank lines are ignored and IPs can be defined in IPv4 or IPv6.

This file is only for hosts aliasing, we don't deal with ports at this stage!

主机的别名

在联系任何类型的DNS之前，操作系统可以能够自己解析IP。 为此，主机文件是纯文本注册表。 向此注册表添加别名定义到任何最终服务器的代理。 这样做是开发环境的常见技术，但不限于此。

每行代表一个IP地址，后跟一个或多个主机名。 每个字段由空格或制表符分隔。 可以在带有＃字符的行的开头指定注释。 忽略空行，并且可以在IPv4或IPv6中定义IP。

这个文件只用于主机别名，我们不处理在这个阶段的端口！

### Alias definition for OAuth developments

In this chapter, we will authenticate with an OAuth2 protocol. In OAuth, there is an **Authentication Server \(AS\)** and a **Service Provider \(SP\)**. In our case, the authentication server will be a third-party system \(Yahoo!\) and the service provider will be our application\(cloudstreetmarket.com\).

The OAuth2 authentication and authorization happen on the third-party side. As soon as these steps are completed, the authentication Server redirects the HTTP request to the service provider using a call-back URL passed as a parameter or stored as a variable.

OAuth开发的别名定义

在本章中，我们将使用OAuth2协议进行身份验证。 在OAuth中，有一个**认证服务器（AS）**和一个**服务提供者（SP）**。 在我们的情况下，认证服务器将是第三方系统（Yahoo!），服务提供商将是我们的应用程序（cloudstreetmarket.com）。

OAuth2认证和授权发生在第三方端。 一旦这些步骤完成，认证服务器就使用作为参数传递的回调URL或作为变量存储的回调URL将HTTP请求重定向到服务提供者。

Third-parties sometimes block call-back URLs that are pointing to localhost:8080.Testing and developing OAuth2 conversations locally remains a necessity.

Configuring a proxy for the hostname \(in the hosts file\) and a virtual host in an HTTP server to manage ports, URL rewriting, and redirections is a good solution for the local environment but also for a production infrastructure.

第三方阻止有时指向localhost的回调URL：8080.在本地测试和开发OAuth2对话仍然是必要的。

为HTTP服务器中的主机名（在主机文件中）和虚拟主机配置代理，以管理端口，URL重写和重定向是本地环境的一个很好的解决方案，也是生产基础设施的一个很好的解决方案。

### Apache HTTP configuration

The Apache HTTP server uses the TCP/IP protocol and provides an implementation of HTTP.TCP/IP allows computers to talk with each other throughout a network.

Each computer using TCP/IP on a network \(Local Network Area or Wide Network Area\) has an IP address. When a request arrives on an interface \(an Ethernet connection for example\), it is attempted to be mapped to a service on the machine \(DNS, SMTP, HTTP, and so on\) using the targeted port number. Apache usually uses the port 80 to listen to. This is a situation when Apache HTTP takes care of one site.

Apache HTTP配置

Apache HTTP服务器使用TCP / IP协议并提供HTTP的实现.TCP / IP允许计算机在整个网络中彼此通信。

在网络（本地网络区域或广域网络）上使用TCP / IP的每台计算机都有一个IP地址。 当请求到达接口（例如以太网连接）时，尝试使用目标端口号映射到机器上的服务（DNS，SMTP，HTTP等）。  Apache通常使用端口80来监听。 这是Apache HTTP处理一个站点时的情况。

### Virtual-hosting

This feature allows us to run and maintain more than one website from a single instance of Apache. We usually group in a `<VirtualHost...> `section, a set of Apache directives for a dedicated site. Each group is identified by a site ID.

虚拟主机

此功能允许我们从单个Apache实例运行和维护多个网站。 我们通常在一个`<VirtualHost ...>`部分，一组Apache指令为adedicated站点。 每个组由站点ID标识。

Different sites can be defined as follows:

不同的站点可以定义如下：

1. By name:

```js
NameVirtualHost 192.168.0.1
<VirtualHost portal.cloudstreetmarket.com>…</VirtualHost>
<VirtualHost api.cloudstreetmarket.com>…</VirtualHost>
```

2.By IP \(you will still have to define a ServerName inside the block\):

2.通过IP（你仍然必须在块中定义一个ServerName）：

```js
<VirtualHost 192.168.0.1>…</VirtualHost>
<VirtualHost 192.168.0.2>…</VirtualHost>
```

3.By port:

```js
Listen 80
Listen 8080
<VirtualHost 192.168.0.1:80>…</VirtualHost>
<VirtualHost 192.168.0.2:8080>…</VirtualHost>
```

Our current configuration with one machine and one Tomcat server is not the ideal scenario to demonstrate all the benefits of virtual hosting. However, we have delimited one site with its configuration. It's a first step towards scalability and load-balancing.

我们使用一台机器和一台Tomcat服务器的当前配置不是展示虚拟主机所有优势的理想场景。 但是，我们已使用其配置对一个网站进行了分隔。 这是可扩展性和负载平衡的第一步。

### The mod\_proxy module

This Apache module offers proxy/gateway capabilities to Apache HTTP server. It's a central feature as it can turn an Apache instance into a unique interface able to manage a complex set of applications balanced across multiple machines on the network.

It pushes Apache beyond its initial purpose: exposing a directory on the filesystem via HTTP. It depends on five specific sub-modules: mod\_proxy\_http, mod\_proxy\_ftp, mod\_proxy\_ajp, mod\_proxy\_balancer, and mod\_proxy\_connect. Each of them, when needed, requires the main mod\_proxy dependency. Proxies can be defined as forward \(ProxyPass\) and/or as reverse \(ProxyPassReverse\). They are often used to provide internet-access to servers located behind firewalls.

The ProxyPass can be replaced with ProxyPassMatch to offer regex-matching capabilities.

这个Apache模块为Apache HTTP服务器提供代理/网关功能。 这是一个核心功能，因为它可以将Apache实例转换为一个独特的接口，能够管理跨网络上多个机器平衡的复杂应用程序集。

它推动Apache超越其最初的目的：通过HTTP暴露文件系统上的目录。 它取决于五个特定的子模块：mod\_proxy\_http，mod\_proxy\_ftp，mod\_proxy\_ajp，mod\_proxy\_balancer和mod\_proxy\_connect。 每个，当需要时，需要主要的mod\_proxy依赖。 代理可以定义为forward（ProxyPass）和/或反向（ProxyPassReverse）。 它们通常用于为位于防火墙后面的服务器提供Internet访问。

ProxyPass可以替换为ProxyPassMatch以提供正则表达式匹配功能。

### ProxyPassReverse

Reverse-proxies handle responses and redirections exactly as if they were webservers on their own. To be activated, they are usually bound to a ProxyPass definition as in our use case here:

反向代理处理响应和重定向，就像它们是自己的网络服务器一样。 要激活，它们通常绑定到ProxyPass定义，如我们在这里的用例：

```js
ProxyPass /api http://localhost:8080/api
ProxyPassReverse /api http://localhost:8080/api
```

### Workers

Proxies manage the configuration of underlying servers and also the communication parameters between them with objects called workers \(see them as a set of parameters\). When used for a reverse-proxy, these workers are configured using ProxyPass or ProxyPassMatch:

代理管理底层服务器的配置，以及它们之间的通信参数，这些对象称为工作者（请参阅作为一组参数）。 当用于反向代理时，这些工作器使用ProxyPass或ProxyPassMatch进行配置

```js
ProxyPass /api http://localhost:8080/api connectiontimeout=5
timeout=30
```

Some examples of worker-parameters are: `connectiontimeout` \(in seconds\), `keepalive`\(On/Off\), `loadfactor`\(from 1 to 100\), `route`\(bound to sessionid when used inside a load balancer\), `ping`\(it sends CPING requests to ajp13 connections to ensure Tomcat is not busy\), `min/max` \(number of connection pool entries to the underlying server\), `ttl`\(expiry time for connections to underlying server\).

工作参数的一些示例是：`connectiontimeout`（以秒为单位），`keepalive`（开/关），`loadfactor`（从1到100），`route`（在负载均衡器内使用时绑定到sessionid），`ping`（它向ajp13发送CPING请求 连接以确保Tomcat不忙），`min / max`（到底层服务器的连接池条目数），`ttl`（到底层服务器的连接的到期时间）。

### The mod\_alias module

This module provides URL aliasing and client-request redirecting. We have used this module for redirecting \(by default\) the requests to cloudstreetmarket.com to the index page of the portal web application \(cloudstreetmarket.com/portal/index\).

Note that, in the same way ProxyPassMatch improves ProxyPass, RedirectMatch improves Redirect with regex-matching capability.

此模块提供了URL别名和客户端请求重定向。 我们已经使用此模块将请求重定向到cloudstreetmarket.com到门户网站应用程序的索引页面（cloudstreetmarket.com/portal/index）。

请注意，以同样的方式ProxyPassMatch改进ProxyPass，RedirectMatch通过正则表达式匹配功能改进了重定向。

### Tomcat connectors

A connector represents a process unit that: listens to a specific port to receive requests, forwards these requests to a specific engine, receives the dynamic content generated by the engine and finally sends back the generated content to the port. Several connectors can be defined in a Service component, sharing one single engine. One or more service\(s\) can be defined for one Tomcat instance \(Server\). There are two types of connectors in Tomcat.

Tomcat连接器

连接器表示以下处理单元：监听特定端口以接收请求，将这些请求转发到特定引擎，接收由引擎生成的动态内容，并且最终将生成的内容发回到端口。 可以在服务组件中定义多个连接器，共享一个引擎。 可以为一个Tomcat实例（服务器）定义一个或多个服务。 Tomcat中有两种类型的连接器。

### HTTP connectors

This connector is setup by default in Tomcat on the 8080 port. It supports the HTTP1/1 protocol and allows Catalina to work as a standalone webserver. HTTP connectors can be used behind a proxy. Tomcat supports mod\_proxy as a load balancer. This is our intended configuration. When implemented behind a proxy, the attributes proxyName and proxyPort can be set so the servlets bind the specified values to the request attributes request. getServerPort\(\) and request.getServerName\(\).

"This connector features the lowest latency and best overall performance."

HTTP连接器

默认情况下，此连接器在8080端口上的Tomcat中设置。 它支持HTTP1 / 1协议，并允许Catalina作为独立的Web服务器工作。 HTTP连接器可以在代理后使用。 Tomcat支持mod\_proxy作为负载均衡器。 这是我们想要的配置。 当在代理后实现时，可以设置属性proxyName和proxyPort，以便servlet将指定的值绑定到请求属性请求。 getServerPort\(\)和request.getServerName\(\)。

“此连接器具有最低的延迟和最佳的整体性能。  
”

The Tomcat documentation also states the following about HTTP proxying:

"It should be noted that the performance of HTTP proxying is usually lower than the performance of AJP."

However, configuring an AJP clustering adds an extra layer on the architecture. The necessity for this extra-layer is arguable for a stateless architecture.

Tomcat文档还说明了有关HTTP代理的以下内容：

“应当注意，HTTP代理的性能通常低于AJP的性能。  
"

但是，配置AJP集群会在架构上添加一个额外的层。 这种额外层的必要性对于无状态架构是有争议的。

### AJP connectors

AJP connectors behave as HTTP connectors except that they support the AJP protocol instead of HTTP. **Apache JServ Protocol \(AJP\)** is an optimized binary version of HTTP connector.

It allows Apache HTTP to balance effectively requests among different Tomcats. It also allows Apache HTTP to serve the static content of web applications while Tomcat focuses on the dynamic content.

On the Apache HTTP side, this connector requires mod\_proxy\_ajp. Our configuration would probably have been:

AJP连接器表现为HTTP连接器，除了它们支持AJP协议而不是HTTP。 **Apache JServ Protocol \(AJP\)**是HTTP连接器的优化二进制版本。

它允许Apache HTTP在不同的Tomcats之间有效地平衡请求。 它还允许Apache HTTP服务Web应用程序的静态内容，而Tomcat关注动态内容。

在Apache HTTP端，此连接器需要mod\_proxy\_ajp。 我们的配置可能是：

```
ProxyPass / ajp://localhost:8009/api
ProxyPassReverse / http://cloudstreetmarket.com/api/
```

## There is more…

In this section, we will provide a few links for a deeper understanding on the topics:

在本节中，我们将提供一些链接，以便更深入地了解这些主题：

* DNS and the distributed system:
   DNS和分布式系统：

[http://computer.howstuffworks.com/dns.htm](http://computer.howstuffworks.com/dns.htm)

[https://en.wikipedia.org/wiki/Root\\_name\\_server](https://en.wikipedia.org/wiki/Root\_name\_server)

* How the domain name system works:
   域名系统的工作原理：

[http://wiki.bravenet.com/How\\_the\\_domain\\_name\\_system\\_works](http://wiki.bravenet.com/How\_the\_domain\_name\_system\_works)

* Apache HTTP:

[http://httpd.apache.org/docs/trunk/getting-started.html](http://httpd.apache.org/docs/trunk/getting-started.html)

* The modules we have used:
   我们使用的模块：

[http://httpd.apache.org/docs/2.2/mod/mod\\_alias.html](http://httpd.apache.org/docs/2.2/mod/mod\_alias.html)

[http://httpd.apache.org/docs/2.2/en/mod/mod\\_proxy.html](http://httpd.apache.org/docs/2.2/en/mod/mod\_proxy.html)

* Tomcat connectors:
   Tomcat连接器：

[http://tomcat.apache.org/tomcat-8.0-doc/connectors.html](http://tomcat.apache.org/tomcat-8.0-doc/connectors.html)

[http://wiki.apache.org/tomcat/FAQ/Connectors](http://wiki.apache.org/tomcat/FAQ/Connectors)

[https://www.mulesoft.com/tcat/tomcat-connectors](https://www.mulesoft.com/tcat/tomcat-connectors)

* In proxy mode:
   在代理模式下：

[http://tomcat.apache.org/tomcat-8.0-doc/proxy-howto](http://tomcat.apache.org/tomcat-8.0-doc/proxy-howto).  
html\#Apache\_2.0\_Proxy\_Support

## See also

* fWhen using an AJP connector, the ProxyPassReverse definition is slightly different from an HTTP connector:

当使用AJP连接器时，ProxyPassReverse定义与HTTP连接器略有不同：

[http://www.apachetutor.org/admin/reverseproxies](http://www.apachetutor.org/admin/reverseproxies)

[http://www.humboldt.co.uk/the-mystery-of-proxypassreverse](http://www.humboldt.co.uk/the-mystery-of-proxypassreverse)

* fIf you wish to implement an AJP Cluster, go through the following URL:

如果要实现AJP集群，请访问以下URL：

[http://www.richardnichols.net/2010/08/5-minute-guideclustering-apache-tomcat/](http://www.richardnichols.net/2010/08/5-minute-guideclustering-apache-tomcat/)

### Alternatives to Apache HTTP

The use of Apache HTTP can be argued on very high traffic, especially because the default configuration can lead the program to create a new process for every single connection.

If we only look for a proxy and load-balancer, we should also consider HAProxy. HAProxy is a high-availability load-balancer and proxy server. It is a free and open source \(GPL v2\) product used in references such as GitHub, StackOverflow, Reddit, Twitter, and others\([http://haproxy.org\](http://haproxy.org\)\).

Nginx is probably \(and currently\) the most adopted alternative to Apache HTTP. Being focused on high concurrency and low memory usage, its license is a 2-clause BSD license\([http://nginx.org\](http://nginx.org\)\).

Apache HTTP的替代方法

Apache HTTP的使用可以在非常高的流量上争论，特别是因为默认配置可以导致程序为每个单个连接创建一个新的进程。

如果我们只寻找一个代理和负载平衡器，我们也应该考虑HAProxy。 HAProxy是一个高可用性负载均衡器和代理服务器。 它是一个免费和开源的（GPL v2）产品，用于参考文献，如GitHub，StackOverflow，Reddit，Twitter和其他（[http://haproxy.org）。](http://haproxy.org）。)

Nginx可能是（目前）最常用的替代Apache HTTP。 由于专注于高并发和低内存使用，它的许可证是一个2子句BSD许可证（[http://nginx.org）。](http://nginx.org）。)

