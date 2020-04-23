# Spring 5
- Container (20%)

- AOP (8%)
- Transactions (8%)

- MVC (8%)
- REST (6%)

- JDBC (4%)
- JPA Spring Data (4%)

- Security (6%)

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

- DevTools
  - restart quick for class load
  - liveload(browser)
  - it will disable caching for all template libraries but will disable itself (and thus reenable template caching) when your application is deployed.

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

- Database initializer
  - Spring's DataSource initializer via Spring Boot by default load schema.sql and data.sql from the root of classpath
  - or set spring.datasource.platform to load schema-${platform}.sql and data-${platform}.sql

- Hibernate DDL Auto
  - spring.jpa.hibernate.ddl-auto
  - options: none, validate, update, create, create-drop
  - Spring Boot uses create-drop for embedded DB or none

- JPA query method
```java
public interface CategoryRepository extends CrudRepository<Category, Long> {

    Optional<Category> findByDescription(String description);
    
    
@Component
public class RecipeBootstrap implements ApplicationListener<ContextRefreshedEvent> {
   
    @Override
    public void onApplicationEvent(ContextRefreshedEvent event) {
        recipeRepository.saveAll(getRecipes());
    }

    private List<Recipe> getRecipes() {
```
- use one side setters to manage JPA Bi-directional relationship
- Recommand Bidirectional which mean both side know the other, allows to navigate the object graph from either 
- JPA Cascade types
  - control how changes are cascasded from parent obj to child
  - No default type in JPA
  - JPA 2.1: PERSIST, MERGE, REFRESH, REMOVE, DETACH, ALL @@

- JPA embedable type like Address @@
- JPA auto update timestamp prop for audit
  - @PrePersist, @PreUpdate

- Hibernate persistence strategy for inheritance
  - default: SINGLE TABLE, create for each => a lot unused DB columns
  - JOIN TABLE

### Testing
- code under test
- Test fixture: a fixed state of objs to get repeatable results
  - input data, mock, know data in DB
- Unit Tests: 
  - specific sections of code, test business logic
  - code coverage: percentage of lines of code tested: 70-78%
  - no external dep like DB, Spring context
- Integration tests
  - parts of overall sys, test interaction
  - with Spring context, DV, message brokers
- Functional tests
  - app is live, deployed
  - end to end testing

- TDD: test first, code to meet the test
- BDD: builds on TDD; given, when, then

- Mock: fake imp of class
- Spy: partial mock, override select methods of a real class

- Junit4 @
  - @Test @Ignore, @Test(expected = Exception.class), @Test(timeout = 10)
  - @Before, @After
  - @BeforeClass, @AfterClass

- Spring Boot testing @
  - @RunWith(SpringRunner.class)
  - @SpringBootTest: search for app for config
  - @TestConfiguration
  - @MockBean, @SpyBean
  - @JsonTest, @WebMvcTest, @DataJpaTest, @JdbcTest, @DataMongoTest, @RestClientTest, @AutoConfigreRestDocks
  - @BootStrapWith, @ContextConfiguration, @ContextHierachy, @ActiveProfiles, @TestPropertySource, @DirtiesContext, @WebAppConfiguration, @TestExecutionListenes
  - @Transactional, @BeforeTransaction, @AfterTransaction, @Commit, @Rollback
  - @Sql, @SqlConfig, @SqlGroup, @Repeat, @Timed
  - @IfProfileValue, @ProfileValueSourceConfiguration


- Mockito
```java
    @Mock
    RecipeRepository recipeRepository;


    @Before
    public void setUp() throws Exception {
        MockitoAnnotations.initMocks(this);  ///

        recipeService = new RecipeServiceImpl(recipeRepository);
    }

    @Test
    public void getRecipes() throws Exception {

        Recipe recipe = new Recipe();
        HashSet recipesData = new HashSet();
        recipesData.add(recipe);

        when(recipeRepository.findAll()).thenReturn(recipesData); ////

        Set<Recipe> recipes = recipeService.getRecipes();

        assertEquals(recipes.size(), 1);
        verify(recipeRepository, times(1)).findAll(); //// interaction, call times
    }
    
    
        verify(model, times(1)).addAttribute(eq("recipes"), argumentCaptor.capture());
        Set<Recipe> setInController = argumentCaptor.getValue();
```


- MockMvc

```java
   @Test
    public void testMockMVC() throws Exception {
        MockMvc mockMvc = MockMvcBuilders.standaloneSetup(controller).build();

        mockMvc.perform(get("/"))
                .andExpect(status().isOk())
                .andExpect(view().name("index"));
    }
```

- Integration test
```java
@RunWith(SpringRunner.class)
@DataJpaTest
public class UnitOfMeasureRepositoryIT {

    @Autowired
    UnitOfMeasureRepository unitOfMeasureRepository;

    @Before
    public void setUp() throws Exception {
    }

    @Test
    @DirtiesContext // indicates that the ApplicationContext associated with a test 
    // is dirty and should therefore be closed and removed from the context cache.
    public void findByDescription() throws Exception {

        Optional<UnitOfMeasure> uomOptional = unitOfMeasureRepository.findByDescription("Teaspoon");

        assertEquals("Teaspoon", uomOptional.get().getDescription());
    }
```
- CircleCI 
- JUnit5 for Java 8+
  - Lambda, streams, Optional
  - test runner for Junit 3/4
  - Assertions.assserThrows, assertTimeout, @ExtendWith
  - @BeforeEach, @AfterEach, @BeforeAll, @AfterAll, @Disabled, @Tag

- WebJars: client-side web libraries (e.g. jQuery & Bootstrap) packaged into JAR (Java Archive) files.


```java
   @Test
    public void handleImagePost() throws Exception {
        MockMultipartFile multipartFile =
                new MockMultipartFile("imagefile", "testing.txt", "text/plain",
                        "Spring Framework Guru".getBytes());

        mockMvc.perform(multipart("/recipe/1/image").file(multipartFile))
                .andExpect(status().is3xxRedirection())
                .andExpect(header().string("Location", "/recipe/1/show"));

        verify(imageService, times(1)).saveImageFile(anyLong(), any());
    }

    // ----------
    @PostMapping("recipe/{id}/image")
    public String handleImagePost(@PathVariable String id, @RequestParam("imagefile") MultipartFile file){

        imageService.saveImageFile(Long.valueOf(id), file);

        return "redirect:/recipe/" + id + "/show";
    }
    
        
    @GetMapping("recipe/{id}/recipeimage")
    public void renderImageFromDB(@PathVariable String id, HttpServletResponse response) throws IOException {
        RecipeCommand recipeCommand = recipeService.findCommandById(Long.valueOf(id));

        if (recipeCommand.getImage() != null) {
            byte[] byteArray = new byte[recipeCommand.getImage().length];
            int i = 0;

            for (Byte wrappedByte : recipeCommand.getImage()){
                byteArray[i++] = wrappedByte; //auto unboxing
            }

            response.setContentType("image/jpeg");
            InputStream is = new ByteArrayInputStream(byteArray);
            IOUtils.copy(is, response.getOutputStream());  //// tomcat
        }
    }
    // ----------
    @Override
    @Transactional
    public void saveImageFile(Long recipeId, MultipartFile file) {

        try {
            Recipe recipe = recipeRepository.findById(recipeId).get();

            Byte[] byteObjects = new Byte[file.getBytes().length]; // boolean[] to Boolean[]

            int i = 0;

            for (byte b : file.getBytes()){
                byteObjects[i++] = b;
            }

            recipe.setImage(byteObjects);

            recipeRepository.save(recipe);
        } catch (IOException e) {
            //todo handle better
            log.error("Error occurred", e);

            e.printStackTrace();
        }
    }
```
- Command Object in Spring as a POJO/JavaBean/etc.. that backs the form in your presentation layer.

- WebDataBinder is used to populate form fields onto beans. 
- An init binder method initializes WebDataBinder and registers specific handlers, etc on it.
```java
    @InitBinder
    public void setAllowedFields(WebDataBinder dataBinder) {
        dataBinder.setDisallowedFields("id");
    }

```

- @ResponseStatus
  - annotate custom **exception class**es to indicate to the framewrok the HTTP status you want returned when that exception is thrown
  - global to the app
  
```java
@ResponseStatus(HttpStatus.NOT_FOUND)
public class NotFoundException extends RuntimeException {
```
- @ExceptionHandler
  - handle in controller level
  - can return view
  - with @ResponseStatus
- HandlerExceptionResolver
  - interface to implement for custom exception handling
  - used internally by MVC
  - model is not passed
  - Impl:
    - ExceptionHandlerExceptionResolver (to @ExceptionHandler)
    - ResponseStatusExceptionResolver (matching @ResponseStatus)
    - DefaultHandlerExceptionResolver (internal: spring exc to HTTP code)
    - SimpleMappingExceptionResolver: map exc to view by defining exc class name and view name
     

```java
@Slf4j
@Controller
public class RecipeController {
....
    @ResponseStatus(HttpStatus.NOT_FOUND)
    @ExceptionHandler(NotFoundException.class)
    public ModelAndView handleNotFound(Exception exception){

        log.error("Handling not found exception");
        log.error(exception.getMessage());

        ModelAndView modelAndView = new ModelAndView();

        modelAndView.setViewName("404error");
        modelAndView.addObject("exception", exception);


        return modelAndView;
    }
    
    
@Slf4j
@ControllerAdvice
public class ControllerExceptionHandler {

    @ResponseStatus(HttpStatus.BAD_REQUEST)
    @ExceptionHandler(NumberFormatException.class)
    public ModelAndView handleNumberFormat(Exception exception){

        log.error("Handling Number Format Exception");
        log.error(exception.getMessage());

        ModelAndView modelAndView = new ModelAndView();

        modelAndView.setViewName("400error");
        modelAndView.addObject("exception", exception);

        return modelAndView;
    }
}
```
- @ControllerAdvice: global level

- To apply validation in Spring MVC, you need to
  - Declare validation rules on the class that is to be validated: @NotNull and @Size in DTO
  - Specify that validation should be performed in the controller methods that
require validation: @Valid and @Error on argument
    - If there are any validation errors, the details of those errors will be captured in an object Errors
  - Modify the form views to display validation errors.

- Data validation JSR-303
 - @Null, @NotNUll, @AssertTrue, @AssertFalse, @Min, @Max
 - @DecimalMin, @DecimalMax, @Negative, @NegativeOrZero, @Positive, @PositiveOrZero
 - @Size: string or colleciton
 - @Digits,
 - date: @Past, @PastOrPresent, @Future, @FutureOrPresent
 - @Pattern, @NotEmpty, @NotBlank, @Email

- @Valid to trigger validations
```java
@PostMapping("recipe")
    public String saveOrUpdate(@Valid @ModelAttribute("recipe") RecipeCommand command, BindingResult bindingResult){
          if(bindingResult.hasErrors()){  // BindingResult extends Errors

            bindingResult.getAllErrors().forEach(objectError -> {
                log.debug(objectError.toString());
            });

@Getter
@Setter
@NoArgsConstructor
public class RecipeCommand {
    private Long id;

    @NotBlank
    @Size(min = 3, max = 255)
    private String description;

    @Min(1)
    @Max(999)
    private Integer prepTime;
    
    @Pattern(regexp="^(0[1-9]|1[0-2])([\\/])([1-9][0-9])$", message="Must be formatted MM/YY")
    private String ccExpiration;
```

- Thymeleaf
  - Spring hands the request over to a view, it copies the model data into **request attributes** that Thymeleaf and other view templating options have ready access to.
  - The ```${}``` operator tells it to use the value of a request attribute
  - ```th:each```  iterates over a **collection** of elements, rendering the HTML once for each item in the collection.
  - Thymeleaf’s operator ```@{}``` is used to produce a **context-relative path to the static** artifacts that they’re referenc ing.```<img th:src="@{/images/TacoCloud.png}"/>```
  - spring.thymeleaf.cache=false
  
  
```html
<span class="validationError"
th:if="${#fields.hasErrors('ccNumber')}"
th:errors="*{ccNumber}">CC Num Error</span>
                                <div class="col-md-3 form-group" th:class="${#fields.hasErrors('description')}
                                ? 'col-md-3 form-group has-error' : 'col-md-3 form-group'">
                                    <label>Recipe Description:</label>
                                    <input type="text" class="form-control" th:field="*{description}" th:errorclass="has-error"/>
                                    <span class="help-block" th:if="${#fields.hasErrors('description')}">
                                        <ul>
                                            <li th:each="err : ${#fields.errors('description')}" th:text="${err}"/>
                                        </ul>
                                    </span>
                                </div>
```

- view controller
  - controller that does nothing but forward the request to a view. GET
  - when a controller is simple enough that it doesn’t populate a model or process input
  - WebMvcConfigurer.addViewControllers() is given a ViewControllerRegistry that you can use to register one or more view controllers.
    - any configuration class can implement WebMvcConfigurer and override the method addViewController

- creating a new configuration class for each kind of configuration (web, data, security, and so on), keeping the application bootstrap configuration clean and simple.

```java

@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("home");
    }
}
```

- messages.properties
```
#Validaiton Messages
#Order of precedence
# 1 code.objectName.fieldName
# 2 code.fieldName
# 3 code.fieldType (Java data type)
# 4 code
NotBlank.recipe.description=Description Cannot Be Blank
Size.recipe.description={0} must be between {2} and {1} characters long.
```

- i18n
  - defualt check header: 'accept-language' = 'en-US'
    - AcceptHeaderLocaleResolver
  - locale of JVM: FixedLocaleResolver
  - MVC: LocaleChangeInterceptor to config a custom poaram to change locale
  - session, cookie
  - Resource buildes hignest match: messages_en.properties

```
<label th:text="#{recipe.description}">Recipe Description d:</label>

recipe.description=Description (default)

```

- There are two different (but related) kinds of configurations in Spring:
  - Bean wiring—Configuration that declares application components to be created
as beans in the Spring application context and how they should be injected into
each other.
  - Property injection—Configuration that sets values on beans in the Spring application
context.
     - you can derive their values from other configuration properties.

```yaml
greeting:
    welcome: You are using ${spring.application.name}.
```
- environment abstraction is a one-stop shop for any configurable property. It abstracts the origins of properties so that beans needing those properties can con sume them from Spring itself.
  - JVM system properties
  - Operating system environment variables
  - Command-line arguments
  - Application property configuration files

```bash
$ export SERVER_PORT=9090
```
- @ConfigurationProperties on Bean to suppoert prop injection prop from spring env abstraction
  - With a **configuration property holder bean**, you’ve collected configuration property specifics in one place, leaving the classes that need those properties relatively clean.
```java
@ConfigurationProperties(prefix="taco.orders")
public class OrderController {


```
  - add meta in additional-spring-configuraton-metadata.json
```json
{
"properties": [
{
"name": "taco.orders.page-size",
"type": "java.lang.String",
"description":
"Sets the maximum number of orders to display in a list."
}
]
}
```

- to set it up to handle HTTPS requests.
  - server.port property is set to 8443,  HTTPS servers. 
  - The server.ssl.key-store property should be set to the path where the keystore file is created. 
  - Here it’s shown with a  ```file://URL``` to load it from the filesystem, but if you package it within the application JAR file, you’ll use a classpath: URL to reference it. 
  - And both the and server.ssl.key-store-password server.ssl.key-password properties are set to the password that was given when creating the keystore.
```yaml
$ keytool -keystore mykeys.jks -genkey -alias tomcat -keyalg RSA

server:
    port: 8443
    ssl:
          key-store: file:///path/to/mykeys.jks
          key-store-password: letmein
          key-password: letmein

```

- logging
  - By default, Spring Boot configures logging via Logback (http://logback.qos.ch) to write to the console at an INFO level
  - prefixed with log.level, followed by the name of the logger
  - logging.path, logging.file
  - By default, the log files rotate once they reach 10 MB in size.
```yaml
logging:
    path: /var/logs/
    file: TacoCloud.log
    level:
         root: WARN
    org:
         springframework:
         security: DEBUG
```

- Profiles are a type of condi tional configuration where different beans, configuration classes, and configuration properties are applied or ignored based on what profiles are active at runtime.
  - application-{profile name}.properties
  - if you deploy a Spring application to Cloud Foundry, a profile named **cloud** is automatically activated for you.
  - on bean ```@Bean @Profile("dev", "!prod")```
```
export SPRING_PROFILES_ACTIVE=prod

% java -jar taco-cloud.jar --spring.profiles.active=prod
```

- code coverage: codecov

- schema gen form hibernate
```yml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/sfg_dev
    username: sfg_dev_user
    password: guru
  jpa:
    hibernate:
      ddl-auto: validate
    database-platform: org.hibernate.dialect.MySQL5InnoDBDialect
    database: mysql
    show-sql: true
#    properties:
#      javax:
#        persistence:
#          schema-generation:
#            create-source: metadata
#            scripts:
#              action: create
#              create-target: guru_database_create.sql
```
-- embeded mongo


- Mongo model

```java
@Document
public class Recipe {

    @Id
    private String id;
    
    private Set<Ingredient> ingredients = new HashSet<>();
    private Byte[] image;


    @DBRef
    private Set<Category> categories = new HashSet<>();

@Document
public class Category {
    @Id
    private String id;

    @DBRef
    private Set<Recipe> recipes;

public class Ingredient {

    private String id = UUID.randomUUID().toString();

    @DBRef
    private UnitOfMeasure uom;
```
- JdbcTemplate
  - queryForObject(), query() -> List
  - update(): a PreparedStatementCreatorFactory, giving it the SQL you want to execute, as well as the types of each query parameter
  - executeAndReturnKey(), execute()


```java
private long saveTacoInfo(Taco taco) {
    taco.setCreatedAt(new Date());
    PreparedStatementCreator psc =
        new PreparedStatementCreatorFactory( // #1 set
            "insert into Taco (name, createdAt) values (?, ?)",
            Types.VARCHAR, Types.TIMESTAMP
        ).newPreparedStatementCreator(  // #2 value
            Arrays.asList(
                taco.getName(),
                new Timestamp(taco.getCreatedAt().getTime())));
    KeyHolder keyHolder = new GeneratedKeyHolder();
    jdbc.update(psc, keyHolder);  // #3 return ID
    return keyHolder.getKey().longValue();
}

@Autowired
public JdbcOrderRepository(JdbcTemplate jdbc) {
    this.orderInserter = new SimpleJdbcInsert(jdbc)
        .withTableName("Taco_Order")
        .usingGeneratedKeyColumns("id");
    this.orderTacoInserter = new SimpleJdbcInsert(jdbc)
        .withTableName("Taco_Order_Tacos");
    this.objectMapper = new ObjectMapper();
}

```
- Spring’s RowMapper for the purpose of mapping each row in the result set to an object
- ```schema.sql/data.sql```: will be executed against the database when the application starts

- class-level @SessionAttributes: kept in session and available across multiple requests
- @ModelAttribute 
   - to indicate that its value should come from the **model**
   - and that Spring MVC **shouldn’t bind request parameters** to it.

- JPA entity requires
  - @Entity, @Id
  - @NoArgsConstructor

```java
@Data
@Entity
public class Taco {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    @NotNull
    @Size(min = 5, message = "Name must be at least 5 characters long")
    private String name;
    private Date createdAt;
    @ManyToMany(targetEntity = Ingredient.class)
    @Size(min = 1, message = "You must choose at least 1 ingredient")
    private List < Ingredient > ingredients;
    
    @PrePersist  // #
    void createdAt() {
        this.createdAt = new Date();
    }
}
```

## Spring Boot
```
Spring Boot Intro
• What is Spring Boot?
  do srping config for you
• What are the advantages of using Spring Boot?

Spring Boot is an exciting new way to develop Spring applications with minimal friction
from the framework itself. Auto-configuration eliminates much of the boilerplate
configuration that infests traditional Spring applications. Spring Boot starters enable
you to specify build dependencies by what they offer rather than use explicit library
names and version. The Spring Boot CLI takes Spring Boot’s frictionless development
model to a whole new level by enabling quick and easy development with Groovy from
the command line. And the Actuator lets you look inside your running application to
see what and how Spring Boot has done.


• Why is it “opinionated”?
• What things affect what Spring Boot sets up?
• What is a Spring Boot starter POM? Why is it useful?
• Spring Boot supports both properties and YML files. Would you recognize and understand
them if you saw them?
• Can you control logging with Spring Boot? How?
• Where does Spring Boot look for property file by default?
• How do you define profile specific property files?
• How do you access the properties defined in the property files?
• What properties do you have to define in order to configure external MySQL?
• How do you configure default schema and initial data?
• What is a fat far? How is it different from the original jar?
• What is the difference between an embedded container and a WAR?
• What embedded containers does Spring Boot support?

Spring Boot Auto Configuration
• How does Spring Boot know what to configure?
• What does @EnableAutoConfiguration do?
• What does @SpringBootApplication do?
• Does Spring Boot do component scanning? Where does it look by default?
• How are DataSource and JdbcTemplate auto-configured?
• What is spring.factories file for?
• How do you customize Spring auto configuration?
• What are the examples of @Conditional annotations? How are they used?

Spring Boot Actuator
• What value does Spring Boot Actuator provide?
• What are the two protocols you can use to access actuator endpoints?
• What are the actuator endpoints that are provided out of the box?
• What is info endpoint for? How do you supply data?
• How do you change logging level of a package using loggers endpoint?
• How do you access an endpoint using a tag?
• What is metrics for?
• How do you create a custom metric with or without tags?
• What is Health Indicator?
• What are the Health Indicators that are provided out of the box?
• What is the Health Indicator status?
• What are the Health Indicator statuses that are provided out of the box
• How do you change the Health Indicator status severity order?
• Why do you want to leverage 3rd-party external monitoring system?

Spring Boot Testing
• When do you want to use @SpringBootTest annotation?
• What does @SpringBootTest auto-configure?
• What dependencies does spring-boot-starter-test brings to the classpath?
• How do you perform integration testing with @SpringBootTest for a web application?
• When do you want to use @WebMvcTest? What does it auto-configure?
• What are the differences between @MockBean and @Mock?
• When do you want @DataJpaTest for? What does it auto-configure?
```
-  Automatic configuration —Spring Boot can automatically provide configuration for application functionality common to many Spring applications.
- Starter dependencies — project dependency/version management: special Maven (and Gradle) dependencies that take advantage of transitive dependency resolution to **aggregate** commonly used libraries under a handful of feature-defined dependencies. **no incompatibilities**
- The command-line interface — This optional feature of Spring Boot lets you write complete applications with just application code, but no need for a traditional project build.```$ spring run HelloController.groovy```
- The Actuator—inspect the internals of your application at runtime via web endpoints or via a SSH shell interface
```
■ What beans have been configured in the Spring application context
■ What decisions were made by Spring Boot’s auto-configuration
■ What environment variables, system properties, configuration properties, and
command-line arguments are available to your application
■ The current state of the threads in and supporting your application
■ A trace of recent HTTP requests handled by your application
■ Various metrics pertaining to memory usage, garbage collection, web requests,
and data source usage
```
- @SpringBootApplication class: 
  - configuration: @Configuration + @ComponentScan + @EnableAutoConfiguration
  - bootstrapping: main() => SpringApplication.run() <= ```$ java -jar xxxx.jar```

- @SpringApplicationConfiguration to load the Spring application context from the SpringBootApplication class.
- Spring Boot Maven plugin 
  - provides a spring-boot:run goal
  - to package the project as an executable uber-JAR.
- starter:
  - facet-based dependencies
  - no need starter version: The versions of the starter dependencies themselves are determined by the version of Spring Boot you’re using ```mvn dependency:tree```
  - override dep: exclusions or express dep directly(Maven always favors the closest dependency)

- auto-config
  - a runtime (more accurately, application startup-time) process => decision on classpath
  - conditional config support: implement the Condition interface and override its matches() & @Conditional (only creat eh bean when meet)
```
By taking advantage of Spring Boot starter dependencies and auto-configuration, you
can more quickly and easily develop Spring applications. Starter dependencies help you
focus on the type of functionality your application needs rather than on the specific
libraries and versions that provide that functionality. Meanwhile, auto-configuration
frees you from the boilerplate configuration that is common among Spring applications
without Spring Boot.
Although auto-configuration is a convenient way to work with Spring, it also represents
an opinionated approach to Spring development.
```
  
```java
import org.springframework.context.annotation.Condition;
import org.springframework.context.annotation.ConditionContext;
import org.springframework.core.type.AnnotatedTypeMetadata;
public class JdbcTemplateCondition implements Condition {
    @Override
    public boolean matches(ConditionContext context,
        AnnotatedTypeMetadata metadata) {
        try {
            context.getClassLoader().loadClass(
                "org.springframework.jdbc.core.JdbcTemplate");
            return true;
        } catch (Exception e) {
            return false;
        }
    }
}

@Conditional(JdbcTemplateCondition.class)
public MyService myService() {
...
}
```

- Spring security: default password is randomly generated and written to the logs each time the application is run
- property sources: order of precedence: CLI > JVM > OS > prop outside app > prop inside app > @PropertySource > default
```
1 Command-line arguments
2 JNDI attributes from java:comp/env
3 JVM system properties
4 Operating system environment variables
5 Randomly generated values for properties prefixed with random.* (referenced
when setting other properties, such as `${random.long})
6 An application.properties or application.yml file outside of the application
7 An application.properties or application.yml file packaged inside of the
application
8 Property sources specified by @PropertySource
9 Default properties
```

- application.properties order of precedence
```
properties in application.yml will override those in application.properties
1 Externally, in a /config subdirectory of the directory from which the application
is run
2 Externally, in the directory from which the application is run
3 Internally, in a package named “config”
4 Internally, at the root of the classpath
```
```
spring.thymeleaf.cache=false
server.port=8000

server.ssl.key-store: file:///path/to/mykeys.jks
server.ssl.key-store-password: letmein
server.ssl.key-password: letmein


logging.path=/var/logs/
logging.file=BookWorm.log
logging.level.root=WARN
logging.level.root.org.springframework.security=DEBUG
```
- log4j
```
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter</artifactId>
<exclusions>
<exclusion>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-logging</artifactId>
</exclusion>
</exclusions>
</dependency>

<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-log4j</artifactId>
</dependency>
```

- @Configuration-Properties. This specifies that this bean should have its properties injected (via setter methods) with values from configuration properties.
  - Spring Boot auto @EnableConfigurationProperties
  - Spring Boot’s property resolver treats camel-cased properties as interchangeable with similarly named properties with hyphens or underscores.
    - amazon.associateId is equivalent to both amazon.associate_id and amazon.associate-id.
  - better to annotate a separate bean with @ConfigurationProperties and let that bean collect all of the configuration properties.

- Profiles are a type of conditional configuration where different beans or configuration classes are used or ignored based on what profiles are active at runtime.
  - @Profile("production")
  - spring.profiles.active=production
  - application-{profile}.properties

- The default error handler that’s auto-configured by Spring Boot looks for a view whose name is “error”. If it can’t find one, it uses its default whitelabel error view
  - Any bean that implements Spring’s interface and has a bean of “error” View ID (resolved by Spring’s BeanNameViewResolver)
  - A Thymeleaf template named “error.html” if Thymeleaf is configured.
  - default available error attributes
```
■ timestamp—The time that the error occurred
■ status—The HTTP status code
■ error—The error reason
■ exception—The class name of the exception
■ message—The exception message (if the error was caused by an exception)
■ errors—Any errors from a BindingResult exception (if the error was caused
by an exception)
■ trace—The exception stack trace (if the error was caused by an exception)
■ path—The URL path requested when the error occurred
```

- Boot Testing
  - Unit test: no need Spring as Loose coupling and interface-driven design, which Spring encourages, makes it really easy to write unit tests.
  - context testing
    - @RunWith is given SpringJUnit4ClassRunner.class to enable Spring integration testing.
    - @ContextConfiguration specifies how to load the application context, but not loading of external properties and Spring Boot logging.
    - @SpringApplicationConfiguration loads the Spring application context using SpringApplication the same way and with the same treatment it would get if it was being loaded in a production application.

  - Web test
    - Spring Mock MVC
    - Web Intg test: embeded sevlet container(tomcat)
    
    
    - MockMvcBuilders to set up MockMvc
      - standaloneSetup(): single contoller with unit test - manually instantiate and inject the controllers
      - webAppContextSetup(): lets Spring load your controllers as well as their dependencies for a full-blown integration test- works from an instance of WebApplicationContext
        - @WebAppConfiguration enable web context testing - declares that the application context created SpringJUnit4ClassRunner should be a WebApplicationContext
	- Inject WebApplicationContext
  - secured 
    - dep: spring-security-test
    - ```.apply(springSecurity())``` for MockMvc so Spring Security will be in play on all requests performed through MockMvc.
    - preform an authenticated req:
      - @WithMockUser Loads the security context with a using the given UserDetails username, password, and authorization
      - @WithUserDetails Loads the security context by looking up a UserDetails object for the given username
        - uses the configured UserDetailsService to load the object UserDetails
	- in model for req
  - running app
    - @WebIntegrationTest 1 create an application context, 2 also to start an embedded servlet container.
  
  - webpage
    - Selenium: fires up a web browser and executes your test within the context of the browser.
    
```java
@WebIntegrationTest(value={"server.port=0"})
@WebIntegrationTest(randomPort=true)

@Value("${local.server.port}")
private int port;
```
```
For unit tests, which focus on a single component or a method of a component,
Spring isn’t really necessary. The benefits and techniques promoted by Spring—loose
coupling, dependency injection, and interface-driven design—make writing unit tests
easy. But Spring doesn’t need to be directly involved in unit tests.
Integration-testing multiple components, however, begs for help from Spring. In
fact, if Spring is responsible for wiring those components up at runtime, then Spring
should also be responsible for wiring them up in integration tests.
The Spring Framework provides integration-testing support in the form of a JUnit
class runner that loads a Spring application context and enables beans from the context
to be injected into a test. Spring Boot builds upon Spring integration-testing support
with a configuration loader that loads the application context in the same way as Spring
Boot itself, including support for externalized properties and Spring Boot logging.
Spring Boot also enables in-container testing of web applications, making it possible
to fire up your application to be served by the same container that it will be served
by when running in production. This gives your tests the closest thing to a real-world
environment for verifying the behavior of the application.
```
- Spring Actuator
  - view internal
  - REST endpoint, remote shell, JMX
  - to enable: 
    - ```spring-boot-starter-actuator```
    - ```management.endpoints.web.exposure.include=*```
  - configuration endpoints
    - actuator/beans: visualize the relationships of the beans: bean name, resource/class, dependencies, scope, java type
    - actuator/mappings: @RequestMapping
  - metrics endpoints
    - heapdump
    - POST shutdown
  - miscellaneous endpoints
  - customize
```
■ Renaming endpoints: changing end point id
■ Enabling and disabling endpoints: endpoints._endpoint-id.enabled to false
■ Defining custom metrics and gauges: injects CounterService and GaugeService; implement the interface PublicMetrics
■ Creating a custom repository for storing trace data
■ Plugging in custom health indicators: implements HealthIndicator
```
- war
```
<packaging>war</packaging>

public class ReadingListServletInitializer
extends SpringBootServletInitializer {
    @Override
    protected SpringApplicationBuilder configure(
        SpringApplicationBuilder builder) {
        return builder.sources(Application.class);  // @SpringBootApplication
    }
}
```
- database migration library: work from a set of database scripts and keep careful track of the ones that have already been applied so that they won’t be applied more than once.
  - Flyway: version, 
  - Liquibase supports several formats for writing migration scripts that are agnostic to the underlying platform.

## Spring Security

• What are authentication and authorization? Which must come first?
- [x] Is security a cross cutting concern? How is it implemented internally?
- Spring Security, a security framework implemented with Spring AOP and servlet filters.
  - provides declarative security
  - web request level: servlet filter
  - method invocation level: AOP
- [x] What is the delegating filter proxy?
- DelegatingFilterProxy intercept requests coming into the application and delegate them to a bean whose ID is springSecurityFilterChain.
- [x] What is the security filter chain?

- springSecurityFilterChain bean is a FilterChainProxy. It’s a single filter that chains together one or more additional filters.

• What is a security context?

- [x] What does the ** pattern in an antMatcher or mvcMatcher do?
? matches one character
* matches zero or more characters
** matches zero or more 'directories' in a path

- [x] Why is the usage of mvcMatcher recommended over antMatcher?
mvcMatcher uses the same rules that Spring MVC uses for matching (when using @RequestMapping annotation).
antMatchers("/secured") matches only the exact /secured URL
mvcMatchers("/secured") matches /secured as well as /secured/, /secured.html, /secured.xyz

• Does Spring Security support password hashing? What is salting?

• Why do you need method security? What type of object is typically secured at the method level (think of its purpose not its Java type).
data

• What do @PreAuthorized and @RolesAllowed do? What is the difference between them?

• How are these annotations implemented?
AOP pointcut

• In which security annotation are you allowed to use SpEL?
4 


- 1. adding spring-boot-starter-security
  - All HTTP request paths require authentication.
  - No specific roles or authorities are required.
  - There’s no login page.
  - Authentication is prompted with HTTP basic authentication.
  - There’s only one user; the username is user, and passowrd in log

- 2. @EnableWebSecurity annotation enables web security. It is useless on its own, however. 
  - Spring Security must be configured in a bean that implements WebSecurityConfigurer or (for convenience) extends WebSecurityConfigurerAdapter.
  - @EnableWebMvcSecurity annotation configures a Spring MVC argument resolver 
    - so that handler methods can receive the authenticated user’s principal (or username) via @AuthenticationPrincipal-annotated parameters. 
    - It also configures a bean that automatically adds a hidden cross-site request forgery (CSRF) token field on forms using Spring’s form-binding tag library.

- 3. configure(AuthenticationManagerBuilder)
- user stores
  - An in-memory user store
  - A JDBC-based user store
  - An LDAP-backed user store
    - The default strategy for authenticating against LDAP is to perform a bind operation, authenticating the user directly to the LDAP server
  - A custom user details service
    - UserDetails.loadByUsername() it must never return null. throw excp
- 
- passwordEncoder(): password in the database is never decoded. Instead, the password that the user enters
at login is encoded using the same algorithm,

- Cross-site request forgery
  - Spring Security has built-in CSRF protection, and it’s enabled by default

- 4. configure(HttpSecurity)
  - formLogin() to enable default simple login page.
  - and() to chain together different configuration instructions.
  - rememberMe() default 2 weeks
  - httpBasic(): another app: HTTP Basic authentication is one way to authenticate a user to an application HTTP directly in the request itself.
  - logout(): logout capability is already enabled by your configuration without you having to do anything else.


- 5. secured method
  - Spring Security provides three different kinds of security annotations:
    - Spring Security’s own @Secured
      - @EnableGlobalMethodSecurity(securedEnabled=true) for @Secured
      - AuthenticationException or Access-DeniedException
    - JSR-250’s @RolesAllowed
      - @EnableGlobalMethodSecurity(jsr250Enabled=true)
      - any methods annotated with @RolesAllowed will be wrapped with Spring Security’s aspects
    - Expression-driven annotations, with @PreAuthorize, @PostAuthorize, @PreFilter, and @PostFilter
      - prePostEnabled = true
      - @PostAuthorize: executed first and intercepted afterward, be care of side effect
      - @PostFilter: filter data collection
      - @PreFilter uses SpEL to filter a collection to only the elements that satisfy the SpEL expression.
    - implements PermissionEvaluator.hasPermission() to replace SpEL
  - enabling annotation-based method security: @EnableGlobalMethodSecurity
    - class extends GlobalMethodSecurityConfiguration can config oauth also
- 6. get user info
  - Inject a Principal object into the controller method
  - Inject an Authentication object into the controller method ```User user = (User) authentication.getPrincipal();```
  - Use SecurityContextHolder to get at the security context (use in lower levels of the code.)
  - Use an @AuthenticationPrincipal annotated method```@AuthenticationPrincipal User user)```

- security 2 cautions
  - By default, Spring AOP proxying is used to apply method security – if a secured method A is called by another method within the same class, security in A is ignored altogether. This means method A will execute without any security checking. The same applies to private methods
  - Spring SecurityContext is thread-bound – by default, the security context isn't propagated to child-threads.


## AOP 
Aspect oriented programming

- [x] What is the concept of AOP? Which problem does it solve? What is a cross cutting
concern?
- a crosscutting concern: functionality that affects multiple points of an application.
- cross-cutting concerns are conceptually separate from (but often embedded directly within) the application’s business logic.
- With AOP, you still define the common functionality in one place, but you can declaratively define how and where this functionality is applied without having to modify the class to which you’re applying the new feature. Crosscutting concerns can now be modularized into special classes called aspects.

- [x] Name three typical cross cutting concerns.
- logging, transactions, security, and caching

- [x] What two problems arise if you don't solve a cross cutting concern via AOP?
- A common object-oriented technique for reusing common functionality is to apply inheritance or delegation. 
- Inheritance can lead to a **brittle object hierarchy** if the same base class is used throughout an application
- Delegation can be **cumbersome** because complicated calls to the delegate object may be required.

- [x] What is a pointcut, a join point, an advice, an aspect, weaving?
- Advice: the **task** of an aspect
  - Advice defines both the what and the when of an aspect
  - Spring: Advice take place when: Before, After, After-returning, After-throwing, Around
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

- [x] How does Spring solve (implement) a cross cutting concern?
- the ability to create pointcuts that define the join points at which aspects should be woven is what makes it an AOP framework.
- 4 Style:
  - Classic Spring proxy-based AOP
  - Pure-POJO aspects
  - @AspectJ annotation-driven aspects
  - Injected AspectJ aspects (available in all versions of Spring)

- **method join points only**: Spring AOP is built around dynamic proxies. Consequently, Spring’s AOP support is limited to **method interception**.
- **Runtime proxy creation**: Spring doesn’t create a proxied object until that proxied bean is needed by the application.

• Which are the limitations of the two proxy-types?

• What visibility must Spring bean methods have to be proxied using Spring AOP?

- [x] How many advice types does Spring support. Can you name each one? What are they used for? Which two advices can you use if you would like to try and catch exceptions?
- @After; return or throw
- @AfterReturning, @AfterThrowing, @Before
- @Around: ProceedingJoinPoint.procceed()
  - pass control to advised method
  - can repeat calls

- [x] What do you have to do to enable the detection of the @Aspect annotation? What does @EnableAspectJAutoProxy do?
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


- [x] If shown pointcut expressions, would you understand them? For example, in the course we matched getter methods on Spring Beans, what would be the correct pointcut expression to match both getter and setter methods?
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

- [x] What is the JoinPoint argument used for?
- get JoinPoint method info like joinPoint.getSignature(), Object[] joinPoint.getArgs()

- [x] What is a ProceedingJoinPoint? When is it used?
- In @Around, invoke the advised method from within your advice using proceed()

```
AOP is a powerful complement to object-oriented programming. With aspects, you can
group application behavior that was once spread throughout your applications into
reusable modules. You can then declare exactly where and how this behavior is applied.
This reduces code duplication and lets your classes focus on their main functionality.
```


## Transaction

- [x] What is a transaction? What is the difference between a local and a global transaction?
- Global transactions enable you to work with multiple transactional resources, typically relational databases and message queues. The application server manages global transactions through the JTA,
- Local transactions are resource-specific, such as a transaction associated with a JDBC connection
  - it cannot help ensure correctness across multiple resources.

• Is a transaction a cross cutting concern? How is it implemented by Spring?


• How are you going to define a transaction in Spring?


• What does @Transactional do? What is the PlatformTransactionManager?
- PlatformTransactionManager defines A transaction strategy
  - TransactionDefinition
    - Isolation
    - Propagation: scope
    - Timeout
    - Read-only
  - PlatformTransactionManager implementations normally require knowledge of the environment in which they work: JDBC, JTA, Hibernate, and so on.


• Is the JDBC template able to participate in an existing transaction?

• What is a transaction isolation level? How many do we have and how are they ordered?

• What is @EnableTransactionManagement for?

• What does transaction propagation mean?

• What happens if one @Transactional annotated method is calling another @Transactional annotated method on the same object instance?

• Where can the @Transactional annotation be used? What is a typical usage if you put it
at class level?

• What does declarative transaction management mean?

• What is the default rollback policy? How can you override it?

• What is the default rollback policy in a JUnit test, when you use the @RunWith(SpringJUnit4ClassRunner.class) in JUnit 4 or @ExtendWith(SpringExtension.class) in JUnit 5, and annotate your @Test annotated method with @Transactional?

• Why is the term "unit of work" so important and why does JDBC AutoCommit violate this pattern?

## Testing

• Do you use Spring in a unit test?
• What type of tests typically use Spring?
• How can you create a shared application context in a JUnit integration test?
• When and where do you use @Transactional in testing?
• How are mock frameworks such as Mockito or EasyMock used?
• How is @ContextConfiguration used?
• How does Spring Boot simplify writing tests?
• What does @SpringBootTest do? How does it interact with @SpringBootApplication
and @SpringBootConfiguration?

Spring Boot Testing
• When do you want to use @SpringBootTest annotation?
• What does @SpringBootTest auto-configure?
• What dependencies does spring-boot-starter-test brings to the classpath?
• How do you perform integration testing with @SpringBootTest for a web application?
• When do you want to use @WebMvcTest? What does it auto-configure?
• What are the differences between @MockBean and @Mock?
• When do you want @DataJpaTest for? What does it auto-configure?




