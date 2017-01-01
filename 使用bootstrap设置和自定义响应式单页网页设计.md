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

3. 
**自定义Bootstrap主题  
**

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

9.作为index.jsp文件的最后一个更改，已对版权部分进行了修改。

