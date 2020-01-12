# Spring 5
## Intro with Spring Boot
- Spring initializr
```bash
./mvnw spring-boot:run

spring.h2.console.enabled=true
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
```
- Equilty for JPA entity object
  - business keys
  - id
