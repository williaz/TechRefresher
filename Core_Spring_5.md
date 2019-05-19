## Agenda:
- Core 1
  - [X] Intro Spring
  - [ ] Spring configuration
  - Java-based DI
  - Annotation-based DI
  - XML-based DI
- Core 2
  - Bean lifecycle
  - Testing with multiple profiles
  - AOP
  - Intro Data access
  - JDBC
- Data & Web
  - Transaction
  - ORM
  - JPA
  - Web Architecture
  - MVC
- Security, REST, Message, Manageability
  - Security
  - REST with MVC
  - JMS
  - JMX


### Intro Spring
- @Configuration and ApplicationContext
#### Application Context

- POJOs + config -> ApplicationContext -> app
- Spring manages the lifecycle of the app: All beans are fully initialized before use
- Beans are always created based on their dependencies
- Each bean is bound to a unqiue ID
  - the ID reflects the service or roe the bean provides to clients
  - Bean ID shuld not contain impl detals
- Spring Application context can be bootstraped in any env:
  - JUnit system test
  - Web app
  - Standalone app
```java
// Create app from config
ApplicationContext context = new AnnotationConfigApplicationContext(ApplicationConfig.class);
// accessing a Bean
// no need bean ID if type is unique from 3.x
KeyGenService keyGen1 = context.getBean(KeyGenService.class);
// type as a param from 3.x
KeyGenService keyGen2 = context.getBean("keyGeneration", KeyGenService.class);
// classic
KeyGenService keyGen3 = (KeyGenService)context.getBean("keyGeneration");
```
#### Config files
- partition bean definitions into logical groups
  - BP: seperate out "application" beans from "infrastructure" beans as Infra-structure often changes between env(datasource)
- Use @Import to combine miltiple config files
```java
@Configuration
@Import({InfrastructureConfig.class, WebConfig.class})
```
- Use @Autowired to reference bean defined in another config file, or referencing the bean in method parameters directly
#### Bean scope
- Default: ```@Scope("singleton")```
- ```@Scope("prototype")``` New instance created every time bean is referenced
- session: created per user session
- request: per request
- custom

#### Dependency Injection 
- simple and reusablity by freeing from resolving its dependencies
- Interface: decouple impl of dependencies
- unit test
- centralized control over object lifecycle



