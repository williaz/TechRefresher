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

