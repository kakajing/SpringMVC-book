It is a good practice to validate submitted data both on the frontend and the backend. It is also good, talking about validation, to distinguish the user experience, from the data integrity conservation from the data integrity conservation. Both are two different responsibilities,potentially for different teams.

We believe the frontend validation has replaced the form-validations that was previously managed by the backend. In a scalable environment where API is decoupled from web content,the validation experiences are now the responsibility of client interfaces that can be multiples\(even implemented by third parties\) such as websites, mobile websites, mobile apps, and so on.

In this recipe, we will focus on form validation and more specifically on AngularJS form validation.

最好在前端和后端验证提交的数据。 它也不错，讨论关于validation，区分用户体验，从数据完整性保护与数据完整性保护。 可能对不同的团队两者都是两个不同的职责。

我们认为前端验证已替换之前由后端管理的表单验证。 在API与Web内容解耦的可扩展环境中，验证体验现在是客户端接口的责任，客户端接口可以是多个（甚至由第三方实现），例如网站，移动网站，移动应用等。

在这个配方中，我们将重点关注表单验证，更具体地关注AngularJS表单验证。

## How to do it…

1. Let's consider the **User Preferences** form again. Here is the HTML definition \(user-account.html\):

1.让我们再次考虑**User Preferences**表单。 这里是HTML定义（user-account.html）：

```
<form name="updateAccount" action="#" ngclass="formSubmitted ? 'submitted':''">
    <fieldset>
        <div class="clearfix span">
            <label for="id" translate>screen.preference.field.username</label>
            <div class="input">
                <input type="text" name="id" placeholder="Username" ngmodel="form.id" ng-minlength="4" ng-maxlength="15" readonly required/>
                <span class="text-error" ng-show="formSubmitted && updateAccount.id.$error.required" translate>
                    error.webapp.user.account.username.required
                </span>
            </div>
            
            <label for="email" translate>screen.preference.field.email</label>
            <div class="input">
                <input type="email" name="email" placeholder="Email" ngmodel="form.email"/>
                <span class="text-error" ng-show="formSubmitted && updateAccount.email.$error" translate>
                    error.webapp.user.account.email
                </span>
            </div>
            
            <label for="password" translate>screen.preference.field.password</label>
            <div class="input">
                <input type="password" name="password" ng-minlength="5" placeholder="Please type again" ng-model="form.password" required/>
                <span class="text-error" ng-show="formSubmitted && updateAccount.password.$error.required" translate>
                    error.webapp.user.account.password.type.again
                </span>
                <span class="text-error" ng-show="formSubmitted && updateAccount.password.$error.minlength" translate>
                    error.webapp.user.account.password.too.short
                </span>
            </div>
            
            <label for="fullname" translate>screen.preference.field.full.name</label>
            <div class="input" >
                <input type="text" name="fullname" placeholder="Full name" ng-model="form.fullname"/>
            </div>
            
            <label for="currencySelector" translate>screen.preference.field.preferred.currency</label>
            <div class="input">
                <select class="input-small" id="currencySelector" ngmodel="form.currency" ng-init="form.currency='USD'" ngselected="USD" ng-change="updateCredit()">
                    <option>USD</option><option>GBP</option>
                    <option>EUR</option><option>INR</option>
                    <option>SGD</option><option>CNY</option>
                </select>
            </div>
            <label for="currencySelector" translate>screen.preference.field.preferred.language</label>
            <div class="input">
                <div class="btn-group">
                    <button onclick="return false;" class="btn" tabindex="-1">
                        <span class="lang-sm lang-lbl" lang="{{form.language | lowercase}}">
                    </button>
                    <button class="btn dropdown-toggle" data-toggle="dropdown" tabindex="-1">
                    <span class="caret"></span>
                    </button>
                    <ul class="dropdown-menu">
                        <li>
                            <a href="#" ng-click="setLanguage('EN')">
                                <span class="lang-sm lang-lbl-full" lang="en"></span>
                            </a>
                        </li>
                        <li>
                            <a href="#" ng-click="setLanguage('FR')"> 
                                <spanclass="lang-sm lang-lbl-full" lang="fr"></span>
                            </a>
                        </li>
                    </ul>
                </div>
            </div>
        </div>
    </fieldset>
</form>
```

2. The JavaScript side of things in the controller of account\_management.js includes two referenced functions and four variables to control form validation and its style:

2. account\_management.js控制器中的JavaScript端包括两个引用的函数和四个变量来控制表单验证及其风格：

```
$scope.update = function () {
    $scope.formSubmitted = true;
    if(!$scope.updateAccount.$valid) {
        return;
    }
    httpAuth.put('/api/users', JSON.stringify($scope.form)).success(function(data, status, headers, config) {
        httpAuth.setCredentials($scope.form.id, $scope.form.password);
        $scope.updateSuccess = true;
    }).error(function(data,status,headers,config) {
        $scope.updateFail = true;
        $scope.updateSuccess = false;
        $scope.serverErrorMessage = errorHandler.renderOnForms(data);
    });
};
$scope.setLanguage = function(language) {
    $translate.use(language);
    $scope.form.language = language;
}
    //Variables initialization
    $scope.formSubmitted = false;
    $scope.serverErrorMessage ="";
    $scope.updateSuccess = false;
    $scope.updateFail = false;
```

Two CSS classes have been created to render properly errors on fields:

已创建两个CSS类以正确地在字段上呈现错误：

```
.submitted input.ng-invalid{
    border: 2px solid #b94a48;
    background-color: #EBD3D5;
    !important;
}
.submitted .input .text-error {
    font-weight:bold;
    padding-left:10px;
}
```

3. If you try to enter a wrong e-mail or if you try to submit the form without entering your password, you should observe the following validation control:

3.如果您尝试输入错误的电子邮件，或者尝试在不输入密码的情况下提交表单，则应遵循以下验证控制：

![](/assets/128.png)

## How it works...

AngularJS provides tools to set up a client-side form validation. As usual with AngularJS, these tools integrate well with modern HTML5 techniques and standards.

HTML5 forms provide a native validation that can be defined using tags and attributes on the different form elements \(input, select…\) to set up a basic field validation \(max-length, required…\)

AngularJS completes and extends fluently these standard definitions to make them interactive and responsive from the beginning and with no overhead.

AngularJS提供了设置客户端表单验证的工具。 像AngularJS一样，这些工具与现代HTML5技术和标准很好地集成。

HTML5表单提供了本地验证，可以使用不同表单元素（input, select...）上的标记和属性来设置基本字段验证（max-length，required ...）

AngularJS完成和扩展这些标准定义，使它们从一开始就具有交互性和响应性，没有开销。

### Validation-constraints

Let's have a closer look at the available validation-options that can be placed on form controls.

让我们仔细看看可用于表单控件的可用验证选项。

### Required

An input field can be tagged as required \(HTML5 tag\):

输入字段可以根据需要进行标记（HTML5标记）：

```
<input type="text" required />
```

### Minimum/maximum length

The ng-minlength directive can be used to assert that the number of entered characters matches a given threshold:

ng-minlength指令可用于断言输入字符数与给定阈值相匹配：

```
<input type="text" ng-minlength="3" />
```

Similarly, ng-maxlength drastically limits the number of entered characters to a maximum:

类似地，ng-maxlength将输入字符的数量大幅限制为最大值：

```
<input type="text" ng-maxlength="15" />
```

### Regex pattern

The ng-pattern directive is often used to make sure that the entered data matches a predefined shape:

ng-pattern伪指令通常用于确保输入的数据与预定义的形状匹配：

```
<input type="text" ng-pattern="[a-zA-Z]" />
```

### Number/e-mail/URL

Those HTML5 input types are handled by AngularJS to be constrained within the format they represent:

这些HTML5输入类型由AngularJS处理，以它们所代表的格式进行约束：

```
<input type="number" name="quantity" ng-model="form.quantity" />
<input type="email" name="email" ng-model=" form.email" />
<input type="url" name="destination" ng-model=" form.url" />
```

### Control variables in forms

AngularJS publishes properties on the containing `$scope` to match the form state in the DOM.This makes the JavaScript form validation very easy to control errors and to render the state.

These properties are accessible from the following structure:

控制表单中的变量

AngularJS在包含`$scope`上发布属性以匹配DOM中的表单状态。这使得JavaScript表单验证非常容易控制错误和呈现状态。

这些属性可从以下结构访问：

```
formName.inputFieldName.property
```

### Modified/Unmodified state

This state can be assessed using the following properties:

这种状态可以使用以下属性来评估：

```
formName.inputFieldName.$pristine;
formName.inputFieldName.$dirty;
```

### Valid/Invalid state

This valid state of a form can be assessed in regards to the defined validation for a field or globally:

有效/无效状态

表格的有效状态可以针对字段或全局的定义验证进行评估：

```
formName.inputFieldName.$valid;
formName.inputFieldName.$invalid;
formName.$valid;
formName.$invalid;
```

### Errors

After the validity assessment that we have defined previously, more information about what went wrong can be extracted from the` $error` property:

在我们之前定义的有效性评估之后，有关错误的更多信息可以从`$error`属性中提取：

```
myForm.username.$error.pattern
myForm.username.$error.required
myForm.username.$error.minlength
```

The `$error` object contains all about the validations of a particular form and reflects whether those validations are satisfactory or not.

`$error`对象包含特定表单的所有验证，并反映这些验证是否令人满意。

### Form state transclusions and style

As often with AngularJS, transclusions are proceeded to bind the DOM state with the scope. Thus, the form state and the control state are reflected in real time with CSS classes.These CSS classes can obviously be defined/overridden, so that a global validation style can be defined:

表单状态的转折和风格

与AngularJS经常一样，进行转换以将DOM状态与范围绑定。 因此，表单状态和控制状态实时地反映在CSS类中。这些CSS类显然可以被定义/重写，从而可以定义全局验证风格：

```
input.ng-invalid {
    border: 1px solid red;
}
input.ng-valid {
    border: 1px solid green;
}
```

## See also

* AngularJS documentation on forms: Read more about AngularJS validation capabilities on Forms \(we have only introduced them here\): https://docs.angularjs.org/guide/forms



* 关于表单的AngularJS文档：阅读有关Forms的AngularJS验证功能的更多信息（我们只在这里介绍）：https://docs.angularjs.org/guide/forms



