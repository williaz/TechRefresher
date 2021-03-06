## Menu
- [Container (20%)](#Container-Dependency-and-IOC)

- [AOP (8%)](#AOP)
- [Security (6%)](#Spring-Security)

- [JDBC (4%)](#JDBC)
- [Transactions (8%)](#Transaction)
- [JPA Spring Data (4%)](#Spring-Data-JPA)

- [MVC (8%)](#Spring-MVC-and-the-Web-Layer)
- [REST (6%)](#REST)

- [Boot Into (8%)](#Spring-Boot-Intro)
- [Boot Autoconfig (8%)](#Spring-Boot-Auto-Configuration)
- [Boot Actuator (8%)](#Spring-Boot-Actuator)
- [Boot Testing (8%)](#Spring-Boot-Testing)

- [Testing (4%)](#Testing)

- One of the central tenets of the Spring Framework is that of non-invasiveness

## Container, Dependency, and IOC

### Core
- ApplicationContext
- BeanFactoryPostProcesser
- BeanPostProcessor
  - @Autowared
    - In the case of a multi-arg constructor or method, the required() attribute is applicable to **all arguments**. Individual parameters may be declared as Java-8 style Optional or, as of Spring Framework 5.0, also as @Nullable or a not-null parameter type in Kotlin, overriding the base 'required' semantics.
    - By Name: @Autowired applies to fields, constructors, and multi-argument methods, allowing for narrowing through @Qualifier annotations at the parameter level. In contrast, **@Resource**（by name） is supported only for fields and bean property setter methods with a **single argument**.
    - can apply to Map as long as **String key** type
    - only 1 attr: required
    - @Inject always required
- [@Configuration](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Configuration.html)
  - a class level @Component
  - a source of bean definitions
  - no final class as CGLIB subclassing unless proxyBeanMethods flag is set to false
  - nested config class must be static
  - ```@Import({ServiceConfig.class, RepositoryConfig.class})```
- Bean:
  - [@Bean](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Bean.html)
    - method-level, 
    - used in @Configuration or @Component; 
      - lite mode(not in @config): general-purpose factory method, cannot declare inter-bean dependencies
    - to register bean definition in IoC
    - @Bean({"dataSource", "subsystemA-dataSource"}) or method’s name 
  - scope
    - @Scope("prototype")
    
  - lifecycle(callback)
  - with @Qualifier, @Profile, @Scope, @Lazy, @DependsOn, @Primary, @Order
    - @Profile({"p1", "production & (us-east | eu-central)", "!p2"}): You cannot mix the & and | operators without using parentheses. 
    - Your target beans can implement the org.springframework.core.Ordered interface or use the @Order or standard @Priority annotation if you want items in the array or list to be sorted in a specific order. 
    - @Priority not on method level
    - @Order
      - class or method level
      - may influence priorities at injection points, but not influence singleton startup order 
  - @Component: implicit
- @Value
  - at the field or method/constructor parameter level
  - PropertySourcesPlaceholderConfigurer: Specialization of **PlaceholderConfigurerSupport** that resolves ${...} placeholders within bean definition property values and **@Value** annotations against the current Spring **Environment** and its set of **PropertySources**.
  - @PropertySources: 
    - add K:V to Environment
    - JVM, env, JNDI, Servlet config/context init param, prop files
- Lifecycle
  - 1. Load Bean definitions
    - process @Configuration, scan @Component
    - Beand def added to BeanFactory
    - invoke BeanFactoryPostProcessor to modify bean def before obj created
  - 2. Initialize bean instance (for each bean)
    - instantiated in right orser
    - dep injection
    - BeanPostProcessor
    - Initializer i.e @PostConstruct
    - BPP
  - 3. Ready to use



### What is dependency injection and what are the advantages?
- IoC is also known as dependency injection (DI). It is a process whereby objects define their dependencies (that is, the other objects they work with) only through constructor **arguments**, arguments to a factory method, or properties that are set on the object instance after it is constructed or returned from a factory method. The container then injects those dependencies when it creates the bean. This process is fundamentally the inverse (hence the name, Inversion of Control) of the bean itself controlling the instantiation or location of its dependencies by using direct construction of classes or a mechanism such as the Service Locator pattern.


### What is an interface and what are the advantages of making use of them in Java? Why are they recommended for Spring beans?
- mock
- JDK dynamic proxy

### What is meant by “application-context?
-  BeanFactory provides the configuration framework and basic functionality, and the ApplicationContext adds more enterprise-specific functionality.

- ApplicationContext is a sub-interface of BeanFactory. It adds:
```
Easier integration with Spring’s AOP features

Message resource handling (for use in internationalization)

Event publication

Application-layer specific contexts such as the WebApplicationContext for use in web applications.
```
- A bean is an object that is instantiated, assembled, and otherwise managed by a Spring IoC container.

### How are you going to create a new instance of an ApplicationContext?
- new AnnotationConfigApplicationContext(MyConfiguration.class);
- new AnnotationConfigWebApplicationContext()
  - listener-class: The Spring ContextLoaderListener creates the root Spring web application context of a web
application.
  - servlet: DispatcherServlet
- Boot: SpringApplication.run() with config in application.properties
```
ConfigurableApplicationContext context = SpringApplication.run(MySpringConfiguration.class, args);
context.getBean(name, Type.class)
context.getBean(Type.class)
```
### Can you describe the lifecycle of a Spring Bean in an ApplicationContext?
- the bean is instantiated (if it is not a pre-instantiated singleton), 
- its dependencies are set
- the relevant lifecycle methods (such as a configured init method or the InitializingBean callback method) are invoked.

### How are you going to create an ApplicationContext in an integration test?
- @RunWith (JUnit 4) or @ExtendWith (JUnit 5)
- @ContextConfiguration or @WebAppConfiguration

### What is the preferred way to close an application context? Does Spring Boot do this for you?

- standalone: Registering a shutdown-hook by calling the method registerShutdownHook
  - ensures a graceful shutdown and calls the relevant **destroy methods** on your singleton beans so that all resources are released.
- Web: taken care of by the ContextLoaderListener which implements the ServletContextListener interface



### Can you describe: Dependency injection using Java configuration? Dependency injection using annotations (@Autowired)? Component scanning, Stereotypes? 
- config class with bean method
- @Component/@Autowired + @ComponetScan
- component-scan implicitly included annotation-config for @Autowired

### Scopes for Spring beans? What is the default scope?

- singleton
- prototype
  - Spring **does not manage the complete lifecycle** of a prototype bean: the container instantiates, configures, and otherwise assembles a prototype object, and hands it to the client, with no further record of that prototype instance. 
  - To get the Spring container to release resources held by prototype-scoped beans, try using a custom **bean post-processor**, which holds a reference to beans that need to be cleaned up.
- web-aware: request, session, applicaton(per ServletContext), websocket
  - DispatcherServlet, RequestContextListener, and RequestContextFilter all do exactly the same thing, namely bind the HTTP request object to the Thread that is servicing that request. This makes beans that are request- and session-scoped available further down the call chain.

### Are beans lazily or eagerly instantiated by default? How do you alter this behavior?
- By default, ApplicationContext implementations eagerly create and configure all singleton beans as part of the initialization process.
- A lazy-initialized bean tells the IoC container to create a bean instance when it is first requested, rather than at startup.
- @Lazy(false)

### What is a property source? How would you use @PropertySource?
- A PropertySource is a simple abstraction over any source of key-value pairs, and Spring’s StandardEnvironment is configured with two PropertySource objects — one representing the set of JVM system properties (System.getProperties()) and one representing the set of system environment variables (System.getenv()).
- @PropertySource annotation provides a convenient and declarative mechanism for adding a PropertySource to Spring’s Environment.

### What is a BeanFactoryPostProcessor and what is it used for? When is it invoked? 
- only modify bean meta-data
- scoped per-container
- the Spring IoC container allows a BeanFactoryPostProcessor to read the configuration metadata and potentially change it **before the container instantiates any beans** other than BeanPostProcessors.
- ex: PropertySourcesPlaceholderConfigurer

### Why would you define a static @Bean method?
- to be created before other beans
- When defining a BeanPostProcessor or a BeanFactoryPostProcessor using an @Bean annotated method, it is recommended that the method is static, in order for the post-processor to be instantiated early in the Spring context creation process.
- you may declare @Bean methods as static, allowing for them to be **called without creating their containing configuration** class as an instance. This makes particular sense when defining post-processor beans, e.g. of type BeanFactoryPostProcessor or BeanPostProcessor, since such beans will get initialized early in the container lifecycle and should avoid triggering other parts of the configuration at that point.

- Note that calls to static @Bean methods will **never get intercepted** by the container, not even within @Configuration classes (see above). This is due to technical limitations: CGLIB subclassing can only override non-static methods.As a consequence, a direct call to another @Bean method will have standard Java semantics, resulting in an independent instance being returned straight from the factory method itself.

### What is a ProperySourcesPlaceholderConfigurer used for?
- Spring bean configuration can be externalized into property files.
- resolves @Value(${PROPERTY_NAME})


## What is a BeanPostProcessor and how is it different to a BeanFactoryPostProcessor? What do they do? When are they called?
- AutowiredAnnotationBeanPostProcessor
  - Implements support for dependency injection with the @Autowired annotation.
- PersistenceExceptionTranslationPostProcessor
  - Applies exception translation to Spring beans annotated with the @Repository annotation.
- @Autowired, @Inject, @Resource, and @Value annotations are handled by Spring BeanPostProcessor implementations which in turn means that you cannot apply these annotations within your own BeanPostProcessor or BeanFactoryPostProcessor types 

- the Spring Framework uses BeanPostProcessor implementations to process bean lifecycle callback
- scoped per-container
```java
public interface BeanPostProcessor {
    @Nullable
    default Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        return bean;
    }

    @Nullable
    default Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        return bean;
    }
}
```
### What is an initialization method and how is it declared on a Spring bean?
- after all prop set and before in use, 3 options in order
- 1. @PostConstruct
  - no param, must void
  - need @ComponentScan
- 2. InitializingBean.afterPropertiesSet()
- 3. @Bean(initMethod="")
### What is a destroy method, how is it declared and when is it called?
- before out of use, in order:
- 1. @PreDestroy
  - when context.close; normally exit
  - not called for prototype bean 
- 2. DisposableBean.destroy()
- 3. @Bean(destroyMethod="")


### Consider how you enable JSR-250 annotations like @PostConstruct and @PreDestroy? When/how will they get called?
- CommonAnnotationBeanPostProcessor, auto registed in AnnotationConfigApplicationContext,

• How else can you define an initialization or destruction method for a Spring bean?
• What does component-scanning do?
### What is the behavior of the annotation @Autowired with regards to field injection, constructor injection and method injection?
- As of Spring Framework 4.3, an @Autowired annotation on such a constructor is no longer necessary if the target bean only defines one constructor to begin with. However, if several constructors are available, at least one must be annotated to teach the container which one to use
  - **Only one** annotated constructor per-class can be marked as required, but multiple non-required constructors can be annotated. => Spring go pick greedist per dep arg
- Array/Typed collection: provide all beans of a particular type from the ApplicationContext
  - @Order/@Priority to order
-  typed Maps can be autowired as long as the expected key type is String. The Map values will contain all beans of the expected type, and the keys will contain the corresponding bean names

- By default, the autowiring fails whenever zero candidate beans are available; the default behavior is to treat annotated methods, constructors, and fields as indicating required dependencies.
  - @Autowired(**required** = false)
  - Alternatively, you may express the non-required nature of a particular dependency through Optional type
- You can also use @Autowired for interfaces that are well-known resolvable dependencies: BeanFactory, ApplicationContext, Environment, ResourceLoader, ApplicationEventPublisher, and MessageSource. These interfaces and their extended interfaces, such as ConfigurableApplicationContext or ResourcePatternResolver, are **automatically resolved, with no special setup necessary**.

- if multiple candidate: @Primary > @Qualifier > try to match the bean name to field/param name > exception
- Field/Method/Constructor: any visibility


### What do you have to do, if you would like to inject something into a private field? How does this impact testing?
- @Autowired: mock
- @Value: @TestPropertySource to use either a test-specific property file or customizing individual property values

### How does the @Qualifier annotation complement the use of @Autowired?
- At injection points, specify name to match
- At bean definitions, define bean name
- At annotation definitions.

• What is a proxy object and what are the two different types of proxies Spring can create?
• What are the limitations of these proxies (per type)?
• What is the power of a proxy object and where are the disadvantages?


• What does the @Bean annotation do?


### What is the default bean id if you only use @Bean? How can you override this?
- Bean name
  - The convention is to use the standard Java convention for instance field names when naming beans. That is, bean names start with a lowercase letter and are camel-cased from there.
  - With component scanning in the classpath, Spring generates bean names for unnamed components, following the rules described earlier: essentially, taking the simple class name and turning its initial character to lower-case. 

### Why are you not allowed to annotate a final class with @Configuration? How do @Configuration annotated classes support singleton beans? Why can’t @Bean methods be final either?
- Configuration classes are **subclassed** by the Spring container using CGLIB and final classes
cannot be subclassed.


### How do you configure profiles? What are possible use cases where they might be useful? Can you use @Bean together with @Profile? Can you use @Component together with @Profile? How many profiles can you have?
- ```@Profile({"p1", "!p2"})``` only format: ```!, &, |```
- conditionally enable or disable a complete @Configuration class or even individual @Bean methods, based on some arbitrary system state.
- built on @Conditional
- ```@Profile("default")```The default profile represents the profile that is enabled by default. 

### How do you inject scalar/literal values into Spring beans? What is @Value used for?
- @Value("${personservice.retry-count}")


• What is Spring Expression Language (SpEL for short)?


### What is the Environment abstraction in Spring?
- prop
- add @PropertySource
• Where can properties in the environment come from – there are many sources for properties – check the documentation if not sure. Spring Boot adds even more.

### What can you reference using SpEL?
- Named Spring bean
- Implicit Ref:
  - sustemProperties and systemEnvironment,  available by default

### What is the difference between $ and # in @Value expressions?
- ${} construct that surrounds the name of the property
- SpEL surrounded by the characters #{}.








## AOP 
- Spring AOP uses either JDK dynamic proxies or CGLIB to create the proxy for a given target object. JDK dynamic proxies are built into the JDK, whereas CGLIB is a common open-source class definition library (repackaged into spring-core).
  - If the target object to be proxied implements at least one interface, a JDK dynamic proxy is used. All of the interfaces implemented by the target type are proxied. If the target object does not implement any interfaces, a CGLIB proxy is created.
  - force use CGLIB: ```<aop:config proxy-target-class="true">```
  - due to the proxy-based nature of Spring’s AOP framework, **calls within** the target object are, by definition, not intercepted. 
    - For JDK proxies, only **public** interface method calls on the proxy can be intercepted. 
    - With CGLIB, **public and protected** method calls on the proxy are intercepted (and even package-visible methods, if necessary). 
    - common interactions through proxies should always be designed through public signatures.
  - Spring JavaConfig requires CGLIB subclassing

- Aspect
  - A modularization of a concern that cuts across multiple classes
  - (advice + pointcut)
    - Advice: interceptor
      - Around
        - The **first parameter** of the advice method must be of type ProceedingJoinPoint.
	- Note that proceed may be invoked **once, many times, or not at all** within the body of the around advice, all of these are quite legal.
    - [Pointcut](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#aop-pointcuts-examples): predicate, the method serving as the pointcut signature must have a **void** return type
    
- Joint point
 - A stage during the execution of a program
 - Spring AOP: always emthod execution
- waving: linking aspects to adviced obj
  - Spring AOP: at runtime
- to enable @AspectJ support: aspectjweaver.jar
- @EnableAspectJAutoProxy(enable JavaConfig), @Aspect

- Using the most specific(least powerful) advice type provides a simpler programming model with less potential for errors. For example, you do not need to invoke the proceed() method on the JoinPoint used for around advice, and, hence, you cannot fail to invoke it.
- All advice parameters are statically typed

### What is the concept of AOP? Which problem does it solve? What is a cross cutting concern?
- a crosscutting concern: functionality that affects multiple points of an application.
- cross-cutting concerns are conceptually separate from (but often embedded directly within) the application’s business logic.
- With AOP, you still define the common functionality in one place, but you can declaratively define how and where this functionality is applied without having to modify the class to which you’re applying the new feature. Crosscutting concerns can now be modularized into special classes called aspects.

### Name three typical cross cutting concerns.
- logging, transactions, security, and caching

### What two problems arise if you don't solve a cross cutting concern via AOP?
- A common object-oriented technique for reusing common functionality is to apply inheritance or delegation. 
- Inheritance can lead to a **brittle object hierarchy** if the same base class is used throughout an application
- Delegation can be **cumbersome** because complicated calls to the delegate object may be required.

- 1. code tangling: a coupling of concerns
- 2. code scattering: same concern spread across modules

### What is a pointcut, a join point, an advice, an aspect, weaving?
- Advice: the **task** of an aspect
  - Advice defines both the what and the when of an aspect
  - Spring: Advice take place when: Before, After, After-returning, After-throwing, Around
    - After-throwing won't stop exception from propagating, Around can
  - The ordering of advice is controlled through the Ordered interface.
- A join point is a point in the execution of the application where an aspect can be plugged in.
  - These are the points where your aspect’s code can be **inserted** into the normal flow of your application to add new behavior.
- Pointcuts: matcher, help narrow down the join points advised by an aspect.
  - If advice defines the what and when of aspects, then pointcuts define the where. A pointcut definition matches one or more join points at which advice should be woven

- An aspect is the merger of advice and pointcuts.
- Weaving is the process of applying aspects to a target object to create a new proxied object.
  - Compile time: AspectJ
  - Class load time: AspectJ5 LTW
  - Runtime: an AOP container dynamically generates a proxy object that delegates to the target object while weaving in the aspects => Spring AOP

- An introduction allows you to add new methods or attributes to existing classes.

### How does Spring solve (implement) a cross cutting concern?
- the ability to create pointcuts that define the join points at which aspects should be woven is what makes it an AOP framework.
- 4 Style:
  - Classic Spring proxy-based AOP
  - Pure-POJO aspects
  - @AspectJ annotation-driven aspects
  - Injected AspectJ aspects (available in all versions of Spring)

- **method join points only**: Spring AOP is built around dynamic proxies. Consequently, Spring’s AOP support is limited to **method interception**.
- **Runtime proxy creation**: Spring doesn’t create a proxied object until that proxied bean is needed by the application.

### Which are the limitations of the two proxy-types?
- Invocation of advised methods on self.
  - A proxy implements the advice which is executed prior to invoking the method on a Spring bean. The Spring bean being proxied is not aware of the proxy and when a calling a method on itself, the proxy will not be invoked.
- JDK Dynamic Proxies
  - proxy through implementing its interface.
  - only public method got proxied
- CGLIB
  - through extending
  - not final class, not final method
  - only piblic or protected method got proxied

### What visibility must Spring bean methods have to be proxied using Spring AOP?
- public, from outside bean
- only apply aspects to Bean

### How many advice types does Spring support. Can you name each one? What are they used for? Which two advices can you use if you would like to try and catch exceptions?
- @After; return or throw
- @AfterReturning, @AfterThrowing, @Before
- @Around: ProceedingJoinPoint.procceed()
  - pass control to advised method
  - can repeat calls

### What do you have to do to enable the detection of the @Aspect annotation? What does @EnableAspectJAutoProxy do?
- @EnableAspectJAutoProxy turn on auto-proxying at the class level of the configuration class.



- @Pointcut defines a named reusable pointcut within an @Aspect aspect.
```java
@Pointcut("execution(** concert.Performance.perform(..))")
public void performance() {}

@Pointcut(
"execution(* soundsystem.CompactDisc.playTrack(int)) " +
"&& args(trackNumber)")
public void trackPlayed(int trackNumber) {}

@Before("performance()")
public void silenceCellPhones() {}

@Before("trackPlayed(trackNumber)")
public void countTrack(int trackNumber) {  // int
int currentCount = getPlayCount(trackNumber);
trackCounts.put(trackNumber, currentCount + 1);
}
```


### If shown pointcut expressions, would you understand them? For example, in the course we matched getter methods on Spring Beans, what would be the correct pointcut expression to match both getter and setter methods?
- In Spring AOP, pointcuts are defined using AspectJ’s pointcut expression language
- pointcut designators
  - perform match: execution()
  - limit match:
    - arg type: args(), @args()
    - this(): bean, target(): obj, @target
    - within(), @within(): certain types
    - @annotation: on subject
    - bean(): takes bean ID
```java
execution(* concert.Performance.perform(..)&& within(concert.*)))  // * any return type; .. any args; in concert package
```
- externalizing expressions into one dedicated class like SystemArchitecture
### What is the JoinPoint argument used for?
- first arg for @After, @AfterReturning, @AfterThrowing, @Before if use
- get JoinPoint method info like joinPoint.getSignature(), Object[] joinPoint.getArgs()

### What is a ProceedingJoinPoint? When is it used?
- In @Around, invoke the advised method from within your advice using proceed()

```
AOP is a powerful complement to object-oriented programming. With aspects, you can
group application behavior that was once spread throughout your applications into
reusable modules. You can then declare exactly where and how this behavior is applied.
This reduces code duplication and lets your classes focus on their main functionality.
```


## Spring Security

### Security
- web: @EnableWebSecurity
- method: @EnableGlobalMethodSecurity(securedEnabled = true, prePostEnabled = true)
  - @Secured, @RolesAllowed, @PreAuthoriz, @PostAuthorize, @PreFilter, @PostFilter

- springSecurityFilterChain
  - filter
  - responsible for all the security (protecting the application URLs, validating submitted username and passwords, redirecting to the log in form, etc) within your application.
  - to register: @EnableWebSecurity
- UserDetailsService
- PasswordEncoder
- HttpSecurity
  - antMatcher, mvcMatcher
  
- SecurityContextHolder, to provide access to the SecurityContext.
- SecurityContext, to hold the Authentication and possibly request-specific security information.
- Authentication, to represent the principal in a Spring Security-specific manner.
- GrantedAuthority, to reflect the application-wide permissions granted to a principal.
- UserDetails, to provide the necessary information to build an Authentication object from your application’s DAOs or other source of security data.
- UserDetailsService, to create a UserDetails when passed in a String-based username (or certificate ID or the like).

```java
Object principal = SecurityContextHolder.getContext().getAuthentication().getPrincipal();
```
- Filter Ordering
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

### What are authentication and authorization? Which must come first?
- Authentication: verify user
- Authorization: permission

### Is security a cross cutting concern? How is it implemented internally?
- Spring Security, a security framework implemented with Spring AOP and servlet filters.
  - provides declarative security
  - web request level: servlet filter
    - First of all a servlet filter of the type DelegatingFilterProxy is configured.
    - The DelegatingFilterProxy delegates to a FilterChainProxy. The FilterChainProxy is defined as a Spring bean and takes one or more SecurityFilterChain instances as constructor parameter(s).
    - A SecurityFilterChain associates a request URL pattern with a list of (security) filters.
  - method invocation level: AOP

## What is the delegating filter proxy?
- implement javax.servlet.Filter
- Spring’s DelegatingFilterProxy provides the link between web.xml and the application context.
- DelegatingFilterProxy intercept requests coming into the application and delegate them to a bean whose ID is springSecurityFilterChain.
- Proxy for a standard Servlet Filter, delegating to a Spring-managed bean that implements the Filter interface. 


### What is the security filter chain?

- springSecurityFilterChain bean is a FilterChainProxy. It’s a single filter that chains together one or more additional filters.
- It maps a particular URL pattern to a list of filters built up from the bean names specified in the filters element, and combines them in a bean of type SecurityFilterChain. The pattern attribute takes an Ant Paths and the most specific URIs should appear first. At runtime the FilterChainProxy will locate the first URI pattern that matches the current web request and the list of filter beans specified by the filters attribute will be applied to that request. The filters will be invoked in the order they are defined, so you have complete control over the filter chain which is applied to a particular URL.

### What is a security context?

- SecurityContextHolder, to provide access to the SecurityContext. default: ThreadLocal 
- SecurityContext, to hold the Authentication and possibly request-specific security information.
- Authentication, to represent the principal in a Spring Security-specific manner.
- GrantedAuthority, to reflect the application-wide permissions granted to a principal.
- UserDetails, to provide the necessary information to build an Authentication object from your application’s DAOs or other source of security data.
- UserDetailsService, to create a UserDetails when passed in a String-based username (or certificate ID or the like).


### What does the ** pattern in an antMatcher or mvcMatcher do?
- ? matches one character
- * one level, matches zero or more characters
- ** all leve, matches zero or more 'directories' in a path

### Why is the usage of mvcMatcher recommended over antMatcher?
- mvcMatcher uses the same rules that Spring MVC uses for matching (when using @RequestMapping annotation).
- antMatchers("/secured") matches only the exact /secured URL
- mvcMatchers("/secured") matches /secured as well as /secured/, /secured.html, /secured.xyz

### Does Spring Security support password hashing? What is salting?
- Password hashing is the process of calculating a hash-value for a password.
- PasswordEncoder
- a salt is random data that is used as an additional input to a one-way function that hashes data, a password or passphrase. Salts are used to safeguard passwords in storage. 
  - A new salt is randomly generated for each password. 

### Why do you need method security? What type of object is typically secured at the method level (think of its purpose not its Java type).
- service layer


### What do @PreAuthorized and @RolesAllowed do? What is the difference between them?
- It is recommended to use the @PreAuthorize annotation in new applications over @Secured
- RolesAllowed: only supports role-based security.

### How are these annotations implemented?
AOP pointcut

### In which security annotation are you allowed to use SpEL?
- 4: @PreAuthoriz, @PostAuthorize, @PreFilter, @PostFilter




## JDBC
- JdbcTemplate
  - Connection: It handles the **creation and release of resources**, which helps you avoid common errors such as forgetting to close the connection. 
  - CRUD: It performs the basic tasks of the **core JDBC workflow** such as statement creation and execution, leaving application code to provide SQL and extract results. The JdbcTemplate class executes SQL queries, update statements and stored procedure calls, performs iteration over ResultSets and extraction of returned parameter values. 
  - Excepotion: It also catches JDBC exceptions and translates them to the generic, more informative, exception hierarchy defined in the org.springframework.dao package.
  - when use, only need to implement callback interfaces
    - PreparedStatementCreator callback interface creates a prepared statement given a Connection provided by this class, providing SQL and any necessary parameters
    - CallableStatementCreator interface, which creates callable statements
    - RowCallbackHandler:
      - no return object, to file 
    - ResultSetExtractor
      - processing an entire ResultSet at once
      - your have to iterate the result set to map entire to a obj
    - RowMapper: each row of a ResultSet maps to a obj  
  - Instances of the JdbcTemplate class are **threadsafe** once configured
  - The JdbcTemplate is **stateful**, in that it maintains a reference to a DataSource, but this state is not conversational state.
- DataSource
  - url, username, password, driverClassName
  - **DriverManagerDataSource** class should only be used for testing purposes since it provides **no pooling** and will perform poorly when multiple requests for a connection are made.
- DataSourceTransactionManager class is a PlatformTransactionManager implementation for single JDBC datasources. It binds a JDBC connection from the specified data source to the currently executing thread, potentially allowing for one thread connection per data source.

  
### What is the difference between checked and unchecked exceptions?
- The class java.lang.Exception and its subclasses, except for java.lang.RuntimeException and any
subclass of RuntimeException, are checked exceptions.
- either declare or try-catch

### Why does Spring prefer unchecked exceptions?
- without having annoying boilerplate catch-and-throw blocks and exception declarations in your DAOs.

### What is the data access exception hierarchy?
- DataAccessException as the root exception
- runtime exception, wrapper
- @Repository: to guarantee that your Data Access Objects (DAOs) or repositories provide exception translation


### How do you configure a DataSource in Spring? Which bean is very useful for development/test databases?
- a JDBC-based repository needs access to a JDBC DataSource, and a JPA-based repository needs access to an @PersistenceContext EntityManager
- You should use the **DriverManagerDataSource and SimpleDriverDataSource** classes (as included in the Spring distribution) only for **testing** purposes! Those variants do not provide pooling and perform poorly when multiple requests for a connection are made.
```java
DriverManagerDataSource dataSource = new DriverManagerDataSource();
dataSource.setDriverClassName("org.hsqldb.jdbcDriver");
dataSource.setUrl("jdbc:hsqldb:hsql://localhost:");
dataSource.setUsername("sa");
dataSource.setPassword("");


// --------
spring.datasource.url= jdbc:hsqldb:hsql://localhost:"
spring.datasource.username=sa
spring.datasource.password=
```

### What is the Template design pattern and what is the JDBC template?
- A template method defines the skeleton of a process.
- The process itself is fixed; it never changes.

### What is a callback? What are the three JdbcTemplate callback interfaces that can be used with queries? What is each used for? (You would not have to remember the interface names in the exam, but you should know what they do if you see them in a code sample).
- a callback, also known as a "call-after" function, is any executable code that is passed as an argument to other code; that other code is expected to call back (execute) the argument at a given time. This execution may be immediate as in a synchronous callback, or it might happen at a later time as in an asynchronous callback.
- ResultSetExtractor: Allows for processing of an entire result set, possibly consisting multiple rows of data, at once.
- RowCallbackHandler: Allows for processing rows in a result set one by one typically accumulating some type of result.
- RowMapper: Allows for processing rows in a result set one by one and creating a Java object for each row.
- The PreparedStatementCreator/PreparedStatementSetter callback interface creates a prepared statement given a Connection, providing SQL and any necessary parameters. 


### Can you execute a plain SQL statement with the JDBC template?
- yes


### When does the JDBC template acquire (and release) a connection, for every method called or once per template? Why?
- The execute() method :
  - picks up Connection object from connection pool.
  - create Statment object.
  - execute sql query.
  - release Connection object.

- Method's like update, queryForObject internally call execute()
- reuse, avoid expensive connection creation

### How does the JdbcTemplate support generic queries? How does it return objects and lists/maps of objects?
- SELCT: 
  - query()
  - queryForObject(): exactly 1, catch EmptyResultDataAccessException
- UPDATE/ INSERT/ DELETE: udpate()
- DDL/any: execute()
- JdbcOperations interface
```java
Actor actor = jdbcTemplate.queryForObject(
        "select first_name, last_name from t_actor where id = ?",
        (resultSet, rowNum) -> {
            Actor newActor = new Actor();
            newActor.setFirstName(resultSet.getString("first_name"));
            newActor.setLastName(resultSet.getString("last_name"));
            return newActor;
        },
        1212L);
	
public List<Actor> findAllActors() {
    return this.jdbcTemplate.query( "select first_name, last_name from t_actor", actorRowMapper);
}

this.jdbcTemplate.update(
        "insert into t_actor (first_name, last_name) values (?, ?)",
        "Leonor", "Watling");
	
this.jdbcTemplate.update(
        "update t_actor set last_name = ? where id = ?",
        "Banjo", 5276L);	
	
this.jdbcTemplate.update(
        "delete from t_actor where id = ?",
        Long.valueOf(actorId));	
// stored procedure
this.jdbcTemplate.update(
        "call SUPPORT.REFRESH_ACTORS_SUMMARY(?)",
        Long.valueOf(unionId));	
this.jdbcTemplate.execute("create table mytable (id integer, name varchar(100))");
```









## Transaction
- @EnableTransactionManagement
- @Transactional
  - value(txn manager), propagation, isolation, readonly, timeout, 
  - rollbackFor rollbackForClassName, noRollbackFor, noRollbackForClassName: for Throwable
- progation, isolation, rollback

- The combination of AOP with transactional metadata yields an AOP proxy that uses a TransactionInterceptor in conjunction with an appropriate PlatformTransactionManager implementation to drive transactions around method invocations.

### What is a transaction? What is the difference between a local and a global transaction?
- A transaction is an operation that consists of a number of tasks that takes place as **a single unit – either all or no**  tasks  are performed.
  - ACID
    - Atomicity: all or nothing
    - Consistency: no violation
    - Isolation: no effect on others
    - Durablility: change is durable

- Global transactions enable you to work with multiple transactional resources, typically relational databases and message queues. The application server manages global transactions through the **JTA**,
- Local transactions are resource-specific, such as a transaction associated with a JDBC connection
  - it cannot help ensure correctness across multiple resources.

### Is a transaction a cross cutting concern? How is it implemented by Spring?
- AOP

### How are you going to define a transaction in Spring?
- 1. PlatformTransactionManager bean.
- 2. @EnableTransactionManagement on @Configuration class
- 3. @Transactional code

### What does @Transactional do? What is the PlatformTransactionManager?
- PlatformTransactionManager defines A **transaction strategy**
  - TransactionDefinition
    - Isolation
    - Propagation: scope
    - Timeout
    - Read-only
  - PlatformTransactionManager implementations normally require knowledge of the environment in which they work: JDBC, JTA, Hibernate, and so on.
    - transactionManager drive advice
    - The **DataSourceTransactionManager** class is a PlatformTransactionManager implementation for single JDBC datasources. It binds a JDBC connection from the specified data source to the currently executing thread, potentially allowing for one thread connection per data source.
    - **JpaTransactionManager**: ```txManager.setEntityManagerFactory(entityManagerFactory)```
    - Jta, Hibernate, WebSphere TM
- Programmatic transaction management
  - Using the TransactionTemplate.
    - write a TransactionCallback implementation to execute code in the context of a transaction. 
  - Using a PlatformTransactionManager implementation directly.
    - using the TransactionDefinition and TransactionStatus objects you can initiate transactions, roll back, and commit
```java
transactionTemplate.execute(new TransactionCallbackWithoutResult() {

    protected void doInTransactionWithoutResult(TransactionStatus status) {
        try {
            updateOperation1();
            updateOperation2();
        } catch (SomeBusinessExeption ex) {
            status.setRollbackOnly();
        }
    }
});

// -----------------------------

DefaultTransactionDefinition def = new DefaultTransactionDefinition();
// explicitly setting the transaction name is something that can only be done programmatically
def.setName("SomeTxName");
def.setPropagationBehavior(TransactionDefinition.PROPAGATION_REQUIRED);

TransactionStatus status = txManager.getTransaction(def);
try {
    // execute your business logic here
}
catch (MyException ex) {
    txManager.rollback(status);
    throw ex;
}
txManager.commit(status);
```


### Is the JDBC template able to participate in an existing transaction?
- yes, TransactionAwareDataSourceProxy.

###  What is a transaction isolation level? How many do we have and how are they ordered?
- determine how the changes within a transaction are **visible to other users** and systems accessing the database prior to the transaction being committed.
- Serializable
  - highest isolcation level
  - read, write, range-lock(if have select where)
  - hold lock until transaction ends
- Repeatable Reads
  - read, write lock until transaction end
  - no range-lock; 
    - A **phantom read** occurs when, in the course of a transaction, **new rows** are added or removed by another transaction to the records being read.
    - INSERT
- Read Committed:
  - keeps write locks (acquired on selected data) until the end of the transaction
  - read locks are released as soon as the SELECT operation is performed
  - A **non-repeatable read** occurs when, during the course of a transaction, **a row** is retrieved twice and the values within the row differ between reads.
    - At the SERIALIZABLE and REPEATABLE READ isolation levels, the DBMS must return the old value for the second SELECT. At READ COMMITTED and READ UNCOMMITTED, the DBMS may return the updated value; this is a non-repeatable read.
    - UPDATE
- Read Uncommitted
  - dirty read: one transaction may see **not-yet-committed** changes made by other transactions


### What is @EnableTransactionManagement for?
- make @Transactional bean instance transactional through an @EnableTransactionManagement annotation in a @Configuration class
### What does transaction propagation mean?
- Defines how transactions relate to each other. Common options:  
  - Required: Code will always run in a transaction. Creates a new transaction or reuses one if available.
  - Requires_new: Code will always run in a new transaction. Suspends the current transaction if one exists.
- PROPAGATION_REQUIRED enforces a physical transaction, either locally for the current scope if no transaction exists yet or **participating in an existing** 'outer' transaction defined for a larger scope. 
- PROPAGATION_REQUIRES_NEW: **always uses an independent** physical transaction for each affected transaction scope, never participating in an existing transaction for an outer scope. 
- PROPAGATION_NESTED uses a single physical transaction with multiple **savepoints** that it can roll back to.

### What happens if one @Transactional annotated method is calling another @Transactional annotated method on the same object instance?
- AOP: a self-invocation of a proxied Spring bean effectively bypasses the proxy
- By default, a participating transaction joins the characteristics of the outer scope, **silently ignoring the local isolation level**, timeout value, or read-only flag (if any). Consider switching the validateExistingTransactions flag to true on your transaction manager if you want isolation level declarations to be rejected when participating in an existing transaction with a different isolation level. 

### Where can the @Transactional annotation be used? What is a typical usage if you put it at class level?
- declare transactional semantics metadata
- class level or method level, better not on interface in case you use class-based proxies (proxy-target-class="true") or the weaving-based aspect (mode="aspectj")
- only to methods with **public** visibility
- does not apply to ancestor classes up the class hierarchy
- the proxy must be fully initialized to provide the expected behavior
- The default advice mode for processing @Transactional annotations is proxy, which allows for interception of calls through the proxy only. **Local calls** within the same class **cannot** get intercepted that way.
- @Transactional(readOnly = true)
- default settings
```
The propagation setting is PROPAGATION_REQUIRED.
The isolation level is ISOLATION_DEFAULT.
The transaction is read-write.
The transaction timeout defaults to the default timeout of the underlying transaction system, or to none if timeouts are not supported.
Any RuntimeException triggers rollback, and any checked Exception does not.
```
- multiple TXM
  - use the value attribute of the @Transactional annotation to optionally specify the bean name/ qualifier value of the PlatformTransactionManager to be used
```
public class TransactionalService {

    @Transactional("orderTxnMgr")
    public void setSomething(String name) { ... }

    @Transactional("accountTxnMgr")
    public void doSomething() { ... }
}

@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Transactional("account")
public @interface AccountTx {
}
```

### What does declarative transaction management mean?
- It keeps transaction management out of business logic and is not difficult to configure. 
- Programmatic transaction management 
  - small tranx ops
  - Being able to **set the transaction name explicitly** is also something that can only be done using the programmatic approach to transaction management.
  
### What is the default rollback policy? How can you override it?

- The recommended way to indicate to the Spring Framework’s transaction infrastructure that a transaction’s work is to be rolled back is to throw an Exception from code that is currently executing in the context of a transaction.
- RuntimeException/Error: In its default configuration, the Spring Framework’s transaction infrastructure code marks a transaction for rollback only in the case of runtime, unchecked exceptions. 

```
    <tx:method name="get*" read-only="true" rollback-for="NoProductInStockException"/>
  <tx:method name="updateStock" no-rollback-for="InstrumentNotFoundException"/>


public void resolvePosition() {
    try {
        // some business logic...
    } catch (NoProductInStockException ex) {
        // trigger rollback programmatically
        TransactionAspectSupport.currentTransactionStatus().setRollbackOnly();
    }
}
```

### What is the default rollback policy in a JUnit test, when you use the @RunWith(SpringJUnit4ClassRunner.class) in JUnit 4 or @ExtendWith(SpringExtension.class) in JUnit 5, and annotate your @Test annotated method with @Transactional?
- Such a transaction will automatically be rolled back after the completion of the test-method.

### Why is the term "unit of work" so important and why does JDBC AutoCommit violate this pattern?
- JDBC AutoCommit will cause each individual SQL statement as to be executed in its own transaction and the transaction committed when each statement is completed.
- JDBC AutoCommit can be disabled by calling the setAutoCommit method with the value false on a JDBC connection.








## Spring Data JPA
### JPA
- EntityManager: 
  - an instance represents a **connection** to a database
  - provides functionality for performing **operations** on a database. 
  - as a **factory for Query** instances, which are needed for executing queries on the database.
  - 3 Options for JPA Setup in a Spring Environment
    - Using LocalEntityManagerFactoryBean
    - Obtaining an EntityManagerFactory from JNDI
    - Using LocalContainerEntityManagerFactoryBean
- An EntityManagerFactory is constructed for a **specific database** as a one time operation, and by managing resources efficiently (e.g. a pool of sockets), provides an efficient way to construct multiple EntityManager instances for that database.
  - The createEntityManagerFactory method takes as an argument a name of a persistence unit. 
 
- A JPA Persistence Unit is a **logical grouping** of user defined **persistable classes** (entity classes, embeddable classes and mapped superclasses) with related settings.
  
- EntityTransaction: Operations that modify the content of a database require active transactions. Transactions are managed by an EntityTransaction instance obtained from the EntityManager.  



- **@PersistenceUnit EntityManagerFactory and @PersistenceContext EntityManager**


### What do you need to do in Spring if you would like to work with JPA? What do you have to configure to use JPA with Spring? How does Spring Boot make this easier?
- 1. Declare the appropriate dependencies.
- 2. Implement entity classes with mapping metadata in the form of annotations.
- 3. Define an EntityManagerFactory bean.
  - An entity manager factory is used to interact with a persistence unit.
  - A persistence unit is a grouping of one or more persistent classes that correspond to a single data source.
  - @PersistenceContext on EntityManagerto assoicate 2
  - 3 setup: 
    - LocalEntityManagerFactoryBean: only for standalone or integ test; no globale transaction
    - LocalContainerEntityManagerFactoryBean:  full JPA capabilities
      -  It supports links to an existing JDBC DataSource, supports both local and global transactions
    - Obtain an EntityManagerFactory using JNDI: JEE server
- 4. Define a DataSource bean.
- 5. Define a TransactionManager bean: JpaTransactionManager
- 6. Implement repositories.

- Spring can understand @PersistenceUnit EntityManagerFactory and @PersistenceContext EntityManager both at field and method level if a PersistenceAnnotationBeanPostProcessor is enabled

- Dealing with multiple persistence units
  - For applications that rely on multiple persistence units locations, stored in various JARS in the classpath, for example, Spring offers the **PersistenceUnitManager** to act as a central repository and to avoid the persistence units discovery process, which can be expensive. The default implementation allows multiple locations to be specified that are parsed and later retrieved through the persistence unit name.

- Although EntityManagerFactory instances are thread-safe, EntityManager instances are not.


- Boot provides
  - dependencies
  - beans need
  - default properties to persistence and JPA

### Are you able to participate in a given transaction in Spring while working with JPA?
- @Transactional indicates that the persistence methods in this repository are involved in a transactional context.

### Which PlatformTransactionManager(s) can you use with JPA?
- global transactions: JTA transaction manage
- JpaTransactionManager: using JPA with one single entity manager factory



### What is a Repository interface? How do you define a Repository interface? Why is it an interface not a class?
- 1. is a repository that need no implementation and that supports the basic CRUD (create, read, update and delete) operations
- 2. The Repository uses Java generics and takes two type arguments; an entity type and a type of the primary key of entities.
- 3.1 in order for Spring Data to be able to use the JDK dynamic proxy mechanism to create the proxy objects that intercept calls to repositories.
- 3.2 This also allows for supplying a custom base repository implementation class that will act as a
(custom) base class for all the Spring Data repositories in the application


### What is the naming convention for finder methods in a Repository interface?
- ```find(First[count])By[property expression][comparison operator][ordering operator]```
  - property expression: And Or
  - comparison opt: LessThan, GreaterThans, Between, like
  - orderer: OrderBy, Asc, desc
- Spring Data allows for four verbs in the method name: **get, read, find, and count**.
- parameter names are irrelevant, but they must be ordered to match up with the method name’s comparators


### How are Spring Data repositories implemented by Spring at runtime?
- JDK dynamic proxy
- check custom impl if have, or route to default

### What is @Query used for?
- to provide Spring Data with the query that should be performed.
- Queries annotated to the query method take precedence over queries defined using **@NamedQuery** or named queries declared in **orm.xml**.
- allows for running **native queries** by setting the nativeQuery flag to true
  - **can't use Sort, but Pageable**: Spring Data JPA does not currently support dynamic sorting for native queries, because it would have to manipulate the actual query declared, which it cannot do reliably for native SQL. You can, however, use native queries for pagination by specifying the count query yourself
- By default, Spring Data JPA uses position-based parameter binding(?1), but you can use @Param annotation to give a method parameter a concrete name and bind the name in the query(:name)
  - As of version 4, Spring fully supports Java 8’s parameter name discovery based on the **-parameters** compiler flag. By using this flag in your build as an alternative to debug information, you can **omit** the @Param annotation for named parameters.
```
  @Query(value = "SELECT * FROM USERS WHERE EMAIL_ADDRESS = ?1", nativeQuery = true)
  User findByEmailAddress(String emailAddress);
  
  @Query("select p from Person p where p.emailAddress = ?1")

  @Query("select u from User u where u.firstname = :firstname or u.lastname = :lastname")
  User findByLastnameOrFirstname(@Param("lastname") String lastname,
                                 @Param("firstname") String firstname);
```

## Spring MVC and the Web Layer

### Web MVC

- root context instantiated by ContextLoader Listener
- InternalResourceViewResolver: JSP
```xml
<web-app>
    <!-- Configure ContextLoaderListener to use AnnotationConfigWebApplicationContext
        instead of the default XmlWebApplicationContext -->
    <context-param>
        <param-name>contextClass</param-name>
        <param-value>
            org.springframework.web.context.support.AnnotationConfigWebApplicationContext
        </param-value>
    </context-param>

    <!-- Configuration locations must consist of one or more comma- or space-delimited
        fully-qualified @Configuration classes. Fully-qualified packages may also be
        specified for component-scanning -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>com.acme.AppConfig</param-value>
    </context-param>

    <!-- Bootstrap the root application context as usual using ContextLoaderListener -->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <!-- Declare a Spring MVC DispatcherServlet as usual -->
    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!-- Configure DispatcherServlet to use AnnotationConfigWebApplicationContext
            instead of the default XmlWebApplicationContext -->
        <init-param>
            <param-name>contextClass</param-name>
            <param-value>
                org.springframework.web.context.support.AnnotationConfigWebApplicationContext
            </param-value>
        </init-param>
        <!-- Again, config locations must consist of one or more comma- or space-delimited
            and fully-qualified @Configuration classes -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>com.acme.web.MvcConfig</param-value>
        </init-param>
    </servlet>

    <!-- map all requests for /app/* to the dispatcher servlet -->
    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>/app/*</url-pattern>
    </servlet-mapping>
</web-app>
```
```java
public class MyWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class<?>[] { RootConfig.class };
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class<?>[] { App1Config.class };
    }

    @Override
    protected String[] getServletMappings() {
        return new String[] { "/app1/*" };
    }
}

```
### MVC is an abbreviation for a design pattern. What does it stand for and what is the idea behind it?
- reusable, loose coupling, seperation of concerns


### What is the DispatcherServlet and what is it used for?
- front controller, Servlet, can be declaed in web.xml or use WebApplicationInitializer, map URL
- 1. receive req and delegates to handlers
- 2. resole view and exception
- each DispatcherServlet has its own WebApplicationContext


###  What is a web application context? What extra scopes does it offer?
- DispatcherServlet expects a WebApplicationContext, an extension of a plain ApplicationContext, for its own configuration. WebApplicationContext has a link to the ServletContext and Servlet it is associated with. It is also bound to the ServletContext such that applications can use static methods on RequestContextUtils to look up the WebApplicationContext if they need access to it.
- request, session, applcation(per ServletContext), websocket

### What is the @Controller annotation used for?
- stereotype of @Component

### How is an incoming request mapped to a controller and mapped to a method?
- @RequestHandler hold be DispathcerServlet


### What is the difference between @RequestMapping and @GetMapping?
- @RequestMapping: by default matches to all HTTP methods, class level
- @GetMapping: shortcut for GET


### What is @RequestParam used for?
- bind request parameters to method parameters.

### What are the differences between @RequestParam and @PathVariable?
- map different parts of request URLs: query param VS url

### [What are some of the parameter types for a controller method? What other annotations might you use on a controller method parameter? (You can ignore form-handling annotations for this exam)](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/web.html#mvc-ann-arguments)

- javax.servlet.ServletRequest, javax.servlet.ServletResponse, javax.servlet.http.HttpSession, java.security.Principal
- java.io.InputStream, java.io.Reader, java.io.OutputStream, java.io.Writer: raw req/respo body
- Map, Model, ModelMap
- Errors, BindingResult
- @RequestParam, @MatrixVariable, @PathVariable, @RequestHeader, @CookieValue, @RequestPart multipart, @ModelAttribute
- @Value("#request.requestURL") StringBuffer url
### What are some of the valid return types of a controller method?
- HttpEntity<E>, ResponseEntity<E>, HttpHeaders
- String, View, ModelAndView
- Map, Model, ModelAndView
- @ResponseBody, @ModelAttribute
- void
  - A method with a void return type (or null return value) is considered to have fully handled the response if it also has a ServletResponse, or an OutputStream argument, or an @ResponseStatus annotation. The same is true also if the controller has made a positive ETag or lastModified timestamp check (see Controllers for details).

  - If none of the above is true, a void return type may also indicate "no response body" for REST controllers, or default view name selection for HTML controllers.
- DeferredResult<V>, Callable<V>, StreamingResponseBody, ResponseBodyEmitter, SseEmitter: async


## REST
- PUT and DELETE is idempotent, not safe, 204(no content)
- POST and PATCH not idepotent

### What does REST stand for? What is a resource?

- REpresentational State Transfer
- any info

### What does CRUD mean?
- Create, Read, Update and Delete

### Is REST secure? What can you do to secure it?
- add seucrity layer for URL

### Is REST scalable and/or interoperable?
- The statelessness, the cacheability and the layered system constraints of the REST architectural
style allows for scaling a REST web service.
- interoperable: URI, HTTP verb, any media type

### Which HTTP methods does REST use?
- POST, GET, PUT, DELETE

```

Method	Idempotent	Safe
OPTIONS	yes	yes
GET	yes	yes
HEAD	yes	yes
PUT	yes	no
POST	no	no
DELETE	yes	no
PATCH	no	no

```


### What is an HttpMessageConverter?
- Convert a HttpInputMessage to an object of specified type.
- Convert an object to a HttpOutputMessage.
- impl
  - MappingJackson2HttpMessageConverter
  - Jaxb2RootElementHttpMessageConverter

### Is REST normally stateless?
- server however is not aware of state

### What does @RequestMapping do?
- consumes, headers, method(HTTP), params, path(value), produces

### Is @Controller a stereotype? Is @RestController a stereotype? What is a stereotype annotation? What does that mean? What is the difference between @Controller and @RestController?
- yes
- marker to fulfill a certain, distinct, role.
- @Contorller + @ResponseBody

### When do you need @ResponseBody?
- indicates a method return value should be bound to the web response body, like REST

### What are the HTTP status return codes for a successful GET, POST, PUT or DELETE operation?
- 200, 201, 200, 404/204

### When do you need @ResponseStatus?
- annotate exception classes
- applied to controller handler methods in order to override the original response status information

### Where do you need @ResponseBody? What about @RequestBody? Try not to get these muddled up!
- used when the web request body is to be bound to a parameter of the controller handler method.

• If you saw example Controller code, would you understand what it is doing? Could you tell if it was annotated correctly?

### Do you need Spring MVC in your classpath?
- spring-web for @

### What Spring Boot starter would you use for a Spring REST application?
- spring-boot-starter-web

### What are the advantages of the RestTemplate?
- **synchronous** HTTP client that simplifies sending requests and also enforces RESTful principles.

### If you saw an example using RestTemplate would you understand what it is doing?
- URI templates are automatically encoded
```java
restTemplate.getForObject("http://example.com/hotel list", String.class);
// Results in request to "http://example.com/hotel%20list"


String result = restTemplate.getForObject(
        "http://example.com/hotels/{hotel}/bookings/{booking}", String.class, "42", "21");
Map<String, String> vars = Collections.singletonMap("hotel", "42");

String result = restTemplate.getForObject(
        "http://example.com/hotels/{hotel}/rooms/{hotel}", String.class, vars);
	
```



## Spring Boot Intro
### What is Spring Boot?
- autoconifg
- starter
- Spring Boot makes it easy to create **stand-alone, production-grade Spring-based** Applications that you can run. We take an **opinionated view of the Spring platform and third-party libraries**, so that you can get started with minimum fuss. 

### What are the advantages of using Spring Boot?
- rapid dev
- no version conflict
- standalone JAR

- Provide a radically faster and widely accessible **getting-started experience** for all Spring development.
- Be opinionated out of the box but get out of the way quickly as requirements start to diverge from the defaults.
- Provide a range of **non-functional features** that are common to large classes of projects (such as embedded servers, security, metrics, health checks, and externalized configuration).
- Absolutely no code generation and **no requirement for XML** configuration.

### Why is it “opinionated”?
- predefined

### What things affect what Spring Boot sets up?
- @ConditionalOnXxxx
- class in classpath

### What is a Spring Boot starter POM? Why is it useful?
- ```spring-boot-starter-*```
- Starters are a set of **convenient dependency descriptors** that you can include in your application. You get a **one-stop shop** for all the Spring and related technologies that you need without having to hunt through sample code and copy-paste loads of dependency descriptors.
- predconfig without version conflict
- - The **spring-boot-starter-parent** is a special starter that provides useful Maven defaults. It also provides a dependency-management section so that you can **omit version** tags for “blessed” dependencies.


### Spring Boot supports both properties and YML files. Would you recognize and understand them if you saw them?
- key:value
- YAML: space


• Where does Spring Boot look for property file by default?
### How do you define profile specific property files?
- applicaton-{profile}.properties

### How do you access the properties defined in the property files?
- @Value

###  What properties do you have to define in order to configure external MySQL?
```YAML
spring:
   profiles: production
   datasource:
	url: jdbc:postgresql://localhost:5432/readinglist
	username: habuma
	password: password
jpa:
    database-platform: org.hibernate.dialect.PostgreSQLDialect
```

### How do you configure default schema and initial data?
- schema.sql, data.sql

### What is a fat jar? How is it different from the original jar?
- include all dep, standalone
- Executable jars (sometimes called “fat jars”) are archives containing your compiled classes along with all of the jar dependencies that your code needs to run.

### What is the difference between an embedded container and a WAR?
- in JAR, one app
- WAR deployed in a (shared) web container
- build WAR from Boot:
  - 1. <packaging>war</packaging>
  - 2. override configure() form SpringBootServletInitializer
  - as long as you don't remove main() from Application, you can run java -jar on the WAR
```java
public class ReadingListServletInitializer extends SpringBootServletInitializer {
    @Override
    protected SpringApplicationBuilder configure( SpringApplicationBuilder builder) {
	return builder.sources(Application.class);
    }
}
```

### What embedded containers does Spring Boot support?
- Tomcat, Jetty, Undertow

### Can you control logging with Spring Boot? How?
- logging.level.
- logging.pattern.

- Spring Boot uses Commons Logging(abstraction) for all internal logging but leaves the underlying log implementation open.
- By default, if you use the “Starters”, Logback is used for logging. (support impl like Java Util Logging, Log4J2 and Logback.)
- Console-output: By default, ERROR-level, WARN-level, and INFO-level messages are logged. 
  - ```$ java -jar myapp.jar --debug```
  - You can also specify debug=true in your application.properties.
- Coloer-coded Output: ```logging.pattern.console= %d{yyyy-MMM-dd HH:mm:ss.SSS} [%thread] %highlight(%-5level) %cyan(%logger{15}) - %msg %n```
- File output: logging.file.path, logging.file.name
- logging.level
- define logging.group.xxxx


## Spring Boot Auto Configuration


### How does Spring Boot know what to configure?
- detects dep available in classpath and then config bean using @Condtional
- @Import config class, @ImportResource XML; @ComponentScan auto from boot app root package
- Spring Boot auto-configuration attempts to automatically configure your Spring application based on the jar dependencies that you have added.
- define **exclusions** both at the annotation level and by using the property.
  - ```@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})```
  - spring.autoconfigure.exclude property
  
### What does @EnableAutoConfiguration do?
-  tells Spring Boot to “guess” how you want to configure Spring, based on the jar dependencies that you have added.
- on one of @Configuration

### What does @SpringBootApplication do?
- = @Configuration + @ComponentScan + @EnableAutoConfiguration
- root packagef for scan: its place implicitly defines a base “search package” for beans
- @SpringBootConfiguration
### Does Spring Boot do component scanning? Where does it look by default?
- @ComponentScan: Either basePackageClasses() or basePackages() (or its alias value()) may be specified to define specific packages to scan. If specific packages are not defined, scanning will occur from the package of the class that declares this annotation.

### How are DataSource and JdbcTemplate auto-configured?
- JAR and prop

### What is spring.factories file for?
- META-INF/spring.factories file is a special file picked up by the spring framework in which you can define how spring context will be customized.
- Locating Auto-configuration Candidates
- Spring Boot checks for the presence of a META-INF/spring.factories file within your published jar. The file should list your configuration classes under the EnableAutoConfiguration key

### How do you customize Spring auto configuration?
- Under the hood, auto-configuration is implemented with standard @Configuration classes. Additional @Conditional annotations are used to constrain when the auto-configuration should apply. 
- The file should list your configuration classes under the EnableAutoConfiguration key
```
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
com.mycorp.libx.autoconfigure.LibXAutoConfiguration,\
com.mycorp.libx.autoconfigure.LibXWebAutoConfiguration
```
### What are the examples of @Conditional annotations? How are they used?
- annotating @Configuration classes or individual @Bean methods. 
- The @ConditionalOnClass and @ConditionalOnMissingClass annotations let **@Configuration classes be included** based on the presence or absence of specific classes.
- @ConditionalOnBean and @ConditionalOnMissingBean let a **bean be included** based on the presence or absence of specific beans.
- @ConditionalOnProperty annotation lets configuration be included based on a Spring Environment property.
- @ConditionalOnResource annotation lets configuration be included only when a specific resource is present
- @ConditionalOnWebApplication and @ConditionalOnNotWebApplication annotations let configuration be included depending on whether the application is a “web application”. 
- @ConditionalOnExpression annotation lets configuration be included based on the result of a SpEL expression.

## Spring Boot Actuator
### What value does Spring Boot Actuator provide?
- help you monitor and manage your application when you push it to production. 

### What are the two protocols you can use to access actuator endpoints?
- using HTTP endpoints or with JMX

### What are the actuator endpoints that are provided out of the box?
- HTTP: endpoint along with a prefix of /actuator is mapped to a URL.
- /actuator/
  - auditevents
  - bean, mappings, env, conditions
  - caches, sessions
  - configprops, scheduledtasks, logs
  - flyway, liquibse
  - health, httptrace, info, metrics, threaddump
  - shutdown
  - WEB: heapdump, jolokia, logfile,  prometheus
### What is info endpoint for? How do you supply data?
- Displays arbitrary application info

### How do you change logging level of a package using loggers endpoint?
- POST: { "configuredLevel": "DEBUG"}; reset: as null


### How do you access an endpoint using a tag?
-  add any number of tag=KEY:VALUE query parameters to the end of the URL to dimensionally drill down on a meter

### What is metrics for?
- quantify the health
- Metric is a measure used to evaluate, to control and/or to select quantitatively
### How do you create a custom metric with or without tags?
- Having a dependency on micrometer-registry-{system} in your runtime classpath is enough for Spring Boot to configure the registry.
- You can register any number of MeterRegistryCustomizer beans to further configure the registry, such as applying common tags, before any meters are registered with the registry:
```java
@Bean
MeterRegistryCustomizer<MeterRegistry> metricsCommonTags() {
    return registry -> registry.config().commonTags("region", "us-east-1");
}
```
- To register custom metrics, inject MeterRegistry into your component:
```java
class Dictionary {

    private final List<String> words = new CopyOnWriteArrayList<>();

    Dictionary(MeterRegistry registry) {
        registry.gaugeCollectionSize("dictionary.size", Tags.empty(), this.words);
    }

    // …

}
```


### What is Health Indicator?  What is the Health Indicator status?
- monitoring software to alert someone when a production system goes down
- A HealthIndicator provides actual health information, including a Status. 
```java
@Component
public class MyHealthIndicator implements HealthIndicator {

    @Override
    public Health health() {
        int errorCode = check(); // perform some specific health check
        if (errorCode != 0) {
            return Health.down().withDetail("Error Code", errorCode).build();
        }
        return Health.up().build();
    }

}
```
### What are the Health Indicators that are provided out of the box?
- DataSourceHealthIndicator, DiskSpaceHealthIndicator, MongoHealthIndicator, RedisHealthIndicator if server is up


### What are the Health Indicator statuses that are provided out of the box
- DOWN, OUT_OF_SEVICE: 503
- UP, UNKNOWN: 200

### How do you change the Health Indicator status severity order?
- management.endpoint.health.status.order=fatal,down,out-of-service,unknown,up

### Why do you want to leverage 3rd-party external monitoring system?
- built-in, visualizing, central metirx, traces, logs

### Spring Boot Testing
- Test support is provided by two modules: spring-boot-test contains core items, and spring-boot-test-autoconfigure supports auto-configuration for tests.


### When do you want to use @SpringBootTest annotation?
- an alternative to the standard spring-test @ContextConfiguration annotation when you need Spring Boot features. 
- works by creating the ApplicationContext used in your tests through SpringApplication.

### What does @SpringBootTest auto-configure?

### What dependencies does spring-boot-starter-test brings to the classpath?
```
JUnit 5 (including the vintage engine for backward compatibility with JUnit 4): The de-facto standard for unit testing Java applications.

Spring Test & Spring Boot Test: Utilities and integration test support for Spring Boot applications.

AssertJ: A fluent assertion library.

Hamcrest: A library of matcher objects (also known as constraints or predicates).

Mockito: A Java mocking framework.

JSONassert: An assertion library for JSON.

JsonPath: XPath for JSON.
```


• How do you perform integration testing with @SpringBootTest for a web application?

### When do you want to use @WebMvcTest? What does it auto-configure?
- Often, @WebMvcTest is limited to a single controller and is used in combination with @MockBean to provide mock implementations for required collaborators.
- @WebMvcTest also auto-configures MockMvc.


### What are the differences between @MockBean and @Mock?
- @MockBean annotation that can be used to define a Mockito mock for a bean inside your ApplicationContext. You can use the annotation to add new beans or replace a single existing bean definition.

### When do you want @DataJpaTest for? What does it auto-configure?
- By default, it scans for @Entity classes and configures Spring Data JPA repositories. If an embedded database is available on the classpath, it configures one as well. Regular @Component beans are not loaded into the ApplicationContext.
- By default, data JPA tests are transactional and roll back at the end of each test. 
- If you want to use TestEntityManager outside of @DataJpaTest instances, you can also use the @AutoConfigureTestEntityManager annotation.
- A JdbcTemplate is also available if you need that. 

## Testing

- TDD
  - dev with well defined requirement in the test form
  - faster, confidence, refactoring, make you think about your design(if hard to test, reconsider), focus on what matters, find bug quick and early
- Unit Testing
  - tests on func, minimal dep, env isolation(like spring)
  - impl:
    - Stubs: sample test impl
    - Mocks: gen on startup time
    
  
- Integration testing
  - interaction of units
  - intg infrastructure
  - Spring:
    - spring-test.jar
    - SpringJUnit4ClassRunner: caches a shared ApplicatonContext across test methods
      - @DirtiesContext on method
    - @ActiveProfiles inside Test class, @Profile in @Configuration


### Do you use Spring in a unit test?
- mostly not, but with some Spring mock obj
- POJO with new and mock for insolation
- quick as  there is no runtime infrastructure to set up

- Mock obj
  - Environment
  - JNDI
  - Servlet API
  - Reactive

- MockEnvironment and MockPropertySource are useful for developing out-of-container tests for code that depends on environment-specific properties.
- To unit test your Spring MVC Controller classes as POJOs, use ModelAndViewAssert combined with MockHttpServletRequest, MockHttpSession


### What type of tests typically use Spring?
- integration testing
```
Spring’s integration testing support has the following primary goals:

To manage Spring IoC container caching between tests.
- By default, once loaded, the configured ApplicationContext is reused for each test. 
To provide Dependency Injection of test fixture instances.
- By default, the framework creates and rolls back a transaction for each test. 
To provide transaction management appropriate to integration testing.

To supply Spring-specific base classes that assist developers in writing integration tests.
```

### How can you create a shared application context in a JUnit integration test?
- @ContextConfiguration

### When and where do you use @Transactional in testing?
- Annotating a test method with @Transactional causes the test to be run within a transaction that is, by default, automatically rolled back after completion of the test. If a test class is annotated with @Transactional, each test method within that class hierarchy runs within a transaction. 
- If your test is @Transactional, it rolls back the transaction at the end of each test method by default. However, as using this arrangement with either RANDOM_PORT or DEFINED_PORT implicitly provides a real servlet environment, the HTTP client and server run in separate threads and, thus, in separate transactions. Any transaction initiated on the server does not roll back in this case.

### How are mock frameworks such as Mockito or EasyMock used?
- A mock object produces **predetermined results** when methods on the object are invoked.
- Mockito and EasyMock allows for dynamic creation of mock objects that can be used to mock collaborators of class(es) under test that are external to the system or trusted.
- you may have a facade over some remote service that is unavailable during development. Mocking can also be useful when you want to simulate failures that might be hard to trigger in a real environment.

- 1. used a mocking lib to genrate a mock
  - impl the dep interface on-the-fly
- 2. record the mock with expoectation of how be used for a scenario
  - what method will be called
  - what return value
- 3. exercise the scenario
- 4. verify mock expectation were met

### How is @ContextConfiguration used?
- @ContextConfiguration defines **class-level** metadata that is used to determine how to load and configure an **ApplicationContext** for integration tests. Specifically, @ContextConfiguration declares the application context **resource locations or the component classes** used to load the context.

- @WebAppConfiguration is a class-level annotation that you can use to declare that the ApplicationContext loaded for an integration test should be a WebApplicationContext. 
  - Note that @WebAppConfiguration must be used in conjunction with @ContextConfiguration, either within a single test class or within a test class hierarchy. 
- @ContextHierarchy should be declared with a list of one or more @ContextConfiguration instances, each of which defines a level in the context hierarchy.
- @ActiveProfiles is a class-level annotation that is used to declare which bean definition profiles should be active when loading an ApplicationContext for an integration test.
- @TestPropertySource is a class-level annotation that you can use to configure the locations of properties files and inlined properties to be added to the set of PropertySources in the Environment for an ApplicationContext loaded for an integration test.

- @DirtiesContext indicates that the underlying Spring ApplicationContext has been dirtied during the execution of a test and should be closed.
  - class or method level

- JUnit 5
  - @SpringJUnitConfig is a composed annotation that combines @ExtendWith(SpringExtension.class) from JUnit Jupiter with @ContextConfiguration from the Spring TestContext Framework.
  - @SpringJUnitWebConfig
    - auto build a MockMvc instance
```java
@SpringJUnitWebConfig(locations = "my-servlet-context.xml")
class MyWebTests {

    MockMvc mockMvc;

    @BeforeEach
    void setup(WebApplicationContext wac) {
        this.mockMvc = MockMvcBuilders.webAppContextSetup(this.wac).build();
    }

    // ...

}
```

### How does Spring Boot simplify writing tests?
- Spring Boot’s @*Test annotations search for your primary configuration automatically whenever you do not explicitly define one. @SpringBootApplication or @SpringBootConfiguration. 

### What does @SpringBootTest do? How does it interact with @SpringBootApplication and @SpringBootConfiguration?
- used as an alternative to the standard spring-test @ContextConfiguration annotation when you need Spring Boot features. The annotation works by creating the ApplicationContext used in your tests through SpringApplication. 
- Searches for a @SpringBootConfiguration
- custom Environment properties to be defined using the properties attribute
- if with JUnit4, also add **@RunWith(SpringRunner.class)**; 5 not need
- webEnvironment
  - MOCK(default): mocj web env
  - RANDOM_PORT, DEFINED_PORT: embedded server started
  - NONE: no web


