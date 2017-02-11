This recipe shows how to populate the Spring MVC View with data and how to render this data within the View.

此配方显示如何使用数据填充Spring MVC视图以及如何在视图中呈现此数据。

## Getting ready **   **

At this point, we don't have any real data to be displayed in our View. For this purpose, we have created three DTOs and two service layers that are injected from their Interface into the controller.

There are two dummy service implementations that are designed to produce a fake set of data. We will use the Java Server Tags Library \(JSTL\) and the JSP Expression Language \(JSP EL\) to render the server data in the right places in our JSP.

在这一点上，我们没有任何真实的数据显示在我们的视图。 为此，我们创建了三个DTO和两个service 层，它们从其接口注入到控制器中。

有两个虚拟服务实现，旨在产生一个假的数据集。 我们将使用Java服务器标记库（JSTL）和JSP表达式语言（JSP EL）在JSP的正确位置呈现服务器数据。

## How to do it...

1. After checking out the v2.x.x branch \(in the previous recipe\), a couple of new components are now showing-up in the cloudstreetmarket-core module: two interfaces, two implementations, one enum, and three DTOs. The code is as follows:

1.检查v2.x.x分支（在前面的配方中）后，cloudstreetmarket-core模块中显示了一些新组件：两个接口，两个实现，一个枚举和三个DTO。 代码如下：

```java
public interface IMarketService {
    DailyMarketActivityDTO getLastDayMarketActivity(String string);
    List<MarketOverviewDTO> getLastDayMarketsOverview();
 }


public interface ICommunityService {
    List<UserActivityDTO> getLastUserPublicActivity(int number);
}
```

As you can see they refer to the three created DTOs:

正如你所看到的，它们指的是三个创建的DTO：

```java
public class DailyMarketActivityDTO {
    String marketShortName;
    String marketId;
    Map<String, BigDecimal> values;
    Date dateSnapshot;
    ... //and constructors, getters and setters
}

public class MarketOverviewDTO {
    private String marketShortName;
    private String marketId;
    private BigDecimal latestValue;
    private BigDecimal latestChange;
    ... //and constructors, getters and setters
}

public class UserActivityDTO {
    private String userName;
    private String urlProfilePicture;
    private Action userAction;
    private String valueShortId;
    private int amount;
    private BigDecimal price;
    private Date date;
    ... //and constructors, getters and setters
}
```

This last DTO refers to the Action enum:

这最后的DTO指的是动作枚举：

```java
public enum Action {
    BUY("buys"), SELL("sells");
    private String presentTense;

    Action(String present){
        presentTense = present;
    }

    public String getPresentTense(){
        return presentTense;
    }
}
```

Also, the previously created DefaultController in cloudstreetmarket-webapp has been altered to look like:

此外，以前在cloudstreetmarket-webapp中创建的DefaultController已更改为：

```java
@Controller
public class DefaultController {

@Autowired
private IMarketService marketService;
@Autowired
private ICommunityService communityService;

@RequestMapping(value="/*", method={RequestMethod.GET,RequestMethod.HEAD})
public String fallback(Model model) {

    model.addAttribute("dailyMarketActivity", marketService.getLastDayMarketActivity("GDAXI"));

    model.addAttribute("dailyMarketsActivity", marketService.getLastDayMarketsOverview());

    model.addAttribute("recentUserActivity", communityService.getLastUserPublicActivity(10));

    return "index";
}
}
```

And there are the two dummy implementations:

还有两个虚拟实现：

```java
@Service
public class DummyMarketServiceImpl implements IMarketService {

private DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm");

public DailyMarketActivityDTO getLastDayMarketActivity(String string){
    Map<String, BigDecimal> map = new HashMap<>();
    map.put("08:00", new BigDecimal(9523));
    map.put("08:30", new BigDecimal(9556));
    ...
    map.put("18:30", new BigDecimal(9758));
    LocalDateTime ldt = LocalDateTime.parse("2015-04-10 17:00", formatter);

    return new DailyMarketActivityDTO("DAX 30","GDAXI", map, Date.from(ldt.toInstant(ZoneOffset.UTC)));
}

@Override
public List<MarketOverviewDTO> getLastDayMarketsOverview() {

    List<MarketOverviewDTO> result = Arrays.asList(new MarketOverviewDTO("Dow Jones-IA", "DJI", 
            new BigDecimal(17810.06), new BigDecimal(0.0051)),
            ...
            new MarketOverviewDTO("CAC 40", "FCHI", new BigDecimal(4347.23), 
            new BigDecimal(0.0267))
    );
    return result;
}
}

@Service
public class DummyCommunityServiceImpl implements ICommunityService {

private DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm");

public List<UserActivityDTO> getLastUserPublicActivity(int number){
    List<UserActivityDTO> result = Arrays.asList(
        new UserActivityDTO("happyFace8", "img/younglad.jpg", Action.BUY, "NXT.L", 6, 
        new BigDecimal(3), LocalDateTime.parse("2015-04-10 11:12", formatter)),
        ...
        new UserActivityDTO("userB", null, Action.BUY, "AAL.L", 7, 
        new BigDecimal(7),
        LocalDateTime.parse("2015-04-10 13:29", formatter))
    );
    return result;
}
}
```

The index.jsp has been altered with the addition of the following section below the graph container:

index.jsp已在图表容器下方添加以下部分进行了更改：

```js
<div class='morrisTitle'>
    <fmt:formatDate value="${dailyMarketActivity.dateSnapshot}" pattern="yyyy-MM-dd"/>
</div>

<select class="form-control centeredElementBox">
    <option value="${dailyMarketActivity.marketId}">
        ${dailyMarketActivity.marketShortName}
    </option>
</select>
```

The market overview table, especially the body, has been added:

market overview表，特别是正文，已添加：

```js
<c:forEach var="market" items="${dailyMarketsActivity}">
    <tr>
        <td>${market.marketShortName}</td>
        <td style='text-align: right'>
            <fmt:formatNumber type="number" maxFractionDigits="3" value="${market.latestValue}"/>
        </td>
        <c:choose>
            <c:when test="${market.latestChange >= 0}">
            <c:set var="textStyle" scope="page" value="text-success"/>
            </c:when>
            <c:otherwise>
                <c:set var="textStyle" scope="page" value="text-error"/>
            </c:otherwise>
        </c:choose>
        <td class='${textStyle}' style='text-align: right'>
            <b><fmt:formatNumber type="percent" maxFractionDigits="2" value="${market.latestChange}"/>
            </b>
        </td>
    </tr>
</c:forEach>
```

The container for the community activity has been added:

添加了社区活动的容器：

```js
<c:forEach var="activity" items="${recentUserActivity}">
    <c:choose>
        <c:when test="${activity.userAction == 'BUY'}">
            <c:set var="icoUpDown" scope="page" value="ico-up-arrow actionBuy"/>
        </c:when>
        <c:otherwise>
            <c:set var="icoUpDown" scope="page" value="ico-down- arrow actionSell"/>
        </c:otherwise>
    </c:choose>
    <c:set var="defaultProfileImage" scope="page" value=""/>
    <c:if test="${activity.urlProfilePicture == null}">
    <c:set var="defaultProfileImage" scope="page" value="ico-user"/>
    </c:if>
    <li>
    <div class="itemTitle">
        <div class="listUserIco ${defaultProfileImage}">
            <c:if test="${activity.urlProfilePicture != null}">
                <img src='${activity.urlProfilePicture}'>
            </c:if>
        </div>
    <span class="ico-white ${icoUpDown} listActionIco"></span>
    <a href="#">${activity.userName}</a>
    ${activity.userAction.presentTense} ${activity.amount}
    <a href="#">${activity.valueShortId}</a>
    at $${activity.price}
        <p class="itemDate">
            <fmt:formatDate value="${activity.date}" pattern="dd/MM/yyyy hh:mm aaa"/>
        </p>
    </div>
    </li>
</c:forEach>
```

At the bottom of the file, a hardcoded set of JavaScript data is now populated from the server:

在文件的底部，现在从服务器填充一组硬编码的JavaScript数据集：

```js
<script>
var financial_data = [];

<c:forEach var="dailySnapshot" items="${dailyMarketActivity.values}">

    financial_data.push({
            "period": '<c:out value="${dailySnapshot.key}"/>', 
            "index": <c:out value='${dailySnapshot.value}'/>
            });
</c:forEach>
</script>

<script>
$(function () {
    Morris.Line({
        element: 'landingGraphContainer',
        hideHover: 'auto', 
        data: financial_data,
        ymax: <c:out value="${dailyMarketActivity.maxValue}"/>,
        ymin: <c:out value="${dailyMarketActivity.minValue}"/>,
        pointSize: 3, 
        hideHover:'always',
        xkey: 'period', 
        xLabels: 'month',
        ykeys: ['index'], 
        postUnits: '',
        parseTime: false, 
        labels: ['Index'],
        resize: true, 
        smooth: false,
        lineColors: ['#A52A2A']
    });
});
</script>
```

## How it works...

These changes don't produce fundamental UI improvements but they shape the data supply for our View layer.

这些更改不会产生基本的UI改进，但它们为我们的View图层提供了数据。

### The approach to handle our data

We are going to review here the server side of the data-supply implementation.

处理我们数据的方法

我们将在这里回顾一下数据源实现的服务器端。

### Injection of services via interfaces

Forecasting application needs to feed the frontpage in dynamic data, the choice has been made to inject two service layers marketService and communityService into the controller. The problem was that we don't yet have a proper Data Access layer. \(This will be covered in Chapter 4, Building a REST API for a Stateless Architecture!\). We need the controller to be wired to render the front page though.

Wiring the controller needs to be loosely coupled to its service layers. With the idea of creating dummy Service implementations in this chapter, the wiring has been designed using interfaces. We then rely on Spring to inject the expected implementations in the service dependencies, typed with the relevant Interfaces.

通过接口注入服务

预测应用程序需要在动态数据中馈送首页，已经做出选择以将两个服务层marketService和communityService注入到控制器中。 问题是我们还没有合适的数据访问层。（这将在第4章，为无状态体系结构构建REST API！）。 我们需要连接控制器以渲染首页。

控制器的接线需要松散耦合到其服务层。 在本章中创建虚拟服务实现的想法，接线是使用接口设计的。 然后我们依赖于Spring在服务相关性中注入预期的实现，用相关接口输入。

```java
@Autowired
private IMarketService marketService;

@Autowired
private ICommunityService communityService;
```

Note the types IMarketService and ICommunityService, which are not DummyCommunityServiceImpl nor DummyMarketServiceImpl. Otherwise, we would be tied to these types when switching to real implementations.

注意类型IMarketService和ICommunityService，它们不是DummyCommunityServiceImpl或DummyMarketServiceImpl。 否则，当切换到实际实现时，我们将被绑定到这些类型。

### How does Spring choose the dummy implementations?

It chooses these implementations in the cloudstreetmarket-core Spring context file: csmcore-config.xml. We have defined the beans earlier:

Spring如何选择虚拟实现？

它在cloudstreetmarket-core Spring上下文文件中选择这些实现：csmcore-config.xml。 我们已经定义了bean:

```js
<context:annotation-config/>
<context:component-scan base-package="edu.zipcloud.cloudstreetmarket.core" />
```

Spring scans all the types matching the root package edu.zipcloud. cloudstreetmarket.core to find stereotypes and configuration annotations.

In the same way that DefaultController is marked with the `@Controller` annotation, our two dummy implementation classes are marked with `@Service`, which is a Spring Stereotype. Among the detected stereotypes and beans, the dummy implementations are the only ones available for the injection configuration:

Spring扫描所有匹配edu.zipcloud. cloudstreetmarket.core包来查找构造型和配置注解。

以与DefaultController标记为`@Controller`注解相同的方式，我们的两个虚拟实现类用`@Service`标记，它是一个SpringStereotype。 在检测到的定型和bean中，虚拟实现是唯一可用于注入配置的实现：

```java
@Autowired
private IMarketService marketService;

@Autowired
private ICommunityService communityService;
```

With only one respective match per field, Spring picks them up without any extra-configuration.

每个字段只有一个匹配项，Spring会在没有任何额外配置的情况下选择它们。

### DTOs to be used in View layer ** **

We have made use of DTOs for the variables fetched in our JSPs. Exposed DTOs can be particularly useful in web services when it comes to maintaining several versions simultaneously. More generally, DTOs are implemented when the target and destination objects differ significantly.

We will implement **Entities **later. It is better not to make use of these **Entities **in the rendering or version-specific logic, but instead defer them to a layer dedicated to this purpose. Although, it must be specified that creating a DTO layer produces a fair amount of boilerplate code related to type conversion \(impacting both sides, other layers, tests, and so on\).

要在View层中使用DTO

我们使用DTO来获取我们的JSP中获取的变量。 当涉及到同时维护多个版本时，暴露的DTO在Web服务中特别有用。 更一般地，当目标和目标对象明显不同时，实现DTO。

我们将在以后实现**Entities **。 最好不要在渲染或版本特定的逻辑中使用这些**Entities **，而是将它们推迟到专用于此目的的层。 虽然，必须指定创建DTO层生成与类型转换相关的大量样板代码（影响双方，其他层，测试等）。

### Dummy service implementations

The DummyMarketServiceImpl implementation with the `getLastDayMarketActivity` method builds an activity map \(made of static daily times associated to values for the market, the index\). It returns a new DailyMarketActivityDTO instance \(built from this map\), it is in the end a wrapper carrying the daily activity for one single market or Index such as DAX 30.

The `getLastDayMarketsOverview`method returns a list of MarketOverviewDTOs also constructed from hardcoded data. It emulates an overview of daily activities for a couple of markets \(indices\).

The DummyCommunityServiceImpl implementation with its getLastUserPublicActivity method returns a list of instantiated UserActivityDTO, which simulates the last six logged user activities.

虚拟服务实现

使用`getLastDayMarketActivity`方法的DummyMarketServiceImpl实现构建活动映射（由与market的值相关联的静态日常时间，索引）。 它返回一个新的DailyMarketActivityDTO实例（从这个映射构建），它是一个包装器，承载单个market 或索引（如DAX 30）的每日活动。

`getLastDayMarketsOverview`方法返回一个也由硬编码数据构造的MarketOverviewDTOs的列表。 它模拟了几个market （指数）的日常活动的概述。

DummyCommunityServiceImpl实现及其getLastUserPublicActivity方法返回实例化的UserActivityDTO的列表，该列表模拟最后六个记录的用户活动。

### Populating the Model in the controller

Presenting the possible method-handler arguments in the first recipe of this chapter, we have seen that it can be injected-as-argument a Model. This Model can be populated with data within the method and it will be transparently passed to the expected View.

That is what we have done in the fallback method-handler. We have passed the three results from the Service layers into three variables dailyMarketActivity, dailyMarketsActivity, and recentUserActivity so they can be available in the View.

在控制器中填充模型 ** **

在本章的第一个方法中提出了可能的方法处理程序参数，我们已经看到它可以作为参数注入一个模型。 此模型可以使用方法中的数据填充，并且将透明地传递到预期的视图。

这就是我们在后备方法处理程序中所做的。 我们将Service层中的三个结果传递给threeMarketActivity，dailyMarketsActivity和recentUserActivity三个变量，以便它们可以在View中使用。

### Rendering variables with the JSP EL

The JSP Expression Language allows us to access application data stored in JavaBeans components. The notation ${…} used to access variables such as `${recentUserActivity}` or `${dailyMarketActivity.marketShortName}`is typically a JSP EL notation.

An important point to remember when we want to access the attributes of an object \(like marketShortName for dailyMarketActivity\) is that the object class must offer JavaBeans standard getters for the targeted attributes.

In other words, `dailyMarketActivity.marketShortName`refers in the MarketOverviewDTO class to an expected:

使用JSP EL呈现变量**  **

JSP表达式语言允许我们访问存储在JavaBeans组件中的应用程序数据。 用于访问变量（例如`${recentUserActivity}`或`${dailyMarketActivity.marketShortName}`）的符号${…}通常是JSP EL表示法。

当我们想要访问对象的属性（例如dailyMarketActivity的marketShortName）时，一个重要的要点是对象类必须为目标属性提供JavaBeans标准getter。

换句话说，`dailyMarketActivity.marketShortName`在MarketOverviewDTO类中引用一个预期：

```java
public String getMarketShortName() {

    return marketShortName;
}
```

### Implicit objects

The JSP EL also offers implicit objects, usable as shortcuts in the JSP without any declaration or prepopulation in the model. Among these implicit objects, the different scopes pageScope, requestScope, sessionScope, and applicationScope reflect maps of attributes in the related scope.

For example, consider the following attributes:

隐式对象**  **

JSP EL还提供了隐式对象，可用作JSP中的快捷方式，在模型中没有任何声明或预填充。 在这些隐式对象中，不同的作用域pageScope，requestScope，sessionScope和applicationScope反映相关作用域中属性的映射。

例如，考虑以下属性：

```java
request.setAttribute("currentMarket", "DAX 30");
request.getSession().setAttribute("userName", "UserA");
request.getServletContext().setAttribute("applicationState", "FINE");
```

These could respectively be accessed in the JSP with:

这些可以分别在JSP中访问：

```js
${requestScope["currentMarket"]}
${sessionScope["username"]}
${applicationScope["applicationState"]}
```

Other useful implicit objects are the map of request headers: header \(that is, `${header["Accept-Encoding"]}`\), the map of request cookies: cookies \(that is,`${cookie["SESSIONID"].value}`\), the map of request parameters: param \(that is, `${param["paramName"]}`\) or the map of context initialization parameters \(from web.xml\) initParam \(that is, `${initParam["ApplicationID"]}`\).

Finally, the JSP EL provides a couple of basic operators:

* **Arithmetic**: +, - \(binary\), \*, / and div, % and mod, - \(unary\).
* **Logical**: and, &&, or, \|\|, not, !.
* **Relational**: ==, eq, !=, ne, &lt;, lt, &gt;, gt, &lt;=, ge, &gt;=, le.

Comparisons can be made against other values, or against Boolean, String, integer, or floating point literals.

* Empty: The empty operator is a prefix operation that can be used to determine whether a value is null or empty.

* Conditional: A ? B : C.

Evaluate B or C, depending on the result of the evaluation of A.

This description of operators comes from the JavaEE 5 tutorial.

其他有用的隐式对象是请求头的映射：header（即`${header["Accept-Encoding"]}`），请求cookie的映射：cookies（即`${cookie["SESSIONID"].value}`），请求参数的映射：param（即`${param["paramName"]}`）或上下文初始化参数的映射（来自web.xml）initParam（即`${initParam["ApplicationID"]}`）。

最后，JSP EL提供了几个基本的运算符：

* 算术：+, - \(binary\), \*, /和div, %和mod, - \(unary\)

* 逻辑：和，&&，或，\|\|, not, !.

* 关系：==，eq，！=，ne，&lt;，lt，&gt;，gt，&lt;=，ge，&gt; =

可以对其他值进行比较，或者对布尔值，字符串，整数或浮点数进行比较  
点文字。

* Empty：空运算符是一个前缀操作，可用于确定值是空还是为空。

* 条件：A？ B：C.

根据A的评价结果评价B或C.

这个操作符的描述来自JavaEE 5教程。

### Rendering variables with the JSTL

The JSP Standard Tag Library \(JSTL\) is a collection of tools for JSP pages. It is not really a brand new feature of Java web but it is still used.

The tags the most used are probably Core and I18N when we need a display logic, or when we need to format data or to build a hierarchy in the View layer.

使用JSTL呈现变量

JSP标准标记库（JSTL）是JSP页面的工具集合。 这不是真正的Java Web的一个全新的功能，但它仍然使用。

当我们需要显示逻辑时，或者当我们需要格式化数据或在View层中构建层次结构时，最常用的标签可能是Core和I18N。

![](/assets/48.png)

![](/assets/49.png)

These presented tags are not the only capabilities of the JSTL, visit the Java EE tutorial for  
 more details:

[http://docs.oracle.com/javaee/5/tutorial/doc/bnakc.html](http://docs.oracle.com/javaee/5/tutorial/doc/bnakc.html)

### Taglib directives in JSPs ** **

If we plan to make use of one or the other of the above tags, we first need to include the suited directive\(s\) in the JSP page:

JSP中的Taglib伪指令**  **

如果我们计划使用上述标签中的一个或另一个，我们首先需要在JSP页面中包含适当的指令：

```js
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/fmt" prefix="fmt" %>
```



