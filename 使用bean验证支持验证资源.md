After introducing the request-payload data binding process, we must talk about validation.

在介绍request-payload data binding过程之后，我们必须谈论验证。

## Getting ready

The goal of this recipe is to show how to get Spring MVC to reject request body payloads that are not satisfying a bean validation \(JSR-303\) or not satisfying the constraints of a defined Spring validator implementation.

After the Maven and Spring configuration, we will see how to bind a validator to an incoming request, how to define the validator to perform custom rules, how to set up a JSR-303 validation, and how to handle the validation results.

这个方法的目的是展示如何让Spring MVC拒绝不满足bean验证（JSR-303）或不满足定义的Spring验证器实现的约束的请求体有效载荷。

在Maven和Spring配置之后，我们将看到如何将验证器绑定到传入的请求，如何定义验证器来执行自定义规则，如何设置JSR-303验证以及如何处理验证结果。

## How to do it…

1. We added a Maven dependency to the hibernate validator:

1.我们向hibernate验证器添加了一个Maven依赖项：

```java
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>4.3.1.Final</version>
</dependency>
```

2.A LocalValidatorFactoryBean has been registered in our dispatcher-servlet.xml \(cloudstreetmarket-api\):

2.在我们的dispatcher-servlet.xml（cloudstreetmarket-api）中注册了一个LocalValidatorFactoryBean：

```java
<bean id="validator" class="org.sfw.validation.beanvalidation.LocalValidatorFactoryBean"/>
```

3.The UsersController and TransactionController have seen their POST and PUT method signature altered with the addition of a `@Valid` annotation on the `@RequestBody` arguments:

3.UsersController和TransactionController已经看到他们的POST和PUT方法签名改变，在`@RequestBody`参数上添加了`@Valid`注解：

```java
@RequestMapping(method=PUT)
@ResponseStatus(HttpStatus.OK)
public void update(@Valid @RequestBody User user, BindingResult result){
    ValidatorUtil.raiseFirstError(result);
    user = communityService.updateUser(user);
}
```

> Note here the BindingResult object injected as method argument.Also we will present the ValidatorUtil class in about a minute.
>
> 注意这里注入的BindingResult对象作为方法参数。我们将在大约一分钟内呈现ValidatorUtil类。

4.Our two CRUD controllers now have a new `@InitBinder` annotated method:

4.我们的两个CRUD控制器现在有一个新的`@InitBinder`注解方法：

```java
@InitBinder
protected void initBinder(WebDataBinder binder) {
    binder.setValidator(new UserValidator());
}
```

5.This method binds an instance of a created Validator implementation to the requests.Check out the created UserValidator which is Validator implementation:

5.此方法将创建的Validator实现的实例绑定到请求。检查创建的UserValidator，它是Validator实现：

```java
package edu.zipcloud.cloudstreetmarket.core.validators;
import java.util.Map;
import javax.validation.groups.Default;
import org.springframework.validation.Errors;
import org.springframework.validation.Validator;
import edu.zc.csm.core.entities.User;
import edu.zc.csm.core.util.ValidatorUtil;
public class UserValidator implements Validator {

    @Override
    public boolean supports(Class<?> clazz) {
        return User.class.isAssignableFrom(clazz);
    }

    @Override
    public void validate(Object target, Errors err) {
        Map<String, String> fieldValidation =ValidatorUtil.validate((User)target, Default.class);
        fieldValidation.forEach((k, v) -> err.rejectValue(k, v));
    }
}
```

6.In the User entity, a couple of special annotations have been added:

6.在用户实体中，添加了几个特殊注解：

```java
@Entity
@Table(name="users")
public class User extends ProvidedId<String> implements UserDetails{
    ...
    private String fullName;

    @NotNull
    @Size(min=4, max=30)
    private String email;

    @NotNull
    private String password;

    private boolean enabled = true;

    @NotNull
    @Enumerated(EnumType.STRING)
    private SupportedLanguage language;

    private String profileImg;

    @Column(name="not_expired")
    private boolean accountNonExpired;

    @Column(name="not_locked")
    private boolean accountNonLocked;

    @NotNull
    @Enumerated(EnumType.STRING)
    private SupportedCurrency currency;

    private BigDecimal balance;
    ...
}
```

7.We have created the ValidatorUtil class to make those validations easier and to reduce the amount of boilerplate code:

7.我们创建了ValidatorUtil类，以使这些验证更容易，并减少样板代码的数量：

```java
package edu.zipcloud.cloudstreetmarket.core.util;
import java.util.Arrays;
import java.util.HashMap;
import java.util.Map;
import java.util.Set;
import javax.validation.ConstraintViolation;
import javax.validation.Validation;
import javax.validation.Validator;
import javax.validation.ValidatorFactory;
import javax.validation.groups.Default;
import org.springframework.validation.BindingResult;

public class ValidatorUtil {

    private static Validator validator;
    static {
        ValidatorFactory factory = Validation.buildDefaultValidatorFactory();
        validator = factory.getValidator();
}
```

The following validate method allows us to call for a JSR validation from whichever location that may require it:

以下验证方法允许我们从任何可能需要它的位置调用JSR验证：

```java
public static <T> Map<String, String> validate(T object,Class<?>... groups) {

        Class<?>[] args = Arrays.copyOf(groups, groups.length + 1);
        args[groups.length] = Default.class;
        return extractViolations(validator.validate(object, args));
    }

    private static <T> Map<String, String> extractViolations(Set<ConstraintViolation<T>> violations) {
        Map<String, String> errors = new HashMap<>();
        for (ConstraintViolation<T> v: violations) {
            errors.put(v.getPropertyPath().toString(), 
                    "["+v.getPropertyPath().toString()+"] " + StringUtils.capitalize(v.getMessage()));
        }
        return errors;
    }
```

The following raiseFirstError method is not of a specific standard, it is our way of rendering to the client the server side errors:

下面的raiseFirstError方法不是一个特定的标准，它是我们向客户端呈现服务器端错误的方式：

```java
public static void raiseFirstError(BindingResult result){
        if (result.hasErrors()) {
            throw new IllegalArgumentException(result.getAllErrors().get(0).getCode());
        }else if (result.hasGlobalErrors()) {
            throw new IllegalArgumentException(result.getGlobalError().getDefaultMessage());
        }
    }
}
```

8.As per Chapter 4, Building a REST API for a Stateless Architecture, the cloudstreetmarket-api's RestExceptionHandler is still configured to handle IllegalArgumentExceptions, rendering them with ErrorInfo formatted responses:

8.根据第4章为无状态体系结构构建REST API，cloudstreetmarket-api的RestExceptionHandler仍被配置为处理IllegalArgumentExceptions，并使用ErrorInfo格式化响应来呈现它们：

```java
@ControllerAdvice
public class RestExceptionHandler extends ResponseEntityExceptionHandler {

    @Autowired
    private ResourceBundleService bundle;

    @Override
    protected ResponseEntity<Object> handleExceptionInternal(Exception ex, Object body, HttpHeaders headers, 
                HttpStatus status, WebRequest request) {

        ErrorInfo errorInfo = null;
        if(body!=null && bundle.containsKey(body.toString())){
            String key = body.toString();
            String localizedMessage = bundle.get(key);
            errorInfo = new ErrorInfo(ex, localizedMessage, key, status);
        }else{
            errorInfo = new ErrorInfo(ex, (body!=null) ? body.toString() : null, null, status);
        }
        return new ResponseEntity<Object>(errorInfo, headers, status);
    }

    @ExceptionHandler({InvalidDataAccessApiUsageException.class,DataAccessException.class,IllegalArgumentException.class })
    protected ResponseEntity<Object> handleConflict(final RuntimeException ex, final WebRequest request) {

        return handleExceptionInternal(ex,I18N_API_GENERIC_REQUEST_PARAMS_NOT_VALID, 
                    new HttpHeaders(), BAD_REQUEST, request);
    }
}
```

9.Navigating through the UI improvements, you will notice a new form for updating the user's **Preferences**. This form is accessible via the **Login **menu, as shown in the following screenshots:

9.浏览UI改进，您会注意到一个新的表单，用于更新用户的**Preferences**。 可以通过**Login **菜单访问此表单，如以下屏幕截图所示：

![](/assets/119.png)

![](/assets/120.png)

10.In this user **Preferences **form, when the frontend validations are deactivated\(frontend validations will be developed in the last recipe of this chapter\), not filling the e-mail field results in the following \(customizable\) ErrorInfo object in the HTTP response:

10.在此用户**Preferences **表单中，当前端验证被禁用（前端验证将在本章的最后一个配方中开发）时，不填写电子邮件字段会在HTTP响应中产生以下（可定制的）ErrorInfo对象：

```java
{
    "error":"[email] Size must be between 4 and 30",
    "message":"The request parameters were not valid!",
    "i18nKey":"error.api.generic.provided.request.parameters.not.valid",
    "status":400,
    "date":"2016-01-05 05:59:26.584"
}
```

11.On the frontend side, in order to handle this error, the accountController\(in account\_management.js\) is instantiated with a dependency to a custom errorHandler factory. The code is as follows:

11.在前端，为了处理此错误，accountController（在account\_management.js中）使用对自定义errorHandler工厂的依赖关系实例化。 代码如下：

```java
cloudStreetMarketApp.controller('accountController',function ($scope, $translate, $location, errorHandler,accountManagementFactory, httpAuth, genericAPIFactory){
    $scope.form = {
        id: "",
        email: "",
        fullName: "",
        password: "",
        language: "EN",
        currency: "",
        profileImg: "img/anon.png"
    };
    ...
}
```

12.The accountController has an update method that invokes the errorHandler.renderOnForm method:

12.accountController有一个update方法，它调用errorHandler.renderOnForm方法：

```js
$scope.update = function () {
    $scope.formSubmitted = true;
    if(!$scope.updateAccount.$valid) {
        return;
    }
        httpAuth.put('/api/users', JSON.stringify($scope.form)).success(function(data, status, headers, config) {
            httpAuth.setCredentials($scope.form.id,$scope.form.password);
            $scope.updateSuccess = true;
        }).error(function(data, status, headers, config) {
            $scope.updateFail = true;
            $scope.updateSuccess = false;
            $scope.serverErrorMessage = errorHandler.renderOnForms(data);
        }
    );
};
```

13.The errorHandler is defined as follows in main\_menu.js. It has the capability to pull translations messages from i18n codes:

13.errorHandler在main\_menu.js中定义如下。 它有能力从i18n代码拉取翻译消息：

```java
cloudStreetMarketApp.factory("errorHandler", ['$translate',function ($translate) {
    return {
        render: function (data) {
        if(data.message && data.message.length > 0){
            return data.message;
        }else if(!data.message && data.i18nKey && data.i18nKey.length > 0){
            return $translate(data.i18nKey);
        }
        return $translate("error.api.generic.internal");
        },

        renderOnForms: function (data) {
            if(data.error && data.error.length > 0){
                return data.error;
            }else if(data.message && data.message.length > 0){
                return data.message;
            }else if(!data.message && data.i18nKey && data.i18nKey.length > 0){
                return $translate(data.i18nKey);
            }
            return $translate("error.api.generic.internal");
        }
    }
}]);
```

The **Preferences **form is as shown here:

**Preferences **窗体如下所示：

![](/assets/121.png)

> As we said, to simulate this error, frontend validations need to be deactivated. This can be done adding a `novalidate`attribute to the `<form name="updateAccount" … novalidate>`markup in user-account.html.
>
> 正如我们所说，为了模拟这个错误，前端验证需要停用。 这可以通过在user-account.html中的`<form name =“updateAccount”... novalidate>` markup添加`novalidate`属性来完成。

14.Back in the server side, we have also created a custom validator for the financial Transaction Entity. This validator makes use of the Spring ValidationUtils:

14.回到服务器端，我们还为财务事务实体创建了一个自定义验证器。 这个验证器利用Spring ValidationUtils：

```java
@Component
public class TransactionValidator implements Validator {

    //
    @Override
    public boolean supports(Class<?> clazz) {
        return Transaction.class.isAssignableFrom(clazz);
    }

    @Override
    public void validate(Object target, Errors errors) {
        ValidationUtils.rejectIfEmpty(errors, "quote", "transaction.quote.empty");
        ValidationUtils.rejectIfEmpty(errors, "user", "transaction.user.empty");
        ValidationUtils.rejectIfEmpty(errors, "type", "transaction.type.empty");
    }
}
```

## How it works...

### Using Spring validator

Spring offers a Validator interface \(org.sfw.validation.Validator\) for creating components to be injected or instantiated in the layer we want. Therefore, Spring validation components can be used in Spring MVC Controllers. The Validator interface is the following:

Spring提供了一个Validator接口\(org.sfw.validation.Validator\) ，用于创建要在我们想要的层中注入或实例化的组件。 因此，Spring validation components可以在Spring MVC控制器中使用。 验证器接口如下：

```java
public interface Validator {
    boolean supports(Class<?> clazz);
    void validate(Object target, Errors errors);
}
```

The `supports(Class<?> clazz)` method is used to assess the domain of a Validator implementation, and also to restrict its use to a specific Type or `super-Type`.

The validate\(Object target, Errors errors\) method imposes its standard so that the validation logic of the validator lives in this place. The passed target object is assessed,and the result of the validation is stored in an instance of the `org.springframework.validation.Errors` interface. A partial preview of the Errors interface is shown here:

`supports(Class<?> clazz)`方法用于评估Validator实现的域，并将其使用限制为特定的Type或`super-Type`。

`validate(Object target, Errors errors)`方法强加了它的标准，使得验证器的验证逻辑在这个地方。 将评估传递的目标对象，并将验证结果存储在`org.springframework.validation.Errors`接口的实例中。 错误界面的部分预览如下所示：

```java
public interface Errors {
    ...
    void reject(String errorCode);
    void reject(String errorCode, String defaultMessage);
    void reject(String errorCode, Object[] errorArgs, String defaultMessage);
    void rejectValue(String field, String errorCode);
    void rejectValue(String field, String errorCode, String defaultMessage);
    void rejectValue(String field, String errorCode, Object[] errorArgs, String defaultMessage);
    void addAllErrors(Errors errors);
    boolean hasErrors();
    int getErrorCount();
    List<ObjectError> getAllErrors();
     ...
}
```

Using Spring MVC, we have the possibility to bind and trigger a Validator to a specific method-handler. The framework looks for a validator instance bound to the incoming request.We have configured such a binding in our recipe at the fourth step:

使用Spring MVC，我们有可能绑定和触发一个Validator到一个特定的方法处理程序。 框架寻找绑定到传入请求的验证器实例。我们在第四步中在我们的配方中配置了这样的绑定：

```java
@InitBinder
protected void initBinder(WebDataBinder binder) {
    binder.setValidator(new UserValidator());
}
```

> We have already used the `@InitBinder` annotation to attach other objects \(formatters\) to incoming requests \(see the Binding requests, marshalling responses recipe of the Chapter 4, Building a REST API for a Stateless Architecture\).
>
> 我们已经使用`@InitBinder`注释将其他对象（formatters）附加到传入的请求（请参阅第4章“为无状态体系结构构建REST API”的绑定请求，编组响应配方）。

The Binders \(org.springframework.validation.DataBinder\) allow setting property values onto a target object. Binders also provide support for validation and binding-results analysis.

The `DataBinder.validate()`method is called after each binding step and this method calls the validate of the primary validator attached to the DataBinder.

The binding-process populates a result object, which is an instance of the `org.springframework.validation.BindingResult`interface. This result object can be retrieved using the `DataBinder.getBindingResult()` method.

Actually, a BindingResult implementation is also an Errors implementation \(as shown here\). We have presented the Errors interface earlier. Check out the following code:

绑定器\(`org.springframework.validation.DataBinder`\)允许在目标对象上设置属性值。  Binders还提供对验证和绑定结果分析的支持。

在每个绑定步骤后调用`DataBinder.validate()` 方法，此方法调用附加到DataBinder的主验证器的验证。

binding-process填充结果对象，它是`org.springframework.validation.BindingResult`接口的一个实例。 可以使用`DataBinder.getBindingResult()`方法检索此结果对象。

实际上，BindingResult实现也是一个Errors实现（如下所示）。 我们已经提出了错误接口。 查看以下代码：

```java
public interface BindingResult extends Errors {
    Object getTarget();
    Map<String, Object> getModel();
    Object getRawFieldValue(String field);
    PropertyEditor findEditor(String field, Class<?> valueType);
    PropertyEditorRegistry getPropertyEditorRegistry();
    void addError(ObjectError error);
    String[] resolveMessageCodes(String errorCode);
    String[] resolveMessageCodes(String errorCode, String field);
    void recordSuppressedField(String field);
    String[] getSuppressedFields();
}
```

The whole design can be summarized as follows:

We create a validator implementation. When an incoming request comes in for a specific Controller method handler, the request payload is converted into the class that is targeted by the`@RequestBody` annotation \(an Entity in our case\). An instance of our validator implementation is bound to the injected `@RequestBody` object. If the injected `@RequestBody` object is defined with a `@Valid` annotation, the framework asks DataBinder to validate the object on each binding step and to store errors in the

`BindingResultobject` of DataBinder.

Finally, this BindingResult object is injected as argument of the method handler, so we can decide what to do with its errors \(if any\). During the binding process, missing fields and property access exceptions are converted into FieldErrors. These FieldErrors are also stored into the Errors instance. The following error codes are used for FieldErrors:

整体设计可概括如下：

我们创建一个验证器实现。 当特定Controller方法处理程序的传入请求进入时，请求有效内容将转换为由`@RequestBody`注解（在我们的示例中为实体）定向的类。 我们的验证器实现的实例绑定到注入的`@RequestBody`对象。 如果注入的`@RequestBody`对象用`@Valid`注解定义，则框架要求DataBinder在每个绑定步骤上验证对象，并在DataBinder的`BindingResultobject`中存储错误。

最后，这个BindingResult对象被注入作为方法处理程序的参数，所以我们可以决定如何处理它的错误（如果有的话）。 在绑定过程中，缺少字段和属性访问异常将转换为FieldErrors。 这些FieldErrors也存储在Errors实例中。 以下错误代码用于FieldErrors：

```java
Missing field error: "required"
Type mismatch error: "typeMismatch"
Method invocation error: "methodInvocation"
```

When it is necessary to return nicer error messages for the user, a MessageSource helps us to process a lookup and retrieve the right localized message from a MessageSourceResolvable implementation with the following method:

当有必要为用户返回更好的错误消息时，MessageSource帮助我们处理查找并从MessageSourceResolvable实现中检索正确的本地化消息，使用以下方法：

```java
MessageSource.getMessage(org.sfw.context.MessageSourceResolvable, java.util.Locale).
```

> The FieldError extends ObjectError and ObjectError extends DefaultMessageSourceResolvable, which is a MessageSourceResolvable implementation.
>
> FieldError扩展ObjectError，ObjectError扩展DefaultMessageSourceResolvable，它是一个MessageSourceResolvable实现。

### ValidationUtils

The ValodationUtils utility class \(org.sfw.validation.ValidationUtils\) provides a couple of convenient static methods for invoking validators and rejecting empty fields. These utility methods allow one-line assertions that also handle at the same time, the population of the Errors objects. In this recipe, the 14th step details our TransactionValidator that makes use of ValidationUtils.

ValodationUtils实用程序类（org.sfw.validation.ValidationUtils）提供了一些方便的静态方法来调用验证器和拒绝空字段。 这些实用程序方法允许一行断言同时处理Errors对象的总体。 在此配方中，第14步详细介绍了使用ValidationUtils的TransactionValidator。

### I18n validation errors

The next recipe will focus on internationalization of errors and content. However, let's see how we catch our errors from the controllers and how we display them. The update method of UserController has this custom method call on its first line:

下一个谱方将关注错误和内容的国际化。 然而，让我们看看我们如何从控制器捕获我们的错误，以及如何显示它们。  UserController的update方法在其第一行具有此自定义方法调用：

```java
ValidatorUtil.raiseFirstError(result);
```

We created the ValidatorUtil support class for our needs; the idea was to throw an IllegalArgumentException for any type of error that can be detected by our validator.The `ValidatorUtil.raiseFirstError(result)` method call can also be found in the `TransactionController.update(…)` method-handler. This method-handler relies on the TransactionValidator presented in the 14th step.

If you remember this TransactionValidator, it creates an error with a `transaction.quote.empty` message code when a quote object is not present in the financial Transaction object. An IllegalArgumentException is then thrown with the `transaction.quote.empty`message detail.

In the next recipe, we will revisit how a proper internationalized JSON response is built and sent back to the client from an IllegalArgumentException.

我们为我们的需要创建了ValidatorUtil支持类; 这个想法是为任何类型的错误抛出一个IllegalArgumentException可以被我们的验证器检测。`ValidatorUtil.raiseFirstError(result)`方法调用也可以在`TransactionController.update(…)`方法处理程序中找到。 此方法处理程序依赖于第14步中提供的TransactionValidator。

如果您记住此TransactionValidator，当金融交易对象中不存在报价对象时，会使用`transaction.quote.empty`消息代码创建错误。 然后使用`transaction.quote.empty`消息详细信息抛出`IllegalArgumentException`。

在下一个配方中，我们将重新考虑一个正确的国际化JSON响应是如何构建的，并从IllegalArgumentException发送回客户端。

### Using JSR-303/JSR-349 Bean Validation

Spring Framework version 4 and above supports bean validation 1.0 \(JSR-303\) and bean validation 1.1 \(JSR-349\). It also adapts this bean validation to work with the Validator interface, and it allows the creation of class-level validators using annotations.

The two specifications, JSR-303 and JSR-349, define a set of constraints applicable to beans,as annotations from the `javax.validation.constraints` package.

使用JSR-303 / JSR-349 Bean验证

Spring Framework版本4及以上版本支持bean validation 1.0 \(JSR-303\)和bean validation 1.1 \(JSR-349\)。 它还使这个bean验证适用于Validator接口，并允许使用注释创建类级验证器。

这两个规范JSR-303和JSR-349定义了一组适用于bean的约束，作为`javax.validation.constraints`包中的注释。

Generally, a big advantage of using the code from specifications instead of the code from implementations is that we don't have to know which implementation is used. Also, the implementation can always potentially be replaced with another one.

Bean validation was originally designed for persistent beans. Even if the specification has a relatively low coupling to JPA, the reference implementation stays Hibernate validator. Having a persistence provider that supports those validation specifications is definitely an advantage.Now with JPA2, the persistent provider automatically calls for JSR-303 validation before persisting. Ensuring such validations from two different layers \(controller and model\) raises our confidence level.

通常，使用规范中的代码而不是实现中的代码的一个很大的优点是，我们不必知道使用哪个实现。 此外，实现可以总是潜在地替换为另一个。

Bean验证最初是为持久性bean设计的。 即使规范与JPA的耦合相对较低，参考实现仍然保持Hibernate validator。 拥有支持这些验证规范的持久性提供程序绝对是一个优势。现在使用JPA2，持久性提供程序在持久化之前自动调用JSR-303验证。 确保来自两个不同层（控制器和模型）的这种验证提高了我们的置信水平。

### On-field constraint annotations

We defined the `@NotNull` and `@Size` JSR-303 annotations on the presented User entity.There are obviously more than two annotations to be found in the specification.

Here's a table summarizing the package of annotations \(`javax.validation.constraints`\) in JEE7:

字段约束注释

我们在提供的用户实体上定义了`@NotNull`和`@Size JSR-303`注释。在规范中显然有两个以上的注释。

下面是一个总结JEE7中注释包\(`javax.validation.constraints`\)的表：

![](/assets/122.png)

![](/assets/123.png)

Implementation-specific constraints

Bean validation implementations can also go beyond the specification and offer their set of extra validation annotations. Hibernate validator has a few interesting ones such as `@NotBlank`, `@SafeHtml`, `@ScriptAssert`, `@CreditCardNumber`, `@Email`, and so on.These are all listed from the hibernate documentation accessible at the following URL

实现特定的约束

Bean验证实现也可以超出规范，并提供他们的一组额外的验证注释。  Hibernate validator有一些有趣的例子，如`@NotBlank`，`@SafeHtml`，`@ScriptAssert`，`@CreditCardNumber`，`@Email`，等等。这些都从hibernate文档列出，可以在以下URL访问：

[http://docs.jboss.org/hibernate/validator/4.3/reference/en-US/html\\_single/\\#table-custom-constraints](http://docs.jboss.org/hibernate/validator/4.3/reference/en-US/html\_single/\#table-custom-constraints)

LocalValidator \(reusable\)

We have defined the following validator bean in our Spring context:

我们在我们的Spring上下文中定义了下面的验证器bean：

```
<bean id="validator" class="org.sfw.validation.beanvalidation.LocalValidatorFactoryBean"/>
```

This bean produces validator instances that implement JSR-303 and JSR-349. You can configure a specific provider class here. By default, Spring looks in the classpath for the Hibernate Validator JAR. Once this bean is defined, it can be injected wherever it is needed.

We have injected such validator instances in our UserValidator and this makes it compliant with JSR-303 and JSR-349.

For internationalization, the validator produces its set of default message codes. These default message codes and values look like the following ones:

此bean生成实现JSR-303和JSR-349的验证器实例。 您可以在此处配置特定的提供程序类。 默认情况下，Spring在Hibernate Validator JAR的类路径中查找。 一旦这个bean被定义，它可以注入任何需要它。

我们在我们的UserValidator中注入了这样的验证器实例，这使它符合JSR-303和JSR-349。

对于国际化，验证器产生其一组默认消息代码。 这些默认消息代码和值类似于以下内容：

```
javax.validation.constraints.Max.message=must be less than or equal to {value}
javax.validation.constraints.Min.message=must be greater than or equal to {value}
javax.validation.constraints.Pattern.message=must match "{regexp}"
javax.validation.constraints.Size.message=size must be between {min} and {max}
```

Feel free to override them in your own resource files!

随意在你自己的资源文件中覆盖它们！

## There's more…

In this section we highlight a few validation concepts and components that we didn’t explain.

在本节中，我们强调了一些我们没有解释的验证概念和组件。

### ValidationUtils

The ValidationUtils Spring utility class provides convenient static methods for invoking a Validator and rejecting empty fields populating the error object in one line:

ValidationUtils Spring实用程序类提供了方便的静态方法，用于调用Validator并拒绝填充错误对象的空字段在一行中：

[http://docs.spring.io/spring/docs/3.1.x/javadoc-api/org/](http://docs.spring.io/spring/docs/3.1.x/javadoc-api/org/)  
springframework/validation/ValidationUtils.html

### Grouping constraints

We can couple constraints across more than one field to define a set of more advanced constraints:

我们可以跨多个字段耦合约束以定义一组更高级的约束：

[http://beanvalidation.org/1.1/spec/\\#constraintdeclarationvalidationprocess-groupsequence](http://beanvalidation.org/1.1/spec/\#constraintdeclarationvalidationprocess-groupsequence)

[http://docs.jboss.org/hibernate/stable/validator/reference/en-US/html\\_single/\\#chapter-groups](http://docs.jboss.org/hibernate/stable/validator/reference/en-US/html\_single/\#chapter-groups)

### Creating a custom validator

It can sometimes be useful to create a specific validator that has its own annotation. Check link, it should get us to:

有时可能有用的是创建具有其自己的注释的特定验证器。 检查链接，它应该让我们：

[http://howtodoinjava.com/2015/02/12/spring-mvc-custom-validator-example/](http://howtodoinjava.com/2015/02/12/spring-mvc-custom-validator-example/)

### The Spring reference on validation

The best source of information remains the Spring reference on Validation. Check link, it should get us to:

最好的信息来源仍然是Spring参考验证。 检查链接，它应该让我们：

[http://docs.spring.io/spring/docs/current/spring-framework-reference/html/validation.html](http://docs.spring.io/spring/docs/current/spring-framework-reference/html/validation.html)

