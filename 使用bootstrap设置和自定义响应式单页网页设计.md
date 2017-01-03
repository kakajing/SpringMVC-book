**Bootstrap **is a UI Framework initially created by Mark Otto and Jacob Thornton at Twitter. It is an amazing source of styles, icons, and behaviors, abstracted to define and enrich components. Bootstrap offers an easy, rational, and unified set of patterns for defining styles. It had no equivalent before. If you have never used it, you will be excited to get so much visual feedback from a quick definition of the DOM.

**Bootstrap**是由Mark Otto和Jacob Thornton最初在Twitter上创建的UI框架。 它是一个惊人的风格，图标和行为的源，被抽象为定义和丰富组件。  Bootstrap提供了一个简单，合理和统一的模式集来定义样式。 它以前没有等价。 如果你从来没有使用它，你会很兴奋得到这么多的视觉反馈从DOM的快速定义。

In June 2014 it was the number 1 project on GitHub with over 73,000 stars and more than 27,000 forks. Their documentation is very fluid and easy to go through.

在2014年6月，它是GitHub上的第一个项目，拥有超过73,000颗星和超过27,000个分叉。 他们的文档非常流畅，很容易通过。

**Getting ready                  
**

In this recipe, we will use Bootstrap to set up the web-design basics for our CloudStreet Market project from an existing Bootstrap theme. We will remake the index.jsp page to render a better looking welcome page that can be previewed with the following screenshot.

**准备                  
**

在本文中，我们将使用Bootstrap从现有的Bootstrap主题为CloudStreet Market项目设置Web设计基础。 我们将重建index.jsp页面以呈现更好看的欢迎页面，可以使用以下屏幕截图预览。

![](/assets/33.png)

**How to do it...                  
**

There are three major steps in this recipe:

* Installing a Bootstrap theme

* Customizing a Bootstrap theme

* Creating responsive content

**怎么做...                  
**

这个谱方有三个主要步骤：

* 安装引导主题

* 自定义Bootstrap主题

* 创建响应内容

From the Git Perspective in Eclipse, checkout the latest version of the branch v2.x.x:

从Eclipse中的Git Perspective中，检出最新版本的分支v2.x.x：

![](/assets/34.png)

**Installing a Bootstrap theme                  
**

1. In the chapter\_2 directory, you can find a freeme.zip archive. It is a responsive Bootstrap template downloadable for free. This zip comes from the bootstrapmaster.com website.

2. Inside this archive, you'll see a css directory, a js directory, an img directory, and finally an index.html file. Opening the index.html file with a web browser should render the following home page:

   We are using this template as a base for the webapp module.

3. All the JavaScript files located in the freeme/js directory have been copied over to the /cloudstreetmarket-webapp/src/main/webapp/js directory.

4. All the CSS files located in the freeme/css directory have been copied over to the /cloudstreetmarket-webapp/src/main/webapp/css directory.

5. All the pictures located in freeme/img have been copied over to the /cloudstreetmarket-webapp/src/main/webapp/img directory.

6. The content of the freeme/index.html file has been copied and pasted into the /cloudstreetmarket-webapp/src/main/webapp/WEB-INF/jsp/index.jsp file, as UTF-8.

7. Also, the freeme/licence.txt has been copied and pasted to the / cloudstreetmarket-webapp/src/main/webapp/WEB-INF/jsp directory.

8. At this point, calling [http://localhost:8080/portal/index](http://localhost:8080/portal/index) with a web browser displayed exactly the same visual you saw earlier, but served by our application.

**安装Bootstrap主题                  
**

1. 在chapter\_2目录中，可以找到freeme.zip归档文件。 它是一个响应的Bootstrap模板可免费下载。 这个zip来自bootstrapmaster.com网站。

2. 在这个档案里，你会看到一个css目录，一个js目录，一个img目录，最后是一个index.html文件。 使用Web浏览器打开index.html文件应呈现以下主页：

   ![](/assets/35.png)

   我们使用这个模板作为webapp模块的基础。

3. 位于freeme / js目录中的所有JavaScript文件已复制到  
   / cloudstreetmarket-webapp / src / main / webapp / js目录。

4. 位于freeme / css目录中的所有CSS文件已复制到  
   / cloudstreetmarket-webapp / src / main / webapp / css目录。

5. 位于freeme / img中的所有图片已复制到/ cloudstreetmarket-webapp / src / main / webapp / img目录中。

6. freeme / index.html文件的内容已复制并粘贴到/cloudstreetmarket-webapp/src/main/webapp/WEB-INF/jsp/index.jsp文件中，为UTF-8。

7. 此外，freeme / licence.txt已复制并粘贴到/ cloudstreetmarket-webapp / src / main / webapp / WEB-INF / jsp目录中。

8. 此时，使用Web浏览器调用 [http://localhost:8080/portal/index](http://localhost:8080/portal/index)  
   显示完全相同的视觉，你看到前面，但由我们的应用程序。

**Customising a Bootstrap theme                  
**

We will detail in this section what has been done in order to adapt the downloaded template to our use case.

1. All the images previously located in cloudstreetmarket-webapp\src\main\ webapp\img\logos have been removed and replaced with six new images representing brands of technical products that we have been using through out this application and this book.

2. In the index.jsp file located in the cloudstreetmarket-webapp module has been implemented the following changes:

   1. The following two lines have been added to the top:  
      `<%@ page contentType="text/html;charset=UTF-8" language="java" %>`

      `<%@ page isELIgnored="false" %>`

   2. The `<!-- start: Meta -->` section has been replaced with the following:

      `<!-- start: Meta -->`

      `<meta charset="utf-8">`

      `<title>Spring MVC: CloudST Market</title>`

      `<meta name="description" content="Spring MVC CookBook: Cloud                  
       Street Market"/>`

      `<meta name="keywords" content="spring mvc, cookbook, packt                  
       publishing, microservices, angular.js" />`

      `<meta name="author" content="Your name"/>`

      `<!-- end: Meta -->`

   3. The `<!--start: Logo -->` section has been replaced with the following:

      `<!--start: Logo -->`

      `<div class="logo span4">`

      `CLOUD<span class="sub">ST</span><span>Market</span>`

      `</div>`

      `<!--end: Logo -->`

   4. The navigation menu definition has been changed:

      `<ul class="nav">`

      `<li class="active"><a href="index">Home</a></li>`

      `<li><a href="markets">Prices and markets</a></li>`

      `<li><a href="community">Community</a></li>`

      `<li><a href="sources">Sources</a></li>`

      `<li><a href="about">About</a></li>`

      `<li><a href="contact">Contact</a></li>`

      `</ul>`

   5. The &lt;!-- start: Hero Unit --&gt; and &lt;!-- start: Flexslider  --&gt; sections have been removed and &lt;div class="row"&gt; coming after  
       the navigation menu \(&lt;!--end: Navigation--&gt;\) has been emptied:

      `<!-- start: Row -->`

      `<div class="row"></div>`

      `<!-- end: Row -->`

   6. The `<!-- start: Row -->` section to the `<!-- end: Row -->` section, which is located after the `<!-- end Clients List -->`, has been removed along with the`<hr>`just after it.

   7. The footer section `<!-- start: Footer Menu -->` to `<!-- end: Footer Menu -->` has been replaced with the following content:

      `<!-- start: Footer Menu -->`

      `<div id="footer-menu" class="hidden-tablet hidden-phone">`

      `<!-- start: Container -->`

      `<div class="container">`

      `<!-- start: Row -->`

      `<div class="row">`

      `<!-- start: Footer Menu Logo -->`

      `<div class="span1">`

      `<div class="logoSmall">CLOUD<span`

      `class="sub">ST</span><span>M!</span>`

      `</div>`

      `</div>`

      `<!-- end: Footer Menu Logo -->`

      `<!-- start: Footer Menu Links-->`

      `<div class="span10" >`

      `<div id="footer-menu-links">`

      `<ul id="footer-nav" style="marginleft:35pt;">`

      `<li><a href="index">Home</a></li>`

      `<li><a href="markets">Prices and`

      `markets</a></li>`

      `<li><a`

      `href="community">Community</a></li>`

      `<li><a href="sources">Sources</a></li>`

      `<li><a href="about">About</a></li>`

      `<li><a href="contact">Contact</a></li>`

      `</ul>`

      `</div>`

      `</div>`

      `<!-- end: Footer Menu Links-->`

      `<!-- start: Footer Menu Back To Top -->`

      `<div class="span1">`

      `<div id="footer-menu-back-to-top">`

      `<a href="#"></a>`

      `</div>`

      `</div>`

      `<!-- end: Footer Menu Back To Top -->`

      `</div>`

      `<!-- end: Row -->`

      `</div>`

      `<!-- end: Container -->`

      `</div>`

      `<!-- end: Footer Menu -->`

   8. The section: `<!-- start: Photo Stream -->`to `<!-- end: Photo Stream -->` has been replaced with:

      `<!-- start: Leaderboard -->`

      `<div class="span3">`

      `<h3>Leaderboard</h3>`

      `<div class="flickr-widget">`

      `<script type="text/javascript" src=""></script>`

      `<div class="clear"></div>`

      `</div>`

      `</div>`

      `<!-- end: Leaderboard -->`

   9. As a last change in the index.jsp file, the copyright section has been adapted.

3. In the previously copied cloudstreetmarket-webapp/src/main/webapp/css/style.css file, the following classes have been added:

```
.logo{
    font-family: 'Droid Sans'; 
    font-size: 24pt; 
    color: #666;
    width:157pt; 
    font-weight:bold; 
    margin-top:18pt; 
    marginleft:10pt; 
    height:30pt;
}
.logo span{
    position:relative;
    float:right; 
    margin-top: 3pt; 
    fontweight:normal; 
    font-family: 'Boogaloo'; font- style:italic; 
    color: #89C236; 
    padding-right: 3pt;
}
.logo .sub {
    vertical-align: super; 
    font-style:normal;
    font-size: 16pt;
    font-family: 'Droid Sans'; 
    font-weight:bold; 
    position: absolute; 
    color: #888; 
    margin:-4pt 0 -4pt 0;
}
.logoSmall{
   font-family: 'Droid Sans';
   font-size: 16pt; 
   color: #888;width:80pt;
   font-weight:bold; 
   margin-top:10pt;
   height:20pt; 
   margin-right:30pt;
}
.logoSmall span{
position:relative;
    float:right; 
    margin-top: 3pt;
    font-weight:normal;
    font-family: 'Boogaloo'; 
    fontstyle:italic;
    color: #89C236;
}
.logoSmall .sub {
    vertical-align: super;
    font-style:normal; 
    font-size: 10pt;
    font-family: 'Droid Sans';
    font-weight:bold;
    position: absolute; 
    color: #666;
    margin:-2pt 0 -4pt 0;
}
```

4.At this point, after all these changes, restarting Tomcat and calling the same URL [http://localhost:8080/portal/index](http://localhost:8080/portal/index) resulted in the following state:

**自定义Bootstrap主题  **

我们将在本节中详细介绍为了使下载的模板适应我们的用例而做了什么。

1. .以前位于cloudstreetmarket-webapp\src\main\ webapp\img\logos中的所有图像已删除，并替换为六个新图像，表示我们通过本应用程序和本书使用的技术产品的品牌。

2.在位于cloudstreetmarket-webapp模块中的index.jsp文件中已实现以下更改：

1.以下两行已添加到顶部：

```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ page isELIgnored="false" %>
```

2.&lt;！-start：Meta - &gt;部分已替换为以下内容：

```
<!-- start: Meta -->
<meta charset="utf-8">
<title>Spring MVC: CloudST Market</title>
<meta name="description" content="Spring MVC CookBook: Cloud Street Market"/>
<meta name="keywords" content="spring mvc, cookbook, packt publishing, microservices, angular.js" />
<meta name="author" content="Your name"/>
<!-- end: Meta -->
```

3.&lt;！-start：Logo - &gt;部分已替换为以下内容：

```
<!--start: Logo -->
<div class="logo span4">
  CLOUD<span class="sub">ST</span><span>Market</span>
</div>
<!--end: Logo -->
```

4.导航菜单定义已更改：

```
<ul class="nav">
<li class="active"><a href="index">Home</a></li>
<li><a href="markets">Prices and markets</a></li>
<li><a href="community">Community</a></li>
<li><a href="sources">Sources</a></li>
<li><a href="about">About</a></li>
<li><a href="contact">Contact</a></li>
</ul>
```

5.`<!-- start: Hero Unit -->`和`<!-- start: Flexslider -->`部分已被删除，`<div class="row">`后的导航菜单\(`<!--end: Navigation-->`\) 已被清空：

```
<!-- start: Row -->
<div class="row"></div>
<!-- end: Row -->
```

6.`<!-- start: Row -->`部分到`<!-- end: Row -->`部分，它位于`<!-- end Clients List -->`之后在`<hr>`之后删除。

7.页脚部分`<!-- start: Footer Menu -->`到`<!-- end: Footer Menu -->`已替换为以下内容：

```
<!-- start: Footer Menu -->
<div id="footer-menu" class="hidden-tablet hidden-phone">
<!-- start: Container -->
<div class="container">
<!-- start: Row -->
<div class="row">
<!-- start: Footer Menu Logo -->
<div class="span1">
<div class="logoSmall">CLOUD<span class="sub">ST</span><span>M!</span>
</div>
</div>
<!-- end: Footer Menu Logo -->
<!-- start: Footer Menu Links-->
<div class="span10" >
<div id="footer-menu-links">
<ul id="footer-nav" style="marginleft:35pt;">
<li><a href="index">Home</a></li>
<li><a href="markets">Prices and markets</a></li>
<li><a href="community">Community</a></li>
<li><a href="sources">Sources</a></li>
<li><a href="about">About</a></li>
<li><a href="contact">Contact</a></li>
</ul>
</div>
</div>
<!-- end: Footer Menu Links-->
<!-- start: Footer Menu Back To Top -->
<div class="span1">
<div id="footer-menu-back-to-top">
<a href="#"></a>
</div>
</div>
<!-- end: Footer Menu Back To Top -->
</div>
<!-- end: Row -->
</div>
<!-- end: Container -->
</div>
<!-- end: Footer Menu -->
```

     8.`<!-- start: Photo Stream -->`到 `<!-- end: Photo Stream -->` 已替换为：

```
<!-- start: Leaderboard -->
<div class="span3">
<h3>Leaderboard</h3>
<div class="flickr-widget">
<script type="text/javascript" src=""></script>
<div class="clear"></div>
</div>
</div>
<!-- end: Leaderboard -->
```

```
9.作为index.jsp文件的最后一个更改，已对版权部分进行了修改。
```

3.在以前复制的cloudstreetmarket-webapp/src/main/webapp/css/style.css文件中，添加了以下类：

```
.logo{ 
    font-family: 'Droid Sans'; 
    font-size: 24pt; 
    color: #666;
    width:157pt; 
    font-weight:bold; 
    margin-top:18pt; 
    marginleft:10pt; 
    height:30pt;
}
.logo span{
    position:relative;
    float:right; 
    margin-top: 3pt; 
    fontweight:normal; 
    font-family: 'Boogaloo'; font- style:italic; 
    color: #89C236; 
    padding-right: 3pt;
}
.logo .sub {
    vertical-align: super; 
    font-style:normal;
    font-size: 16pt;
    font-family: 'Droid Sans'; 
    font-weight:bold; 
    position: absolute; 
    color: #888; 
    margin:-4pt 0 -4pt 0;
}
.logoSmall{
   font-family: 'Droid Sans';
   font-size: 16pt; 
   color: #888;width:80pt;
   font-weight:bold; 
   margin-top:10pt;
   height:20pt; 
   margin-right:30pt;
}
.logoSmall span{
position:relative;
    float:right; 
    margin-top: 3pt;
    font-weight:normal;
    font-family: 'Boogaloo'; 
    fontstyle:italic;
    color: #89C236;
}
.logoSmall .sub {
    vertical-align: super;
    font-style:normal; 
    font-size: 10pt;
    font-family: 'Droid Sans';
    font-weight:bold;
    position: absolute; 
    color: #666;
    margin:-2pt 0 -4pt 0;
}
```

4.此时，在所有这些更改后，重新启动Tomcat并调用相同的URL [http：// localhost：8080 / portal / index](http://localhost:8080/portal/index)导致以下状态：

![](/assets/36.png)

**Creating responsive content          
**

We will focus in this section on the changes that have been made to fill the welcome page with responsive content. By responsive, understand that the content will be rendered under a style appropriate for the device size and orientation.

**创建响应内容          
**

我们将在本节重点介绍为填充欢迎页面而做出的更改以及响应内容。 通过响应，了解内容将在适合于设备大小和方向的样式下呈现。

1.在index.jsp文件中：

1\).&lt;div class="row"&gt;添加了以下内容：

```
<div class='span12'>
    <div class="hero-unit hidden-phone"><p>Welcome to CloudStreet Market, the educational platform.</p></div>
</div>
<div class='span5'>
<div id='landingGraphContainer'></div>
<div id='tableMarketPrices'>
    <table class="table table-hover table-condensed table-bordered table-striped">
        <thead>
            <tr>
                <th>Index</th>
                <th>Value</th>
                <th>Change</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td>Dow Jones-IA</td><td>17,634.74</td>
                <td class='text-success'><b>-18.05</b></td>
            </tr>
            ...
            <tr>
                <td>FTSE MIB</td><td>18,965.41</td>
                <td class='text-error'><b>-182.86</b></td>
            </tr>
            ...
        </tbody>
    </table>
</div>
</div>
<div id="containerCommunity" class='span7'>
    <div id="divRss3"></div>
</div>
```

> In the previously added landingGraphContainer, we have inserted a generated graph that renders the evolution of specific markets during the last opened day. The graph uses the morris.js library \(http://  
> morrisjs.github.io/morris.js\), which also relies on the raphael.js library\([https://cdnjs.com/libraries/raphael\](https://cdnjs.com/libraries/raphael\)\).
>
> 在之前添加的landingGraphContainer中，我们插入了一个生成的图形，在最后一个打开的日子里呈现特定市场的演变。 该图表使用morris.js库（[https://cdnjs.com/libraries/raphael），它也依赖于raphael.js库（https//cdnjs.com/libraries/raphael）。](https://cdnjs.com/libraries/raphael），它也依赖于raphael.js库（https//cdnjs.com/libraries/raphael）。)

2\). At the bottom of the file, the &lt;!-- start: Java Script --&gt; section to the &lt;!-- end: Java Script --&gt; section has been added the following content:

2\).在文件底部，&lt;!-- start: Java Script --&gt;部分的&lt;!-- end: Java Script --&gt;部分添加了以下内容：

```
<script src="js/jquery-1.8.2.js"></script>
<script src="js/bootstrap.js"></script>
<script src="js/flexslider.js"></script>
<script src="js/carousel.js"></script>
<script def src="js/custom.js"></script>
<script src="js/FeedEk.js"></script>
<script src="js/raphael.js"></script>
<script src="js/morris.min.js"></script>
<script>
$(function () {
var financial_data = [
    {"period": "08:00", "index": 66},{"period": "09:00", "index": 62},
    {"period": "10:00", "index": 61},{"period": "11:00", "index": 66},
    {"period": "12:00", "index": 67},{"period": "13:00", "index": 68},
    {"period": "14:00", "index": 62},{"period": "15:00", "index": 61},
    {"period": "16:00", "index": 61},{"period": "17:00", "index": 54}
];
    Morris.Line({
        element: 'landingGraphContainer',
        hideHover: 'auto', 
        data: financial_data,
        ymax: 70, 
        ymin: 50,
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

2.In the cloudstreetmarket-webapp\src\main\webapp\js directory, the morris.min.js and raphael.js libraries have been copied and pasted from their respective websites.

在cloudstreetmarket-webapp \ src \ main \ webapp \ js目录中，morris.min.js和raphael.js库已从其各自的网站复制和粘贴。

1. Back to the index.jsp file:

   1\). The previously created &lt;div id='containerCommunity'&gt; has been filled with the following content:

3.返回index.jsp文件：

1\).先前创建的&lt;div id ='containerCommunity'&gt;已填写以下内容：

```
<div id="divRss3">
    <ul class="feedEkList">
        <li>
            <div class="itemTitle">
            <div class="listUserIco">
                <img src='img/young- lad.jpg'>
            </div>
                <span class="ico-white ico-up-arrow listActionIco actionBuy"></span>
                <a href="#">happyFace8</a> buys 6 <a href="#">NXT.L</a> at $3.00
                <p class="itemDate">15/11/2014 11:12 AM</p>
            </div>
        </li>
        <li>
            <div class="itemTitle">
            <div class="ico-user listUserIco"></div>
                <span class="ico-white ico-down-arrow listActionIco actionSell"></span>
                <a href="#">actionMan9</a> sells 6 <a href="#">CCH.L</a> at $12.00
                <p class="itemDate">15/11/2014 10:46 AM</p>
            </div>
        </li>
        ...
    </ul>
</div>
```

1. The section here uses the feedEk jQuery plugin. It comes with its own CSS and JavaScript file.

   这里的部分使用feedEk jQuery插件。 它有自己的CSS和JavaScript文件。

2. The cloudstreetmarket-webapp\src\main\webapp\js directory includes the FeedEk.js file related to the feedEk jQuery plugin. This plugin can be found online\([http://jquery-plugins.net/FeedEk/FeedEk.html\](http://jquery-plugins.net/FeedEk/FeedEk.html\)\).

3. The cloudstreetmarket-webapp\src\main\webapp\css directory also has the related FeedEk.css file.

4. Still in index.jsp, under the &lt;!-- start: CSS --&gt; comment, the FeedEk css document has been added:

&lt;link href="css/FeedEk.css" rel="stylesheet"&gt;

1. In the style.css file, before the first media query definition \(@media only screen and \(min-width: 960px\)\), the following style definitions have been added:

2. cloudstreetmarket-webapp\src\main\webapp\js 目录包括与feedEk jQuery插件相关的FeedEk.js文件。 这个插件可以在网上找到（http//jquery-plugins.net/FeedEk/FeedEk.html）。

3. cloudstreetmarket-webapp \ src \ main \ webapp \ css目录也有相关的FeedEk.css文件。

6.仍然在index.jsp中，在&lt;!-- start: CSS --&gt;注释下，添加了FeedEk css文档：

`<link href =“css / FeedEk.css”rel =“stylesheet”>`

7.在style.css文件中，在第一个媒体查询定义（@media only screen和（min-width：960px））之前，添加了以下样式定义：

```
.listUserIco {
    background-color:#bbb;
    float:left;
    margin:0 7px 0 0;
}

.listActionIco {
    float:right;
    margin-top:-3px;
}

.actionSell {
    background-color:#FC9090;
}

.actionBuy {
    background-color:#8CDBA0;
}

#landingGraphContainer{
    height:160px;
    padding: 0px 13px 0 10px;
}

.tableMarketPrices{
    padding: 13px 13px 0 15px;
}
```

8.Finally, two new images \(profile pictures\) have been added to cloudstreetmarketwebapp\src\main\webapp\img.

1. Try to dynamically resize a browser window that renders: [http://localhost:8080/portal/index](http://localhost:8080/portal/index). You should observe a responsive and adaptive style as in the following picture:

8.两个新的图像（配置文件图片）已添加到cloudstreetmarketwebapp \ src \ main \ webapp \ img。

9.尝试动态调整呈现以下内容的浏览器窗口的大小：http：// localhost：8080 / portal / index。 你应该观察到一个自适应的风格，如下图所示：

![](/assets/37.png)

**How it works...    
**

To understand our Bootstrap deployment, we are going to review now how it has been installed as a predesigned theme. We will then discover some key features of the Bootstrap Framework—not only the implemented features because, logically enough, only a few features of the Framework can visually be used on one single page example.

**怎么运行的...    
**

要了解我们的Bootstrap部署，我们现在将回顾它如何作为预先设计的主题安装。 然后我们将发现Bootstrap框架的一些关键特性 - 不仅是已实现的特性，因为在逻辑上足够的情况下，只有一个框架的几个特性可以在一个单独的页面示例上使用。

**The theme installation    
**

The theme we have obtained is no more than a classical static theme, as you can find thousands of them over the Internet. They are made by web designers and distributed for free or commercially. This one is made with the basic structure of HTML files, a JS directory, a CSS directory, and an IMG directory.

The theme installation is quite straightforward to understand, since we have just placed the JavaScript files, CSS files, and images in their expected locations for our application.

> The Bootstrap core features are self-contained in bootstrap. js, bootstrap.css, and bootstrap-responsive.css. You should not really have to tweak these files directly.
>
> Bootstrap核心功能在引导中自包含。  js，bootstrap.css和bootstrap-responsive.css。 你不应该真的必须直接调整这些文件。

**主题安装    
**

我们所获得的主题只不过是一个经典的静态主题，因为你可以通过互联网找到数千个。 它们由网页设计师制作并免费或商业分发。 这个是用HTML文件，一个JS目录，一个CSS目录和一个IMG目录的基本结构。

主题安装非常直观，因为我们刚刚将JavaScript文件，CSS文件和图像放置在我们的应用程序的预期位置。

**Bootstrap highlights    
**

The implemented theme \(FreeME\) uses Bootstrap 2. We are going to review a couple of features that have been implemented in the template and for the needs of our project.

**Bootstrap亮点    
**

实现的主题（FreeME）使用Bootstrap 2.我们将审查已经在模板中实现的几个功能以及我们项目的需要。

**Bootstrap scaffolding    
**

The Bootstrap scaffolding helps with designing the HTML structure usually built from a grid model. The Bootstrap strategy on this topic is described in the following sections.

Bootstrap框架有助于设计通常由网格模型构建的HTML结构。 有关此主题的引导策略将在以下部分中介绍。

**Grid system and responsive design    
**

Bootstrap offers a styleframe to handle a page-specific grid system. The key point is in the default grid-system made up of 12 columns and designed for a 940px wide nonresponsive container.

The Bootstrap responsive features are activated with the use of &lt;meta name="viewport"…&gt; tag and with the import of the boostrap-responsive.css file. The container width can extend from 724px to 1170px in that case.

> Also, below 767px, the columns become fluid and stack vertically.
>
> 此外，低于767px，列变成流体和垂直堆叠。

These Bootstrap specifications define quite a drastic set of constraints but Bootstrap somehow creates an easy-to-understand design uniformity for its implementations.

这些Bootstrap规范定义了相当严格的约束，但Bootstrap以某种方式为其实现创建了易于理解的设计一致性。

**网格系统和响应设计    
**

Bootstrap提供了一个styleframe来处理页面特定的网格系统。 关键点在于由12列组成的默认网格系统，并设计用于一个940像素宽的非响应容器。

Bootstrap响应功能使用`<meta name =“viewport”...>`标记并导入boostrap-responsive.css文件来激活。 在这种情况下，容器宽度可以从724像素延伸到1170像素。

In the case of our template, the viewport metatag is the following:

在我们的模板的情况下，视口元标记如下：

`<meta name="viewport" content="width=device-width, initial-scale=1,maximum-scale=1">`

> If you are not familiar with this tag, its main purpose is to define device-specific sizes in the document. From these sizes, rules are defined for orientation-specific and device-specific rendering. These rules that are bound to style definitions are called mediaqueries. You can find an example of a mediaquery in the style.css file:
>
> 如果您不熟悉此标记，其主要目的是在文档中定义设备特定的大小。 从这些大小中，为定向特定和特定于设备的呈现定义规则。 绑定到样式定义的这些规则称为媒体查询。 您可以在style.css文件中找到媒体查询的示例：
>
> /\* Higher than 960 \(desktop devices\)
>
> ===================================================
>
> ============= \*/
>
> @media only screen and \(min-width: 960px\) {
>
> ...
>
> \#footer-menu {
>
> ```
>   padding-left: 30px;
>
>   padding-right: 30px;
>
>   margin-left: -30px;
>
>   margin-right: -30px;
> ```
>
> }
>
> ...
>
> }
>
> This media query overrides the style that is specific to the id footer menu only where the device presents a width greater than 960px.
>
> 此媒体查询会覆盖特定于id页脚菜单的样式，只有当设备显示的宽度大于960像素时。

**Defining columns  
**

To define columns within the grid system, Bootstrap drives us towards the use of a row div tagged as a row class element. Then, the idea is to define subdivs marked with custom span\* class elements where the \* characters represents subdivisions of the 12-column grid we have to deal with.

For example, consider the following two possible designs:

**定义列**

为了在网格系统中定义列，Bootstrap驱动我们使用标记为行类元素的行div。 然后，想法是定义标记自定义**span \***类元素的子元素，其中**\***字符表示我们必须处理的12列网格的细分。

例如，考虑以下两种可能的设计：

![](/assets/38.png)

The two columns on the left example can be rendered from the DOM definition:

左边示例中的两列可以从DOM定义呈现：

```
<div class="row">
    <div class="span4">...</div>
    <div class="span8">...</div>
</div>
```

The two columns on the right example can be rendered from the DOM definition:

右侧示例中的两列可以从DOM定义呈现：

```
<div class="row">
    <div class="span6">...</div>
    <div class="span6">...</div>
</div>
```

With this in mind, the grid of our welcome page is actually the following:

考虑到这一点，我们的欢迎页面的网格实际上是以下：

![](/assets/39.png)



**Offsetting and nesting**

Offsetting a column allows you to create a fixed-sized decay corresponding to one or more invisible columns. For example, consider the following snippet:

**偏移和嵌套**

偏移列允许您创建与一个或多个不可见列相对应的固定大小的衰减。 例如，考虑以下代码段：

```
<div class="row">
    <div class="span6">...</div>
    <div class="span4 offset2">...</div>
</div>
```

This DOM definition will correspond to the following columns:

此DOM定义将对应于以下列：

![](/assets/40.png)

A column can also be nested inside another column redefining a new row. The sum of the newly created columns must correspond to the parent's size:

列也可以嵌套在另一个列中，重新定义新行。 新创建的列的总和必须与父级的大小相对应：

```
<div class="row">
    <div class="span6">
        <div class="row">
            <div class="span2">...</div>
            <div class="span4">...</div>
        </div>
    </div>
</div>
```



**Fluid gridding**

We were saying earlier that, with Boostrap2, below 767px the columns become fluid and stack vertically. The template gridding can be changed from static to fluid turning .row classes to** .row-fluid**. Rather than fixed pixels sized columns, this system will use percentages.

**流体网格化**

我们之前说过，使用Boostrap2，低于767px的列变成流体，垂直堆叠。 模板网格化可以从静态转换为流体转向**.row classes**转换为**.row-fluid**。 此系统将使用百分比，而不是固定像素大小的列。



**Bootstrap CSS utilities**

Bootstrap also provides a few pre-designed elements such as buttons, icons, tables, forms and also utilities to support typography or images.

**Bootstrap CSS实用程序**

Bootstrap还提供了一些预先设计的元素，如按钮，图标，表格，表单以及支持排版或图像的实用程序。



**Uniform Buttons**

Default styled buttons can be created from the `<a> `and `<button> `tags with only addition of the `.btn class` element. The created default gray button with a gradient can then be declined in different colour variations. For example, **by default**, the following classes combination:

* .btn .btn-primary: This produces an intense ultramarine blue button to identify the primary action among other buttons
* .btn .btn-info: This produces a moderate turquoise blue button
* .btn .btn-success: This produces a positive green button
* .btn .btn-warning: This produces a warning orange button
* .btn .btn-danger: This produces a dangerous red button
* .btn .btn-inverse: This produces a black button with white text
* .btn .btn-link: This produces a link while maintaining a button behavior

These buttons are also resizable declaratively by adding a .btn-large class, adding a .btn-small class, or adding a .btn-mini class:
**Uniform 按钮**

可以从`<a>`和`<button>`标记中创建默认样式按钮，只添加`.btn class`元素。 然后，可以以不同的颜色变化来拒绝所创建的具有渐变的默认灰色按钮。 例如，**默认情况下**，以下类组合：

* `.btn .btn-primary`：这将产生一个强烈的深蓝色按钮，以识别其他按钮中的主要操作

* `.btn .btn-info`：这会产生一个温和的蓝绿色按钮

* `.btn .btn-success`：这将产生一个正的绿色按钮

* `.btn .btn-warning`：这将产生一个警告橙色按钮
* `.btn .btn-danger`：这将产生一个危险的红色按钮
* `.btn .btn-inverse`：这将产生一个带白色文本的黑色按钮
* `.btn .btn-link`：这会在保持按钮行为的同时产生链接

这些按钮也可以通过添加.btn-large类，添加.btn-small类或添加.btn-mini类来声明性地调整大小：

![](/assets/41.png)



A button can be disabled by adding it as a **disabled **attribute. Similarly, a `<a>` tagged button can be disabled with the addition of a `.disabled class`. We didn't make use of buttons yet, but it is a great feature to be presented at this point.

可以通过将按钮添加为**禁用**属性来禁用按钮。 类似地，可以通过添加`.disabled class`来禁用`<a>`标记的按钮。 我们没有使用按钮，但它是一个伟大的功能，在这一点上提出。



**Icons**

Bootstrap 2 comes with an impressive set of 140 dark gray icons available as sprites and provided by Glyphicons:

Bootstrap 2提供了令人印象深刻的140灰色图标集作为精灵，由Glyphicons提供：

![](/assets/42.png)

> These icons are normally available commercially but they are also usable for free as part of the Bootstrap product. However Bootstrap asks us to provide an optional backlink to http://glyphicons.com.
>
> 这些图标通常是商业上可用的，但它们也可作为Bootstrap产品的一部分免费使用。 但是Bootstrap要求我们提供一个可选的反向链接到http://glyphicons.com。



All these icons can be pulled from the DOM with a simple class within a `<i>` tag such as `<i class="icon-search"></i>`.

The amazing thing is that you can actually embed these icons in every suitable Bootstrap component. For example, the button definition: `<a class="btn btn-mini" href="#"><i class="icon-star"></i> Star</a>`, produces the following:

所有这些图标都可以使用`<i>`标签（例如`<i class="icon-search"></i>.`）中的简单类从DOM中拉出。

令人惊讶的是，你可以在每个合适的Bootstrap组件中嵌入这些图标。 例如，按钮定义：`<a class="btn btn-mini" href="#"><i class="icon-star"></i> Star</a>`，生成以下内容：

![](/assets/43.png)



**Tables**

We have implemented a Bootstrap table for the market activity overview. We have basically shaped the following table:

我们已经为市场活动概述实现了一个Bootstrap表。 我们基本上塑造了下表：





```
<table class="table table-hover table-condensed table-bordered
table-striped">
<thead>
<tr><th>Index</th>
<th>Value</th>
<th>Change</th></tr>
</thead>
<tbody>
<tr><td>...</td>
<td>...</td>
<td>...</td>
</tr>
</tbody>
</table>
```

