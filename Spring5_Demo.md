# Spring 5
- Container (20%)
- AOP (8%)
- JDBC (4%)
- Transactions (8%)
- MVC (8%)
- Security (6%)
- REST (6%)
- JPA Spring Data (4%)
- Testing (4%)
- Boot Into (8%)
- Boot Autoconfig (8%)
- Boot Actuator (8%)
- Boot Testing (8%)
## Intro with Spring Boot
- Spring initializr
```bash
./mvnw spring-boot:run

spring.h2.console.enabled=true

# o.s.b.a.h2.H2ConsoleAutoConfiguration    : H2 console available at
```
- JPA: ORM
```java
@Entity
public class Book {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    private String title;
    private String isbn;
    private String publisher;

    @ManyToMany
    @JoinTable(name = "author_book", joinColumns = @JoinColumn(name = "book_id"),
    inverseJoinColumns = @JoinColumn(name = "author_id"))
    private Set<Author> authors = new HashSet<>();
    
    @ManyToOne
    private Publisher publisher;
...

@Entity                                            
public class Author {                              
                                                   
    @Id                                            
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;                               
    private String firstName;                      
    private String lastName;                       
                                                   
    @ManyToMany(mappedBy = "authors")      // target side        
    private Set<Book> books  = new HashSet<>();    

...

@Entity
public class Publisher {

    @OneToMany
    @JoinColumn(name = "publisher_id")
    private Set<Book> books = new HashSet<>();
    
...
public interface PublisherRepository extends CrudRepository<Publisher, Long> {
}
```
- Equilty for JPA entity object
  - business keys
  - id

```java

		ApplicationContext ctx = SpringApplication.run(DemoBootApplication.class, args);
		MyBean bean = (MyBean) ctx.getBean("myBean");
        
        
@Service
public class GreetingServiceImpl implements GreetingService {...}

@Controller
public class ConstructorInjectedController {
    private final GreetingService greetingService;

    public ConstructorInjectedController(GreetingService greetingService) {
        this.greetingService = greetingService;
    }
....
}
```
- DI: composition 
  - perfer Constructor(@Autowired is optional) with final field, no DI field, less DI setter
  - via Interface: decide imp in runtime; @ on impl
- IoC runtime env, control DI


- @Qualifier and @Primary
```java

    public ConstructorInjectedController(@Qualifier("beanName") GreetingService greetingService) {
        this.greetingService = greetingService;
    }
    
    
@Primary
@Service
public class PrimaryGreetingService implements GreetingService {
    
```
- @Profile

```java
@Profile("ES")
@Service("i18nService")
public class I18NSpanishService implements GreetingService {

spring.profiles.active=ES

@Profile({"EN", "default"})
```

- for module not need be built as fat jar, as not main class
```xml
    <properties>
        <spring-boot.repackage.skip>true</spring-boot.repackage.skip>
    </properties>
```
- External property
```java
@Configuration
// @PropertySource("classpath:datasource.properties")
@PropertySources({ // Spring 4 // no need for Spring Boot app.prop
        @PropertySource("classpath:datasource.properties"),
        @PropertySource("classpath:jms.properties")
})
public class PropertyConfig {

    @Value("${guru.username}") // SpEL
    String user;

    @Value("${guru.password}")
    String password;

    @Value("${guru.dburl}")
    String url;
    
    @Autowired
    Environment env;  // env.getProperty("USERNAME")

    @Bean
    public FakeDataSource fakeDataSource(){
        FakeDataSource fakeDataSource = new FakeDataSource();
        fakeDataSource.setUser(user);
        fakeDataSource.setPassword(password);
        fakeDataSource.setUrl(url);
        return fakeDataSource;
    }

    @Bean // no need for Spring Boot app.prop
    public static PropertySourcesPlaceholderConfigurer properties(){
        PropertySourcesPlaceholderConfigurer propertySourcesPlaceholderConfigurer =new           PropertySourcesPlaceholderConfigurer();
        return  propertySourcesPlaceholderConfigurer;
    }
```


- Externalized config order
- test usage > command line > Java System prop > OS env > profile based prop > application.prop outside > app.prop in jar > default SpringApplication.setDefaultProperties

- Axis TCPMon
  - http req info

- devtools
  - restart quick for class load
  - liveload(browser)

### JPA

- Relationship
```java
@Entity
public class Recipe {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Lob
    private Byte[] image;

    @OneToOne(cascade = CascadeType.ALL)  // owner
    private Notes notes;
    
    @OneToMany(cascade = CascadeType.ALL, mappedBy = "recipe") // target property from Ingredient 
    private Set<Ingredient> ingredients;
    
    @Enumerated(value = EnumType.STRING) // default is EnumType.ORDINAL
    private Difficulty difficulty;
    
    @ManyToMany  
    @JoinTable(name = "recipe_category",  
        joinColumns = @JoinColumn(name = "recipe_id"),
            inverseJoinColumns = @JoinColumn(name = "category_id"))  // to have only one join table
    private Set<Category> categories;

@Entity
public class Notes {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @OneToOne
    private Recipe recipe;

    @Lob
    private String recipeNotes;

@Entity
public class Ingredient {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne
    private Recipe recipe;

@Entity
public class Category {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToMany(mappedBy = "categories")
    private Set<Recipe> recipes;

```






