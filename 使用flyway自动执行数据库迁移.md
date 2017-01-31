In the delivery life cycle, maintaining databases across versions and multiple environments can be a real headache. Flyway is an assertive protection against the entropy that schema changes can induce. Managing and automating migrations, Flyway stands as a tremendously valuable asset for software makers.

在输送生命周期中，跨版本和多个环境维护数据库可能是一个真正的头痛。 Flyway对于熵的变化可以引起一个自信的保护模式。 管理和自动化迁移，Flyway对于软件制造商来说是一个非常宝贵的资产。

## Getting ready

In this recipe, we review the Flyway configuration. We especially review its integration in to Maven. This will get every build to upgrade \(if necessary\) the corresponding database so that it matches the expectation level.

在这个谱方，我们回顾了Flyway配置。 特别是检查其在Maven中的集成。 这将使每个构建都升级（如果需要）相应的数据库，使其符合预期水平。

## How to do it…

1.From the **Git Perspective** in Eclipse, checkout the latest version of the branch v9.x.x.

2.In the/app directory of your workspace, the `cloudstreetmarket.properties` file has been updated. Also, one extra db/migration directory shows up with a `Migration-1_0__init.sql` file inside, as well as a new /logs directory.

3.Please do reflect all these changes to the app directory located in your OS user home directory: `<home-directory>/app`.

4.Also ensure that your **MySQL Server** is running.

5.Run the **Maven clean** and **Maven install** commands on the zipcloud-parent project \(right-click on the project **Run as… \| Maven Clean** and then** Run as… \| Maven Install**\).

6.Now, run the **Maven clean** and **Maven install** commands on the cloudstreetmarket-parent project.

7.At the top of the stack trace \(at the package Maven phase\), you should see the following logs:

8.At this stage, the database should have been reset to match a standard state of structure and data.

9.If you rerun the build again, you should now see the following logs:

1.从Eclipse中的**Git Perspective**中，检出最新版本的分支v9.x.x.

2.在工作空间的/app目录中，`cloudstreetmarket.properties`文件已更新。 此外，一个额外的db/migration目录显示有一个`Migration-1_0__init.sql`文件，以及一个新的/logs目录。

3.请将所有这些更改反映到位于操作系统用户主目录中的应用程序目录：`<home-directory>/app`。

4.还要确保**MySQL Server**正在运行。

5.在zipcloud-parent项目上运行**Maven clean**和**Maven install** 命令（右键单击项目**Run as ... \| Maven Clean**，然后**Run as ... \| Maven Install**）。

6.现在，在cloudstreetmarket-parent项目上运行**Maven clean**和**Maven** **Install**命令。

7.在堆栈跟踪的顶部（在包Maven阶段），您应该看到以下日志：  
![](/assets/151.png)

8.在这个阶段，数据库应该被重置以匹配结构和数据的标准状态。

9.如果再次重新运行构建，您现在应该会看到以下日志：

![](/assets/152.png)

10.In the parent` pom.xml` \(in cloudstreetmarket-parent\), you can notice a new plugin definition:

10.在父`pom.xml`（在cloudstreetmarket-parent中），您可以注意到一个新的插件定义：

```java
<plugin>
    <groupId>com.googlecode.flyway</groupId>
    <artifactId>flyway-maven-plugin</artifactId>
    <version>2.3.1</version>
    <inherited>false</inherited>
    <executions>
        <execution>
            <id>package</id>
            <goals>
                <goal>migrate</goal>
            </goals>
        </execution>
    </executions>
    <configuration>
        <driver>${database.driver}</driver>
        <url>${database.url}</url>
        <serverId>${database.serverId}</serverId>
        <schemas>
            <schema>${database.name}</schema>
        </schemas>
        <locations>
            <location>
                filesystem:${user.home}/app/db/migration
            </location>
        </locations>
        <initOnMigrate>true</initOnMigrate>
        <sqlMigrationPrefix>Migration-</sqlMigrationPrefix>
        <placeholderPrefix>#[</placeholderPrefix>
        <placeholderSuffix>]</placeholderSuffix>
        <placeholderReplacement>true</placeholderReplacement>
        <placeholders>
            <db.name>${database.name}</db.name>
        </placeholders>
    </configuration>
    <dependencies>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.6</version>
        </dependency>
    </dependencies>
</plugin>
```

11.A few variables \(for example, `${database.driver}`\) used in this definition correspond to default properties, set at the top level of this `pom.xml`:

11.在此定义中使用的一些变量（例如，`${database.driver}`）对应于在此`pom.xml`的顶层设置的默认属性：

```java
<database.name>csm</database.name>
<database.driver>com.mysql.jdbc.Driver</database.driver>
<database.url>jdbc:mysql://localhost</database.url>
<database.serverId>csm_db</database.serverId>
```

12.The` database.serverId` must match a new Server entry in the Maven `settings.xml` file \(described in the next point\).

12.` database.serverId`必须匹配Maven `settings.xml`文件中的新服务器entry（如下一点所述）。

13.Edit the Maven `settings.xml` file \(that you must have created in Chapter 1, Setup Routine for an Enterprise Spring Application\) located at &lt;home-directory&gt;/.m2/settings.xml. Add somewhere in the root node the following block:

13.编辑位于&lt;home-directory&gt;/.m2/settings.xml的Maven `settings.xml`文件（您必须已在第1章中创建）。 在根节点中添加以下块的某处：

```java
<servers>
    <server>
        <id>csm_db</id>
        <username>csm_tech</username>
        <password>csmDB1$55</password>
    </server>
</servers>
```

14. In the parent pom.xml \(in cloudstreetmarket-parent\), a new Profile has been added to optionally override the default properties \(of this pom.xml\):

14.在父`pom.xml`（在cloudstreetmarket-parent中）中，添加了一个新的配置文件以可选地覆盖（`pom.xm`l的）默认属性：

```
<profiles>
    <profile>
        <id>flyway-integration</id>
        <properties>
            <database.name>csm_integration</database.name>
            <database.driver>com.mysql.jdbc.Driver</database.driver>
            <database.url>jdbc:mysql://localhost</database.url>
            <database.serverId>csm_db</database.serverId>
        </properties>
    </profile>
</profiles>
```

> Running Maven Clean Install with the csm\_integration profile \(mvn clean install –Pcsm\_integration\) would upgrade in this case, if necessary, a csm\_integration database.
>
> 使用csm\_integration配置文件运行Maven Clean Install（mvn clean install -Pcsm\_integration）将在此情况下升级（如果需要）csm\_integration数据库。

## How it works...

Flyway is a database versioning and migration tool licensed Apache v2 \(free software\). It is a registered trademark of the company Boxfuse GmbH.

Flyway is not the only product in this category but is widely present in the industry for its simplicity and easy configuration. The migration scripts can be written in plain old SQL and many providers are supported. From classical RDBMS \(Oracle, MySQL, SQL Server, and so on\) to in-memory DB \(HSQLDB, solidDB, and so on\), and even cloud-based solutions \(AWS Redshift, SQL Azure, and so on\).

Flyway是Apache v2（免费软件）许可的数据库版本控制和迁移工具。 它是Boxfuse GmbH公司的注册商标。

  
Flyway不是此类别中的唯一产品，但由于其简单和易于配置而广泛存在于行业中。 migration scripts可以用普通的SQL编写，并且支持许多提供程序。 从传统的RDBMS（Oracle，MySQL，SQL Server等）到内存数据库（HSQLDB，solidDB等），甚至基于云的解决方案（AWS Redshift，SQL Azure等）。

### A limited number of commands

Flyway provides the six following commands for reporting and operation purposes.

有限数量的命令

Flyway提供以下六个命令用于报告和操作目的。

### Migrate

The Migrate command is the goal we have integrated to the Maven package phase. It looks up the classpath or the filesystem for potential migrations to be executed. Several locations \(script repositories\) can be configured. In the Flyway Maven plugin, these locations are defined in the root configuration node. Patterns are set up to retain specific filenames.

Migrate命令是我们集成到Maven package 阶段的目标。 它查找类路径或文件系统以执行潜在的migrations 。 可以配置多个位置（脚本存储库）。 在Flyway Maven插件中，这些位置在根配置节点中定义。 模式设置为保留特定的文件名。

### Clean

The Clean command restores pristine a database schema. All the objects \(tables, views, functions, and so on\) are dropped with this command.

Clean命令恢复原始数据库模式。 使用此命令删除所有对象（表，视图，函数等）。

### Info

The Info command provides feedback about the current state and the migration history of a given schema. If you have a look into your local MySQL server, in the csm schema, you will notice that a metadata table has been created with the name schema\_version. Flyway uses the following table to compare the script repository state with the database state and to fill the gaps.

Info命令提供有关给定模式的当前状态和migration history的反馈。 如果您查看本地MySQL服务器，在csm模式中，您会注意到已使用名称schem\_version创建了元数据表。 Flyway使用下表来比较脚本存储库状态与数据库状态并填补空白。

![](/assets/153.png)

The Info command basically prints out this table as a report.

Info命令基本上将此表打印为报告。

### Validate

The Validate command can be useful to ensure that the migrations executed on a database actually correspond to the scripts currently present in the repositories

Validate命令可用于确保在数据库上执行的migrations 实际上对应于存储库中当前存在的脚本

### Baseline

The Baseline command can be used when we have an existing database that hasn't been managed yet by Flyway. A Baseline version is created to tag the state of this database and to make it ready to live with upcoming versions. Versions prior to this Baseline will simply be ignored.

当我们有一个尚未由Flyway管理的现有数据库时，可以使用Baseline命令。 创建基准版本以标记此数据库的状态，并使其准备好与即将到来的版本一起使用。 此基线之前的版本将被忽略。

### Repair

The Repair command can clean up a corrupted state of the metadata table. To do that, Flyway removes the failed migration entries and resets the stored checksums to match the scripts checksums.

Repair命令可以清除元数据表的损坏状态。 为此，Flyway删除失败的migration entries，并重置存储的校验和以匹配脚本校验和。

### About Flyway Maven plugin

The Flyway Maven plugin provides the interface for Maven to control the Flyway program. Our configuration of the plugin has been the following:

Flyway Maven插件为Maven提供了控制Flyway程序的界面。 我们的插件配置如下：

```java
<plugin>
    <groupId>com.googlecode.flyway</groupId>
    <artifactId>flyway-maven-plugin</artifactId>
    <version>2.3.1</version>
    <inherited>false</inherited>
    <executions>
        <execution>
            <id>package</id>
            <goals>
                <goal>migrate</goal>
            </goals>
        </execution>
    </executions>
    <configuration>
        <driver>${database.driver}</driver>
        <url>${database.url}</url>
        <serverId>${database.serverId}</serverId>
        <schemas>
            <schema>${database.name}</schema>
        </schemas>
        <locations>
            <location>
                filesystem:${user.home}/app/db/migration
            </location>
        </locations>
        <initOnMigrate>true</initOnMigrate>
        <sqlMigrationPrefix>Migration-</sqlMigrationPrefix>
        <placeholderPrefix>#[</placeholderPrefix>
        <placeholderSuffix>]</placeholderSuffix>
        <placeholderReplacement>true</placeholderReplacement>
        <placeholders>
            <db.name>${database.name}</db.name>
        </placeholders>
    </configuration>
</plugin>
```

As usual with Maven plugins, the executions section allows the binding of Maven phases to one or more Goals of the plugin. For Flyway Maven plugin, the goals are the Flyway commands presented previously. We tell Maven when to consider the plugin and what to invoke in this plugin.

和Maven插件一样，执行部分允许将Maven阶段绑定到插件的一个或多个目标。 对于Flyway Maven插件，目标是之前提供的Flyway命令。 我们告诉Maven什么时候考虑插件和什么在这个插件中调用。

Our configuration section presents a few parameters checked during migrations. For example, the locations specifies migration repositories to be scanned recursively \(they can start with `classpath:` or `filesystem:`\). The schemas defines the list of schemas managed by Flyway for the whole set of migrations. The first schema will be the default one across migrations.

我们的配置部分介绍了在迁移期间检查的一些参数。 例如，位置指定要递归扫描的迁移存储库（它们可以以`classpath：`或`filesystem :`\)开头。 模式定义了由Flyway管理的用于整个迁移集的模式列表。 第一个模式将是跨迁移的默认模式。

An interesting feature is the ability to use variables in migration scripts so that these scripts can be used as template for multiple environments. Variable names are defined with placeholders, and the way variables are identified in scripts is configurable with `placeholderPrefix `and `placeholderSuffix`.

The whole list of configuration parameters can be found at:

一个有趣的功能是能够在migration scripts中使用变量，以便这些脚本可以用作多个环境的模板。 变量名用占位符定义，变量在脚本中标识的方式可以用`placeholderPrefix`和`placeholderSuffix`配置。

可以在以下位置找到配置参数的完整列表：

http://flywaydb.org/documentation/maven/migrate.html

## There is more…

### The official documentation

Flyway is well-documented and actively supported by its community. Read more about the product online at http://flywaydb.org.

You can also follow or contribute to the project through the GitHub repository at https://github.com/flyway/flyway.

Flyway有良好的记录，并得到其社区的积极支持。 有关该产品的更多信息，请访问http://flywaydb.org。

您还可以通过GitHub存储库https://github.com/flyway/flyway跟踪或贡献项目。

## See also

Liquibase: The main Flyway competitor is probably Liquibase. Liquibase doesn't use plain SQL for its scripts; it has instead its own multirepresentation DSL. For more information, visit:Liquibase：

* 主要的Flyway竞争对手可能是Liquibase。 Liquibase不对其脚本使用纯SQL; 它具有它自己的多表示DSL。 有关详细信息，请访问： http://www.liquibase.org

  
  


