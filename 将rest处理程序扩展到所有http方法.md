This is the core recipe of the chapter. We will detail how to use the Spring MVC methodhandlers for HTTP methods that we haven't covered yet: the non-readonly ones.

这是本章的核心配方。 我们将详细介绍如何使用Spring MVC方法处理程序来处理我们尚未讨论的HTTP方法：非readonly方法。

## Getting ready

We will see the returned status codes and the HTTP standards driving the use of the PUT, POST,and DELETE methods. This will get us to configure HTTP-compliant Spring MVC controllers.

We will also review how request-payload mapping annotations such as `@RequestBody` work under the hood and how to use them efficiently.

Finally, we open a window on Spring transactions, as it is a broad and important topic in itself.

我们将看到返回的状态代码和驱动使用PUT，POST和DELETE方法的HTTP标准。 这将使我们配置HTTP兼容的Spring MVC控制器。

我们还将审查request-payload mapping注释（如`@RequestBody`）如何工作，以及如何有效地使用它们。

最后，我们在Spring事务上打开一个窗口，因为它是一个广泛和重要的话题。

## How to do it…

Following the next steps will present the changes applied to two controllers, a service and a repository:

接下来的步骤将显示应用于两个控制器（服务和存储库）的更改：

1. From the Git Perspective in Eclipse, checkout the latest version of the branch v7.x.x. Then, run a maven clean install on the cloudstreetmarketparent module \(right-click on the module and go to Run as… \| Maven Clean and then again go to Run as… \| Maven Install\) followed by a Maven Update project to synchronize Eclipse with the maven configuration \(right-click on the module and then go to Maven \| Update Project…\).

2. Run the Maven clean and Maven install commands on zipcloud-parent and then on cloudstreetmarket-parent. Then, go to Maven \| Update Project.

3. In this chapter, we are focused on two REST controllers: the UsersController and a newly created TransactionController.

1.从Eclipse中的Git Perspective中，检出最新版本的分支v7.x.x. 然后，在cloudstreetmarket-parent模块上运行maven clean install（右键单击模块，并转到Run as ... \| Maven Clean然后再次运行为... \| Maven Install），随后是一个Maven Update项目，以将Eclipse与 maven配置（右键单击模块，然后转到Maven \|Update Project...）。

2.在zipcloud-parent上，然后在cloudstreetmarket-parent上运行Maven clean和Maven install 命令。 然后，转到Maven \| Update Project。

3.在本章中，我们将重点介绍两个REST控制器：UsersController和一个新创建的TransactionController。

> The TransactionController allows users to process financial transactions \(and thus to buy or sell products\).
>
> TransactionController允许用户处理金融交易（从而购买或销售产品）。

1. A simplified version of UserController is given here:

4.这里给出了UserController的简化版本：

```
@RestController
@RequestMapping(value=USERS_PATH,
produces={"application/xml", "application/json"})
public class UsersController extends CloudstreetApiWCI{

    @RequestMapping(method=POST)
    @ResponseStatus(HttpStatus.CREATED)
    public void create(@RequestBody User user,
            @RequestHeader(value="Spi", required=false) String guid, 
            @RequestHeader(value="OAuthProvider", required=false) String provider,
            HttpServletResponse response) throws IllegalAccessException{
        ...
        response.setHeader(LOCATION_HEADER, USERS_PATH + user.getId());
    }

    @RequestMapping(method=PUT)
    @ResponseStatus(HttpStatus.OK)
    public void update(@RequestBody User user, BindingResult result){
        ...
    }

    @RequestMapping(method=GET)
    @ResponseStatus(HttpStatus.OK)
    public Page<UserDTO> getAll(@PageableDefault(size=10,page=0) Pageable pageable){
        return communityService.getAll(pageable);
    }

    @RequestMapping(value="/{username}", method=GET)
    @ResponseStatus(HttpStatus.OK)
    public UserDTO get(@PathVariable String username){
        return communityService.getUser(username);
    }

    @RequestMapping(value="/{username}", method=DELETE)
    @ResponseStatus(HttpStatus.NO_CONTENT)
    public void delete(@PathVariable String username){
        communityService.delete(username);
    }
}
```

5. The TransactionController is represented here in a simplified version:

5. TransactionController在这里以简化版本表示：

```
@RestController
@ExposesResourceFor(Transaction.class)
@RequestMapping(value=ACTIONS_PATH + TRANSACTIONS_PATH, produces={"application/xml", "application/json"})
public class TransactionController extends CloudstreetApiWCI<Transaction> {

    //(The GET method-handlers given here come from previous recipes.)
    //这里给出的GET方法处理程序来自以前的谱方
    @RequestMapping(method=GET)
    @ResponseStatus(HttpStatus.OK)
    public PagedResources<TransactionResource> search(@RequestParam(value="user", required=false) String userName,
                        @RequestParam(value="quote:[\\d]+", required=false) Long quoteId,
                        @RequestParam(value="ticker:[a-zA-Z0-9-:]+", required=false) String ticker,
                        @PageableDefault(size=10, page=0, sort={"lastUpdate"}, direction=Direction.DESC) Pageable pageable){
    
        Page<Transaction> page = transactionService.findBy(pageable, userName, quoteId,ticker);
        return pagedAssembler.toResource(page, assembler);
    }
    
    @RequestMapping(value="/{id}", method=GET)
    @ResponseStatus(HttpStatus.OK)
    public TransactionResource get(@PathVariable(value="id") Long transactionId){
        return assembler.toResource(transactionService.get(transactionId));
    }
    
    //(The PUT and DELETE method-handlers introduced here are non-readonly methods.)
    //（这里介绍的PUT和DELETE方法处理程序是非独立的方法。）
    @RequestMapping(method=POST)
    @ResponseStatus(HttpStatus.CREATED)
    public TransactionResource post(@RequestBody Transaction transaction) {
    
        transactionService.hydrate(transaction);
        ...
        TransactionResource resource = assembler.toResource(transaction);
        response.setHeader(LOCATION_HEADER, resource.getLink("self").getHref());
        return resource;
    }
    
    @PreAuthorize("hasRole('ADMIN')")
    @RequestMapping(value="/{id}", method=DELETE)
    @ResponseStatus(HttpStatus.NO_CONTENT)
    public void delete(@PathVariable(value="id") Long transactionId){
        transactionService.delete(transactionId);
    }
}
```

6. The call to the hydrate method in the post method prepares the Entity for underlying service uses. It populates its relationships from IDs received in the request payload.

6.在post方法中对hydrate方法的调用准备实体用于基础服务使用。 它根据请求有效内容中收到的ID填充其关系。

> This technique will be applied to all the REST resources used for CRUD.
>
> 此技术将实用于CRUD的所有REST资源。

7. Here are the details of the hydrate method in transactionServiceImpl:

7.以下是transactionServiceImpl中的hydrate 的详细信息：

```
@Override
public Transaction hydrate(final Transaction transaction) {

    if(transaction.getQuote().getId() != null){
        transaction.setQuote(stockQuoteRepository.findOne(transaction.getQuote().getId()));
    }
    
    if(transaction.getUser().getId() != null){
        transaction.setUser(userRepository.findOne(transaction.getUser().getId()));
    }
    
    if(transaction.getDate() == null){
        transaction.setDate(new Date());
    }
    return transaction;
}
```

> Nothing amazing here; it is mainly about building our Entity to suit our needs. An interface can be created to standardize the practice.
>
> 这里没有什么惊人的; 它主要是建立我们的实体，以适应我们的需要。 可以创建一个界面来标准化实践。

8. All the service layers have been reviewed to drive uniform database transactions.

9. The service implementations are now annotated by default with

8.所有服务层已经过审查，以驱动统一数据库事务。

9.现在，服务实现现在已注释为

```
@Transactional(readOnly = true). Check the following
```

TransactionServiceImpl example:

```
@Service
@Transactional(readOnly = true)
public class TransactionServiceImpl implements TransactionService{
    ...
}
```

10. The non-readonly methods of these service implementations override the class definition with the `@Transactional` annotation:

10.这些服务实现的非唯一方法使用`@Transactional`注释覆盖类定义：

```
@Override
@Transactional
public Transaction create(Transaction transaction) {

    if(!transactionRepository.findByUserAndQuote(transaction.getUser(), transaction.getQuote()).isEmpty()){
        throw new DataIntegrityViolationException("A transaction for the quote and the user already exists!");
    }
    return transactionRepository.save(transaction);
}
```

11. This principle has also been applied to custom repository implementations \(such as IndexRepositoryImpl\):

11.这个原则也被应用于自定义仓库实现（如IndexRepositoryImpl）：

```
@Repository
@Transactional(readOnly = true)
public class IndexRepositoryImpl implements IndexRepository{

    @PersistenceContext
    private EntityManager em;
    
    @Autowired
    private IndexRepositoryJpa repo;
    ...
    
    @Override
    @Transactional
    public Index save(Index index) {
        return repo.save(index);
    }
    ...
}
```

## How it works...

First, let's quickly review the different CRUD services presented in the controllers of this recipe. The following table summarizes them:

首先，让我们快速查看本配方的控制器中提供的不同CRUD服务。 下表总结了它们：

![](/assets/115.png)

### HTTP/1.1 specifications – RFC 7231 semantics and content

To understand the few decisions that have been taken in this recipe \(and to legitimate them\),we must shed some light on a few points of the HTTP specification.

Before starting, feel free to visit Internet standards track document \(RFC 7231\) for HTTP 1/1 related to Semantics and Content:

HTTP / 1.1规范 - RFC 7231语义和内容

为了理解在这个谱方（和合法的他们）已经采取的一些决定，我们必须阐明HTTP规范的几个点。

在开始之前，请随意访问与语义和内容相关的HTTP 1/1的Internet标准跟踪文档（RFC 7231）：

https://tools.ietf.org/html/rfc7231

### Basic requirements

In the HTTP specification document, the request methods overview \(section 4.1\) states that it is a requirement for a server to support the GET and HEAD methods. All other request methods are optional.

The same section also specifies that a request made with a recognized method name\(GET, POST, PUT, DELETE, and so on\) but that doesn't match any method-handler should be responded with a 405 Not supported status code. Similarly, a request made with an unrecognized method name \(nonstandard\) should be responded with a 501 Not implemented status code. These two statements are natively supported and auto-configured by Spring MVC.

基本要求

在HTTP规范文档中，请求方法概述（第4.1节）声明，服务器需要支持GET和HEAD方法。 所有其他请求方法是可选的。

同一部分还指定使用已识别的方法名（GET，POST，PUT，DELETE等）但与任何方法处理程序不匹配的请求都应使用405不支持的状态代码进行响应。 类似地，使用无法识别的方法名称（非标准）发出的请求应使用501未实现的状态代码进行响应。 这两个语句由Spring MVC本地支持和自动配置。

### Safe and Idempotent methods

The document introduces introduces the Safe and Idempotent qualifiers that can be used to describe a request method. Safe methods are basically readonly methods. A client using such a method does not explicitly requests a state change and cannot expect a state change as a result of the request.

As the Safe word suggests, such methods can be trusted to not cause any harm to the system.

安全和幂等方法

本文档介绍了可用于描述请求方法的安全和幂等限定符。 安全方法基本上是只读方法。 使用这种方法的客户端不明确地请求状态改变，并且不能期望作为请求的结果的状态改变。

正如Safe字面所暗示的，这种方法可以被信任不会对系统造成任何伤害。

An important element is that we are considering the client's point of view. The concept of Safe methods don't prohibit the system from implementing "potentially" harmful operations or processes that are not effectively read only. Whatever happens, the client cannot be held responsible for it. Among all the HTTP methods, only the GET, HEAD, OPTIONS, and TRACE methods are defined as safe.

The specification makes use of the idempotent qualifier to identify HTTP requests that, when identically repeated, always produce the same consequences as the very first one. The client's point of view must be considered here.

一个重要的因素是我们正在考虑客户的观点。 安全方法的概念不禁止系统实施“潜在”有害操作或不是有效只读的过程。 无论发生什么，客户不能对其负责。 在所有HTTP方法中，只有GET，HEAD，OPTIONS和TRACE方法被定义为安全的。

规范使用幂等限定符来标识HTTP请求，当相同地重复时，总是产生与第一个相同的结果。 在这里必须考虑客户的观点。

The idempotent HTTP methods are GET, HEAD, OPTIONS, TRACE \(the Safe methods\) as well as PUT and DELETE.

A method's idempotence guarantees a client for example that sending a PUT request can be repeated even if a connection problem has occurred before any response is received.

幂等的HTTP方法是GET，HEAD，OPTIONS，TRACE（安全方法）以及PUT和DELETE。

方法的幂等性保证客户端例如即使在接收到任何响应之前发生连接问题，发送PUT请求也可以重复。

> The client knows that repeating the request will have the same intended effect, even if the original request succeeded, though the response might differ.
>
> 客户端知道重复请求将具有相同的预期效果，即使原始请求成功，尽管响应可能不同。

### Other method-specific constraints

The POST methods are usually associated with the creation of resources on a server. Therefore, this method should return the 201 \(Created\) status code with a location header that provides an identifier for the created resource.

However, if there hasn't been creation of resource, a POST method can \(in practice\) potentially return all types of status codes except 206 \(Partial Content\), 304 \(Not Modified\),and 416 \(Range Not Satisfiable\).

其他特定于方法的约束

POST方法通常与在服务器上创建资源相关联。 因此，此方法应返回201（已创建）状态代码，其中带有为创建的资源提供标识符的位置头。

然而，如果没有资源的创建，POST方法（实际上）可能返回除206（部分内容），304（未修改）和416（范围不满足）之外的所有类型的状态码。

The result of a POST can sometimes be the representation of an existing resource. In that case, for example, the client can be redirected to that resource with a 303 status code and a Location header field. As an alternative to POST methods, PUT methods are usually chosen to update or alter the state of an existing resource, sending a 200 \(OK\) or a 204\(No Content\) to the client.

Edge cases with inconsistent matches raise errors with 409 \(Conflict\) or 415 \(Unsupported Media Type\).

POST的结果有时可以是现有资源的表示。 在这种情况下，例如，客户端可以被重定向到具有303状态码和位置报头字段的资源。 作为POST方法的替代方案，通常选择PUT方法来更新或改变现有资源的状态，向客户端发送200（OK）或204（No Content）。

具有不一致匹配的边缘案例引起409（冲突）或415（不支持的介质类型）的错误。

Edge cases of no match found for an update should induce the creation of the resource with a 201 \(Created\) status code.

Another set of constraints applies on the DELETE requests that are successfully received.Those should return a 204 \(No Content\) status code or a 200 \(OK\) if the deletion has been processed. If not, the status code should be 202 \(Accepted\).

发现更新的不匹配的边缘情况应导致使用201（已创建）状态代码创建资源。

另一组约束适用于成功接收的DELETE请求。如果已处理删除，则应返回204（无内容）状态码或200（确定）。 如果不是，状态代码应为202（接受）。

### Mapping request payloads with @RequestBody

In Chapter 4, Building a REST API for a Stateless Architecture, we have presented the RequestMappingHandlerAdapter. We have seen that Spring MVC delegates to this bean to provide an extended support to `@RequestMapping` annotations.

In this perspective, RequestMappingHandlerAdapter is the central piece to access and override HttpMessageConverters through `getMessageConverters()` and `setMessag eConverters(List<HttpMessageConverter<?>> messageConverters)`.

The role of` @RequestBody` annotations is tightly coupled to HttpMessageConverters. We will introduce the HttpMessageConverters now.

使用@RequestBody映射请求有效内容

在第4章为无状态体系结构构建REST API中，我们提供了RequestMappingHandlerAdapter。 我们已经看到Spring MVC委托给这个bean来为`@RequestMapping`注解提供扩展支持。

在这个角度看，RequestMappingHandlerAdapter是通过`getMessageConverters()`和`setMessag eConverters(List<HttpMessageConverter<?>> messageConverters)`访问和覆盖HttpMessageConverters的核心部分。

`@RequestBody`注释的作用与HttpMessageConverters紧密耦合。 我们将介绍HttpMessageConverters。

### HttpMessageConverters

HttpMessageConverters, custom or native, are bound to specific mime types. They are used in the following instances:

* To convert Java objects into HTTP response payloads. Selected from Accept request header mime types, they serve the `@ResponseBody` annotation's purposes \(and indirectly `@RestController` annotations that abstract the `@ResponseBody `annotations\).

* To convert HTTP request payloads into Java objects. Selected from the ContentType request header mime types, these converters are called when the `@RequestBody `annotation are present on a method handler argument.

More generally, HttpMessageConverters match the following HttpMessageConverter interface:

HttpMessageConverters，自定义或本机，绑定到特定的MIME类型。 它们用于以下实例中：

* 将Java对象转换为HTTP响应有效内容。 从接受请求头mime类型中选择，它们提供`@ResponseBody`注释的用途（以及间接的`@RestController`注释，用于抽象@`ResponseBody`注释）。

* 将HTTP请求有效内容转换为Java对象。 从ContentType请求头中选择mime类型，当`@RequestBody`注释出现在方法处理程序参数上时，将调用这些转换器。

更一般地，HttpMessageConverters匹配以下HttpMessageConverter接口：

```
public interface HttpMessageConverter<T> {
    boolean canRead(Class<?> clazz, MediaType mediaType);
    boolean canWrite(Class<?> clazz, MediaType mediaType);
    List<MediaType> getSupportedMediaTypes();
    T read(Class<? extends T> clazz, HttpInputMessage inputMessage) throws IOException, HttpMessageNotReadableException;
    void write(T t, MediaType contentType, HttpOutputMessage outputMessage) throws IOException, HttpMessageNotWritableException;
}
```

The `getSupportedMediaTypes()` method returns the list of mediaTypes \(mime types\) that a specific converter supports. This method is mainly used for reporting purposes and for the canRead and canWrite implementations. These canRead and canWrite eligibility methods are used by the framework to pick up at runtime the first HttpMessageConverter that either:

f Matches the client-provided `Content-Type` request header for the given Java class targeted by `@RequestBody`

f Matches the client-provided `Accept `request header for the Java class that the HTTP response-payload will correspond to \(the Type targeted by `@ResponseBody`\)

`getSupportedMediaTypes()`方法返回特定转换器支持的mediaTypes（mime类型）的列表。 此方法主要用于报告目的和canRead和canWrite实现。 这些canRead和canWrite合格性方法被框架用于在运行时拾取第一个HttpMessageConverter：

* 匹配由`@RequestBody`定位的给定Java类的客户提供的`Content-Type`请求头

* 匹配客户端提供的用于HTTP response-payload将对应的Java类的`Accept`请求头（由`@ResponseBody`定位的类型）

### Provided HttpMessageConverters

With the latest versions of Spring MVC \(4+\), a few extra HttpMessageConverters come natively with the framework. We have thought that summarizing them would be helpful. The following table represents all the native HttpMessageConverters, the mime types, and the Java Types they can be associated with. Short descriptions, mostly coming from the JavaDoc,give more insight about each of them.

提供了HttpMessageConverters

使用最新版本的Spring MVC（4+），一些额外的HttpMessageConverters本地与框架。 我们认为，总结他们将是有益的。 下表表示所有本机HttpMessageConverters，mime类型和它们可以关联的Java类型。 简短的描述，大多来自JavaDoc，给出了对它们中的每一个的更多洞察。

![](/assets/116.png)

![](/assets/117.png)

![](/assets/118.png)

### Using MappingJackson2HttpMessageConverter

In this recipe, the MappingJackson2HttpMessageConverter is used extensively. We used this converter for both the financial transaction creation/update side and the User-Preferences update side.

Alternatively, we used AngularJS to map an HTML form to a built json object whose properties match our Entities. Proceeding this way, we POST/PUT the json object as the application/json mime type.

This method has been preferred to posting an `application/x-www-form-urlencoded` form content, because we can actually map the object to an Entity. In our case, the form matches exactly a backend resource. This is a beneficial result \(and constraint\) of a REST design.

在这个配方中，MappingJackson2HttpMessageConverter被广泛使用。 我们将这个转换器用于financial transaction creation/update side和User-Preferences update side。

或者，我们使用AngularJS将HTML表单映射到其属性与我们的实体匹配的已构建json对象。 继续这样，我们POST/PUT json对象作为application/json mime类型。

这种方法优先于发布一个`application/x-www-form-urlencoded `表单内容，因为我们实际上可以将对象映射到一个实体。 在我们的示例中，表单完全匹配后端资源。 这是REST设计的有益结果（和约束）。

### Using @RequestPart to upload an image

The `@RequestPart` annotation can be used to associate part of a `multipart/form-data` request with a method argument. It can be used with argument Types such as` org.springframework.web.multipart.MultipartFile` and `javax.servlet.http.Part`.

For any other argument Types, the content of the part is passed through an HttpMessageConverter just like `@RequestBody`.

The `@RequestBody` annotation has been implemented to handle the user-profile picture.Here's our sample implementation from the **UserImageController**:

使用@RequestPart上传图片

`@RequestPart`注释可用于将`multipart/form-data`请求的一部分与方法参数相关联。 它可以与参数类型一起使用，例如`org.springframework.web.multipart.MultipartFile`和`javax.servlet.http.Part`。

对于任何其他参数类型，部分的内容通过HttpMessageConverter传递，就像`@RequestBody`。

`@RequestBody`注释已经实现来处理用户配置文件图片。这是我们从**UserImageController**的示例实现：

```
@RequestMapping(method=POST, produces={"application/json"})
@ResponseStatus(HttpStatus.CREATED)
public String save( @RequestPart("file") MultipartFile file, HttpServletResponse response){

    String extension = ImageUtil.getExtension(file.getOriginalFilename());
    String name = UUID.randomUUID().toString().concat(".").concat(extension);
        if (!file.isEmpty()) {
            try {
            byte[] bytes = file.getBytes();
            Path newPath = Paths.get(pathToUserPictures);
            Files.write(newPath, bytes, StandardOpenOption.CREATE);
        ...
    ...
    response.addHeader(LOCATION_HEADER, env.getProperty("pictures.user.endpoint").concat(name));
    return "Success";
    ...
}
```

The file part of the request is injected as an argument. A new file is created on the server filesystem from the content of the request file. A new Location header is added to the Response with a link to the created image.

On the client side, this header is read and injected as background-image CSS property for our div \(see user-account.html\).

请求的文件部分作为参数注入。 从请求文件的内容在服务器文件系统上创建一个新文件。 新的位置标头将添加到响应中，并包含创建的图像的链接。

在客户端，这个头被读取并注入为`div`的`background-image` CSS属性（见user-account.html）。

### Transaction management

The recipe highlights the basic principles we applied to handle transactions across the different layers of our REST architecture. Transaction management is a whole chapter in itself and we are constrained here to present just an overview.

该配方突出了我们应用于处理REST架构的不同层之间的事务的基本原则。 事务管理本身就是一个整章，我们在这里只讨论一个概述。

### The simplistic approach

To build our transaction management, we kept in mind that Spring MVC Controllers are not transactional. Under this light, we cannot expect a transaction management over two different service calls in the same method handler of a Controller. Each service call starts a new transaction, and this transaction is expected to terminate when the result is returned.

We defined our services as `@Transactional(readonly="true")` at the Type level, then methods the that need Write access override this definition with an extra `@Transactional `annotation at the method level. The tenth step of our recipe presents the Transactional changes on the TransactionServiceImpl service. With the default propagation, transactions are maintained and reused between Transactional services, repositories, or methods.

简单的途径

为了建立我们的事务管理，我们记住Spring MVC控制器不是事务。 在这种情况下，我们不能期望在Controller的同一个方法处理程序中对两个不同的服务调用进行事务管理。 每个服务调用启动一个新的事务，并且该事务在返回结果时将终止。

我们在类型级别将服务定义为`@Transactional(readonly="true")`，然后在方法级别上附加`@Transactional`注释需要访问覆盖这个定义。 我们的配方的第十步显示TransactionServiceImpl服务上的事务性更改。 使用默认传播，事务在事务服务，存储库或方法之间维护和重用。

By default, abstracted Spring Data JPA repositories are transactional. We only had to specify transactional behaviors to our custom repositories, as we did for our services.

The eleventh step of our recipe shows the Transactional changes made on the custom repository IndexRepositoryImpl.

默认情况下，抽象的Spring Data JPA存储库是事务性的。 我们只需要为我们的自定义仓库指定事务行为，就像我们为我们的服务做的那样。

我们的配方的第十一步显示了对自定义存储库IndexRepositoryImpl所做的事务性更改。

## There's more…

As mentioned earlier, we configured a consistent transaction management over the different layers of our application.

如前所述，我们在应用程序的不同层上配置了一致的事务管理。

### Transaction management

Our coverage is limited and we advise you to find external information about the following topics if you are not familiar with them.

我们的覆盖范围有限，如果您不熟悉以下主题，我们建议您查找有关以下主题的外部信息。

### ACID properties

Four properties/concepts are frequently used to assess the transaction's reliability. It is therefore useful and important to keep them in mind when designing transactions. Those properties are Atomicity, Consistency, Isolation and Durability. Read more about ACID transactions on the Wikipedia page:

经常使用四个属性/概念来评估交易的可靠性。 因此，在设计交易时记住他们是有用和重要的。 这些属性是原子性，一致性，隔离和耐久性。 阅读更多关于维基百科页面上的ACID交易：

https://en.wikipedia.org/wiki/ACID

### Global versus local transactions

We only defined local transactions in the application. Local transactions are managed at the application level and cannot be propagated across multiple Tomcat servers. Also, local transactions cannot ensure consistency when more than one transactional resource type is involved. For example, in a use case of database operations associated with messaging, when we rollback a message that couldn’t have been delivered, we might need to also rollback the related database operations that have happened beforehand. Only global transactions implementing 2-step commits can take on this kind of responsibility. Global transactions are handled by JTA transaction manager implementations.

Read more about the difference in this Spring reference document:

全球与本地交易

我们只定义应用程序中的本地事务。 本地事务在应用程序级别进行管理，不能跨多个Tomcat服务器传播。 此外，当涉及多个事务资源类型时，本地事务不能确保一致性。 例如，在与消息传递相关联的数据库操作的用例中，当我们回滚不能被传递的消息时，我们可能还需要回滚先前发生的相关数据库操作。 只有实现两步提交的全局事务可以承担这种责任。 全局事务由JTA事务管理器实现处理。

阅读更多关于这个Spring参考文档的区别：

http://docs.spring.io/spring/docs/2.0.8/reference/transaction.html

Historically, JTA transaction managers were exclusively provided by J2EE/JEE containers. With application-level JTA transaction manager implementations, we now have other alternatives such as Atomikos \(http://www.atomikos.com\), Bitronix \(https://github.com/bitronix/btm\), or JOTM \(http://jotm.ow2.org/xwiki/bin/view/Main/WebHome\) to assure global transactions in J2SE environments.

Tomcat \(7+\) can also work along with application-level JTA transaction manager implementations to reflect the transaction management in the container using the TransactionSynchronizationRegistry and JNDI datasources.

历史上，JTA事务管理器仅由J2EE / JEE容器提供。 使用application-level JTA事务管理器实现，我们现在有其他替代品，如Atomikos\(http://www.atomikos.com\)，Bitronix（https://github.com/bitronix/btm）或JOTM\(http://jotm.ow2.org/xwiki/bin/view/Main/WebHome\)，以确保J2SE环境中的全局事务。

Tomcat（7+）还可以与应用程序级JTA事务管理器实现一起使用，以使用TransactionSynchronizationRegistry和JNDI数据源反映容器中的事务管理。

https://codepitbull.wordpress.com/2011/07/08/tomcat-7-with-full-jta

## See also

Performance and useful metadata benefits can be obtained from these three headers that are not detailed in the recipe.

* Cache-Control, ETag, and Last-Modified: Spring MVC supports these headers and as an entry point, we suggest you check out the Spring reference: http://docs.spring.io/spring-framework/docs/current/spring-frameworkreference/html/mvc.html\#mvc-caching-etag-lastmodified

可以从这三个标题中获取性能和有用的元数据优势，这些标题在配方中没有详细说明。

* Cache-Control，ETag和Last-Modified：Spring MVC支持这些头文件，作为一个入口点，我们建议你查看Spring的参考文档：http://docs.spring.io/spring-framework/docs/current/ spring-frameworkreference / html / mvc.html＃mvc-caching-etag-lastmodified







