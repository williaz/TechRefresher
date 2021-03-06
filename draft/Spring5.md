### Keys
- IoC, ApplicationContext
- bean lifecycle
- http request flow
- dispatcher servlet
- REST constraints


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
    - decouple contract and impl
    - modularization
    - Polymorphism, handle a gouple of objects
    - ease to test
- [x] Why are interfaces recommended for Spring beans
  - ease to test, switch bean imp
  - can use JDK dynamic proxying
  - hide impl
#### IoC  
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
  
- [ ] How are you going to create an ApplicationContext in an integration test ?
          
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
  - [x] Dependency injection using Java configuration?
  - [x] Dependency injection using annotations (@Component, @Autowired)?
    - @Component(@Service, @Repository, @Controller) allow Spring beans to be automatically detected at component scanning
    - @Autowired annotation can be applied to constructors, methods, parameters and properties of a class.
      - need annotate if have multiple constructors
  - [x] Component scanning, Stereotypes and Meta-Annotations?
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
- [x] What is a BeanFactoryPostProcessor and what is it used for? When is it invoked?
- An interface with a method to modify bean meta-data at a container extension point(after prop and dep of bean are set)
- lifecycle: bean def -> BeanFactoryPostProcessor -> bean constructed -> prop and dep injected -> BeanPostProcessor -> bean init method -> bean ready for use
- ex:
  - DeprecatedBeanWarner
  - PropertySourcesPlaceholderConfigurer
    - @Value, properties-file
  - PropertyOverrideConfigurer
```java
@Bean
public static PropertySourcesPlaceholderConfigurer propertyConfigurer() {
return new PropertySourcesPlaceholderConfigurer();
}
```
- use @Order to order bean def method
- impl own with Ordered interface
  - [x] Why would you define a static @Bean method?
  - bean like that need to be instantiated prior to the instantiation of any beans used it
  - [x] What is a ProperySourcesPlaceholderConfigurer used for?
  - **resolves property placeholders**, on the ${PROPERTY_NAME} format, in Spring bean properties with  **@Value** annotation.
  - Using property placeholders, Spring bean configuration can be **externalized** into property files
- [x] What is a BeanPostProcessor and how is it different to a BeanFactoryPostProcessor? What do they do?
When are they called?
- both allow for def of container extensions
  - operate on beans; 
  - can use Order interface(ower earlier)
  - static method
- A BeanPostProcessor is an interface that defines **callback** methods called after bean instaniated to modify or replace(AOP) **bean instance** (BeanFactoryPostProcessor modify bean definitions.)
- BeanPostProcessor-s can be **registered programmatically** using the addBeanPostProcessor method as defined in the ConfigurableBeanFactory interface.
  - AutowiredAnnotationBeanPostProcessor: @Autowired
  - PersistenceExceptionTranslationPostProcessor: @Repository
  - [x] What is an initialization method and how is it declared on a Spring bean?
  - perform any initialization that depend on the properties of the bean to have been set
  - 1. afterPropertiesSet of InitializingBean(No Recommanded as coupled with Spring)
  - 2. Annotate the initialization method with the @PostConstruct
  - 3. Use the initMethod element init() of the @Bean annotation.
```java
public class ExampleBean {

    public void init() {
        // do some initialization work
    }
    
    public void cleanup() {
        // do some destruction work (like releasing pooled connections)
    }
}
```
  - [x] What is a destroy method, how is it declared and when is it called?
  - invoked immediately before the bean is to be taken out of use
  - 1. destroy() method of DisposableBean interface (X)
  - 2. Annotate the initialization method with the @PreDestroy
  - 3. Use the destroyMethod element cleanup() of the @Bean annotation
  - By default, beans defined using Java config that have a public close or shutdown method are automatically enlisted with a destruction callback. If you have a public close or shutdown method and you do not wish for it to be called when the container shuts down, simply add @Bean(destroyMethod="") to your bean definition to disable the default (inferred) mode.
    - do that by default for a resource that you acquire via JNDI as its lifecycle is managed outside the application. In particular, make sure to always do it for a DataSource 
   - [x] Consider how you enable JSR-250 annotations like @PostConstruct and @PreDestroy? When/how will they get called?
   - The CommonAnnotationBeanPostProcessor support, among other annotations, the @PostConstruct and @PreDestroy JSR-250 annotations.
   - uses annotation-based context, a default CommonAnnotationBeanPostProcessor is automatically registered
   - [x] How else can you define an initialization or destruction method for a Spring bean?
   - init(), destory()

- [x] What does component-scanning do?
- search for bean defs
- [] What is the behavior of the annotation @Autowired with regards to field injection, constructor injection and method injection?
- Autowiring is a mechanism which enables more or less automatic dependency resolution primarily based on types.
  - search bean with type match -> @Primary -> @Qualifier -> name of field -> exception
- @Autowired(@Inject)
  - AutowiredAnnotationBeanPostProcessor.
    - the @Autowired annotation cannot be used in neither BeanPostProcessor-s nor in BeanFactoryPostProcessor-s.
  - on array/Collection: collect all beans
  - on map: bean name as key
    - annotate the @Bean methods with the @Order or @Priority annotations for ordering
    - an empty array, collection or map will not be injected unless marked as optional => null
  - any visibility
```java 
public class MovieRecommender {

    @Autowired
    private MovieCatalog[] movieCatalogs;

    // ...
    
    @Autowired
    public void setMovieFinder(Optional<MovieFinder> movieFinder) {
        ...
    }
    
    @Autowired
    public void setMovieFinder(@Nullable MovieFinder movieFinder) {
        ...
    }
}


```
- [ ] What do you have to do, if you would like to inject something into a private field? How does this impact testing?
- Using the annotations @Autowired or @Value to annotate private fields in a class in order to
perform injection of dependencies or values is perfectly feasible if the source-code can be modified.
- use constructor-parameter dependency injection, where the reference or
value of the private field is assigned a value in the constructor using a constructor-parameter.
- TestinG:  replace the reference injected with a mock bean by using a test configuration that replaces the
original bean.
  - @TestPropertySource: To customize property values in a test
  - ReflectionTestUtils class
- [x] How does the @Qualifier annotation complement the use of @Autowired?
- @Qualifer when multiple beans of the same type
  - at Injection points: with @Autowired to select bean
  - at Bean def: with @BEan to supply a name
  - at Annotation def: crate custom @
- [ ] What is a proxy object and what are the two different types of proxies Spring can create?
- Decorator design pattern
  - same methods(at least same public) => proxy is indistinguishable from the object it proxies
  - as a reference to the org object +. when org obj is requested, proxy is supplied
  - invoke method on proxy
- Spring Proxy
  - JDK Dynamic Proxy(default)
    - Creates a proxy object that **implements all the interfaces** implemented by the proxied object.
    - Does not support self-invocations
      - Self-invocation is where one method of the object invokes another method on the same object.
  - CGLIB Proxy
    - Creates a **subclass** of the class of the object to be proxied.
    - no final class/method, no self-invocations
  - [x] What are the limitations of these proxies (per type)?
  - [x] What is the power of a proxy object and where are the disadvantages?
  - power:
    - add behavior to existing beans: transaction, logging
    - sepearte concerns
  - disadv:
    - proxy may chose not invoke the proxy obj
    - take care of order if multiple layers
    - Can only throw checked exceptions as declared on the original method.@@
      - Any recoverable errors in proxy objects that are not covered by checked exceptions declared on the original method may need to result in unchecked exceptions.
    - overhead: external resources
    - object identity issue: obj != proxy
- [x] What are the advantages of Java Config? What are the limitations?
- @Configuration class
  - regualr Java refactoring tool
  - type- check from IDE
  - Can be used with arbitrary classes: Java, Groovy, Kotlin, Scala etc
  - Separation of concerns: bean impl vs bean config
- limitation:
  - Configuration classes cannot be final.
  - subclassed by the Spring container using CGLIB
- [x] What does the @Bean annotation do?
- tells IoC the method annotated with the @Bean annotation will instantiate, configure and initialize an object that is to be managed by IoC.
- @Bean(name = "myFoo")
- @Scope("prototype")
- initMethod, destringMethod: @Bean(initMethod = "init") @Bean(destroyMethod = "cleanup")
- bean aliasing: multi-names: @Bean(name = { "dataSource", "subsystemA-dataSource", "subsystemB-dataSource" })
- default bean name is the name of the method

- [x] What is the default bean id if you only use @Bean? How can you override this?
- bean default name: method name; @Qualifier or name attr
- [x] Why are you not allowed to annotate a final class with @Configuration
- CGLIB, need subclass config class
  - [x] How do @Configuration annotated classes support singleton beans?
  - proxy of config class intercepts bean creation method, if existed, return directly; else trigger config class @Bean method
  - [x] Why can’t @Bean methods be final either?
  - proxy class need override it
- [x] How do you configure profiles?, What are possible use cases where they might be useful?
- profile: register bean depends on conditions
  - env
  - performance monitoring
  - app customization
```java
@Profile("!prod")

@Profile({"dev", "qa"})
```
- apply on @Configuration; @Component; @Bean
- Beans that do not belong to any profile will always be created and registered regardless of which profile(s) are active.
- Activating Profile
  - programmatic for IoC
  - spring.profiles.active
  - test: @ActiveProfiles on class
  - There is a default profile named “default” that will be active if no other profile is activated
```java
final AnnotationConfigApplicationContext theApplicationContext =
new AnnotationConfigApplicationContext();
theApplicationContext.getEnvironment().setActiveProfiles("dev1", "dev2");
theApplicationContext.scan("se.ivankrizsan.spring");
theApplicationContext.refresh();

java -Dspring.profiles.active=dev1,dev2 -jar myApp.jar
```
  
- [x] Can you use @Bean together with @Profile?
- [x] Can you use @Component together with @Profile?
- [x] How many profiles can you have?
- The Spring framework (in the class ActiveProfilesUtils) use an integer to iterate over an array of active profiles, which implies a maximum number of 2^32 – 1 profiles. 

- [x] How do you inject scalar/literal values into Spring beans?
- @Value: env, prop, beans
  - @Value("${personservice.retry-count}")
  - SpEL: @Value("#{ T(java.lang.Math).random() * 50.0 }")
- on field, method, method param, def of @

- [x] What is @Value used for?
- [x] What is Spring Expression Language (SpEL for short)?[SpEL](https://docs.spring.io/spring/docs/5.0.3.RELEASE/spring-framework-reference/core.html#expressions)
- supports querying and manipulating an object graph at runtime
- referencing bean: @mySuperComponent.injectedValue
- create map: {1 : "one", 2 : "two", 3 : "three", 4 : "four"}
- access variables: #num, #countMap\['xyz']
- Method invocation: #javaObject.firstAndLastName()

- [x] What is the Environment abstraction in Spring?
- The Environment is a part of the application container. The Environment contains profiles and properties(JVM, Sys; Servlet, ServletContext, JNDI)
- ApplicationContext interface extends the EnvironmentCapable interface, which contain one single method namely the getEnvironment method,
- [x] Where can properties in the environment come from – there are many sources for properties – check the documentation if not sure. Spring Boot adds even more.
- [x] What can you reference using SpEL?
- static method/field: T(se.ivankrizsan.spring.MyBeanClass).myStaticMethod()
- bean's method/field: @mySuperComponent.toString()
- Properties and methods in Java objects with references stored in SpEL variables: #javaObject.firstName
- (JVM) System properties:@systemProperties\['os.name']
- System environment properties: @systemEnvironment\['KOTLIN_HOME']
- Spring application environment: @environment\['defaultProfiles']\[0]

- [x] What is the difference between $ and # in @Value expressions?
- $
  - can only be used in @Value
  - refering property name in the application’s environment
  - evaluated by the PropertySourcesPlaceholderConfigurer
- \# SpEL

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
- [x] What is the difference between checked and unchecked exceptions?
- checked exception: must be catch or throw
- unchecked exceptions are java.lang.RuntimeException and any its subclass
  - [x] Why does Spring prefer unchecked exceptions?
  - result in cluttered code and/or unnecessary coupling to the underlying methods.
  - [x] What is the data access exception hierarchy?
  - DataAccessException
  - isolate application developers from the particulars of JDBC data access APIs
  
- [x] How do you configure a DataSource in Spring? Which bean is very useful for development/test
databases?
- After having chosen the appropriate DataSource implementation class, a data-source bean is created like any other bean
- Spring Boot: Setting the values of a few properties are sufficient
- JNDI lookup

- [x] What is the Template design pattern and what is the JDBC template?
- JdbcTemplate.execute()
- JdbcTemplate class is a Spring class that simplifies the use of JDBC by implementing common workflows for querying, updating, statement execution
  - simplify, handle common operation to avoid commn mistakes
  - handle exception and translate to DataAccessException hierarchy to be vendor-agnostic
  - allow customization
- Instances of JdbcTemplate are thread-safe after they have been created and configured.


- [x] What is a callback? What are the three JdbcTemplate callback interfaces that can be used with queries? What is each used for? (You would not have to remember the interface names in the exam, but you should know what they do if you see them in a code sample).
- Callback: is any executable code that is passed as an argument to other code that is expected to call back (execute) the argument at a given time. Lambda/Interface
- callbacks for JdbcTemplate:
  - ResultSetExtractor: Allows for processing of an entire result set, possibly consisting multiple rows of data, at once. stateless
  - RowCallbackHandler: Allows for processing rows in a result set one by one typically accumulating some type of result. stateless, void processRow()
  - RowMapper: Allows for processing rows in a result set one by one and creating a Java object for each row.

- [x] Can you execute a plain SQL statement with the JDBC template?
- batchUpdate
- execute
- query
- queryForList
- queryForMap
- queryForObject
- queryForRowSet
- update


- [x] When does the JDBC template acquire (and release) a connection - for every method called or once per template? Why?

- JdbcTemplate acquire and release a database connection for every method called. immediately
- avoid holding on to resources, back to connection pool



- [x] How does the JdbcTemplate support generic queries? How does it return objects and lists/maps of
objects?
- The **queryForList** methods all return a list containing the resulting rows of the query
  - with type as param or not
- All **queryForMap** methods are expected to return one single row.


- [x] What is a transaction? What is the difference between a local and a global transaction?
- A transaction is an operation that consists of a number of tasks that takes place as a single unit – either all tasks are performed or no tasks are performed.
- A reliable transaction system enforces the ACID principle:
  - Atomicity: **“All or nothing”**, The changes within a transaction are either all applied or none applied.
  - Consistency: Any integrity **constraints**, for instance of a database, are not violated.
  - Isolation: Transactions are isolated from each other and do not affect each other. Visible during
  - Durability: Changes applied as the **result** of a successfully completed transaction are durable.
- Global transactions allow for transactions to span multiple transactional resources. like DB + MQ
- Local transactions are transactions associated with one **single** resource

- [x] Is a transaction a cross cutting concern? How is it implemented by Spring?
- Yes, using AOP

- [x] How are you going to define a transaction in Spring?
- Declare a PlatformTransactionManager bean: impls like
  - JmsTransactionManager
  - JpaTransactionManager
- Add @EnableTransactionManagement on @Configuration class
- Declare transaction boundaries in the application code
  - @Transactional, can replaced with JPA's
  - Programmatic transaction management
  - XML config

  - [x] What does @Transactional do? What is the PlatformTransactionManager?
  - @Transactional
    - to specify attr, can be applied to methods and classes.
    - transactionManager, isolation, propagation, readOnly, timeout
    - rollbackFor, rollbackForClassName, noRollbackFor, noRollbackForClassName
  - PlatformTransactionManager
    - void commit/rollback(TransactionStatus)
    - TransactionStatus getTransaction(TransactionDefinition): create new
    - recommended to use declarative transactions or the TransactionTemplate class
  
- [x] Is the JDBC template able to participate in an existing transaction?
- accomplished by wrapping the DataSource using a TransactionAwareDataSourceProxy.



- [ ] What is a transaction isolation level? How many do we have and how are they ordered?
- Transaction isolation in database systems determine how the changes within a transaction are visible to other users and systems accessing the database prior to the transaction being committed
  - reduce concurrency
- Serializable
  - highest: read/write lock + range-locks for select-where
- Repeatable reads
  - no range-lock, can read uncommitted data from itself
  - Phantom Read occurs when two same queries are executed, but the rows retrieved by the two, are different.
- Read committed
  - Read locks are held, but only until the select statement with which the read lock is associated has completed.
  - non-repeatable reads can occur
  - A non-repeatable read is one in which data read twice inside the same transaction cannot be guaranteed to contain the same value
- Read uncommitted
  - A Dirty read is the situation when a transaction reads a data that has not yet been committed. 
  
  
- [x] What is @EnableTransactionManagement for?
- to annotate exactly one configuration class in an application in order to enable annotation-driven transaction management using the @Transactional
- it registers:
  - A TransactionInterceptor
  - A JDK Proxy or AspectJ advice.
- optional attr:
  - mode: AdviceMode.ASPECTJ and AdviceMode.PROXY
  - order: Default value is Ordered.LOWEST_PRECEDENCE
  - proxyTargetClass: True of CGLIB proxies are to be used, false if JDK interface-based proxies are to be used in the application (affects proxies for all Spring managed beans in the application!).


- [x] What does transaction propagation mean?
- Typically, all code executed within a transaction scope will run in that transaction. However, you have the option of specifying the behavior in the event that a transactional method is executed when a transaction context already exists. For example, code can continue running in the existing transaction (the common case); or the existing transaction can be suspended and a new transaction created.
- 7 opts:
  - MANDATORY: There **must be an existing** transaction when the method is invoked, or an exception will be thrown.
  - NESTED: Executes in a nested transaction if a transaction exists, otherwise a new transaction will be created. This transaction propagation mode is not implemented in all transaction managers.
  - NEVER: Method is executed **outside** of a transaction. Throws exception if a transaction exists.
  - NOT_SUPPORTED: Method is executed outside of a transaction. **Suspends any existing** transaction.
  - REQUIRED Method will be executed in the **current** transaction. If no transaction exists, one will be created.
  - REQUIRES_NEW: Creates a new transaction in which the method will be executed. **Suspends any existing **transaction
  - SUPPORTS: Method will be executed in the current transaction, if one exists, or outside of a transaction if one does not exist.

- [ ] What happens if one @Transactional annotated method is calling another @Transactional annotated
method on the same object instance?
- a self-invocation of a proxied Spring bean effectively bypasses the proxy and thus also any transaction interceptor managing transactions.
- sec same transaction context as first 
- ?? If Spring transaction management is used with AspectJ, then any transaction-configuration using @Transactional on non-public methods will be honored.


- [x] Where can the @Transactional annotation be used? What is a typical usage if you put it at class level?
- When using Spring AOP proxies, only @Transactional annotations on public methods will have any effect
- no control no error for other access

- [x] What does declarative transaction management mean?
- not implemented, but use annotations or Spring XML configuration.

- [x] What is the default rollback policy? How can you override it?
- automatic rollback only takes place in the case of an unchecked exception being thrown.
- use rollbackFor, noRollbackFor control which exception

- [x] What is the default rollback policy in a JUnit test, when you use the
@RunWith(SpringJUnit4ClassRunner.class) in JUnit 4 or @ExtendWith(SpringExtension.class) in JUnit 5,
and annotate your @Test annotated method with @Transactional?
- automatically be rolled back after the completion of the test-method.
- to change: @Rollback annotation and setting the value to false.


- Why is the term "unit of work" so important and why does JDBC AutoCommit violate this pattern?
- JDBC AutoCommit commit each individual SQL 
- impossible to perform operations that consist of multiple SQL statements as a unit of work.
- JDBC AutoCommit can be disabled by calling the **setAutoCommit** method with the value false on a JDBC connection.


#### PersistenceContext, EntityManager, persistence unit, Entity
- [] What does JPA stand for - what about ORM?
- JPA stands for Java Persistence API, Is the Java API for the management of persistence and object/relational mapping in Java EE and Java SE environments”.
- ORM is an abbreviation for Object-Relational Mapping and is a technique for converting data format typically between what can be inserted into a SQL database and what can be represented by a Java object hierarchy residing in memory.

  - [x] What is the idea behind an ORM? What are benefits/disadvantages or ORM?
  - to allow developers to work with  domain objects, with their properties and relations as Java objects in memory without having to concern they are persisted in DB.
  - Data-type conversion, Maintaining relationships between objects, no need deal with SQL
  - Caching, Lazy loading
  - may affect preformance, add complexity, hard to use with legacy DB
  
  
  - [x] What is a PersistenceContext and what is an EntityManager. What is the relationship between
both?
  - a persistence context is a set of managed objects, entities
  - A persistence context can exists either for one single transaction(default) or extended time
  - EntityManager: service for managing a persistence context and interacting
  - The entity types, typically Java classes, that are managed by an entity manager are defined in a persistence unit. The entity classes in a persistence unit must be located in one and the same database.
  - [x] Why do you need the @Entity annotation. Where can it be placed?
  - turn class into persistent entities, to be included in a persistence unit.
  - only class
  
- [x] What do you need to do in Spring if you would like to work with JPA?
- 1. Declare the appropriate **dependencies**. ORM fraework dep, DV driver dep, transaction dep
- 2. Implement **entity** classes with mapping metadata in the form of annotations.@Entity, @Id
- 3. Define an EntityManagerFactory bean.
  - An adapter for the ORM implementation: Hibernate
  - type of database
  - ORM configuration properties
  - package(s) to scan for entities
- 4. Define a DataSource bean.
- 5. Define a TransactionManager bean: JpaTransactionManager
- 6. Implement repositories, Spring Data JPA: only need to create repository interfaces.

- [ ] Are you able to participate in a given transaction in Spring while working with JPA?
- JpaTransactionManager supports direct DataSource access within one and the same transaction allowing for mixing plain JDBC code
- JtaTransactionManager will delegate to the JavaEE server’s transaction coordinator.

- [ ] Which PlatformTransactionManager(s) can you use with JPA?
- JTA transaction manager can be used with JPA since JTA transactions are global transactions, that is they can span multiple resources such as databases, queues
- one single entity manager factory: JpaTransactionManager
- multiple JPA entity manager factories: JTA

- What does @PersistenceContext do?
- @PersistenceContext annotation is applied to a instance variable of the type EntityManager or a setter method
- Expresses a dependency on a container-managed EntityManager and its associated persistence context.

- [x] What do you have to configure to use JPA with Spring? How does Spring Boot make this easier?
- Spring Boot:
  - Provides a default set of dependencies needed for using JPA
  - Provides all the Spring beans needed to use JPA.
  - Provides a number of default properties related to persistence and JPA



- [x] What is an "instant repository"? (hint: recall Spring Data)
- An instant repository, also known as a Spring Data repository, is a repository that need no implementation and that supports the basic CRUD (create, read, update and delete) operations.
- interface Repository<EnityType, KeyType>


- [x] How do you define an “instant” repository? Why is it an interface not a class?
- extends JpaRepository
  - @Embeddable: for a custom primary key class.
  - @Id: for a regular primary key class, like Long, Integer or String
- @EnableJpaRepositories to enable the discovery and creation of repositories.

- reasons as a interface:
  - usually is no need for implementation of the methods defined in the interface
  - to use the JDK dynamic proxy mechanism to create the proxy objects that intercept calls to repositories.
  - allows for supplying a custom base repository implementation class

- [x] What is the naming convention for finder methods?
- ```find(First[count])By[property expression][comparison operator][ordering operator]```
- Optional findIFrst10BySourceAndSetIgnoreCaseAndValueLessThan100OrderByValueDesc();

- [x] How are Spring Data repositories implemented by Spring at runtime?
- For a Spring Data repository a JDK dynamic proxy is created which intercepts all calls to the repository. to route calls to the default repository implementation in SimpleJpaRepository class or invoke custom impl
- The fundamental approach is that a JDK proxy instance is created programmatically using Spring's ProxyFactory API to back the interface and a MethodInterceptor intercepts all calls to the instance and routes the method into the appropriate places:https://stackoverflow.com/questions/38509882/how-are-spring-data-repositories-actually-implemented

- [x] What is @Query used for?
- annotated repository method or supplying a query that is to be used for a repository method

   
          
          
### 4.SPRING BOOT
- [x] What is Spring Boot?
- easy to create standalone Spring app
- provides coomon non-functional features: embedded servers, security, metrics, health checks, and externalized configuration
- auto-config: no XML

- starters: dependecy descriptors: menaged dependencis
- Autoconfiuration
- Actuator

- [x] What are the advantages of using Spring Boot?
- auto-config: reducing boilerplate configuration, no XML config
- quick getting-started
- easy customization: prop file
- executable standalone JAR
- managed dependencis and Maven plug-ins
- non-functional features commonly needed in projects
- Standardize parts of application structure


- [x] Why is it “opinionated”?
- preset by Spring with defaults
- but allow to customize

- [x] How does it work? How does it know what to configure?
- Spring Boot detects the dependencies available on the classpath and configures Spring beans
accordingly.
- alow for conditional: @ConditionalOnClass, @ConditionalOnBean, @ConditionalOnMissingBean and @ConditionalOnMissingClass

- [x] What things affect what Spring Boot sets up?
- @Conditional annotations on your auto-configuration class.


- [x] How are properties defined? Where is Spring Boot’s default property source?
- types:
  - Property file: key-value pairs
  - YAML files
  - Env
  - Command-line args: ```java -jar xx.jar -server.port=8081```
- src/main/resources/application.properties
- externalized config to override config
  - ```--spring.config.location=``` in the command line
  - auto pick up application.properties in the same directory as the jar file

- [x] Would you recognize common Spring Boot annotations and configuration properties if you saw them in
the exam?
- config prop:
  - format: spring.xxx.yyy=somevalue; xxx as technology; yyy as prop
  - https://docs.spring.io/spring-boot/docs/2.0.6.RELEASE/reference/htmlsingle/#common-application-properties

- @SpringBootApplication, @EnableAutoConfiguration
- @Conditional[yyy] Class of Spring Boot annotations that enable conditional creation of Spring beans. Commonly used in auto-configuration modules.
- @EnableConfigurationProperties Enables support for Spring beans annotated with @ConfigurationProperties.
- @ConfigurationProperties Allows for binding a selected group of configuration properties to a class.
- @EntityScan Specifies which packages to scan when scanning for entity classes.
- @SpringBootConfiguration Alternative to @Configuration for Spring Boot applications.
- @OverrideAutoConfiguration Used to override the @EnableAutoConfiguration.
- @ImportAutoConfiguration Import an apply specified auto-configuration classes.


- @ServletComponentScan Enables scanning for servlets, WebFilter filters, WebServlet servlets and WebListener listeners when using an embedded web server.
- @LocalServerPort Alternative to @Value("${local.server.port}")


- @JsonComponent Specialization of the @Component annotation for components that provides a Jackson JSON serializer or deserializer.

- @WebMvcTest Annotation used in Spring MVC tests testing only Spring MVC components.
- @TestConfiguration Alternative to @Configuration for test configuration classes.
- @MockBean Used to add a mock bean to the Spring application context.
- @SpyBean Applies Mockito spies to one or more Spring beans.
- @SpringBootTest Annotates test classes that run Spring Boot based tests.
- @TestComponent Component annotation for beans that only are to be used in tests.
- @AutoConfigureTestEntityManager Enable auto-configuration of a test entity manager in tests.
- @DataJpaTest Used with @RunWith(SpringRunner.class) in tests that only tests JPA components.
- @PropertyMapping Maps attributes from a test annotation into a PropertySource.
- @AutoConfigureRestDocs Enable auto-configuration of Spring REST Docs in tests.
- @AutoConfigureMockRestServiceServer Enable auto-configuration of a single mock REST service server in tests.
- @RestClientTest Used with @RunWith(SpringRunner.class) in tests that use a Spring REST client.
- @AutoConfigureWebClient Enables auto-configuration of web clients in test classes.
- @AutoConfigureWebTestClient Enables a web test client in WebFlux application tests.
- @WebFluxTest Used with @RunWith(SpringRunner.class) in tests that only tests Spring WebFlux components.
- @AutoConfigureMockMvc Enables auto-configuration of MockMvc in testclasses.
- @WebMvcTest Used with @RunWith(SpringRunner.class) in tests that focuses on Spring MVC components.


- @Endpoint Identifies a class as being an actuator endpoint that provides information about the Spring Boot application.
- @ReadOperation Marks a method in a class annotated with @Endpoint as being a read operation.



- [x] What is the difference between an embedded container and a WAR?
- a WAR is a build for an app, will be deployed in web container which may server multiple apps
- An embedded container is packaged in the application JAR-file and will contain only one single application.


- [x] What embedded containers does Spring Boot support?
- Tomcat, Jetty, Undertow

- [x] What does @EnableAutoConfiguration do?
- The @EnableAutoConfiguration annotation enables Spring Boot auto-configuration, which attempts to create and configure Spring beans based on the dependencies available on the class-path to allow developers to quickly get started with different technologies in a Spring Boot application and reducing boilerplate code and configuration.


- [x] What about @SpringBootApplication?
- = @Configuration, @EnableAutoConfiguration and @ComponentScan

- [x] Does Spring Boot do component scanning? Where does it look by default?
- nope unless with @Configuration + @ComponentScan
- look up basePackages, classes' packages in @ComponentScan
- look config class's package

- [x] What is a Spring Boot starter POM? Why is it useful?
- all the dependencies needed to get started with a certain technology have been gathered with proper version management.

- [x] Spring Boot supports both Java properties and YML files. Would you recognize and understand them if you saw them?
- A Java properties file consists of one or more rows with a key-value pair
- YML files are files containing properties in the YAML format. YAML stands for YAML Ain’t Markup Language.
```YML
# This is a comment.
se:
  ivan:
    username: ivan
    password: secret
city: Gothenburg
```


- [x] Can you control logging with Spring Boot? How?
- Controlling Log Levels
  - default message level: ERROR, WARN and INFO levels
  - To enable DEBUG or TRACE logging for the entire application, use the -- debug or --trace flags or set the properties debug=true or trace=true in the application.properties file.
```
logging.level.root=WARN
logging.level.org.wtc.service=DEBUG
logging.pattern.console=%clr(%d{yyyy-MM-dd HH:mm:ss}){yellow}

# When using the default Logback setup in a Spring Boot applicatio
logging.pattern.console=# or sys prop CONSOLE_LOG_PATTERN
logging.pattern.file=# or sys prop FILE_LOG_PATTERN
```
- Spring Boot uses the Commons Logging API for logging. suport: Logback, Log4J2
- Logging config
  - logback-spring.xml: logback.xml + Boot templating features
  - log4j2.xml: log4j2.yaml, log4j2.json
  - logging.properties: Java core logging
  
Note that the second Spring Boot section (Going Further) is not required for this exam.
Remember: Unless a question explicitly references Spring Boot (like those in this section) you
can assume Spring Boot is not involved in any question.
### 5.SPRING MVC AND THE WEB LAYER
- [x] MVC is an abbreviation for a design pattern. What does it stand for and what is the idea behind it?
- Model-View-Controller, which is a design pattern used to separate concerns of an application
  - Model: The model holds the current data and business logic of the application.
  - View: The view is responsible for presenting the data of the application to the user. The user interacts with the view.
  - Controller: a mediator between the model and the view
- Adv:
  - Reuse of model and controllers with different views.
  - Reduced coupling between the model, view and controller.
  - Separation of concerns: in turn result in increased maintainability and extensibility
- [x] Do you need spring-mvc.jar in your classpath or is it part of spring-core?
- Spring Web MVC is spring-webmvc
- Spring WebFlux is spring-webflux

- [x] What is the DispatcherServlet and what is it used for?
- implement the front controller design pattern(allows for centralizing matters, like security and error handling)
- Receives **requests** and delegates them to registered handlers.
  - **1 per app**
- Resolves **views** by mapping view-names to View instances.
- Resolves exceptions that occur during handler mapping or execution.
  - error view

- [ ] Is the DispatcherServlet instantiated via an application context?
- no, It is instantiated before any application context is created.

- [x] What is a web application context? What extra scopes does it offer?
- WebApplicationContext interface 
  - extends the ApplicationContext
  - add a method for retrieving the standard Servlet API ServletContext for the web application
- extra scope:
  - request
  - session
  - application: per ServletContext

- [x] What is the @Controller annotation used for?
- Indicates "Controller" class
- @Component: enable autodetect through classpath scanning

- [x] How is an incoming request mapped to a controller and mapped to a method?
- The DispatcherServlet of the application receives the request.
- The DispatcherServlet maps the request to a method in a controller.
  - The DispatcherServlet holds a list of classes implementing the HandlerMapping interface.
- The DispatcherServlet dispatches the request to the controller.
-  The method in the controller is executed.
- @ComponentScan, @EnableWebMvc, @Controller, @RequestMapping

- [x] What is the difference between @RequestMapping and @GetMapping?
- If no method element specified in the @RequestMapping annotation, then requests using any HTTP method will be mapped to the annotated method.

- [x] What is @RequestParam used for?
- bind request parameters to method parameters.
- question mark, ampersand
```java
@RequestMapping("/greeting")
public String greeting(@RequestParam(name="name", required=false) String inName) {}
```
- [x] What are the differences between @RequestParam and @PathVariable?
- The values in the @PathVariable annotations match the name of the template variables in
the URL.
  - optional if same
```java
@RequestMapping("/firstname/{firstName}/lastname/{lastName}")
public String greetWithFirstAndLastName(
@PathVariable("firstName") final String inFirstName,
@PathVariable("lastName") final String inLastName) {
```
- @RequestParam maps query string parameters to handler method arguments.
- @PathVariable maps a part of the URL to handler method arguments.

- [ ] What are some of the parameter types for a controller method?
- WebRequest, NativeWebRequest
- javax.servlet.ServletRequest, javax.servlet.ServletResponse
- javax.servlet.http.HttpSession
- java.security.Principal
- HttpMethod
- java.io.InputStream, java.io.Reader
- java.io.OutputStream, java.io.Writer
- HttpEntity\<B>

  - [] What other annotations might you use on a controller method parameter? (You can ignore form-handling annotations for this exam)
  - @MatrixVariable
  - @RequestHeader
  - @CookieValue
  - @RequestPart: "multipart/form-data" 
  - @ModelAttribute
  - SessionStatus + class-level @SessionAttributes
  - @SessionAttribute
  - @RequestAttribute
  
- [x] What are some of the valid return types of a controller method?
- @ResponseBody
- HttpEntity\<B>, ResponseEntity\<B>
- HttpHeaders
- String: view name
- View
- ModelAndView  
- @ModelAttribute
- void: A method with a void return type (or null return value) is considered to have fully handled the response if it also has a ServletResponse, or an OutputStream argument, or an @ResponseStatus annotation. The same is true also if the controller has made a positive ETag or lastModified timestamp check (see Controllers for details).
- DeferredResult<V>, Callable<V>
- ResponseBodyEmitter, SseEmitter
- StreamingResponseBody
- Reactive types — Reactor, RxJava, or others via ReactiveAdapterRegistry
- https://docs.spring.io/spring/docs/5.0.7.RELEASE/spring-framework-reference/web.html#mvc-ann-arguments
  
- [ ] What is a View and what's the idea behind supporting different types of View?
- Present model data in different **formats**.(bar/pie chart)
- Adapt the view to different **platforms**.(web/mobile)
- Use a view-**technology** that is familiar to developers

- [ ] How is the right View chosen when it comes to the rendering phase?
- dispatcher servlet holds a list of **view resolvers**, it will use them to resolve the view name available from the ModelAndView during the request-processing process

- [x] What is the Model?
- Model interface from the Spring framework is a collection of key-value pairs
- contains information for rendering the view

- [x] Why do you have access to the model in your View? Where does it come from?
- The model is passed as a parameter to the view when the dispatcher servlet asks the selected view to render itself as part of processing a request. 
- The dispatcher servlet obtains an instance of ModelAndView as a result of invoking the handle method on a HandlerAdpater when processing a request.


- [x] What is the purpose of the session scope?
- The bean instance will remain the same during all requests the user makes within one
and the same HTTP session.

- [x] What is the default scope in the web context?
- singleton

- [x] Why are controllers testable artifacts?
- plain Java classes
- Spring MVC Test Framework
  - Servlet 3.0 API mock objects.
  - full Spring MVC runtime behavior
- [x] What does a ViewResolver do?
- map a view name to a View.



### 6.SECURITY
Please note that @Secured and the Spring Security JSP tag library may be referenced in the exam but are not
in the main course notes. @Secured is in the advanced section.
- [x] What are authentication and authorization? Which must come first?
- Authentication: confrim info is true => verify user
  - Spring security authen process:
    - combine user and password in UsernamePasswordAuthenticationToken instance
    - token is passed to an instance of AuthenticationManager for validation
    - AuthenticationManager returns a fully populated Authentication instance on successful authentication.
    - security context is established by calling SecurityContextHolder.getContext().setAuthentication(…), passing in the returned authentication object.
- Authorization: permission, access to resource
- authen first

- [x] Is security a cross cutting concern? How is it implemented internally?
- yes
- Spring AOP proxy that inherits from the AbstractSecurityInterceptor
  - Applied to method invocation authorization on objects secured with Spring Security
- Web: servlet filters.
  - The DelegatingFilterProxy delegates to a FilterChainProxy. 
  - The FilterChainProxy is defined as a Spring bean and takes one or more SecurityFilterChain instances as constructor parameter(s).
  - A SecurityFilterChain associates a request URL pattern with a list of (security) filters
- Spring Security Core Components
  - SecurityContextHolder: Contains and provides access to the SecurityContext of the application. Default behavior is to **associate the SecurityContext** with the current thread.
  - SecurityContext: Default and only implementation in Spring Security **holds an Authentication** object.
  - Authentication: Represents token for authentication request or authenticated **principal**, contains its **granted authorities**
  - GrantedAuthority: Represents **an authority** granted to an authenticated principal.
  - UserDetails: user info, create Authentication
  - UserDetailsService: retrieves information about the user in a UserDetails
```
SecurityContextHolder, to provide access to the SecurityContext.
SecurityContext, to hold the Authentication and possibly request-specific security information.
Authentication, to represent the principal in a Spring Security-specific manner.
GrantedAuthority, to reflect the application-wide permissions granted to a principal.
UserDetails, to provide the necessary information to build an Authentication object from your application’s DAOs or other source of security data.
UserDetailsService, to create a UserDetails when passed in a String-based username (or certificate ID or the like).
```

- [] What is the delegating filter proxy?
- DelegatingFilterProxy class implements the javax.servlet.Filter interface 
  - servlet filter
  - delegate filtering to a bean
    - The servlet filter bean lifecycle methods init and destroy will by default not be called.
      - to enable: targetFilterLifecycle property to true

- [x] What is the security filter chain?
- a SecurityFilterChain associates a request URL pattern with a list of (security) filters
- implements the SecurityFilterChain interface, only impl: DefaultSecurityFilterChain class
  - order matters
- request matcher
  - RequestMatcher interface
  - MvcRequestMatcher and AntPathRequestMatcher.
  - URL pattern
- Filters
  - DefaultSecurityFilterChain constructor takes args as request matcher first then optional filter with below order

    - ChannelProcessingFilter, because it might need to redirect to a different protocol
    - SecurityContextPersistenceFilter, so a SecurityContext can be set up in the SecurityContextHolder at the beginning of a web request, and any changes to the SecurityContext can be copied to the HttpSession when the web request ends (ready for use with the next web request)
    - ConcurrentSessionFilter, because it uses the SecurityContextHolder functionality and needs to update the SessionRegistry to reflect ongoing requests from the principal
    - Authentication processing mechanisms - UsernamePasswordAuthenticationFilter, CasAuthenticationFilter, BasicAuthenticationFilter etc - so that the SecurityContextHolder can be modified to contain a valid Authentication request token
    - The SecurityContextHolderAwareRequestFilter, if you are using it to install a Spring Security aware HttpServletRequestWrapper into your servlet container
    - The JaasApiIntegrationFilter, if a JaasAuthenticationToken is in the SecurityContextHolder this will process the FilterChain as the Subject in the JaasAuthenticationToken
    - RememberMeAuthenticationFilter, so that if no earlier authentication processing mechanism updated the SecurityContextHolder, and the request presents a cookie that enables remember-me services to take place, a suitable remembered Authentication object will be put there
    - AnonymousAuthenticationFilter, so that if no earlier authentication processing mechanism updated the SecurityContextHolder, an anonymous Authentication object will be put there
     - ExceptionTranslationFilter, to catch any Spring Security exceptions so that either an HTTP error response can be returned or an appropriate AuthenticationEntryPoint can be launched
     - FilterSecurityInterceptor, to protect web URIs and raise exceptions when access is denied


- [x] What is a security context?
- SecurityContextHolder
  - store SecurityContext
  - mode:
    - Threadlocal
    - Inheritable thread local
    - global
- SecurityContext
  - min security info
  - set/get Authentication
- Authentication
  - collection of authorities
  - credentials(user/password)
  - details
  - principal
  - Auth flag
```java
Object principal = SecurityContextHolder.getContext().getAuthentication().getPrincipal();

if (principal instanceof UserDetails) {
String username = ((UserDetails)principal).getUsername();
} else {
String username = principal.toString();
}
```
- [x] Why do you need the intercept-url?
- URL pattern & access
  - access: role
  - filters
  - method: HTTP verb
  - pattern: URL path
  - request-matcher-ref: RequestMatcher
  - requirs-channel
    - http, https, any(default)
```java
<intercept-url pattern="/services/users*" access="ROLE_ADMIN" />

protected void configure(final HttpSecurity inHttpSecurity) throws Exception {
inHttpSecurity
.authorizeRequests()
.antMatchers("/services/users/**").hasRole("ADMIN")
.anyRequest().authenticated()
.and()
.formLogin();
...
```


- [x] In which order do you have to write multiple intercept-url's?
- once match, stop evaluation
- more specific earlierm, more general patterns later

- [x] What does the ** pattern in an antMatcher or mvcMatcher do?
- \*: mathc any path in this level
- \*\*: match any path start from this level
- ** or ** match any request

- Why is an mvcMatcher more secure than an antMatcher?
- antMatchers("/services") only matches the exact “/services” URL
- mvcMatchers("/services") matches “/services” but also “/services/”, “/services.html” and “/services.abc”.
  - more forgiving, same matching rule as @RequestMapping, newer


- [x] Does Spring Security support password hashing? What is salting?
- Password Hashing
  - process of calculating a hash-value for a password
  - store hash in DB, and compare with encoded user password
  - PasswordEncoder
- salting
  - a seq of random bytes as input with password, store with password
  - to avoid same hash for certain word

- [x] Why do you need method security? What type of object is typically secured at the method level (think of its purpose not its Java type).
- Security on the method level needs to be explicitly enabled
  - @EnableGlobalMethodSecurity
  - @EnableReactiveMethodSecurity
- additioanl security level for web, or only layer without web interface
- commly applied to services layer
  
- [x] What do @PreAuthorized and @RolesAllowed do? What is the difference between them?
- on method or on class
- @PreAuthorize
  - evaluate access using SpEL before invocation
  - @EnableGlobalMethodSecurity(prePostEnabled=true)
- @RoleAllowed
  - role-based secuity
  - @EnableGlobalMethodSecurity(jsr250Enabled=true)
  
  - [x] What does Spring’s @Secured do?
  - legancy
  - not support SPEL
  - more than role-based
  - @EnableGlobalMethodSecurity(securedEnabled=true)
  
  - [x] How are these annotations implemented?
  - AOP
  - [x] In which security annotation are you allowed to use SpEL?
  - support SpEL: @PreAutorize, @PostAuthorize, @PreFilter, @PostFilter
  - Not support: @Secured, @RoleAllowed
  
  
- [x] Is it enough to hide sections of my output (e.g. JSP-Page or Mustache template)?
- it depends on what information is to be hidden and what the consequences are if the information is displayed to the wrong person(s).
  - [x] Spring security offers a security tag library for JSP, would you recognize it if you saw it in an
example?
  - require to make security tag available: ```<%@ taglib prefix="sec" uri="http://www.springframework.org/security/tags" %>```
```jsp
<sec:authorize access="hasRole('aadministrator')">
Hello, I know you are an administrator!
</sec:authorize>

<sec:authorize url="/resources/users">
Hello, I know you are an administrator!
</sec:authorize>

<sec:authentication property="principal.username" />

<sec:accesscontrollist hasPermission="5, 7" domainObject="${order}">

<form action="login" method="post">
<sec:csrfInput />
<div><label>User Name: <input type="text" name="username"/> </label></div>
<div><label>Password : <input type="password" name="password"/> </label></div>
<div><input type="submit" value="Sign In"/></div>
</form>



<sec:csrfMetaTags />
<script type="text/javascript" language="javascript">
var csrfParameter = $("meta[name='_csrf_parameter']").attr("content");
var csrfHeader = $("meta[name='_csrf_header']").attr("content");
var csrfToken = $("meta[name='_csrf']").attr("content");
```


### 7.REST
- [x] What does REST stand for?
- REST stands for REpresentational State Transfer and is an **architectural** style which allows clients to **access and manipulate** textual representations of web resources given a set of constraints.
- 6 Constraints to gain desirable **non-functional** properties, such as performance, scalability, simplicity, modifiability, visibility, portability, and reliability
  - Client-server architecture: scalability
  - Statelessness: no client context being stored on the server => state DB
  - Cacheability: clients and intermediaries can cache responses
  - Layered system: client won't tell diff => proxy, LB, security
  - Code on demand (optional): dynamic web page
  - Uniform interface: 
    - URI
    - self-descriptive: MIME
    - Hypermedia as the engine of application state
- [x] What is a resource?
- Any information is a resource as long as we can name it
- [x] What does CRUD mean?
- Create, Read, Update and Delete
- [x] Is REST secure? What can you do to secure it?
- no by itself, but can add security layer
- HTTPS for in transit
- [x] What are safe REST operations?
- safe = no change or side-effect
- GET, HEAD, OPTIONS, TRACE

- [x] What are idempotent\['yidin po] operations? Why is idempotency important?
- same effect for same request
- Methods PUT and DELETE are defined to be idempotent, meaning that multiple identical requests should have the same effect as a single request
- plus safe operations

- [x] Is REST scalable and/or interoperable?
- stateless, cacheability, layered: cluster, LB
- diff format, URI, fixed set of vrb

- [x] Which HTTP methods does REST use?
- POST, GET, PUT, DELETE
- [x] What is an HttpMessageConverter?
- convert HttpInputMessage to an Object, convert obj to HttpOutputMessage
- impl:
  - AtomFeedHttpMessageConverter: Converts to/from Atom feeds.
  - ByteArrayHttpMessageConverter: Converts to/from byte arrays.
  - FormHttpMessageConverter: Converts to/from HTML forms.
  - Jaxb2RootElementHttpMessageConverter: Reads classes annotated with the JAXB2 annotations @XmlRootElement and @XmlType and writes classes annotated with @XmlRootElement.
  - MappingJackson2HttpMessageConverter: Converts to/from JSON using Jackson 2.x.
  
- [x] Is REST normally stateless?
- client hold state, server no retain
- [x] What does @RequestMapping do?
- mark method to handle requests that match the config
- web req:
  - @GetMapping, @PostMapping, @PutMapping, @DeleteMapping, @PatchMapping
- config param in @
  - consumes: MIME
  - headers
  - method
  - params: only mapped if each such parameter is found to have the given value.
  - path
  - produces

- [x] Is @Controller a stereotype? Is @RestController a stereotype?
- yes
  - [x] What is a stereotype annotation? What does that mean?
  - mark as a role
  - @Component, @Controller, @Indexed, @Repository, @Service
- [x] What is the difference between @Controller and @RestController?
- @RestController = @Controller + @ResponseBody(apply to all methods in the class)
- [x] When do you need @ResponseBody?
- indicates a method return value should be bound to the web response body
- on class or handler method
- [x] What does @PathVariable do?
- bound a method parameter to a URI template variable

- [x] What are the HTTP status return codes for a successful GET, POST, PUT or DELETE operation?
- 1xx: Informational. The request has been received and processing of it continues.
- 2xx: Successful. The request has been successfully received, understood and accepted.
- 3xx: Redirection. Further action is needed to complete the request.
- 4xx: Client error. The request is incorrect or cannot be processed.
- 5xx: Server error. The server failed to process what appears to be a valid request.

- [x] When do you need @ResponseStatus?
- Marks a method or exception class with the status code() and reason() that should be returned. will override
- as use HttpServletResponse.sendError, error page involved, not suitable for REST <= preferable to use a ResponseEntity as a return type 
- can on class also

- [x] Where do you need @ResponseBody? What about @RequestBody? Try not to get these muddled up!
- @ResponseBody: It is used when the web response body is to contain the result produced by the controller method(s).
- @RequestBody: bound a method parameter to the body of the web request
- [x] If you saw example Controller code, would you understand what it is doing? Could you tell if it was
annotated correctly?


- [x] Do you need Spring MVC in your classpath?
- yes, as @RestController, @ResponseBody, @RequestBody, @PathVariable in spring-web module
- [x] What Spring Boot starter would you use for a Spring REST application?
- Spring Boot Web Starter
- https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-web/pom.xml
- [x] What are the advantages of the RestTemplate?
- Template Method (Behavior) design pattern
  - template method in superclass, outline the steps
  - subclass overrides step functions
- The RestTemplate implements a **synchronous** HTTP client that **simplifies** sending requests and also **enforces** RESTful principles
- adv:
  - URI template
  - auto encode URI: space with %20
  - auto detect MIME
  - auto conversion betweeen obj and HTTP message
  - easy register ResponseErrorHandler
  - Provides methods for conveniently sending common HTTP request types
    - delete, getForObject, getForEntity, headForHeaders, postForObject and put
  - provides methods that allow for increased detail when sending requests.
    - execute()
- If you saw an example using RestTemplate would you understand what it is doing?
```java
restTemplate.getForObject("http://example.com/hotel list", String.class);
// Results in request to "http://example.com/hotel%20list"

String uriTemplate = "http://example.com/hotels/{hotel}";
URI uri = UriComponentsBuilder.fromUriString(uriTemplate).build(42);

RequestEntity<Void> requestEntity = RequestEntity.get(uri)
        .header(("MyRequestHeader", "MyValue")
        .build();

ResponseEntity<String> response = template.exchange(requestEntity, String.class);

String responseHeader = response.getHeaders().getFirst("MyResponseHeader");
String body = response.getBody();
```

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


