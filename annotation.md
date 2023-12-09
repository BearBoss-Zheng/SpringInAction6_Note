1. `@MessagingGateway`---This annotation tells Spring Integration to generate an implementation of this interface at run time—similar to how Spring Data automatically generates implementations of repository interfaces.

2. `@SessionAttributes` 是 Spring MVC 中的注解，用于指定哪些模型属性应该被存储在会话（session）中，以便在多个请求之间共享数据。

3. `@ExtendWith(SpringExtension.class)`: 这是JUnit 5中的注解，用于扩展测试运行时环境，以便支持Spring测试上下文。它替代了JUnit 4中的`@RunWith(SpringRunner.class)`。通过使用`@ExtendWith(SpringExtension.class)`，你告诉JUnit 5在运行测试时使用Spring的测试扩展。这个扩展负责在测试之前创建和配置Spring应用程序上下文，以及在测试之后进行清理。

4. `@SpringBootTest(webEnvironment=WebEnvironment.RANDOM_PORT)`: 这个注解用于标识一个Spring Boot测试类。`@SpringBootTest` 会加载整个Spring应用程序上下文，并提供一个可以与正在运行的应用程序进行集成测试的环境。`webEnvironment=WebEnvironment.RANDOM_PORT` 表示测试时会使用一个随机分配的端口启动嵌入式的Web服务器，这样可以避免端口冲突，同时允许你进行集成测试

5. `DirtiesContext `: is used here to ensure that the Spring application context is reset between test runs, resulting in a new and empty H2 database on each run.

6. `@DataR2dbcTest` 是 Spring Framework 中用于测试 Spring Data R2DBC 数据访问层的注解。这个注解通常用于测试仓库接口（Repository）和与数据库交互的相关组件。

7. `@DataMongoTest`: This annotation performs a function similar to the @DataR2dbcTest annotation. It ensures that a Spring application context is created with the generated repositories available as beans to be injected into the test. 

8. `@PrimaryKeyColumn` 注解用于定义主键列（Primary Key Columns）的属性。主键在 Cassandra 中是非常重要的概念，它用于唯一标识表中的每一行数据。使用 `@PrimaryKeyColumn` 注解，你可以定义一个或多个列，这些列将组成表的主键。

   ```java
   @PrimaryKeyColumn(name = "user_id", type = PrimaryKeyType.PARTITIONED)
   private UUID userId;
   
   @PrimaryKeyColumn(name = "creation_date", ordinal = 0, type = PrimaryKeyType.CLUSTERED)
   private Instant creationDate;
   ```

   在上面的例子中，`userId` 列是分区键（Partition Key），`creationDate` 列是集群键（Clustering Key）。这两者一起形成了表的主键。

9. `@UserDefinedType()` 是 Spring Data Cassandra 中的注解，用于标识一个类为用户定义类型（User-Defined Type，UDT），并指定该 UDT 在 Cassandra 中的名称。

   ```java
   @UserDefinedType("ingredient")
   public class IngredientUDT {
   
       // ... (其他字段和方法)
       
   }
   ```

   这表示 `IngredientUDT` 类是一个用户定义类型，并且在 Cassandra 中被命名为 "ingredient"。

10. @DataCassandraTest

11. `@MessageMapping`: 用于映射特定消息目的地的处理方法

    ```java
    @MessageMapping("greeting")
    	public Mono<String> handleGreeting(Mono<String> greetingMono) {
    		return greetingMono
    			.doOnNext(greeting ->
    				log.info("Received a greeting: {}", greeting))
    			.map(greeting -> "Hello back to you!");
    	}
    ```

    In this example, the handleGreeting() method is invoked when a request is sent from the client to the route named "greeting".

12. `@DestinationVariable` :  references the placeholder variable

    ```java
    @MessageMapping("greeting/{name}")
    	public Mono<String> handleGreeting(
    			@DestinationVariable("name") String name,
    			Mono<String> greetingMono) {
    
    		return greetingMono
    			.doOnNext(greeting ->
    				log.info("Received a greeting from {} : {}", name, greeting))
    			.map(greeting -> "Hello to you, too, " + name);
    	}
    ```

    

    







。