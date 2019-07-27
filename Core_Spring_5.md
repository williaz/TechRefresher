[todo]: https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png "TODO logo"

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
- Spring manages the lifecycle of the app
  - all beans are fully initializaed before use
- Beans are always created in the right order
  - Based on their dependencies
- Each bean is bound to a unique id
  - the id reflects the service or role the bean provides to clients
  - Bean id should not contains implementation details


#### Application Context

- POJOs + config -> ApplicationContext -> app
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
- Use @Autowired to reference bean defined in another config file, or Referencing the bean in method parameters directly
#### Bean scope
- Default: ```@Scope("singleton")```
- ```@Scope("prototype")``` New instance created every time bean is referenced
- session: created per user session
- request: per request
- custom

#### Dependency Injection 
- Your Object is handed what it needs to work
  - frees it from the burden of resolving its dependencies
  - Simplifies your code, improves code reusability
- Promotes programminh to interfaces
  - Conceals impl details of dep
- Improve testability
  - dep easily stubbed out for unit testing
- Allows for centralized control over obj lifecycle
  - Opens the door for new possibilites


### DI
- External Properties
- Profiles
- Proxying


#### Env
- Env object used to obtain properties from runtime env  
  - JVM sys prop
  - Java Prop files
  - Servlet Context Param
  - Sys env var
  - JNDI
  
- Environment obtains values from "property sources"
  - System prop & Environment variables populated auto
  - use @PropertySources to contribute additional prop
    - ${} placeholders in a @PropertySource are resolved against existing properties
  - Aailable resource prefixes: plasspath: file: http:
  
```java
@Autowired public Environment env

@PropertySource("classpath:/com/org/config/app.properties")
```
- Beans can be grouped into Profiles
  - Profiles can represent purpse: "web", "offline"
  - Or environment: "dev", "qa", "uat", "prod"
  - Beans included / excluded based on profile membership
    - Benas with no profile always available
- Using @Profile on config class
  - All beans in config belong to the profile
-  Activate Profiles at runtime
  - Integration Test: Use @ActiveProfiles
  - System prop `-Dspring.profiles.active=dev,jpa`
  - in web.xml 
  ```xml
<context-param>
  <param-name>spring.profiles.active</param-name>
  <param-value>jpa,web</param-value>
</context-param>
  ```
  - Programmatically on ApplicationConext, used when Spring is started from a Main method
  
![alt text][todo]
- Spring actually proxies your @Configuration classes to allow for scope control
- Inheritance-based Proxies
  - At startup time, a child class is created
    - For each bean, an instance is cached in the child class
    - Child class only calls super at first instantiation
  - Child class is the entry point
  - Java config uses cglib for inheritance-based proxies
  - Prefer call to dedicated bean method
  
### Annotation
- Annotation-based config
- Best practices for component-scanning
- Java config VS annotation
- Mixing config
- @PostConstruct and @PreDestroy
- Steretypes and meta annotations
- @Resource
- Standard annotation JSR330

#### Implicit Config
- Annotation-based config within bean-class
  - @ embedded with POJOs
  - Find @Component classes within designated (sub)package
  ```java
  @Configuration
  @ComponentScan("com.bank")
  public class AnnotationConfig {
    // No bean def needed any more
  }
  ```
- @Autowired: unique dependency of correct type must exist
  - Constructor- injection
  - Method-injection
  - Field- injection: even when field is private(hard to unit test)
  - exception if no dependcy found
  ```java
  @Autowired(required=false) // only inject if exists
  ```
- @Qualifer: when many impl for same interface
```java
@Autowired
public TransferServiceImpl(@Qualifier("jdbcAccRepo")) AccRepo accRepo {...}

@Component("jdbcAccRepo")
public class JdbcAccRepo implements AccRepo{...}

@Component("jpaAccRepo")
public class JpaAccRepo implements AccRepo{...}
```
  - Component names are auto-generated: De-capitalized non-qalified classname by default(never rely on)


#### Component scanning
- Components are scanned at startup, JAR dep alsp scanned => slower startup time
```java
@ComponentScan("com.acme.app.repo, com.acme.app.service, com.acme.app.controller")
```

#### Mixing config
- use @ for Spring MVC beans
  - beans that are not referenced elsewhere
- Use Java Config for Service and Repo beans
- use @ when possible, still use Java config for legacy code can't be changed


![alt text][todo]
#### @PostConstruct and @PreDestroy

- Add behavior at startup and shutdown
- Annotated methods can have any visibilty but must take no param and only return void
- @ComponentScan requied
```java
public class JdbcAccRepo {
  @PostConstruct
  void populateCache() {} // called ar startup after DI(constructor, setter, @PostConstruct)
  
  @PreDestroy
  void clearCache() {} // called at shutdown pior to destroying the bean instance
}

ConfigurableApplicationContext context = ...

// Destory the app
context.close(); // triggers call of all @PreDestroy annotated methods
```











