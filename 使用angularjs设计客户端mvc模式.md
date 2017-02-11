This recipe explains the installation and the configuration of AngularJS to manage a singlepage web application.

此配方解释了AngularJS的安装和配置以管理单页Web应用程序。



## Getting ready

In this recipe, we explain how we got rid of the rendering logic introduced previously in the JSPs to build the DOM. We will now rely on AngularJS for this job.

Even if we don't have yet a REST API that our frontend could query, we will temporarily make the JSP build the needed JavaScript objects as if they were provided by the API.

在这个配方中，解释了我们如何摆脱前面在JSP中引入的渲染逻辑来构建DOM。 我们现在将依靠AngularJS来完成这项工作。

即使我们还没有一个我们的前端可以查询的REST API，将暂时使JSP构建所需的JavaScript对象，就像它们由API提供的一样。

AngularJS is an open source Web application Framework. It provides support for building single-page applications that can directly accommodate microservice architecture requirements. The first version of AngularJS was released in 2009. It is now maintained by Google and an open source community.

AngularJS is a whole topic in itself. As a Framework, it's deep and wide at the same time. Trying to present it as a whole would take us beyond the scope of this book and wouldn't really suit our approach.

For this reason, we are going to highlight details, features, and characteristics of the Framework that we can use to our advantage for the application.

AngularJS是一个开源的Web应用程序框架。 它为构建可以直接适应微服务体系结构要求的单页应用程序提供支持。AngularJS的第一个版本于2009年发布。它现在由Google和一个开源社区维护。

AngularJS本身就是一个整体主题。 作为一个框架，它的深度和广度在同一时期。 试图把它作为一个整体，将使我们超出了本书的范围，并不真的适合我们的方法。

出于这个原因，我们要突出的细节，功能和框架的特点，以便我们为应用程序使用它们。



### How to do it...

### Setting up the DOM and creating modules

1.Still from the previously checked-out v2.x.x branch, the index.jsp file has been added an Angular directive to the HTML tag:

`<HTML ng-app="cloudStreetMarketApp">`

2.The AngularJS JavaScript library \(angular.min.js from [https://angularjs.org](https://angularjs.org)) has been placed in the cloudstreetmarket-webapp/src/main/webapp/js directory.

设置DOM和创建模块

1.仍然从以前检出的v2.x.x分支，index.jsp文件已经添加了一个Angular指令到HTML标签：

   &lt;HTML ng-app =“cloudStreetMarketApp”&gt;

2.AngularJS JavaScript库（来自https//angularjs.org的angular.min.js）已放置在cloudstreetmarket-webapp / src / main / webapp / js目录中。

The index.jsp file has been added a wrapper landingGraphContainerAndTools div around landingGraphContainer, a select box and an ng-controller="homeFinancialGraphController":

index.jsp文件已经添加了一个包装器landingGraphContainerAndTools在landingGraphContainer周围，一个选择框和一个`ng-controller =“homeFinancialGraphController”`：

```java
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

```javascript
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

```javascript
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

```javascript
<script>
    var cloudStreetMarketApp = angular.module('cloudStreetMarketApp', []);
    var tmpYmax = <c:out value="${dailyMarketActivity.maxValue}"/>;
    var tmpYmin = <c:out value="${dailyMarketActivity.minValue}"/>;
</script>
```

This graph generation has been externalized in one of the three custom JavaScript files, included with the declarations:

此图形生成已在三个自定义JavaScript文件中的一个中外部化，包括在声明中：

```javascript
<script src="js/angular.min.js"></script>

<script src="js/home_financial_graph.js"></script>
<script src="js/home_financial_table.js"></script>
<script src="js/home_community_activity.js"></script>
```

We are going to see those three custom JavaScript files next.

接下来我们将看到这三个自定义JavaScript文件。



### Defining the module's components

1. As introduced, three custom JavaScript files are located in the cloudstreetmarket-webapp/src/main/webapp/js directory.

2. The first one, home\_financial\_graph.js, relates to the graph. It creates a factory whose ultimate role is to pull and provide data:

定义模块的组件  

1.如前所述，三个自定义JavaScript文件位于cloudstreetmarket-webapp/src/main/webapp/js目录中。

2.第一个，home\_financial\_graph.js，与图有关。 它创建了一个工厂，其最终作用是提取和提供数据：

```javascript
cloudStreetMarketApp.factory("financialDataFactory",
 function () {

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

```javascript
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

The second file: home\_financial\_table.js relates to the markets overview table.  
Just like home\_financial\_graph.js, it creates a factory:

第二个文件：home\_financial\_table.js与markets overview表相关。  
就像home\_financial\_graph.js一样，它创建了一个工厂：

```javascript
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

```javascript
cloudStreetMarketApp.controller('homeFinancialTableController',function ($scope, financialMarketsFactory){

    financialMarketsFactory.pull();
    $scope.financialMarkets = financialMarketsFactory.fetchData();
});
```

3.The third and last file, home\_community\_activity.js relates to the community activity table. It defines a factory:

3.第三个也是最后一个文件，home\_community\_activity.js与community activity表有关。 它定义了一个工厂：

```javascript
cloudStreetMarketApp.factory("communityFactory", function() {
    var data=[];
    return {
        fetchData: function () {
            return data;
        },
        pull: function () {
            $.each( userActivities, function(index, el ) {
            if(el.userAction =='BUY'){
                userActivities[index].iconDirection='ico-up-arrow actionBuy';
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

And its controller:

及其控制器：

```javascript
cloudStreetMarketApp.controller('homeCommunityActivityController', function ($scope, communityFactory){
    communityFactory.pull();
    $scope.communityActivities = communityFactory.fetchData();
});
```

## How it works...

To understand better how our AngularJS deployment works, we will see how AngularJS is started and how our Angular module \(app\) is started. Then, we will discover the AngularJS Controllers and factories and finally the implemented Angular directives.

为了更好地了解我们的AngularJS部署如何工作，我们将了解AngularJS如何启动以及我们的Angular模块（app）如何启动。 然后，我们将发现AngularJS控制器和工厂，最后实现Angular指令。

### One app per HTML document

AngularJS is automatically initialized when the DOM is loaded.

每个HTML文档有一个应用

当加载DOM时，AngularJS会自动初始化。



> The **Document Object Model \(DOM\)** is the cross-platform convention for interacting with HTML, XHTML objects. When the browser loads a web page, it creates a Document Object Model of this page.
>
> 文档对象模型（DOM）是用于与HTML，XHTML对象交互的跨平台约定。 当浏览器加载网页时，它将创建此页面的文档对象模型。



AngularJS looks up the DOM for an `ng-app` declaration in order to bind a module against a DOM element and start \(autobootstrap\) this module. Only one application \(or module\) can be autobootstrapped per HTML document.

We can still define more than one application per document and bootstrap them manually, though, if required. But the AngularJS community drives us towards binding an app to an HTML or BODY tag.

AngularJS查找一个`ng-app`声明的DOM，以便将一个模块绑定到一个DOM元素并启动（自动启动）这个模块。 每个HTML文档只能自动启动一个应用程序（或模块）。

我们仍然可以为每个文档定义多个应用程序，并手动引导它们，如果需要的话。 但AngularJS社区驱使我们将应用程序绑定到HTML或BODY标记。

### Module autobootstrap

Our application is autobootstrapped because it's referenced in the HTML tag:

模块autobootstrap

我们的应用程序是自动启动，因为它在HTML标记中引用：

`<HTML ng-app="cloudStreetMarketApp">`

Also, because the module has been created \(directly in a &lt;script&gt; element of the HTML document\):

`var cloudStreetMarketApp= angular.module('cloudStreetMarketApp', []);`

Note the empty array in the module creation; it allows the injection of dependencies into the module. We will detail the AngularJS dependency injection shortly.

此外，因为模块已经创建（直接在HTML文档的&lt;script&gt;元素中）：

`var cloudStreetMarketApp= angular.module('cloudStreetMarketApp', []);`

注意模块创建中的空数组; 它允许将依赖注入到模块中。 我们将很快详细介绍AngularJS依赖注入。

### Manual module bootstrap

As introduced before, we can bootstrap an app manually, especially if we want to control the initialization flow, or if we have more than one app per document. The code is as follows:

手动模块引导

如前所述，我们可以手动引导应用程序，特别是如果我们想要控制初始化流程，或者如果我们每个文档有多个应用程序。 代码如下：

```javascript
angular.element(document).ready(function() {
    angular.bootstrap(document, ['myApp']);
});
```

### AngularJS Controllers

AngularJS controllers are the central piece of the Framework. They monitor all the data changes occurring on the frontend. A controller is bound to a DOM element and corresponds to a functional and visual area of the screen.

At the moment, we have defined three controllers for the market graph, the markets list, and for the community activity feed. We will also need controllers for the menus and for the footer elements.

AngularJS控制器

AngularJS控制器是框架的核心部分。 它们监视在前端发生的所有数据更改。 控制器绑定到DOM元素并且对应于屏幕的功能和可视区域。

目前，我们已经为market graph，markets list和markets list定义了三个控制器。 我们还需要用于菜单和页脚元素的控制器。

![](/assets/51.png)

The DOM binding is operated with the directive's ng-controller:

DOM绑定操作与指令的`ng-controller`：

```javascript
<div ng-controller="homeFinancialGraphController">
    <table data-ng-controller='homeFinancialTableController'>
    <ul data-ng-controller='homeCommunityActivityController'>
```

Each controller has a scope and this scope is being passed as a function-argument on the controller's declaration. We can read and alter it as an object:

每个控制器都有一个作用域，这个作用域作为控制器声明上的函数参数传递。 我们可以读取和修改它作为一个对象：

```javascript
cloudStreetMarketApp.controller('homeCommunityActivityController', function ($scope, communityFactory){
    ...
    $scope.communityActivities = communityFactory.fetchData();
    $scope.example = 123;
}
```

### Bidirectional DOM-scope binding

The scope is synchronized with the DOM area the controller is bound to. AngularJS manages a bidirectional data-binding between the DOM and the controller's scope. This is probably the most important AngularJS feature to understand.

The AngularJS model is the controller's scope object. Unlike Backbone.js, for example, there is not really a view layer in Angular since the model is directly reflected in the DOM.

The content of a scope variable can be rendered in the DOM using the `{{…}}` notation. For example, the `$scope.example` variable can be fetched in the DOM with `{{example}}`.

双向DOM范围绑定

范围与控制器绑定的DOM区域同步。AngularJS管理DOM和控制器的范围之间的双向数据绑定。这可能是最重要的功能AngularJS要了解。

AngularJS模型是控制器的范围对象。 与Backbone.js不同，例如，Angular中没有真正的视图层，因为模型直接反映在DOM中。

范围变量的内容可以使用`{{…}}`表示法在DOM中呈现。 例如，`$scope.example`变量可以在具有`{{example}}`的DOM中获取。



### AngularJS directives

The directives are also a famous feature of AngularJS. They provide the ability of attaching directly to the DOM some. We can create our own directives or use built-in ones.

We will try to visit as many directives as we can along this book. For the moment, we have used the following.

AngularJS指令

这些指令也是AngularJS的一个着名特性。 它们提供了直接附加到DOM的功能。 我们可以创建自己的指令或使用内置的指令。

我们将尝试访问尽可能多的指令，可以沿着这本书。 目前，我们使用了以下。



### ng-repeat

In order to iterate the communityActivities and financialMarkets collections, we define a local variable name as part of the loop and each item is accessed individually with the {{…}} notation. The code is as follows:

为了迭代communityActivities和financialMarkets集合，我们定义一个局部变量名称作为循环的一部分，每个项目使用{{…}} 符号单独访问。 代码如下：

```javascript
<li data-ng-repeat="value in communityActivities">
    <div class="itemTitle">
        <div class="listUserIco {{value.defaultProfileImage}}">
            <img ng-if="value.urlProfilePicture" src='{{value.urlProfilePicture}}'>
        </div>
        ...
    </div>
</li>

```

### ng-if

This directive allows removing, creating, or recreating an entire DOM element or DOM hierarchy depending on a condition.

In the next example, the {{value.defaultProfileImage}} variable only renders the CSS class ".ico-user" when the user doesn't have a custom profile image \(in order to display a default generic profile picture\).

此指令允许根据条件删除，创建或重新创建整个DOM元素或DOM层次结构。

在下一个示例中，{{value.defaultProfileImage}}变量仅在用户没有自定义配置文件图像（以显示adefault通用配置文件图片）时才呈现CSS类“.ico-user”。



When the user has a profile picture, the `value.urlProfilePicture` variable is therefore populated, the `ng-if` condition is satisfied, and the &lt;img&gt; element is created in the DOM.The code is as follows:

当用户具有简档图片时，因此填充`value.urlProfilePicture`变量，满足`ng-if`条件，并且在DOM中创建&lt;img&gt;元素。代码如下：

```javascript
<div class="listUserIco {{value.defaultProfileImage}}">
    <img ng-if="value.urlProfilePicture" src='{{value.urlProfilePicture}}'>
</div>
```

### AngularJS factories

Factories are used to obtain new object instances. We have used factories as data generator. We will also use them as services coordinator and intermediate layer between the services and Controller. The Services will pull the data from the server APIs. The code is as follows:

工厂用于获取新的对象实例。 我们使用工厂作为数据生成器。 还将使用它们作为服务协调器和服务与控制器之间的中间层。 服务将从服务器API拉取数据。 代码如下：

```javascript
cloudStreetMarketApp.factory("communityFactory", function () {
    var data=[];
    return {
    fetchData: function () {
        return data;
    },
    pull: function () {
        $.each( userActivities, function(index, el ) {
            if(el.userAction =='BUY'){
                userActivities[index].iconDirection='ico-up-arrow actionBuy';
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

In this factory, we define two functions: pull\(\) and fetchData\(\) that populate and retrieve the data:

在这个工厂中，我们定义了两个函数：pull\(\)和fetchData\(\)来填充和检索数据：

```javascript
cloudStreetMarketApp.controller('homeCommunityActivityController', function ($scope, communityFactory){
    communityFactory.pull();
    $scope.communityActivities = communityFactory.fetchData();
});
```

Once the controller is loaded, it will pull\(\) and fetchData\(\) into the $scope. communityActivities. These operations are in this case executed only once.

一旦控制器被加载，它将pull\(\) 和 fetchData\(\)到$scope. communityActivities。 在这种情况下，这些操作只执行一次。

> Our factories are injected as dependencies into our controller declarations:
>
> cloudStreetMarketApp.controller\('homeCommunityActivityController', function \($scope, communityFactory\)
>
> 我们的工厂作为依赖注入到我们的控制器声明：
>
> cloudStreetMarketApp.controller\('homeCommunityActivityController', function \($scope, communityFactory\)





### Dependency injection

In our factories, controllers, and module definitions, we use AngularJS Dependency Injection to handle the components' lifecycle and their dependencies.

AngularJS uses an injector to perform the configured injections. There are three ways of annotating dependencies to make them eligible for injection:

依赖注入

在我们的工厂，控制器和模块定义中，我们使用AngularJS依赖注入来处理组件的生命周期及其依赖性。

AngularJS使用进样器执行配置的进样。 有三种注释依赖关系的方法，使它们有资格注入：

Using the inline array annotation:

* 使用内联数组注解：

```javascript
cloudStreetMarketApp.controller('homeCommunityActivityController', ['$scope', 'communityFactory', 
    function ($scope, communityFactory){
    
    communityFactory.pull();
    $scope.communityActivities = communityFactory.fetchData();
}]);
```

sing the $inject property annotation:

* sing注入`$ inject`属性：

```javascript

var homeCommunityActivityController = function ($scope, communityFactory){
    communityFactory.pull();
    $scope.communityActivities = communityFactory.fetchData();
}

homeCommunityActivityController.$inject = ['$scope', 'communityFactory'];
cloudStreetMarketApp.controller('homeCommunityActivityController', homeCommunityActivityController);

```

Using the implicit annotation mode from the function parameter names:

* 从函数参数名称中使用隐式注解模式：

```javascript
cloudStreetMarketApp.controller('homeCommunityActivityController', function ($scope, communityFactory){
    communityFactory.pull();
    $scope.communityActivities = communityFactory.fetchData();
});
```

While we have been using mostly the implicit annotation style and the inline array annotation style, we have to highlight the fact that the implicit annotation dependency injection will not work using JavaScript minification.

虽然我们主要使用隐式注解样式和内联数组注解样式，但我们必须强调隐式注解依赖注入不会使用JavaScript缩小的工作。





