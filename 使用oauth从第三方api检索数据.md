After having authenticated a user with OAuth2, it is useful to know how to call a remote third-party API with the user's OAuth2 account.

在使用OAuth2验证用户后，了解如何使用用户的OAuth2帐户调用远程第三方API非常有用。

## How to do it…

1. You may have noticed that IndexController, StockProductController, ChartIndexController, and ChartStockController invoke underlying service methods named `gather(…)`. This concept suggests that lookups to third-party providers \(Yahoo!\) are proceeded.

2. In IndexServiceImpl, for example, you can find the `gather(String indexId)` method:

1.您可能已经注意到IndexController，StockProductController，ChartIndexController和ChartStockController调用名为`gather(…)`的底层服务方法。 这个概念表明，对第三方提供商（Yahoo!）的查找继续进行。

2.例如，在IndexServiceImpl中，可以找到`gather(String indexId)`方法：

```java
@Override
public Index gather(String indexId) {
    Index index = indexRepository.findOne(indexId);
    if(AuthenticationUtil.userHasRole(Role.ROLE_OAUTH2)){
        updateIndexAndQuotesFromYahoo(index != null ? Sets.newHashSet(index) : Sets.newHashSet(new Index(indexId)));
        return indexRepository.findOne(indexId);
    }
    return index;
}
```

3.It is really the `updateIndexAndQuotesFromYahoo(…)` method that bridges the service layer to the third-party API:

3.实际上是将服务层桥接到第三方API的`updateIndexAndQuotesFromYahoo(…)`方法：

```java
@Autowired
private SocialUserService usersConnectionRepository;

@Autowired
private ConnectionRepository connectionRepository;

private void updateIndexAndQuotesFromYahoo(Set<Index> askedContent) {
    Set<Index> recentlyUpdated = askedContent.stream()
            .filter(t -> t.getLastUpdate() != null && DateUtil.isRecent(t.getLastUpdate(), 1)).collect(Collectors.toSet());

    if(askedContent.size() != recentlyUpdated.size()){
        String guid = AuthenticationUtil.getPrincipal().getUsername();
        String token = usersConnectionRepository.getRegisteredSocialUser(guid) .getAccessToken();
        Connection<Yahoo2> connection = connectionRepository.getPrimaryConnection(Yahoo2.class);

        if (connection != null) {
            askedContent.removeAll(recentlyUpdated);
            List<String> updatableTickers = askedContent.stream().map(Index::getId)
                                .collect(Collectors.toList());
            List<YahooQuote> yahooQuotes = connection.getApi().financialOperations()
                                .getYahooQuotes(updatableTickers, token);
            Set<Index> upToDateIndex = yahooQuotes.stream().map(t -> yahooIndexConverter.convert(t))
                                .collect(Collectors.toSet());
            final Map<String, Index> persistedStocks = indexRepository.save(upToDateIndex).stream()
                                .collect(Collectors.toMap(Index::getId,Function.identity()));
            yahooQuotes.stream().map(sq -> new IndexQuote(sq, persistedStocks.get(sq.getId()))).collect(Collectors.toSet());

            indexQuoteRepository.save(updatableQuotes);
        }
    }
}
```

4.In the case of Facebook, Twitter, or LinkedIn, you should be able to find a complete API adaptor to execute calls to their APIs without having to alter it. In our case, we had to develop the needed adaptor so that financial data can be retrieved and exploited from Yahoo!

5.We added two methods to a FinancialOperations interface as shown in this code snippet:

4.对于Facebook，Twitter或LinkedIn，您应该能够找到一个完整的API适配器来执行对其API的调用，而无需对其进行更改。 在我们的情况下，我们不得不开发所需的适配器，以便财务数据可以检索和利用自Yahoo!

5.我们向FinancialOperations接口添加了两个方法，如此代码段所示：

```java
public interface FinancialOperations {
    List<YahooQuote> getYahooQuotes(List<String> tickers, String accessToken) ;
    byte[] getYahooChart(String indexId, ChartType type, ChartHistoSize histoSize, 
                ChartHistoMovingAverage histoAverage, ChartHistoTimeSpan histoPeriod, 
                Integer intradayWidth, Integer intradayHeight, String token);
}
```

6.This interface has a FinancialTemplate implementation as follows:

6.该接口具有如下的FinancialTemplate实现：

```java
public class FinancialTemplate extends AbstractYahooOperations implements FinancialOperations {

    private RestTemplate restTemplate;

    public FinancialTemplate(RestTemplate restTemplate, boolean isAuthorized, String guid) {
        super(isAuthorized, guid);
        this.restTemplate = restTemplate;
        this.restTemplate.getMessageConverters()
        add( new YahooQuoteMessageConverter(MediaType.APPLICATION_OCTET_STREAM));
    }

    @Override
    public List<YahooQuote> getYahooQuotes(List<String> tickers, String token) {

        requiresAuthorization();

        final StringBuilder sbTickers = new StringBuilder();
        String url = "quotes.csv?s=";
        String strTickers = "";
        if(tickers.size() > 0){
            tickers.forEach(t -> strTickers = sbTickers.toString();
            strTickers = strTickers.substring(0, strTickers.length()-1);
        }
        HttpHeaders headers = new HttpHeaders();
        headers.set("Authorization", "Bearer "+token);
        HttpEntity<?> entity = new HttpEntity<>(headers);
        return restTemplate.exchange(buildUri(FINANCIAL, url.concat(strTickers)
                .concat("&f=snopl1c1p2hgbavx c4")), HttpMethod.GET, entity, QuoteWrapper.class).getBody();
    }
    ...
}
```

7.The FinancialTemplate class is initialized as part of the global Yahoo2Template that is returned with the `connection.getApi()`calls of IndexServiceImpl.

8.Using this technique to pull \(as needed\) not only indices and stock quotes from Yahoo! but also graphs, we are now able to display real-time data from more than 25,000 stocks and 30,000 indices.

7.FinancialTemplate类被初始化为全局Yahoo2Template的一部分，它与IndexServiceImpl的`connection.getApi()`调用一起返回。

8.使用这种技术，不仅从Yahoo!拉取（根据需要）索引和股票报价，还显示图表，我们现在可以显示超过25,000股股票和30,000个指数的实时数据。

![](/assets/113.png)

9.The client side is capable of using the provided HATEOAS links that come along with each result element. It uses these links to render detail views such as Index detail or Stock detail \(new screens\).

9.客户端能够使用随每个结果元素一起提供的HATEOAS链接。 它使用这些链接来呈现详细信息视图，如索引详细信息或股票详细信息（新屏幕）。

![](/assets/114.png)

## How it works...

Let's understand the theory behind this recipe.

让我们来了解这个谱方背后的理论。

### Introduction to the financial data of Yahoo!

In the context of our application, there is still one refactoring that needs to be explained. It is about historical data and graphs.

The Yahoo! financial API provides historical data. This data can be used to build graphs, and it was initially planned to do it this way. Now, Yahoo! also generates graphs \(for both historical and intraday data\) and these graphs are quite customizable \(time period, average lines, chart or stock's display option, and so on\).

We have decided to drop the historical part, which technically is very similar to quote retrieval\(data snapshots\), to exclusively use graphs generated by Yahoo!

雅虎财经数据简介

在我们的应用程序的上下文中，仍然有一个重构需要解释。 它是关于历史数据和图表。

Yahoo!金融API提供历史数据。 此数据可用于构建图形，最初计划以此方式进行。 现在，Yahoo!还生成图表（历史和日内数据），这些图表是相当可定制的（时间段，平均线，图表或股票的显示选项，等等）。

我们决定删除历史部分，这在技术上非常类似于报价检索（数据快照），专门使用由Yahoo!

### Graph generation/display

Our implementation provides an interesting example of image serving in REST. Have a look at ChartIndexController \(or ChartStockController\) and see how images are returned  
 as byte arrays.

Also have a look at the home\_financial\_graph.js file, how the received content is set into an HTML `<img…>` markup.

图形生成/显示

我们的实现提供了REST中图像服务的一个有趣的示例。 看看ChartIndexController（或ChartStockController）并查看图像如何作为字节数组返回。

还要看看home\_financial\_graph.js文件，如何将接收到的内容设置为HTML `<img ...>`标记。

### How is the financial data pulled/refreshed?

The idea here is to rely on OAuth authenticated users. Yahoo! provides different rates and limits for authenticated and non-authenticated users. Non-authenticated calls are identified on the Yahoo! side by the calling IP, which will be \(more or less\) the entire CloudstreetMarket application IP in our case. If Yahoo! considers that there are too many calls coming from our IP, that will be an issue. However, if there are too many calls coming from one specific user, Yahoo! will restrict that user without affecting the rest of the application \(and this situation can further be recovered by the application\).

As you can see, the method-handlers that potentially deal with the financial data of Yahoo! call the appropriated underlying service through methods named `gather()`.

财务数据如何提取/刷新？

这里的想法是依赖于OAuth身份验证的用户。  Yahoo!为已验证和未验证的用户提供不同的费率和限制。 在Yahoo!上识别非认证调用侧由调用IP标识，在我们的情况下，这将是（或多或少）整个CloudstreetMarket应用程序IP。 如果雅虎认为有太多来自我们的IP的调用，这将是一个问题。 但是，如果来自一个特定用户的调用过多，Yahoo!将限制该用户，而不会影响应用程序的其余部分（这种情况可以由应用程序进一步恢复）。

正如你所看到的，可能处理Yahoo!财务数据的方法处理程序通过名为`gather()`的方法调用适当的底层服务。

In these `gather()` methods, the Yahoo! third-party API interferes between our database and our controllers.

If the user is authenticated with OAuth2, the underlying service checks whether the data exists or not in the database and whether it has been updated recently enough to match a predefined buffer period for the data type \(one minute for indices and stocks\):

* If the answer is yes, this data is returned to the client

* If the answer is no, the expected data is requested from Yahoo!, transformed, stored in the database, and returned to the client

There is nothing planned at the moment for users who are not authenticated with OAuth, but we can imagine easily making them using a common Yahoo! OAuth account.

在这些`gather()`方法中，Yahoo!第三方API干扰我们的数据库和我们的控制器之间。

如果用户使用OAuth2进行身份验证，则底层服务将检查数据库中是否存在数据，以及它是否已经最近更新，以匹配数据类型的预定义缓冲期（索引和股票为一分钟）：

* 如果答案是肯定的，则将此数据返回给客户端

* 如果答案为否，则从Yahoo!请求预期的数据，转换，存储在数据库中，并返回给客户端

目前没有计划为未通过OAuth验证的用户计划，但我们可以想象，很容易让他们使用常见的Yahoo! OAuth帐户。

### Calling third-party services

For the presented recipe, this part is done in the updateIndexAndQuotesFromYahoo method. Our Spring configuration defines a connectionRepository bean created with a request scope for each user. The connectionRepository instance is created from the createConnectionRepository factory method of our SocialUserServiceImpl.

Based on this, we @Autowire these two beans in our service layer:

调用第三方服务

对于显示的配方，此部分在updateIndexAndQuotesFromYahoo方法中完成。 我们的Spring配置定义了使用每个用户的请求范围创建的connectionRepository bean。  connectionRepository实例是从SocialUserServiceImpl的createConnectionRepository工厂方法创建的。

基于这一点，我们`@Autowire`这两个bean在我们的服务层：

```java
@Autowired
private SocialUserService usersConnectionRepository;
@Autowired
private ConnectionRepository connectionRepository;
```

Then, the updateIndexAndQuotesFromYahoo method obtains the logged-in `userId(guid)` from the Spring Security:

然后，updateIndexAndQuotesFromYahoo方法从Spring Security获取登录的`userId(guid)`：

```java
String guid = AuthenticationUtil.getPrincipal().getUsername();
```

The access token is extracted from the SocialUser Entity \(coming from the database\):

访问令牌从SocialUser实体（来自数据库）中提取：

```java
String token = usersConnectionRepository.getRegisteredSocialUser(guid).getAccessToken();
```

The Yahoo! connection is retrieved from the database:

从数据库检索Yahoo!连接：

```java
Connection<Yahoo2> connection = connectionRepository.getPrimaryConnection(Yahoo2.class);
```

If the connection is not null, the third-party API is called from the connection object:

如果连接不为空，则从连接对象调用第三方API：

```java
List<YahooQuote> yahooQuotes = connection.getApi().financialOperations().getYahooQuotes(updatableTickers, token);
```

Once again, we had to develop the actual FinancialTemplate \(the Java representation of the Yahoo! financial API\), but you should be able to find such existing implementations for your third-party provider.

再次，我们必须开发实际的FinancialTemplate（Yahoo! financial API的Java表示），但是您应该能够为您的第三方提供商找到这样的现有实现。

## There's more…

This section provides a list of many existing open-source Spring Social adaptors that we can use in our projects

本节提供了许多我们可以在项目中使用的现有开源Spring Social adaptors 的列表

### Spring Social — existing API providers

The following address provides an up-to-date aggregation of Spring social extensions for connection-support and API-binding to many popular service providers:

以下地址提供了Spring social 扩展的最新聚合，用于连接支持和API绑定到许多流行的服务提供商：

[https://github.com/spring-projects/spring-social/wiki/Api-Providers](https://github.com/spring-projects/spring-social/wiki/Api-Providers)

## See also

* **Yahoo! financial stock tickers**: We have prefilled our database with a set of financial references to Yahoo! \(stock references and index references\), which allows us to point and search for resources that can, for the second time, be updated with real-time data through the Yahoo! APIs. This set of references comes from the great work published by Samir Khan on his blog accessible at [http://investexcel.net/all-yahoo-finance-stock-tickers](http://investexcel.net/all-yahoo-finance-stock-tickers). This XLS data has then been transformed into SQL by us, using a basic text editor and macros.

* **Yahoo! financial stock tickers** ：我们已经使用一组对Yahoo!（股票参考和指数参考）的财务参考预填充我们的数据库，这使我们能够指向和搜索可以第二次用真实 - 通过Yahoo! API的时间数据。 这组参考资料来自**Samir Khan**在他的博客上发表的伟大作品，可访问http//investexcel.net/all-yahoo-finance-stock-tickers。 然后，我们使用基本的文本编辑器和宏将此XLS数据转换为SQL。



