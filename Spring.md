# Spring

# Part 1

## 1.1 What is Spring

### 1.1.1 Spring application context

Spring offers a container, often referred to as the Spring application context, that creates and manages application components. These components, or beans,are wired together inside the Spring application context to make a complete application.

the next Figure illustrates the relationships between these beans and the Springapplication context

![image-20231120211539806](Spring.assets/image-20231120211539806.png)

Historically,the way you would guide Spring’s application context to wire beans together was with one or more XML files that described the components and their relationship to other components.For example:

```xml
<bean id="inventoryService"
 	class="com.example.InventoryService" />
<bean id="productService"
 	class="com.example.ProductService" />
 <constructor-arg ref="inventoryService" />
</bean>
```

In recent versions of Spring, however, a Java-based configuration is more common.

```java
@Configuration
public class ServiceConfiguration {
 	@Bean
	 public InventoryService inventoryService() {
 		return new InventoryService();
 	 }
	@Bean
 	public ProductService productService() {
 		return new ProductService(inventoryService());
 	 }
}
```

The @Configuration annotation indicates to Spring that this is a configuration class that will provide beans to the Spring application context.
 The configuration’s methods are annotated with @Bean, indicating that the objects they return should be added as beans in the application context (where, bydefault, their respective bean IDs will be the same as the names of the methods thatdefine them).

>  Java-based configuration offers several benefits,including ==greater type safety and improved refactorability.==

### 1.1.2 Dependency injection

DI is a design pattern where the dependencies (i.e., other objects or services) required by a component are provided to it, rather than the component creating or managing those dependencies itself. 

## 1.2 Initializing a Spring application

### 1.2.1 bootstrap the application

One of the most powerful lines of code is also one of the shortest.

```java
package sia.tacocloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication //the most powerful lines of code
public class TacoCloudApplication {

    public static void main(String[] args) {
        SpringApplication.run(TacoCloudApplication.class, args);
    }

}
```

`@SpringBootApplication` is a composite annotation that combines the following three annotations:

* `@SpringBootConfiguration`----Designates this class as a configuration class.Although there’s not much configuration in the class yet, you can add Javabased Spring Framework configuration to this class if you need to. This annotation is, in fact, ==a specialized form of the `@Configuration` annotation==.
* `@EnableAutoConfiguration`---Enables Spring Boot automatic configuration.We’ll talk more about autoconfiguration later. For now, know that this annotation tells Spring Boot to automatically configure any components that it thinksyou’ll need.
* `@ComponentScan`---Enables component scanning. This lets you declare otherclasses with annotations like `@Component`, `@Controller`, and `@Service` to have Spring automatically discover and register them as components in the Spring application context.

### 1.2.2 Testing The Application

Manual testing implies that there’s a human involved and thus potential for human error and inconsistent testing. Automated tests are more consistent and repeatable.

Recognizing this, the Spring Initializr gives you a test class to get started. The following listing shows the baseline test class.

```java
package sia.tacocloud;

import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class TacoCloudApplicationTests {

    @Test
    void contextLoads() {
    }

}
```

This test class does perform an essential check to ensure that the Spring application context can be loaded successfully. If you make any changes that prevent the Spring application context from being created, this test fails, and you can react by fixing the problem.

`@SpringBootTest` is a compositeannotation, which is itself annotated with `@ExtendWith(SpringExtension.class)`, to add Spring testing capabilities to JUnit 5. 

## 1.3 Writing a Spring application

### 1.3.1 Handing web requests

In the context of web development, a request and a response are fundamental concepts in the communication between a client (typically a web browser) and a server.

1. **Request:** When you, ==as a user==, access a website by entering a URL in your browser or clicking a link, your browser sends a request to the server hosting that website. This request includes information about the resource you want (e.g., a webpage, an image) and additional details like the type of request (GET, POST, etc.), headers, and sometimes data, depending on the type of request.
2. **Response:** The server processes the request and sends back a response to the browser. This response includes the requested resource (like an HTML page or an image), along with metadata such as status codes, headers, and sometimes additional data. The browser then interprets the response and renders the content to display on your screen.

```java
package tacos;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller  //the controller
public class HomeController {
    @GetMapping("/") //handlers requests for the root path/
    public String home(){
        return "home";// returns the view name
    }

}
```

`@Controller`--- Its primary purpose is to identify this class as a component for component scanning.Because HomeController is annotated with `@Controller`, Spring’s component scanning automatically discovers it and creates an instance of HomeController as a bean in the Spring application context.In fact, a handful of other annotations (including `@Component`, `@Service`, and`@Repository`) serve a purpose similar to `@Controller`.The choice of `@Controller `is, however, more ==descriptive of this component’s role== in the application.

`@GetMapping`--- The home() annotated with `@GetMapping` to indicate that if an HTTP GET request is received for the root path /, then this method should handle that request. It does so by doing nothing more than ==returning a `String` value of `home`==. This value is interpreted as the logical name of a view.

The template name is derived from the logical view name by prefixing it with /templates/ and postfixing it with .html. The resulting path for the template is /templates/home.html.

> Why Thymeleaf?
>
> Even though JSP may seem like an obvious choice,there are some challenges to overcome when using JSP with Spring Boot. I didn’t want to go down that rabbit hole in chapter 1. Hang tight. We’ll look at other template options, including JSP, in chapter 2.

```html
<!DOCTYPE html>
<html lang="en"  xmlns="http://www.w3.org/1999/xhtml"
                xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Taco Cloud</title>
</head>
<body>
    <h1>Welcome to ...</h1>
    <img th:src="@{/images/TacoCloud.jpg}">
</body>
</html>
```

The following code will test the home page

```java
package tacos;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.test.web.servlet.MockMvc;

import static 
 org.hamcrest.Matchers.containsString;
import static      org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static
 org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static
 org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;
import static
 org.springframework.test.web.servlet.result.MockMvcResultMatchers.view;

@WebMvcTest(HomeController.class)
public class HomeControllerTest {
    @Autowired
    private MockMvc mockMvc;

    @Test
    public  void testHomePage() throws Exception {
        mockMvc.perform(get("/"))
                .andExpect(status().isOk())
                .andExpect(view().name("home"))
                .andExpect(content().string(
                        containsString("Welcome to ...")
                ));
    }
}
```

### 1.3.2 Getting to know Spring Boot DevTools

As its name suggests, DevTools provides Spring developers with some handy development-time tools. Among those are the following:

* Automatic application restart when code changes
* Automatic browser refresh when browser-destined resources (such as templates,JavaScript, stylesheets, and so on) change
* Automatic disabling of template caches
* Built in H2 Console, if the H2 database is in use

### 1.3.3 How does application work

What is Spring doing behind the scenes to make sure your application needs are met? To understand what Spring is doing, let’s start by looking at the build specification.

```xml
<dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```



In the pom.xml file, you declared a dependency on the Web and Thymeleaf starters. These two dependencies transitively brought in a handful of other dependencies,including the following:

1. Spring’s MVC framework
2. Embedded Tomcat
3. Thymeleaf and the Thymeleaf layout dialect

It also brought Spring Boot’s autoconfiguration library along for the ride. When the application starts, Spring Boot autoconfiguration detects those libraries and automatically performs the following tasks:

1. Configures the beans in the Spring application context to enable Spring MVC
2. Configures the embedded Tomcat server in the Spring application context
3. Configures a Thymeleaf view resolver for rendering Spring MVC views with Thymeleaf templates

In short, ==autoconfiguration does all the grunt work==, leaving you to focus on writing code that implements your application functionality.

## 1.4 Surverying the Spring landscape

### 1.4.1 The core Spring Framework

The core Spring Framework is the foundation of everything else in the Spring universe.It provides the ==core container and dependency== injection framework. But it also provides a few other essential features.

1. Using Spring MVC to write a controller class to handle web requests.Spring MVC can also be used to create REST APIs that produce non-HTML output.
2. Offerring some elemental data persistence support,specifically, template-based JDBC support.
3. Spring includes support for reactive-style programming, including a new reactive web framework called Spring WebFlux that borrows heavily from Spring MVC.

### 1.4.2 Spring Boot

In addition to starter dependencies and autoconfiguration,Spring Boot also offers the following other useful features:

1. The Actuator provides runtime insight into the inner workings of an application, including metrics, thread dump information, application health, and environment properties available to the application.
2. Flexible specification of environment properties.
3. Additional testing support on top of the testing assistance found in the core framework.

### 1.4.3 Spring Data

Spring Data provides something quite amazing: the ability to define your application’s data repositories as simple Java interfaces, using a naming convention when defining methods to drive how data is stored and retrieved.

What’s more, Spring Data is capable of working with several different kinds of databases, including relational (via JDBC or JPA), document (Mongo), graph (Neo4j),and others.

### 1.4.4 Spring Security

Spring Security addresses a broad range of application security needs, including authentication, authorization, and API security.

### 1.4.5 Spring Integration and Spring Batch

At some point, most applications will need to integrate with other applications or even with other components of the same application. Several patterns of application integration have emerged to address these needs. Spring Integration and Spring Batch provide the implementation of these patterns for Spring applications.

Spring Integration addresses real-time integration where data is processed as it’s made available. In contrast, Spring Batch addresses batched integration where data is allowed to collect for a time until some trigger (perhaps a time trigger) signals that it’s time for the batch of data to be processed.

### 1.4.6 Spring Cloud

The application development world is entering a new era where we’ll no longer develop our applications as single-deployment, unit monoliths and will instead compose applications from several individual deployment units known as microservices.
Microservices are a hot topic, addressing several practical development and runtime concerns. In doing so, however, they bring to fore their own challenges. Those challenges are met head-on by Spring Cloud, a collection of projects for developing cloud-native applications with Spring.

For a complete discussion of Spring Cloud, I suggest taking a look at Cloud Native Spring in Action by Thomas Vitale (Manning, 2020, www.manning.com/books/ cloud-native-spring-in-action).

### 1.4.7 Spring Native

A relatively new development in Spring is the Spring Native project. This experimental project enables compilation of Spring Boot projects into native executables using the GraalVM native-image compiler, resulting in images that start significantly faster and have a lighter footprint.
For more information on Spring Native, see https://github.com/spring-projectsexperimental/spring-native

## 1.5 Summary 

1. Spring aims to ==make developer challenges easy==, like creating web applications,working with databases, securing applications, and microservices.
2. Spring Boot builds on top of Spring to make Spring even easier with ==simplified dependency management, automatic configuration, and runtime insights==.
3. Spring applications can be initialized using the Spring Initializr, which is webbased and ==supported natively in most Java development environments==.
4. The components, commonly referred to as beans, in a Spring application context can be declared explicitly with Java or XML, discovered by component scanning, or automatically configured with Spring Boot autoconfigurations.





