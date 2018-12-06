Spring Boot

# pom.xml

parent: spring-boot-starter-parent => add maven defaults (use `mvn spring-boot:run` to start up the application)
dependency: spring-boot-starter-web => add Tomcat and Spring MVC (use `mvn dependency:tree` to find out)
plugin: spring-boot-maven-plugin => enable packaging (use `mvn package` to create a standalone jar)

# build.gradle

plugin: id 'org.springframework.boot' version '2.1.1.RELEASE'
dependencies: compile 'org.springframework.boot:spring-boot-starter-web:2.1.1.RELEASE'

# Application.java

@RestController => stereotype annotation
@EnableAutoConfiguration => assume a web application 

@RequestMapping(“/“) => routing

# More Reading:

- https://docs.spring.io/spring-boot/docs/current/reference/html/using-boot-build-systems.html#using-boot-dependency-management
- https://docs.spring.io/spring/docs/5.1.3.RELEASE/spring-framework-reference/web.html#mvc