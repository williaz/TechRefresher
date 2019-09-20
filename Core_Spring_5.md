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











