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



4. A simplified version of UserController is given here:

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



