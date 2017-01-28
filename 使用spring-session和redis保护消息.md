To summarize, so far we have seen how to broadcast STOMP messages to StockJS clients, how to stack messages in an external multi-protocol broker, and how to interact with this broker \(RabbitMQ\) in the Spring ecosystem.

总之，到目前为止，我们已经看到了如何广播STOMP消息到StockJS客户端，如何在外部多协议代理中堆栈消息，以及如何在Spring生态系统中与这个代理（RabbitMQ）进行交互。

## Getting ready

This recipe is about implementing dedicated queues, no longer topics \(broadcast\), so that users can receive real-time updates related to the specific content they are viewing. It is also a demonstration of how SockJS clients can send data to their private queues.

For private queues, we had to secure messages and queue accesses. We have broken down our stateless rule of thumb for the API to make use of Spring Session. This extends the authentication performed by cloudstreetmarket-api and reuses the Spring Security context within cloudstreetmarket-websocket.

这个配方是关于实现专用队列，不再是主题（广播），以便用户可以接收与他们正在查看的特定内容相关的实时更新。 它也是SockJS客户端如何将数据发送到其专用队列的演示。

对于私有队列，我们必须保护消息和队列访问。 我们已经分解我们的无状态经验法则的API，以利用Spring会话。 这扩展了cloudstreetmarket-api执行的认证，并在cloudstreetmarket-websocket中重用了Spring Security上下文。

## How to do it…

### Apache HTTP proxy configuration

Because the v8.2.x branch introduced the new cloudstreetmarket-websocket web app, the Apache HTTP proxy configuration needs to be updated to fully support our WebSocket implementation. Our VirtualHost definition is now:

Apache HTTP代理配置

因为v8.2.x分支引入了新的cloudstreetmarket-websocket web应用程序，所以需要更新Apache HTTP代理配置以完全支持我们的WebSocket实现。 我们的VirtualHost定义现在是：

![](/assets/142.png)

### Redis server installation

1. If you are on a Linux-based machine, download the latest stable version \(3+\) at http://redis.io/download. The format of the archive to download is tar.gz.Follow the instructions on the page to install it \(unpackage it, uncompress it, and build it with the make command\).

Once installed, for a quick start, run Redis with:

Redis服务器安装

1.如果您使用基于Linux的计算机，请从http://redis.io/download下载最新的稳定版本（3+）。 要下载的存档格式为tar.gz.按照页面上的说明安装它（解包，解压缩，并使用make命令构建它）。

安装后，为了快速启动，请运行Redis：

```
$ src/redis-server
```

2. If you are on a Windows-based machine, we recommend this repository: https://github.com/ServiceStack/redis-windows. Follow the instructions on the README.md page. Running Microsoft's native port of Redis allows you to run Redis without any other third-party installations. To quickly start Redis server, run the following command:

2.如果您使用基于Windows的计算机，我们建议您使用以下存储库：https://github.com/ServiceStack/redis-windows。 按照README.md页面上的说明进行操作。 运行Microsoft的Redis本机端口允许您在没有任何其他第三方安装的情况下运行Redis。 要快速启动Redis服务器，请运行以下命令：

```
$ redis-server.exe redis.windows.conf
```

3. When Redis is running, you should be able to see the following welcome screen:

3.当Redis运行时，您应该能够看到以下欢迎屏幕：

![](/assets/143.png)

4. Update your Tomcat configuration in Eclipse to use the local Tomcat installation. To do so, double-click on your current server \(the Servers tab\):

4.在Eclipse中更新Tomcat配置以使用本地Tomcat安装。 为此，请双击当前服务器（Servers选项卡）：

![](/assets/144.png)

5. This should open the configuration panel as follows:

5.这应该打开配置面板，如下所示：

![](/assets/145.png)

Make sure the** Use Tomcat installation** radio button is checked.

确保选中**Use Tomcat installation**单选按钮。

> If the panel is greyed out, right-click on your current server again, then click** Add, Remove...** Remove the three deployed web apps from your server and right click on the server once more, then click **Publish**.
>
> 如果面板变灰，请再次右键单击当前服务器，然后单击**Add**，**Remove**...从服务器中删除三个已部署的Web应用程序，并再次右键单击服务器，然后单击**Publish**。



