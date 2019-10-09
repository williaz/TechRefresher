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
- # SpEL

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
  - params
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

- When do you need @ResponseStatus?
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


