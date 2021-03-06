Spring Boot

# Build Configuration

### pom.xml

parent: spring-boot-starter-parent => add maven defaults (use `mvn spring-boot:run` to start up the application)
dependency: spring-boot-starter-web => add Tomcat and Spring MVC (use `mvn dependency:tree` to find out)
plugin: spring-boot-maven-plugin => enable packaging (use `mvn package` to create a standalone jar)

### build.gradle

plugin: id 'org.springframework.boot' version '2.1.1.RELEASE'
dependencies: compile 'org.springframework.boot:spring-boot-starter-web:2.1.1.RELEASE'

### Starters

https://github.com/spring-projects/spring-boot/tree/v2.1.1.RELEASE/spring-boot-project/spring-boot-starters



# Spring Application

__Application__

### @SpringBootApplication vs @EnableAutoConfiguration

- Availability 

@EnableAutoConfiguation => Spring Boot 1.0
@SpringBootApplication => Spring Boot 1.2

- @SpringBootApplication = @Configuration + @ComponentScan + @EnableAutoConfgiuration

@Configuration => enable Java configuration
@ComponentScan => enable component scanning, i.e. @Controller
@EnableAutoConfiguration => assume a web application 

- @EnableAutoConfiguration exclusion

@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})

__Controllers__

### @Controller vs @RestController

- Funtionality

@Controller => create a Map of model object and find a view
@RestController => every method in the class returns the object as JSON or XML in HTTP Response

- Availability

@Controller => Spring 2.5
@RestController => Spring 4

- @RestController = @Controller + @ResponseBody

- @RestController <: @Controller <: @Component

### @RequestMapping(“/“) => routing

__Service__

### @Service

### @Autowired

- Used for DI via constructor
- If a bean has one constructor, @Autowired can be ommited



# More Reading

- https://docs.spring.io/spring-boot/docs/current/reference/html/using-boot-build-systems.html#using-boot-dependency-management
- https://docs.spring.io/spring/docs/5.1.3.RELEASE/spring-framework-reference/web.html#mvc