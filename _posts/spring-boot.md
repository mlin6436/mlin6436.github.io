Spring Boot

# pom.xml

parent: spring-boot-starter-parent => add maven defaults (use `mvn spring-boot:run` to start up the application)
dependency: spring-boot-starter-web => add Tomcat and Spring MVC (use `mvn dependency:tree` to find out)
plugin: spring-boot-maven-plugin => enable packaging (use `mvn package` to create a standalone jar)

# Application.java

@RestController => stereotype annotation
@EnableAutoConfiguration => assume a web application 

@RequestMapping(“/“) => routing

# More Reading:

- https://docs.spring.io/spring-boot/docs/current/reference/html/using-boot-build-systems.html#using-boot-dependency-management
- https://docs.spring.io/spring/docs/5.1.3.RELEASE/spring-framework-reference/web.html#mvc