### 1. CONTAINER, DEPENDENCY, AND IOC
- [x] What is dependency injection and what are the advantages?
  - container controls the relationships between diff parts of an app, instead of by themselves
  - Benefits:
    - decoupling
    - cohesion
    - testable
    - reusable
    - maintainable
    - standardize
    - reduce boilerplate
- [x] What is a pattern? What is an anti-pattern. Is dependency injection a pattern?
  - Pattern: template, best practice; general, reusable solution to a commonly occuring problem
  - Anti-pattern: attempt to solve problem, but reduce the producitve and efficiency of the code
  - yes
- [x] What is an interface and what are the advantages of making use of them in Java?
  - a reference type, contains
  
    - constants and nested types like enum
    - method signatures(no impl)
    - default method
    - static methods(with impl)
  - benefits:
    - decouple contact and impl
    - modularization
    - Polymorphism, handle a gouple of objects
    - ease to test
- [x] Why are interfaces recommended for Spring beans
  - ease to test, switch bean imp
  - can use JDK dynamic proxying
  - hide impl
- [x] What is meant by “application-context?
  - represents the Spring IoC container
  - Object implements ApplicationContext, used for
    - Instantiating beans in the application context.
    - Configuring the beans in the application context.
    - Assembling the beans in the application context.
    - Managing the life-cycle of Spring beans.
  - Acts as
    - bean factory
    - hierarcgical bean factory
    - resource loader: load file resources in a generic fashion
    - event publisher: to listeners
    - message source: resolve message and supports i18n
    - an environment: resolve properties, maintains profiles
  - can be more than one application context in a Spring app, arranged in parent-child hierarchy
  - common impl:
    - AnnotationConfigApplicationContext: standalone
    - AnnotationConfigWebApplicationContext
    - ClassPathXmlApplicationContext
    - FileSystemXmlApplicationContext
    - XmlWebApplicationContext
- [x] What is the concept of a “container” and what is its lifecycle?
  - As an environment for Spring beans, managing their lifecycle and supplying services
  - Lifecycle:
```text
1. Spring container is created as the application is started.
2. The container reads configuration data.
3. Bean definitions are created from the configuration data.
4. Bean factory post-processors processes the bean definitions.
BeanFactoryPostProcessor interface
5. Spring beans are instantiated by the container using the bean definitions.
6. Spring beans are configured and assembled.
Property values and dependencies are injected into the beans by the container.
7. Bean post-processors processes the beans in the container and any initialization callbacks
are invoked on the beans.
Bean post-processors are called both before and after any initialization callbacks are invoked
on the bean. BeanPostProcessor interface 
8. The application runs.
9. Application shut down is initialized.
10. The Spring container is closed.
11. Destruction callbacks are invoked on the singleton Spring beans in the container.
```
- [ ] How are you going to create a new instance of an ApplicationContext?
  - Non-Web

```java

/* Creates an empty application context. */
public AnnotationConfigApplicationContext()
/*
* Creates an application context that uses the supplied bean factory.
* Whether the application context is empty depends on the supplied bean factory.
*/
public AnnotationConfigApplicationContext(DefaultListableBeanFactory beanFactory)
/*
* Creates an application context populated with the beans from the supplied
* classes annotated with @Configuration.
*/
public AnnotationConfigApplicationContext(Class<?>... annotatedClasses)
// a @Configuration class
AnnotationConfigApplicationContext theApplicationContext =
new AnnotationConfigApplicationContext(MyConfiguration.class);
/*
* Creates an application context populated with the beans from the
* classes annotated with @Configuration found in the supplied package and its
* sub-packages.
*/
public AnnotationConfigApplicationContext(String... basePackages)
// all @Configuration classes in the package
AnnotationConfigApplicationContext theParentApplicationContext =
new AnnotationConfigApplicationContext(
"se.ivankrizsan.spring.examples.configuration");

```

  - Web
    - Servlet2: a web.xml file located in WEB-INF and a Spring XML configuration file,
      - The Spring ContextLoaderListener creates the **root** Spring web application context of a web application.
      - The dispatcher servlet will read a Spring XML configuration file named **[dispatcher servlet name]- servlet.xml** located in the WEB-INF directory and create a Spring application context that is a **child** to the Spring application root context created by the ContextLoaderlistener.
  

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://java.sun.com/xml/ns/j2ee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" id="WebApp_ID" version="2.4" xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd">
   <display-name>SpringWebServlet2</display-name>
   <context-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>/WEB-INF/springwebservlet2-config.xml</param-value>
   </context-param>
   <listener>
      <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
   </listener>
   <servlet>
      <servlet-name>SpringDispatcherServlet</servlet-name>
      <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
      <load-on-startup>1</load-on-startup>
   </servlet>
   <servlet-mapping>
      <servlet-name>SpringDispatcherServlet</servlet-name>
      <url-pattern>/</url-pattern>
   </servlet-mapping>
</web-app>

```
    - Servlet3: the web.xml file has become optional and a class implementing the WebApplicationInitializer can be used to create a Spring application context
      - Impl class:
        - AbstractContextLoaderInitializer: registers a ContextLoaderListener in the servlet context
        - AbstractDispatcherServletInitializer: DispatcherServlet
        - AbstractAnnotationConfigDispatcherServletInitializer: Java-config
        - AbstractReactiveWebInitializer: Spring reactive web app
      - XmlWebApplicationContext
```java
public class MyXmlWebApplicationInitializer implements WebApplicationInitializer {
    @Override
    public void onStartup(final ServletContext inServletContext) {
        /* Create Spring web application context using XML configuration. */
        final XmlWebApplicationContext theXmlWebApplicationContext =
            new XmlWebApplicationContext();
        theXmlWebApplicationContext
            .setConfigLocation("/WEB-INF/applicationContext.xml");
        theXmlWebApplicationContext.setServletContext(inServletContext);
        theXmlWebApplicationContext.refresh();
        theXmlWebApplicationContext.start();
        /*
         * Create and register the DispatcherServlet.
         * This is not strictly necessary if the application does not need
         * to receive web requests.
         */
        final DispatcherServlet theDispatcherServlet =
            new DispatcherServlet(theXmlWebApplicationContext);
        ServletRegistration.Dynamic theServletRegistration =
            inServletContext.addServlet("app", theDispatcherServlet);
        theServletRegistration.setLoadOnStartup(1);
        theServletRegistration.addMapping("/app/*");
    }
}
```

      - AnnotationConfigWebApplicationContext

```java
public class MyJavaConfigWebApplicationInitializer implements WebApplicationInitializer {
    @Override
    public void onStartup(ServletContext inServletContext) {
        /* Create Spring web application context using Java configuration. */
        final AnnotationConfigWebApplicationContext
        theAnnotationConfigWebApplicationContext =
            new AnnotationConfigWebApplicationContext();
        theAnnotationConfigWebApplicationContext.setServletContext(inServletContext);
        theAnnotationConfigWebApplicationContext.register(
            ApplicationConfiguration.class);
        theAnnotationConfigWebApplicationContext.refresh();
        /* Create and register the DispatcherServlet. */
        final DispatcherServlet theDispatcherServlet =
            new DispatcherServlet(theAnnotationConfigWebApplicationContext);
        ServletRegistration.Dynamic theServletRegistration =
            inServletContext.addServlet("app", theDispatcherServlet);
        theServletRegistration.setLoadOnStartup(1);
        theServletRegistration.addMapping("/app-java/*");
    }
}
```


- [ ] Can you describe the lifecycle of a Spring Bean in an ApplicationContext?
  - Spring bean configuration is read and metadata in the form of a BeanDefinition object is created for each bean.
  - All instances of BeanFactoryPostProcessor are invoked in sequence and are allowed an opportunity to alter the bean metadata.
  - For each bean in the container:
    - An instance of the bean is created using the bean metadata.
    - Properties and dependencies of the bean are set.
    - Any instances of BeanPostProcessor are given a chance to process the new bean instance before and after initialization.
      - Any methods in the bean implementation class annotated with @PostConstruct are invoked.
        - This processing is performed by a BeanPostProcessor.
      - Any afterPropertiesSet method in a bean implementation class implementing the InitializingBean interface is invoked.
        - This processing is performed by a BeanPostProcessor. If the same initialization method has already been invoked, it will not be invoked again.
      - Any custom bean initialization method is invoked.

        - Bean initialization methods can be specified either in the value of the init-method attribute in the corresponding <bean> element in a Spring XML configuration or in the initMethod property of the @Bean annotation.
        - This processing is performed by a BeanPostProcessor. If the same initialization method has already been invoked, it will not be invoked again.
     - The bean is ready for use.
  - When the Spring application context is to shut down, the beans in it will receive destruction callbacks in this order:
    - Any methods in the bean implementation class annotated with @PreDestroy are invoked.
    - Any destroy method in a bean implementation class implementing the DisposableBean interface is invoked.
      -  If the same destruction method has already been invoked, it will not be invoked again.
    - Any custom bean destruction method is invoked.
      - Bean destruction methods can be specified either in the value of the destroy-method attribute in the corresponding <bean> element in a Spring XML configuration or in the destroyMethod property of the @Bean annotation.
      - If the same destruction method has already been invoked, it will not be invoked again.
  
- [ ] How are you going to create an ApplicationContext in an integration test test?
          
- @RunWith (JUnit 4) or @ExtendWith (JUnit 5) is used to annotate the test-class.
- @ContextConfiguration for xml/Java config class
```java
//Junit4
@RunWith(SpringRunner.class)
@ContextConfiguration(classes=MyConfiguration.class)

//Junit 5
/* The @SpringJUnitConfig annotation is a combination of the JUnit 5
* @ExtendWith(SpringExtension.class) annotation and the Spring
* @ContextConfiguration annotation.
*/
@SpringJUnitConfig(classes=MyConfiguration.class)

// add for creating web type's          
@WebAppConfiguration
```

- [x] What is the preferred way to close an application context? Does Spring Boot do this for you?
          
- Standalone app
  - Registering a shutdown-hook by calling registerShutdownHook() and implements AbstractApplicationContext
    - context would be closed when JVM shut down normally
    - Recommanded
  - Calling close(); close immediately
- Web
  - taken care of by the ContextLoaderListener, which implements ServletContextListener;
  - it will receive ServletContextEvent when web container stops
- Boot
  - auto register a shutdown-hook
          
  - same as web
          
- [x] Can you describe:
  - DI for type ny config
    - define bean's Dependencies using constructor arg, setter, or factory method
    - IoC injects dependencies
  - Dependency injection using Java configuration?
  - Dependency injection using annotations (@Component, @Autowired)?
    - @Component(@Service, @Repository, @Controller) allow Spring beans to be automatically detected at component scanning
    - @Autowired annotation can be applied to constructors, methods, parameters and properties of a class.
      - need annotate if have multiple constructors
  - Component scanning, Stereotypes and Meta-Annotations?
    - @ComponentScan for searching annotated classess and registering bean in IoC
      - @Configuration annotation is annotated with the @Component
      - Using the basePackageClasses property, a base package to be scanned is specified by setting
a class located in the base package. Prefered
```Java
@ComponentScan(
basePackages = "se.ivankrizsan.spring.corespringlab.service",
basePackageClasses = CalculatorService.class,
excludeFilters = @ComponentScan.Filter(type = FilterType.REGEX, pattern =
".*Repository"),
includeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes =
MyService.class)
)
```
    - Stereotype annotations for role info: @RestController, @Configuration, @Component
    - Meta-Annotations: define @
```Java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Controller
@ResponseBody
public @interface RestController {
```
  - Scopes for Spring beans? What is the default scope?
```
singleton: Single bean instance per Spring container.
prototype: Each time a bean is requested, a new instance is created.
Only in web-aware Spring application contexts:
request: Single bean instance per HTTP request.
session: Single bean instance per HTTP session.
application: Single bean instance per ServletContext.
websocket: Single bean instance per WebSocket.
```
- [x] Are beans lazily or eagerly instantiated by default? How do you alter this behavior?
  - Singleton: eagerly initialized as the app context created
  - prototype: Lazily, except when it's a dependecny of a singleton bean
  - use @Lazy(boolean param, default is true)
    - on method with @Bean
    - on @Configuration class: apply on all beans declared inside
    - on @Component class: apply on the bean
    - also can use with @Autowire/@Inject to control injection
- [x] What is a property source? How would you use @PropertySource?
  -  A PropertySource is a simple abstraction over any source of key-value pairs
     - JVM sys prop: System.getProperties()
     - Sys env: System.getenv()
     - JNDI prop
     - Servlet config init param
     - Servlet context inti param
     - prop files: .properites file or XML
  - @PropertySource annotation can be used to add a property source, to apply on @Configuration class
```java
@Configuration
@PropertySource("classpath:testproperties.properties")
```

### 2.ASPECT ORIENTED PROGRAMMING
- What is the concept of AOP? Which problem does it solve? What is a cross cutting concern?
  - Name three typical cross cutting concerns.
  - What two problems arise if you don't solve a cross cutting concern via AOP?
- What is a pointcut, a join point, an advice, an aspect, weaving?
- How does Spring solve (implement) a cross cutting concern?
- Which are the limitations of the two proxy-types?
  - What visibility must Spring bean methods have to be proxied using Spring AOP?
- How many advice types does Spring support. Can you name each one?
  - What are they used for?
  - Which two advices can you use if you would like to try and catch exceptions?
- What do you have to do to enable the detection of the @Aspect annotation?
  - What does @EnableAspectJAutoProxy do?
- If shown pointcut expressions, would you understand them?
  - For example, in the course we matched getter methods on Spring Beans, what would be the
correct pointcut expression to match both getter and setter methods?
- What is the JoinPoint argument used for?
- What is a ProceedingJoinPoint? When is it used?


### 3.DATA MANAGEMENT: JDBC, TRANSACTIONS, JPA, SPRING DATA
- What is the difference between checked and unchecked exceptions?
  - Why does Spring prefer unchecked exceptions?
  - What is the data access exception hierarchy?
- How do you configure a DataSource in Spring? Which bean is very useful for development/test
databases?
- What is the Template design pattern and what is the JDBC template?
- What is a callback? What are the three JdbcTemplate callback interfaces that can be used with queries?
What is each used for? (You would not have to remember the interface names in the exam, but you
should know what they do if you see them in a code sample).
- Can you execute a plain SQL statement with the JDBC template?
- When does the JDBC template acquire (and release) a connection - for every method called or once per
template? Why?
- How does the JdbcTemplate support generic queries? How does it return objects and lists/maps of
objects?
- What is a transaction? What is the difference between a local and a global transaction?
- Is a transaction a cross cutting concern? How is it implemented by Spring?
- How are you going to define a transaction in Spring?
  - What does @Transactional do? What is the PlatformTransactionManager?
- Is the JDBC template able to participate in an existing transaction?
- What is a transaction isolation level? How many do we have and how are they ordered?
- What is @EnableTransactionManagement for?
- What does transaction propagation mean?
- What happens if one @Transactional annotated method is calling another @Transactional annotated
method on the same object instance?
- Where can the @Transactional annotation be used? What is a typical usage if you put it at class level?
- What does declarative transaction management mean?
- What is the default rollback policy? How can you override it?
- What is the default rollback policy in a JUnit test, when you use the
@RunWith(SpringJUnit4ClassRunner.class) in JUnit 4 or @ExtendWith(SpringExtension.class) in JUnit 5,
and annotate your @Test annotated method with @Transactional?
- Why is the term "unit of work" so important and why does JDBC AutoCommit violate this
- pattern?
- What does JPA stand for - what about ORM?
  - What is the idea behind an ORM? What are benefits/disadvantages or ORM?
  - What is a PersistenceContext and what is an EntityManager. What is the relationship between
both?
  - Why do you need the @Entity annotation. Where can it be placed?
- What do you need to do in Spring if you would like to work with JPA?
- Are you able to participate in a given transaction in Spring while working with JPA?
- Which PlatformTransactionManager(s) can you use with JPA?
- What does @PersistenceContext do?
- What do you have to configure to use JPA with Spring? How does Spring Boot make this easier?
- What is an "instant repository"? (hint: recall Spring Data)
- How do you define an “instant” repository? Why is it an interface not a class?
- What is the naming convention for finder methods?
- How are Spring Data repositories implemented by Spring at runtime?
- What is @Query used for?
### 4.SPRING BOOT
- What is Spring Boot?
- What are the advantages of using Spring Boot?
- Why is it “opinionated”?
- How does it work? How does it know what to configure?
- What things affect what Spring Boot sets up?
- How are properties defined? Where is Spring Boot’s default property source?
- Would you recognize common Spring Boot annotations and configuration properties if you saw them in
the exam?
- What is the difference between an embedded container and a WAR?
- What embedded containers does Spring Boot support?
- What does @EnableAutoConfiguration do?
- What about @SpringBootApplication?
- Does Spring Boot do component scanning? Where does it look by default?
- What is a Spring Boot starter POM? Why is it useful?
- Spring Boot supports both Java properties and YML files. Would you recognize and
- understand them if you saw them?
- Can you control logging with Spring Boot? How?
Note that the second Spring Boot section (Going Further) is not required for this exam.
Remember: Unless a question explicitly references Spring Boot (like those in this section) you
can assume Spring Boot is not involved in any question.

### 5.SPRING MVC AND THE WEB LAYER
- MVC is an abbreviation for a design pattern. What does it stand for and what is the idea
- behind it?
- Do you need spring-mvc.jar in your classpath or is it part of spring-core?
- What is the DispatcherServlet and what is it used for?
- Is the DispatcherServlet instantiated via an application context?
- What is a web application context? What extra scopes does it offer?
- What is the @Controller annotation used for?
- How is an incoming request mapped to a controller and mapped to a method?
- What is the difference between @RequestMapping and @GetMapping?
- What is @RequestParam used for?
- What are the differences between @RequestParam and @PathVariable?
- What are some of the parameter types for a controller method?
  - What other annotations might you use on a controller method parameter? (You can ignore
form-handling annotations for this exam)
- What are some of the valid return types of a controller method?
- What is a View and what's the idea behind supporting different types of View?
- How is the right View chosen when it comes to the rendering phase?
- What is the Model?
- Why do you have access to the model in your View? Where does it come from?
- What is the purpose of the session scope?
- What is the default scope in the web context?
- Why are controllers testable artifacts?
- What does a ViewResolver do?

### 6.SECURITY
Please note that @Secured and the Spring Security JSP tag library may be referenced in the exam but are not
in the main course notes. @Secured is in the advanced section.
- What are authentication and authorization? Which must come first?
- Is security a cross cutting concern? How is it implemented internally?
- What is the delegating filter proxy?
- What is the security filter chain?
- What is a security context?
- Why do you need the intercept-url?
- In which order do you have to write multiple intercept-url's?
- What does the ** pattern in an antMatcher or mvcMatcher do?
- Why is an mvcMatcher more secure than an antMatcher?
- Does Spring Security support password hashing? What is salting?
- Why do you need method security? What type of object is typically secured at the method level (think of
its purpose not its Java type).
- What do @PreAuthorized and @RolesAllowed do? What is the difference between them?
  - What does Spring’s @Secured do?
  - How are these annotations implemented?
  - In which security annotation are you allowed to use SpEL?
- Is it enough to hide sections of my output (e.g. JSP-Page or Mustache template)?
  - Spring security offers a security tag library for JSP, would you recognize it if you saw it in an
example?

### 7.REST
- What does REST stand for?
- What is a resource?
- What does CRUD mean?
- Is REST secure? What can you do to secure it?
- What are safe REST operations?
- What are idempotent operations? Why is idempotency important?
- Is REST scalable and/or interoperable?
- Which HTTP methods does REST use?
- What is an HttpMessageConverter?
- Is REST normally stateless?
- What does @RequestMapping do?
- Is @Controller a stereotype? Is @RestController a stereotype?
  - What is a stereotype annotation? What does that mean?
- What is the difference between @Controller and @RestController?
- When do you need @ResponseBody?
- What does @PathVariable do?
- What are the HTTP status return codes for a successful GET, POST, PUT or DELETE operation?
- When do you need @ResponseStatus?
- Where do you need @ResponseBody? What about @RequestBody? Try not to get these muddled up!
- If you saw example Controller code, would you understand what it is doing? Could you tell if it was
annotated correctly?
- Do you need Spring MVC in your classpath?
- What Spring Boot starter would you use for a Spring REST application?
- What are the advantages of the RestTemplate?
- If you saw an example using RestTemplate would you understand what it is doing?
### 8.TESTING
- Do you use Spring in a unit test?
- What type of tests typically use Spring?
- How can you create a shared application context in a JUnit integration test?
- When and where do you use @Transactional in testing?
- How are mock frameworks such as Mockito or EasyMock used?
- How is @ContextConfiguration used?
- How does Spring Boot simplify writing tests?
- What does @SpringBootTest do? How does it interact with @SpringBootApplication and
@SpringBootConfiguration?


