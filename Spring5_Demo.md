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
```








