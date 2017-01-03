This recipe explains the installation and the configuration of AngularJS to manage a singlepage web application.

此配方解释了AngularJS的安装和配置以管理单页Web应用程序。

Getting ready

In this recipe, we explain how we got rid of the rendering logic introduced previously in the JSPs to build the DOM. We will now rely on AngularJS for this job.

Even if we don't have yet a REST API that our frontend could query, we will temporarily make the JSP build the needed JavaScript objects as if they were provided by the API.

在这个配方中，我们解释了我们如何摆脱前面在JSP中引入的渲染逻辑来构建DOM。 我们现在将依靠AngularJS来完成这项工作。

即使我们还没有一个我们的前端可以查询的REST API，我们将暂时使JSP构建所需的JavaScript对象，就像它们由API提供的一样。

AngularJS is an open source Web application Framework. It provides support for building single-page applications that can directly accommodate microservice architecture requirements. The first version of AngularJS was released in 2009. It is now maintained by Google and an open source community.

AngularJS is a whole topic in itself. As a Framework, it's deep and wide at the same time. Trying to present it as a whole would take us beyond the scope of this book and wouldn't really suit our approach.

For this reason, we are going to highlight details, features, and characteristics of the Framework that we can use to our advantage for the application.

AngularJS是一个开源的Web应用程序框架。 它为构建可以直接适应微服务体系结构要求的单页应用程序提供支持。  AngularJS的第一个版本于2009年发布。它现在由Google和一个开源社区维护。

AngularJS本身就是一个整体主题。 作为一个框架，它的深度和广泛的同时。 试图把它作为一个整体，将使我们超出了本书的范围，并不真的适合我们的方法。

因此，我们将重点介绍框架的细节，特性和特性，以便我们为应用程序使用它们。

**How to do it...  
**

**Setting up the DOM and creating modules  
**

1. Still from the previously checked-out v2.x.x branch, the index.jsp file has been added an Angular directive to the HTML tag:

`<HTML ng-app="cloudStreetMarketApp">`

1. The AngularJS JavaScript library \(angular.min.js from [https://angularjs.org\](https://angularjs.org\)\) has been placed in the cloudstreetmarket-webapp/src/main/webapp/js directory.

**设置DOM和创建模块  
**

1. 仍然从以前检出的v2.x.x分支，index.jsp文件已经添加了一个Angular指令到HTML标签：

   &lt;HTML ng-app =“cloudStreetMarketApp”&gt;

2. AngularJS JavaScript库（来自https//angularjs.org的angular.min.js）已放置在cloudstreetmarket-webapp / src / main / webapp / js目录中。

The index.jsp file has been added a wrapper landingGraphContainerAndTools div around landingGraphContainer, a select box and an ng-controller="homeFinancialGraphController":

index.jsp文件已经添加了一个包装器landingGraphContainerAndTools在landingGraphContainer周围，一个选择框和一个ng-controller =“homeFinancialGraphController”：

```
<div id='landingGraphContainer' ng-controller="homeFinancialGraphController">
    <select class="form-control centeredElementBox">
        <option value="${dailyMarketActivity.marketId}">
            ${dailyMarketActivity.marketShortName}
        </option>
    </select>
</div>
```

The whole tableMarketPrices div has been reshaped in the following way:

整个tableMarketPrices div已按以下方式重新形成：

```
<div id='tableMarketPrices'>
    <script>
        var dailyMarketsActivity = [];
        var market;
    </script>
    <c:forEach var="market" items="${dailyMarketsActivity}">
    
    <script>
        market = {};
        market.marketShortName = '${market.marketShortName}';
        
        market.latestValue = (${market.latestValue}).toFixed(2);
        market.latestChange = (${market.latestChange}*100).toFixed(2);
        dailyMarketsActivity.push(market);
    </script>
    </c:forEach>
    <div>
    <table class="table table-hover table-condensed tablebordered table-striped" data-ngcontroller='homeFinancialTableController'>
        <thead>
            <tr>
                <th>Index</th>
                <th>Value</th>
                <th>Change</th>
            </tr>
        </thead>
    <tbody>
        <tr data-ng-repeat="value in financialMarkets">
            <td>{{value.marketShortName}}</td>
            <td style="textalign:right">{{value.latestValue}}</td>
            <td class='{{value.style}}' style="textalign:right">
                <strong>{{value.latestChange}}%</strong>
            </td>
        </tr>
    </tbody>
    </table>
    </div>
</div>
```

Then, the `<div id="divRss3">` div has received significant refactoring:

然后，`<div id =“divRss3”>` div已经收到了重大的重构：

```
<div id="divRss3">
    <ul class="feedEkList" data-ngcontroller='homeCommunityActivityController'>
        <script>
            var userActivities = [];
            var userActivity;
        </script>
    <c:forEach var="activity" items="${recentUserActivity}">
    
    <script>
        userActivity = {};
        userActivity.userAction = '${activity.userAction}';
        userActivity.urlProfilePicture ='${activity.urlProfilePicture}';
        userActivity.userName = '${activity.userName}';
        userActivity.urlProfilePicture ='${activity.urlProfilePicture}';
        userActivity.date = '<fmt:formatDate="${activity.date}" pattern="dd/MM/yyyy hh:mm aaa"/>';
        userActivity.userActionPresentTense ='${activity.userAction.presentTense}';
        userActivity.amount = ${activity.amount};
        userActivity.valueShortId ='${activity.valueShortId}';
        userActivity.price = (${activity.price}).toFixed(2);
        userActivities.push(userActivity);
    </script>
    </c:forEach>
    <li data-ng-repeat="value in communityActivities">
    <div class="itemTitle">
    <div class="listUserIco{{value.defaultProfileImage}}">
        <img ng-if="value.urlProfilePicture" src='{{value.urlProfilePicture}}'>
    </div>
    <span class="ico-white {{value.iconDirection}} listActionIco"></span>
    <a href="#">{{value.userName}}</a>{{value.userActionPresentTense}} {{value.amount}}
    <a href="#">{{value.valueShortId}}</a> at{{value.price}}
    <p class="itemDate">{{value.date}}</p>
    </div>
    </li>
    </ul>
</div>
```

The graph generation section has disappeared, and it is now replaced with:

图形生成部分已消失，现在替换为：

```
<script>
    var cloudStreetMarketApp = angular.module('cloudStreetMarketApp', []);
    var tmpYmax = <c:out value="${dailyMarketActivity.maxValue}"/>;
    var tmpYmin = <c:out value="${dailyMarketActivity.minValue}"/>;
</script>
```

This graph generation has been externalized in one of the three custom JavaScript files, included with the declarations:

此图形生成已在三个自定义JavaScript文件中的一个中外部化，包括在声明中：

```
<script src="js/angular.min.js"></script>

<script src="js/home_financial_graph.js"></script>
<script src="js/home_financial_table.js"></script>
<script src="js/home_community_activity.js"></script>
```

We are going to see those three custom JavaScript files next.

接下来我们将看到这三个自定义JavaScript文件。



**Defining the module's components**

1. As introduced, three custom JavaScript files are located in the cloudstreetmarket-webapp/src/main/webapp/js directory.

2. The first one, home\_financial\_graph.js, relates to the graph. It creates a factory whose ultimate role is to pull and provide data:

**定义模块的组件**

1.如前所述，三个自定义JavaScript文件位于cloudstreetmarket-webapp / src / main / webapp / js目录中。

2.第一个，home\_financial\_graph.js，与图有关。 它创建了一个工厂，其最终作用是提取和提供数据：

```
cloudStreetMarketApp.factory("financialDataFactory", function () {

    return {
        getData: function (market) {
            return financial_data;
        },
        getMax: function (market) {
            return tmpYmax;
        },
        getMin: function (market) {
            return tmpYmin;
        }
    }
});
```

This same file also creates a controller:

同一个文件也创建一个控制器：

```
cloudStreetMarketApp.controller('homeFinancialGraphController', function ($scope, financialDataFactory){

    readSelectValue();
    drawGraph();
    
    $('.form-control').on('change', function (elem) {
        $('#landingGraphContainer').html('');
            readSelectValue()
            drawGraph();
        });
        
    function readSelectValue(){
        $scope.currentMarket = $('.form-control').val();
    }
    
    function drawGraph(){
        Morris.Line({
        element: 'landingGraphContainer',
        hideHover: 'auto',
        data:financialDataFactory.getData($scope.currentMarket),
        ymax:financialDataFactory.getMax($scope.currentMarket),
        ymin:financialDataFactory.getMin($scope.currentMarket),
        pointSize: 3,
        hideHover:'always',
        xkey: 'period', 
        xLabels: 'time',
        ykeys: ['index'], 
        postUnits: '',
        parseTime: false, 
        labels: ['Index'],
        resize: true, 
        smooth: false,
        lineColors: ['#A52A2A']
        });
    }
});
```

The second file: home\_financial\_table.js relates to the markets overview table.Just like home\_financial\_graph.js, it creates a factory:

第二个文件：home\_financial\_table.js与市场概览表相关。就像home\_financial\_graph.js一样，它创建了一个工厂：

```
cloudStreetMarketApp.factory("financialMarketsFactory", function () {
    var data=[];
    return {
        fetchData: function () {
            return data;
    }, 
    pull: function () {
        $.each( dailyMarketsActivity, function(index, el ) {
            if(el.latestChange >=0){
                dailyMarketsActivity[index].style='text-success';
            }
            else{
                dailyMarketsActivity[index].style='text-error';
            }
        });
    data = dailyMarketsActivity;
    }
    }
});
```

The home\_financial\_table.js file also have its own controller:

home\_financial\_table.js文件也有自己的控制器：

```
cloudStreetMarketApp.controller('homeFinancialTableController',function ($scope, financialMarketsFactory){
    
    financialMarketsFactory.pull();
    $scope.financialMarkets = financialMarketsFactory.fetchData();
});
```

3. The third and last file, home\_community\_activity.js relates to the community activity table. It defines a factory:

3.第三个也是最后一个文件，home\_community\_activity.js与社区活动表有关。 它定义了一个工厂：

```
cloudStreetMarketApp.factory("communityFactory", function() {
    var data=[];
    return {
        fetchData: function () {
            return data;
        },
        pull: function () {
            $.each( userActivities, function(index, el ) {
            if(el.userAction =='BUY'){
                userActivities[index].iconDirection='ico-up-arrow actionBuy';
            }
            else{
                userActivities[index].iconDirection='ico-downarrow actionSell';
            }
            userActivities[index].defaultProfileImage='';
            if(!el.urlProfilePicture){
                userActivities[index].defaultProfileImage='icouser';
            }
            userActivities[index].price='$'+el.price;
            });
        data = userActivities;
        }
    }
});
```



```
z
```



