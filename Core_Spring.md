- AOP (8%)
- Security (6%)
- Transactions (8%)

- JDBC (4%)
- JPA Spring Data (4%)

- Testing (4%)
- Boot Testing (8%)

- MVC (8%)
- REST (6%)

- Container (20%)

- Boot Into (8%)
- Boot Autoconfig (8%)
- Boot Actuator (8%)




## Spring Security

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
- It maps a particular URL pattern to a list of filters built up from the bean names specified in the filters element, and combines them in a bean of type SecurityFilterChain. The pattern attribute takes an Ant Paths and the most specific URIs should appear first [7]. At runtime the FilterChainProxy will locate the first URI pattern that matches the current web request and the list of filter beans specified by the filters attribute will be applied to that request. The filters will be invoked in the order they are defined, so you have complete control over the filter chain which is applied to a particular URL.

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
- 4: @PreAuthoriz2, @PostAuthorize, @PreFilter, @PostFilter


## AOP 
Aspect oriented programming

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

### What is a pointcut, a join point, an advice, an aspect, weaving?
- Advice: the **task** of an aspect
  - Advice defines both the what and the when of an aspect
  - Spring: Advice take place when: Before, After, After-returning, After-throwing, Around
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
- public, from ooutside bean

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


## JDBC
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


### What is the Template design pattern and what is the JDBC template?
- A template method defines the skeleton of a process.
- The process itself is fixed; it never changes.

### What is a callback? What are the three JdbcTemplate callback interfaces that can be used with queries? What is each used for? (You would not have to remember the interface names in the exam, but you should know what they do if you see them in a code sample).
- a callback, also known as a "call-after"[1] function, is any executable code that is passed as an argument to other code; that other code is expected to call back (execute) the argument at a given time. This execution may be immediate as in a synchronous callback, or it might happen at a later time as in an asynchronous callback.
- ResultSetExtractor: Allows for processing of an entire result set, possibly consisting multiple rows of data, at once.
- RowCallbackHandler: Allows for processing rows in a result set one by one typically accumulating some type of result.
- RowMapper: Allows for processing rows in a result set one by one and creating a Java object for each row.

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
	
this.jdbcTemplate.execute("create table mytable (id integer, name varchar(100))");
```

## Transaction

### What is a transaction? What is the difference between a local and a global transaction?
- A transaction is an operation that consists of a number of tasks that takes place as **a single unit – either all** tasks are performed or no tasks are performed.
  - ACID
    - Atomicity: all or nothing
    - Consistency: no violation
    - Isolation: no effect on others
    - Durablility: change is durable

- Global transactions enable you to work with multiple transactional resources, typically relational databases and message queues. The application server manages global transactions through the JTA,
- Local transactions are resource-specific, such as a transaction associated with a JDBC connection
  - it cannot help ensure correctness across multiple resources.

### Is a transaction a cross cutting concern? How is it implemented by Spring?
- AOP

### How are you going to define a transaction in Spring?
- 1. PlatformTransactionManager bean.
- 2. @EnableTransactionManagement on @Configuration class
- 3. @Transactional code

### What does @Transactional do? What is the PlatformTransactionManager?
- PlatformTransactionManager defines A transaction strategy
  - TransactionDefinition
    - Isolation
    - Propagation: scope
    - Timeout
    - Read-only
  - PlatformTransactionManager implementations normally require knowledge of the environment in which they work: JDBC, JTA, Hibernate, and so on.
    - transactionManager drive advice


### Is the JDBC template able to participate in an existing transaction?
- yes, TransactionAwareDataSourceProxy.

###  What is a transaction isolation level? How many do we have and how are they ordered?
- determine how the changes within a transaction are visible to other users and systems accessing the database prior to the transaction being committed.
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

    @Transactional("order")
    public void setSomething(String name) { ... }

    @Transactional("account")
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
• What do you need to do in Spring if you would like to work with JPA?

• Are you able to participate in a given transaction in Spring while working with JPA?

• Which PlatformTransactionManager(s) can you use with JPA?

• What do you have to configure to use JPA with Spring? How does Spring Boot make this easier?

• What is a Repository interface?
• How do you define a Repository interface? Why is it an interface not a class?
• What is the naming convention for finder methods in a Repository interface?
• How are Spring Data repositories implemented by Spring at runtime?
• What is @Query used for?


## Testing

- [x] Do you use Spring in a unit test?
- mostly not, but with some Spring mock obj
- POJO with new and mock for insolation
- quick as  there is no runtime infrastructure to set up
- MockEnvironment and MockPropertySource are useful for developing out-of-container tests for code that depends on environment-specific properties.
- To unit test your Spring MVC Controller classes as POJOs, use ModelAndViewAssert combined with MockHttpServletRequest, MockHttpSession


- [x] What type of tests typically use Spring?
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

• How can you create a shared application context in a JUnit integration test?

- [x] When and where do you use @Transactional in testing?
- Annotating a test method with @Transactional causes the test to be run within a transaction that is, by default, automatically rolled back after completion of the test. If a test class is annotated with @Transactional, each test method within that class hierarchy runs within a transaction. 

- [x] How are mock frameworks such as Mockito or EasyMock used?
- you may have a facade over some remote service that is unavailable during development. Mocking can also be useful when you want to simulate failures that might be hard to trigger in a real environment.



- [x] How is @ContextConfiguration used?
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

- [x] How does Spring Boot simplify writing tests?
- Spring Boot’s @*Test annotations search for your primary configuration automatically whenever you do not explicitly define one. @SpringBootApplication or @SpringBootConfiguration. 

- [x] What does @SpringBootTest do? How does it interact with @SpringBootApplication and @SpringBootConfiguration?
- used as an alternative to the standard spring-test @ContextConfiguration annotation when you need Spring Boot features. The annotation works by creating the ApplicationContext used in your tests through SpringApplication. 
- if with JUnit4, also add @RunWith(SpringRunner.class); 5 not need
- webEnvironment
  - MOCK(default): mocj web env
  - RANDOM_PORT, DEFINED_PORT: embedded server started
  - NONE: no web

### Spring Boot Testing

• When do you want to use @SpringBootTest annotation?

• What does @SpringBootTest auto-configure?

- [x] What dependencies does spring-boot-starter-test brings to the classpath?
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

- [x] When do you want to use @WebMvcTest? What does it auto-configure?
- Often, @WebMvcTest is limited to a single controller and is used in combination with @MockBean to provide mock implementations for required collaborators.
- @WebMvcTest also auto-configures MockMvc.


- [x] What are the differences between @MockBean and @Mock?
- @MockBean annotation that can be used to define a Mockito mock for a bean inside your ApplicationContext. You can use the annotation to add new beans or replace a single existing bean definition.

- [x] When do you want @DataJpaTest for? What does it auto-configure?
- By default, it scans for @Entity classes and configures Spring Data JPA repositories. If an embedded database is available on the classpath, it configures one as well. Regular @Component beans are not loaded into the ApplicationContext.
- By default, data JPA tests are transactional and roll back at the end of each test. 
- If you want to use TestEntityManager outside of @DataJpaTest instances, you can also use the @AutoConfigureTestEntityManager annotation.
- A JdbcTemplate is also available if you need that. 

