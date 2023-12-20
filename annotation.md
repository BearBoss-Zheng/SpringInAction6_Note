## SpringFramework

1. `@SpringBootConfiguration`----Designates this class as a configuration class.Although there’s not much configuration in the class yet, you can add Javabased Spring Framework configuration to this class if you need to. This annotation is, in fact, ==a specialized form of the `@Configuration` annotation==.其中包含有：`@EnableAutoConfiguration`和`@ComponentScan`.

2. `@EnableAutoConfiguration`---Enables Spring Boot automatic configuration.We’ll talk more about autoconfiguration later. For now, know that this annotation tells Spring Boot to automatically configure any components that it thinksyou’ll need.

3. `@ComponentScan`---Enables component scanning. This lets you declare otherclasses with annotations like `@Component`, `@Controller`, and `@Service` to have Spring automatically discover and register them as components in the Spring application context.

4. `@SpringBootTest` is a compositeannotation, which is itself annotated with `@ExtendWith(SpringExtension.class)`, to add Spring testing capabilities to JUnit 5. 

5. `@SessionAttributes("tacoOrder")`--- This indicates that the TacoOrder object that is put into the model a little later in the class should be maintained in session

6. `@ModelAttribute` --- is an annotation in Spring Framework used in the context of Spring MVC. It is primarily used to bind method parameters or method return values to a model attribute.

   ```java
   @Controller
   public class MyController {
       @ModelAttribute("myModel")
       public MyModelObject populateModel() {
           // 返回一个初始化的 MyModelObject 对象，会自动添加到模型中
           return new MyModelObject();
       }
   
       @RequestMapping("/example")
       public String example() {
           // 不需要方法参数，模型中已经存在 "myModel" 属性
           return "viewName";
       }
   }
   ```

   > 在这个例子中，`populateModel` 方法返回一个新的 `MyModelObject` 对象，并且该方法上有 `@ModelAttribute("myModel")` 注解，其中的 `"myModel"` 就是模型属性的名称。Spring MVC 在处理请求时会自动调用 `populateModel` 方法，获取其返回值，并将返回的对象添加到模型中，属性名为 `"myModel"`。
   >
   > 接着，在处理请求的 `example` 方法中，由于没有显式的方法参数，Spring MVC 会从模型中取出已经添加的 `"myModel"` 属性，并将其作为方法的隐式参数。因此，在 `example` 方法中可以直接使用 `myModel` 对象，而无需在方法内部显式声明或初始化。

   ```java
   @Controller
   public class MyController {
       @RequestMapping("/example")
       public String example(@ModelAttribute("myModel") MyModelObject myModel) {
           // 请求参数中的 "myModel.field1" 和 "myModel.field2" 会被映射到 myModel 对象的属性
           return "viewName";
       }
   }
   
   ```

   > 在上述例子中，请求参数中的参数名会以 `"myModel."` 为前缀，自动映射到 `MyModelObject` 对象的属性。

7. `@Repository` --- By annotating with `@Repository`, you declare that it should be automatically discovered by Spring component scanning and instantiated as a bean in the Spring application context.

8. `@PathVariable` 是 Spring MVC 中用于从请求路径中获取变量值的注解。它可以用于将 URI 模板变量的值绑定到方法的参数上。

   ```JAVA
   @GetMapping("/{id}")
   public Optional<Taco> tacoById(@PathVariable("id") Long id) {
         return tacoRepo.findById(id);
   }
   ```

9. `@RequestBody` 是 Spring MVC 中用于从 HTTP 请求的 body 中获取对象的注解。它主要用于处理 POST、PUT、PATCH 等请求中的请求体，将请求体的内容映射到方法的参数上。

   ```JAVA
   @RestController
   public class MyController {
   
       @PostMapping("/createUser")
       public String createUser(@RequestBody User user) {
           // 处理接收到的用户对象
           return "User created: " + user.getUsername();
       }
   }
   
   ```

10. `@SessionAttributes` 是 Spring MVC 中的注解，用于指定哪些模型属性应该被存储在会话（session）中，以便在多个请求之间共享数据。

11. `@ExtendWith(SpringExtension.class)`: 这是JUnit 5中的注解，用于扩展测试运行时环境，以便支持Spring测试上下文。它替代了JUnit 4中的`@RunWith(SpringRunner.class)`。通过使用`@ExtendWith(SpringExtension.class)`，你告诉JUnit 5在运行测试时使用Spring的测试扩展。这个扩展负责在测试之前创建和配置Spring应用程序上下文，以及在测试之后进行清理。

12. `@SpringBootTest(webEnvironment=WebEnvironment.RANDOM_PORT)`: 这个注解用于标识一个Spring Boot测试类。`@SpringBootTest` 会加载整个Spring应用程序上下文，并提供一个可以与正在运行的应用程序进行集成测试的环境。`webEnvironment=WebEnvironment.RANDOM_PORT` 表示测试时会使用一个随机分配的端口启动嵌入式的Web服务器，这样可以避免端口冲突，同时允许你进行集成测试

13. `@ConditionalOnMissingBean` : 这就像是在说，“嘿，只有在上下文中没有相同类型的bean时才创建这个bean。”

14. `@ConditionalOnClass ` : It says, "This bean should only be created if a specific class is present in the classpath."

12. 



## Spring Data JDBC

1. `@Table` ---  The object is mapped to a table based on the domain class name. If you’d prefer to map it to a different table name, then you can specify the table name as a parameter to @Table like this:

   ```java
   @Table("Taco_Cloud_Order")
   public class TacoOrder {
    ...
        @Id
    	private Long id;
       
       @Column("customer_name")
   @NotBlank(message="Delivery name is required")
   private String deliveryName;
   }
   ```

2. `@Id` ---  It designates the id property as being the identity for a TacoOrder

3. `@Column` --- Explicitly define the column name mapping



## Spring Data JPA

1. ```java
   package tacos;
   
   import lombok.AccessLevel;
   import lombok.AllArgsConstructor;
   import lombok.Data;
   import lombok.NoArgsConstructor;
   
   
   import javax.persistence.Entity;
   import javax.persistence.Id;
   
   @Data
   @Entity
   @AllArgsConstructor
   @NoArgsConstructor(access = AccessLevel.PRIVATE,force = true)
   public class Ingredient {
       @Id
       private final String id;
       private final String name;
       private final Type type;
   
       public enum Type{
           WRAP,PROTEIN,VEGGIES,CHEESE,SAUCE
       }
   }
   ```

   `@Entity`: 用于将 Java 类标记为持久化实体类。持久化实体类是与数据库表相关联的 Java 类，通过 JPA 可以实现对象与关系数据库的映射。

2. `@Id` 是 JPA（Java Persistence API）中的注解，用于标识实体类的主键属性。主键是数据库表中的唯一标识符，用于唯一地标识表中的每一行数据。

3. `@GeneratedValue` 是 JPA（Java Persistence API）中的注解，通常与 `@Id` 注解一起使用，用于指定主键的生成策略。主键生成策略定义了如何为实体生成唯一的主键值。

   ```java
   @Id
   @GeneratedValue(strategy = GenerationType.AUTO)
   private Long id;
   ```

4. `@ManyToMany` ---  To declare the relationship between a Taco and its associated Ingredient list.A Taco can have many Ingredient objects,and an Ingredient can be a part of many Tacos.

   ```java
   @OneToMany(cascade = CascadeType.ALL)
   private List<Taco> tacos = new ArrayList<>();
   ```

5.  `@Query`: annotate it with `@Query` to explicitly specify the query to be performed when the method is called, as this example shows:

   ```java
   @Query("Order o where o.deliveryCity='Seattle'")
   List<TacoOrder> readOrdersDeliveredInSeattle();
   ```

   

6. `@Transient` :  is used to indicate that a field should not be persisted in the underlying data store (like a database) when using Spring Data repositories.It's often applied to fields that are meant for temporary calculations, caching, or any other scenario where you don't want the data to be stored permanently.

   ```java
   import javax.persistence.Entity;
   import javax.persistence.Transient;
   
   @Entity
   public class MyClass {
       private Long id;
       private String name;
   
       @Transient
       private transient String temporaryData;
   
       // Constructors, getters, setters...
   }
   
   ```
   
   
   
   

## Cassandra

1. `@Table` 注解来映射 Java 实体类与 Cassandra 中的表

   ```java
   import org.springframework.data.cassandra.core.cql.Ordering;
   import org.springframework.data.cassandra.core.cql.PrimaryKeyType;
   import org.springframework.data.cassandra.core.mapping.Column;
   import org.springframework.data.cassandra.core.mapping.PrimaryKeyColumn;
   import org.springframework.data.cassandra.core.mapping.Table;
   
   @Data
   @Table("tacos")
   public class Taco {
   
       @PrimaryKeyColumn(type = PrimaryKeyType.PARTITIONED)
       private UUID id = Uuids.timeBased();
   
       @PrimaryKeyColumn(type = PrimaryKeyType.CLUSTERED,
                         ordering = Ordering.DESCENDING)
       private Date createdAt = new Date();
   
       ...
       
       @Column("ingredients")
       private List<IngredientUDT> ingredients;
   
       
   }
   ```

3. `@Column("ingredients")`: 该注解用于映射 Java 实体类中的属性到 Cassandra 表中的列，指定了该列在表中的名称为 "ingredients"。

4. `@PrimaryKeyColumn(type = PrimaryKeyType.PARTITIONED)`: 这个注解指定了一个分区键列，表示该列用于将数据分布到不同的分区。在 Cassandra 中，分区键决定了数据在集群中的分布，以提高查询性能。

5. `@PrimaryKeyColumn(type = PrimaryKeyType.CLUSTERED, ordering = Ordering.DESCENDING)`: 这个注解指定了一个聚簇列，表示该列在分区内进行排序，同时通过 `ordering` 属性指定了排序的方式，这里是降序（DESCENDING）。聚簇列的值决定了数据在分区内的物理排序。

6. `@UserDefinedType()` : 用于映射用户自定义类型（User Defined Type，UDT）的注解。

   ```java
   import org.springframework.data.cassandra.core.mapping.UserDefinedType;
   
   @Data
   @RequiredArgsConstructor
   @NoArgsConstructor(access = AccessLevel.PRIVATE, force = true)
   @UserDefinedType("ingredient")
   public class IngredientUDT {
     
     private final String name;
     
     private final Ingredient.Type type;
     
   }
   ```

   

## MongoDB

1. `@Id`—Designates a property as the document ID (from Spring Data Commons)

2. `@Document`—Declares a domain type as a document to be persisted to MongoDB

   > By default, the collection name (the Mongo analog to a relational database table) is based on the class name, with the first letter lowercase. Because you haven’t specified otherwise, Ingredient objects will be persisted to a collection named ingredient. But you can change that by setting the collection attribute of `@Document` as follows:
   >
   > ```java
   > @Data
   > @AllArgsConstructor
   > @NoArgsConstructor(access=AccessLevel.PRIVATE, force=true)
   > @Document(collection="ingredients")
   > public class Ingredient {
   > ...
   > }
   > ```

3. `@Field`—Specifies the field name (and, optionally, the order) for storing a property in the persisted document 

4. `@Transient`—Specifies that a property is not to be persisted

   > Unless you specify otherwise, properties that aren’t annotated with `@Field` or `@Transient` will assume a field name equal to the property name.

5. 



## Spring Security

1. `@PreAuthorize` annotation takes a SpEL expression, and, if the expression evaluates to false, the method will not be invoked. 

   ```java
    @PreAuthorize("hasRole('ADMIN')")
       public void deleteAllOrders() {
           orderRepository.deleteAll();
       }
   ```

2. The `@PostAuthorize` annotation works almost the same as the `@PreAuthorize` annotation, except that its expression won’t be evaluated until after the target method is invoked and returns.

3. `@AuthenticationPrincipal` : 用于在方法参数上获取当前认证用户的Principal对象。这个注解可以简化从Spring Security上下文中获取认证用户的过程。

   ```java
   @PostMapping
   public String processOrder(@Valid TacoOrder order, Errors errors, 
       SessionStatus sessionStatus, 
       @AuthenticationPrincipal User user) {
     	if (errors.hasErrors()) {
       	return "orderForm";
     	}
     	order.setUser(user);
     	orderRepo.save(order);
     	sessionStatus.setComplete();
     	return "redirect:/";
   }
   ```

   

## Property

1.  `@ConfigurationProperties`  : 用于绑定配置文件中的属性到 Java 对象上。通过使用这个注解，你可以轻松地将配置文件中的属性值映射到一个对应的 Java 类中，方便统一管理配置。

   ```java
   @ConfigurationProperties(prefix="taco.orders")
   public class OrderController {
    	private int pageSize = 20;
    	public void setPageSize(int pageSize) {
    	this.pageSize = pageSize;
    	}
       
    	...
   
   }
   ```

   ```yaml
   taco:
    	orders:
   	 pageSize: 10
   ```

2. `@Profile()` 是 Spring 框架中用于定义特定配置在哪些环境下激活的注解。通过使用 `@Profile` 注解，你可以使某个配置类或方法只在特定的环境（如开发环境）中生效。

   ```java
   @Bean
   @Profile("dev")
   public CommandLineRunner dataLoader(IngredientRepository repo,
    	UserRepository userRepo, PasswordEncoder encoder) {
    		...
   }
   
   @Bean
   @Profile({"dev", "qa"})
   public CommandLineRunner dataLoader(IngredientRepository repo,
    UserRepository userRepo, PasswordEncoder encoder) {
    ...
   }
   
   @Bean
   @Profile("!prod")
   public CommandLineRunner dataLoader(IngredientRepository repo,
    UserRepository userRepo, PasswordEncoder encoder) {
    ...
   }
   
   @Profile({"!prod", "!qa"})
   @Configuration
   public class DevelopmentConfig {
    	@Bean
    	public CommandLineRunner dataLoader(IngredientRepository repo,
    	UserRepository userRepo, PasswordEncoder encoder) {
    		...
    	}
   }
   ```

3. `@RestResource()` : 用于自定义 Spring Data Repository 的资源。这个注解主要用于控制 Spring Data REST 暴露的资源路径和关系的一些属性。

   ```java
   @Data
   @Entity
   @RestResource(rel="tacos", path="tacos")
   public class Taco {
    ...
   }
   ```

4. 

   

   

## REST service

1. `@RestController` : The @RestController annotation serves two purposes. First, it’s a stereotype annotation like @Controller and @Service that marks a class for discovery by component scanning. But most relevant to the discussion of REST, ==the @RestController annotation tells Spring that all handler methods in the controller should have their return value written directly to the body of the response, rather than being carried in the model to a view for rendering.==

   ```java
   @RestController
   @RequestMapping(path="/api/tacos",
                   produces = "application/json")
   @CrossOrigin
   public class TacoController {
       ...
           
       @GetMapping(params = "recent")
       //the recentTacos() method is responsible for 
       //handling GET requests for /design?recent.
       public Iterable<Taco> recentTacos() {
           PageRequest page = PageRequest.of(
                   0, 12, Sort.by("createdAt").descending());
           return tacoRepo.findAll(page).getContent();
       }
   }
   
   ```

   ==@RestController = @Controller + @ResponseBody==

2. `@CrossOrigin`是Spring Framework中用于处理跨域请求的注解。跨域请求是指在Web应用程序中，前端代码和后端代码运行在不同的域（域名、端口或协议）下的情况。浏览器的同源策略通常会阻止跨域请求，但通过使用`@CrossOrigin`注解，你可以在Spring框架中配置允许跨域访问的规则。

   @CrossOrigin allows clients from localhost, port 8080, to access the API. The origins attribute accepts an array, however, so you can also specify multiple values, as shown next:

   ```java
   @RestController
   @RequestMapping(path="/api/tacos",
    				produces="application/json")
   @CrossOrigin(origins={"http://tacocloud:8080", "http://tacocloud.com"})
   public class TacoController {
    ...
   }
   ```

3. `@ResponseStatus(HttpStatus.CREATED).`--- when the request successful but a resource was created as a result an HTTP status of 201 (CREATED) will return.

   ```JAVA
   @PostMapping(consumes="application/json")
   @ResponseStatus(HttpStatus.CREATED)
   public Taco postTaco(@RequestBody Taco taco) {
    	return tacoRepo.save(taco);
   }
   ```

4. 

   

## JMS

1. `@JmsListener` : `@JmsListener` 是 Spring Framework 提供的注解，用于声明一个方法作为 JMS（Java Message Service）消息监听器。通过在方法上添加 `@JmsListener` 注解，你可以指定要监听的消息队列或主题，并定义处理消息的方法。



## RabbitMQ

1. `@RabbitListener` : 这和 `JmsTemplate` 中的 `@JmsListener` 几乎一样



## Kafka

1. `@KafkaListener` : 这和 `JmsTemplate` 中的 `@JmsListener` 几乎一样



## Integrating Spring

1. `@MessagingGateway`---This annotation tells Spring Integration to generate an implementation of this interface at run time—similar to how Spring Data automatically generates implementations of repository interfaces.

   ```java
   import org.springframework.integration.annotation.MessagingGateway;
   import org.springframework.integration.file.FileHeaders;
   import org.springframework.messaging.handler.annotation.Header;
   
   @MessagingGateway(defaultRequestChannel="textInChannel")   // 								//<1> Declares a  message gateway
   public interface FileWriterGateway {
   
     void writeToFile(
         @Header(FileHeaders.FILENAME) String filename,       // 										//<2> Writes to a file
         String data);
   
   }
   
   ```

   > The defaultRequestChannel attribute of `@MessagingGateway` indicates that any messages resulting from a call to the interface methods should be sent to the given message channel.In this case, you state that any messages that result from a call to writeToFile() should be sent to the channel whose name is textInChannel.

2. `@Header`---In this case, the `@Header` annotation indicates that the value passed to filename should be placed in a message header (specified as `FileHeaders.FILENAME`, which is a constant in the `FileHeaders` class that is equal to the value "file_name") rather than in the message payload. The data parameter value, on the other hand, is carried in the message payload.

3. `@Transformer` --- 它标识一个方法或类作为消息转换器（Message Transformer）。消息转换器负责将一个消息转换成另一种形式，从而使得消息在不同通道之间能够正确地传递和处理。

   ```java
   @Transformer(inputChannel="textInChannel", outputChannel="fileWriterChannel")
   public String transformTextMessage(String textMessage) {
       // 进行消息转换的逻辑，将文本消息转换为其他形式
       String transformedMessage = /* ... */;
       
       return transformedMessage;
   }
   
   ```

4. `@ServiceActivator`--- 它标识一个方法或类作为服务激活器（Service Activator）。服务激活器负责处理从消息通道接收到的消息，执行特定的业务逻辑，并可能生成一个响应消息。

   ```java
   @Bean
   @ServiceActivator(inputChannel="someChannel")
   public MessageHandler sysoutHandler() {
    return message -> {
      System.out.println("Message payload: " + message.getPayload());
    };
   }
   ```

5. `@Filter` 注解用于声明一个过滤器，该过滤器用于过滤消息。在你提供的例子中，`@Filter` 注解声明了一个过滤器，指定了输入通道和输出通道。

   ```java
   @Filter(inputChannel="numberChannel",
    outputChannel="evenNumberChannel")
   public boolean evenNumberFilter(Integer number) {
    return number % 2 == 0;
   }
   ```

   

6. `@Router` 注解用于声明一个路由器，该路由器用于根据消息的内容将消息路由到不同的通道。在你提供的例子中，`@Router` 注解指定了输入通道为 "numberChannel"。

   ```java
   @Router(inputChannel="numberChannel")
   public String routeNumber(int number) {
       // 根据消息内容路由到不同的通道
       if (number % 2 == 0) {
           return "evenNumberChannel";
       } else {
           return "oddNumberChannel";
       }
   }
   
   ```

   

7. `@Splitter` 注解用于声明一个分离器（Splitter），该分离器用于将接收到的消息拆分成多个消息，然后将这些消息发送到指定的输出通道。在你提供的例子中，`@Splitter` 注解指定了输入通道为 "poChannel"，输出通道为 "splitOrderChannel"。

   ```java
   @Bean
   @Splitter(inputChannel="poChannel", outputChannel="splitOrderChannel")
   public OrderSplitter orderSplitter() {
    return new OrderSplitter();
   }
   ```

   

8. `@InboundChannelAdapter` 注解用于声明一个入站通道适配器（Inbound Channel Adapter），该适配器用于将外部系统或数据源的信息转换为消息，并发送到指定的输入通道。在你提供的例子中，`@InboundChannelAdapter` 注解中包含了一个轮询器（Poller）的配置。

   ```java
   @Bean
   @InboundChannelAdapter( poller=@Poller(fixedRate="1000"), channel="numberChannel")
   public MessageSource<Integer> numberSource(AtomicInteger source) {
    return () -> {
    	return new GenericMessage<>(source.getAndIncrement());
    };
   }
   ```

   

9. `@Poller` 是 Spring Integration 中用于配置轮询器的注解。轮询器用于定期地触发消息的生成或处理，以便与外部系统或数据源进行交互。`@Poller` 注解通常用于配置入站通道适配器（Inbound Channel Adapter）或轮询消息处理器（Polling Message Handler）的轮询行为。

   > 基本的 `@Poller` 配置包括以下属性：
   >
   > - **fixedRate：** 指定轮询的固定速率（毫秒）。例如，`fixedRate="1000"` 表示每隔 1000 毫秒（1 秒）触发一次轮询。
   > - **fixedDelay：** 指定轮询的固定延迟（毫秒）。与 `fixedRate` 不同，`fixedDelay` 表示上一次轮询结束后，等待指定的延迟时间后再次触发轮询。
   > - **maxMessagesPerPoll：** 指定每次轮询最多处理的消息数量。用于限制一次轮询处理的消息数量，避免一次轮询处理太多消息。
   > - **cron：** 使用 Cron 表达式来指定轮询的时间规则。可以更灵活地定义触发轮询的时间。

   

## Spring WebFlux

和 SpringMVC 的注释几乎是一样的



## Lombok

1. `@Data`: the `@Data` annotation at the class level is provided by Lombok and tells Lombok to generate all of those missing methods(equals(), hashCode(),toString(), and others.) as well as a constructor that accepts all final properties as arguments. By using Lombok, you can keep the code for Ingredient slim and trim.

2. `@Slf4j`--- is a Lombok-provided annotation that, at compilation time, will automatically generate an SLF4J (Simple Logging Facade for Java,https://www.slf4j.org/)

3. `@RequiredArgsConstructor` --- The `@Data` annotation implicitly adds a required arguments constructor, but when a `@NoArgsConstructor` is used, that constructor is removed. An explicit `@RequiredArgsConstructor` ensures that you’ll still have a required arguments constructor, in addition to the private noarguments constructor.

4. `@NonNull` : When you slap `@NonNull` on a parameter, it's like saying, "Hey, this shouldn't be null, and if it is, throw a fit!"

5. `@EqualsAndHashCode()` : When you annotate a class with `@EqualsAndHashCode(exclude = "id")`, Lombok generates `equals` and `hashCode` methods for you, considering all fields except the one specified in the `exclude` parameter (in this case, the "id" field).

   ```java
   import lombok.EqualsAndHashCode;
   
   @EqualsAndHashCode(exclude = "id")
   public class MyClass {
       private int id;
       private String name;
       private double value;
   
       // Constructors, getters, setters...
   }
   ```

   





## RRocket

1. `DirtiesContext `: is used here to ensure that the Spring application context is reset between test runs, resulting in a new and empty H2 database on each run.

2. `@DataR2dbcTest` 是 Spring Framework 中用于测试 Spring Data R2DBC 数据访问层的注解。这个注解通常用于测试仓库接口（Repository）和与数据库交互的相关组件。

3. `@DataMongoTest`: This annotation performs a function similar to the @DataR2dbcTest annotation. It ensures that a Spring application context is created with the generated repositories available as beans to be injected into the test. 

4. `@MessageMapping`: 用于映射特定消息目的地的处理方法

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

5. `@DestinationVariable` :  references the placeholder variable

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

   


## Validating form input

1. ```java
   package tacos;
   import javax.validation.constraints.Digits;
   import javax.validation.constraints.NotBlank;
   import javax.validation.constraints.Pattern;
   import org.hibernate.validator.constraints.CreditCardNumber;
   import java.util.List;
   import java.util.ArrayList;
   import lombok.Data;
   
   @Data
   public class TacoOrder {
   
       @NotBlank(message="Delivery name is required")
       private String deliveryName;
   
       @NotBlank(message="Street is required")
       private String deliveryStreet;
   
       @NotBlank(message="City is required")
       private String deliveryCity;
   
       @NotBlank(message="State is required")
       private String deliveryState;
   
       @NotBlank(message="Zip code is required")
       private String deliveryZip;
   
       @CreditCardNumber(message="Not a valid credit card number")
       private String ccNumber;
   
       @Pattern(regexp="^(0[1-9]|1[0-2])([\\/])([2-9][0-9])$",
               message="Must be formatted MM/YY")
       private String ccExpiration;
   
       // contains exactly three numeric digits.
       @Digits(integer=3, fraction=0, message="Invalid CVV")
       private String ccCVV;
   
       private List<Taco> tacos = new ArrayList<>();
   
       public void addTaco(Taco taco) {
           this.tacos.add(taco);
       }
   }
   ```

2. `@Valid` 是 Java 中用于在方法参数上进行验证的注解，通常与`javax.validation`包中的验证框架（例如 Hibernate Validator）一起使用。在 Spring 中，它经常用于处理表单提交等场景，确保传递给方法的对象满足特定的验证规则。

   ```java
   @PostMapping
       public String processTaco(
               @Valid Taco taco, Errors errors,
               @ModelAttribute TacoOrder tacoOrder) {
   
           if (errors.hasErrors()) {
               return "design";
           }
   
           tacoOrder.addTaco(taco);
           log.info("Processing taco: {}", taco);
   
           return "redirect:/orders/current";
       }
   ```

   > The `@Valid` annotation tells Spring MVC to perform validation on the submitted Taco object after it’s bound to the submitted form data and before the processTaco() method is called.

3. 



## Administering Spring

1. ` @EnableAdminServer` : enable the Admin server

   ```java
   package tacos.admin;
   
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   
   import de.codecentric.boot.admin.server.config.EnableAdminServer;
   
   @EnableAdminServer
   @SpringBootApplication
   public class AdminServerApplication {
   
   	public static void main(String[] args) {
   		SpringApplication.run(AdminServerApplication.class, args);
   	}
   
   }
   ```

   

2. `@ManagedResource`: 这个注解用于标记类，表示这个类是一个被JMX管理的资源。通常，你会在类级别使用它，表明整个类的实例是一个JMX可管理的资源。

3. `@ManagedOperation`: 当你想将一个方法暴露给JMX以便远程调用时，你可以使用这个注解标记该方法。这样，JMX客户端可以调用这个方法来执行特定的操作。

4. `@ManagedAttribute`: 当你希望将类的属性作为JMX可管理的属性时，你可以使用这个注解。它通常用于标记类的getter和setter方法，允许JMX客户端读取或修改这些属性。

   ```java
   package tacos.jmx;
   
   import java.util.concurrent.atomic.AtomicLong;
   import org.springframework.data.rest.core.event.AbstractRepositoryEventListener;
   import org.springframework.jmx.export.annotation.ManagedAttribute;
   import org.springframework.jmx.export.annotation.ManagedOperation;
   import org.springframework.jmx.export.annotation.ManagedResource;
   
   import tacos.Taco;
   import tacos.data.TacoRepository;
   
   @Service
   @ManagedResource
   public class TacoCounter
           extends AbstractRepositoryEventListener<Taco> {
   
     private AtomicLong counter;
     public TacoCounter(TacoRepository tacoRepo) {
       tacoRepo
               .count()
               .subscribe(initialCount -> {
                 this.counter = new AtomicLong(initialCount);
               });
     }
   
     @Override
     protected void onAfterCreate(Taco entity) {
       counter.incrementAndGet();
     }
   
     @ManagedAttribute
     public long getTacoCount() {
       return counter.get();
     }
   
     @ManagedOperation
     public long increment(long delta) {
       return counter.addAndGet(delta);
     }
   
   }
   
   ```

   > The tacoCounter class is annotated with `@ManagedResource` to indicate that this bean should be an MBean. As an MBean, it will expose one attribute and one operation. The `getTacoCount()` method is annotated with `@ManagedAttribute ` so that it will be exposed as an MBean attribute, whereas the increment() method is annotated with `@ManagedOperation`, exposing it as an MBean operation.

   













。