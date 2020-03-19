# Spring 5
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
