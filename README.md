# springBootBookNotes

There are seven different levels of logging: TRACE, DEBUG, INFO, WARN, ERROR, FATAL, and OFF. You can configure the level of logging in your Spring Boot application. properties file.
If we set the logging level to DEBUG, we can see log messages from levels that are log level DEBUG or higher (that is DEBUG, INFO, WARN, and ERROR).
DI is a software development technique where we can create objects that depend on other objects. DI helps with interaction between classes but at the same time keeps the classes independent.
There are three types of classes in DI - Service, Client, Injector.
A service is a class that can be used (this is the dependency).
The client is a class that uses the dependency. 
The injector passes the dependency (the service) to the dependent class (the client).
We use @AutoWired in springboot for injection.

An entity class is a simple Java class that is annotated with JPA's @Entity annotation.
JPA creates a database table called by the name of the class when the application is initialized. If you want to use some other name for the database table, you can use the @Table annotation in your entity class.

With the @Column annotation, you can define the column's length and whether the column is nullable. 
ex @Column(name="explanation", nullable=false, length=512)

The CrudRepository interface provides multiple CRUD methods that we can now start to use.
If the method returns only one item, Optional<T> is returned instead of T. The
Optional class was introduced in Java 8 SE and is a type of single-value container that either contains a value or doesn't. If there is value, the isPresent() method returns true; otherwise, it returns false. If there is a value, you can get it by using the get() method.

You can define your queries in the Spring Data repositories. The query must start with a prefix; for example, findBy. After the prefix, you must define the entity class fields that are used in the query. 
There can be multiple fields after the By keyword, concatenated with the And and or keywords.
ex List<Car> findByBrandAndModel(String brand, String model);
Queries can be sorted by using the OrderBy keyword in the query method.
ex List<Car> findByBrandOrderByYearAsc(String brand);
With the @Query annotation, you can use more advanced expressions, such as like. The following example shows the usage of the like query in CrudRepository:
@Query(“select c from Car c where c.brand like %?1")
List<Car> findByBrandEndsWith(String brand);

Spring Data JPA also provides PagingAndSortingRepository, which extends CrudRepository. This offers methods to fetch entities using pagination and sorting. This is a good option if you are dealing with larger amounts of data because you don't have to return everything from a large result set. You can also sort
your data into some meaningful order. PagingAndSortingRepository can be created in a similar way to how we created CrudRepository:
you now have the two new additional methods that the repository provides. 
iterable<T> findAll (Sort sort) - returns all entities sorted by the given options
&
Page<T> findAll(Pageable pageable) - returns all entities according to the given paging options

a new table called owner that has a one-to-many relationship with the car table. In this case, a one-to-many relationship means that the owner can own multiple cars, but a car can only have one owner. 

The one-to-many relationship can be added by using the @ManyToOne and @OneToMany annotations. In the car entity class, which contains a foreign key,
you must define the relationship with the @ManyToOne annotation. You should also add the getter and setter for the owner field. It is recommended that you use FetchType.LAZY for all associations.

the lazy strategy means that when the owner is fetched from the database, all the cars associated with the owner will be fetched when needed. Eager means that the cars will be fetched immediately by the owner.

The @OneToMany annotation has two attributes that we are using. The cascade attribute defines how cascading affects the entities in the case of deletions or updates. The ALL attribute setting means that all operations are cascaded. For example, if the owner is deleted, the cars that are linked to that owner are deleted as well. The mappedBy="owner" attribute setting tells us that the Car class has the owner field, which is the foreign key for this relationship.

If you want to create a many-to-many relationship instead, which means, in practice, that an owner can have multiple cars and a car can have multiple owners, you should use the @ManyToMany annotation. 
  
Next, you will learn how to change the relationship to many-to-many. In a many-to-many
relationship, it is recommended that you use Set instead of List with Hibernate:
  ex Car Class @ManyToMany(mappedBy="cars")
private Set<Owner> owners = new HashSet<Owner>();
  
  Owner Class @ManyToMany(cascade=CascadeType.PERSIST)
@JoinTable(name="car_owner",
joinColumns = { @JoinColumn(name="ownerid") },
inverseJoinColumns = { @JoinColumn(name="id") })
private Set<Car> cars = new HashSet<Car>();
  
  Now, if you run the application, there will be a new join table called car_owner that is created between the car and owner tables. The join table is a special kind of table that manages the many-to-many relationship between two tables. The join table is defined by using the @JoinTable annotation. 
  
  
  
Web services are applications that communicate over the internet using the HTTP protocol. There are many different types of web service architectures, but the principal idea across all designs is the same. In this book, we are creating a RESTful web service from what is nowadays a really popular design.

Representational State Transfer (REST) is an architectural style for creating web services. REST is not a standard, but it defines a set of constraints defined by Roy Fielding.
  The six constraints are,
Stateless: The server doesn't hold any information about the client state.  
Client: The client and server act independently. The server does not send any
information without a request from the client.
Cacheable: Many clients often request the same resources; therefore, it is useful to cache responses in order to improve performance.
Uniform interface: Requests from different clients look the same. Clients may include, for example, a browser, a Java application, and a mobile application.  
            The uniform interface is an important constraint, and it means that every REST architecture should have the following elements:
           - Identification of resources: There are resources with their unique identifiers, for example, URIs in web-based REST services. REST resources          
            should expose easily understood directory structure URIs. Therefore, a good resource naming strategy is very important.
           - Resource manipulation through representation: When making a request to
            a resource, the server responds with a representation of the resource. Typically, the format of the representation is JSON or XML.
           - Self-descriptive messages: Messages should have sufficient information that the server knows how to process them.
           - Hypermedia as the Engine of Application State (HATEOAS): Responses can contain links to other areas of service.
Layered system: REST allows us to use a layered system architecture.
Code on demand: This is an optional constraint.
  
  
  
  You can define the endpoint of service in your application.properties file as follows:
  spring.data.rest.basePath=/api
Now, you can access the RESTful web service from the localhost:8080/api endpoint. By calling the root endpoint of the service, it returns the resources that are available. Spring Data REST returns JSON data in the Hypertext Application Language (HAL) format. The HAL format provides a set of conventions for expressing hyperlinks in JSON and it makes your RESTful web service easier to use for frontend developers:
  
  In the previous chapter, we created queries to our repository. These queries
can also be included in our service. To include queries, you have to add the @RepositoryRestResource annotation to the repository class. Query parameters are annotated with the @Param annotation.
  ex @RepositoryRestResource
public interface CarRepository extends CrudRepository<Car,
Long> {
// Fetch cars by brand
List<Car> findByBrand(@Param("brand") String brand);
  
  
Spring Security provides security services for Java-based web applications  
By default, Spring Security enables the following features.
    An AuthenticationManager bean with an in-memory single user. The username is user, and the password is printed to the console output.
    Ignored paths for common static resource locations, such as /css and /images.HyperText Transfer Protocol (HTTP) basic security for all other endpoints.
    Security events published to Spring's ApplicationEventPublisher interface.
    ommon low-level features are on by default (HTTP Strict Transport Security (HSTS), cross-site scripting (XSS), cross-site request forgery (CSRF), and                 so forth).
    Default autogenerated login page.
  
You can include Spring Security in your application by adding the Spring security dependency to the pom.xml file.  
  To configure how Spring Security behaves, we have to add a new configuration class
that extends WebSecurityConfigurerAdapter. Create a new class called SecurityConfig in your application root package (com.packt.cardatabase). The following source code shows the structure of the security configuration class. The @Configuration and @EnableWebSecurity annotations switch off the default web security configuration, and we can define our own configuration in this class. Inside the configure(HttpSecurity http) method, we can define which endpoints in our application are secure and which are not. We don't actually need this method yet because we can use the default settings where all the endpoints are secured.
  Pg.94
We can also add in-memory users to our application by adding the userDetailsService() method to our SecurityConfig class. The following source code of the method will create an in-memory user with a username of user and a password of password.

To save users to the database, you have to create a user entity class and repository. Passwords shouldn't be saved to the database in plaintext format. Spring Security provides multiple hashing algorithms, such as bcrypt, that you can use to hash passwords. 
Annotate the User class with the @Entity annotation. Add the identifier (ID), username, password, and role class fields. Finally, add the constructors, getters, and setters. We will set all the fields to be nullable, and specify that the username must be unique by using the @Column annotation. Refer to the following User. java source code of the fields and constructors.  
Next, we will create a class that implements the UserDetailsService interface that's provided by Spring Security. Spring Security uses this for user authentication and authorization.

The findByUsername method that we implemented earlier returns Optional, therefore we can use
the isPresent() method to check if user exists. If user doesn't exist,
we throw a UsernameNotFoundException exception. The loadByUsername method returns the UserDetails object, which is required for authentication.
We are using the Spring Security UserBuilder class to build the user for the authentication.
  
  In our security configuration class, we have to define that Spring Security
should use users from the database instead of in-memory users. Delete the userDetailsService() method from the SecurityConfig class to disable in-memory users. Add a new configureGlobal method to enable users from the database. We should never save the password as plain text to the database. Therefore, we will define a password hashing algorithm in the configureGlobal method. In this example, we are using the bcrypt algorithm. This can be
easily implemented with the Spring Security BCryptPasswordEncoder
class that encodes a hashed password in the authentication process. Here is the SecurityConfig.java source code. Now, the password must be hashed using BCrypt before it's saved to the database.
  
Finally, we can save a couple of test users to the database using the CommandLineRunner interface. Open the CardatabaseApplication. java file and add the following code at the beginning of the class to inject UserRepository into the main class.
  
 save two users to the database with bcrypt hashed passwords. In the following code snippet, we save two users with bcrypt hashed passwords. You can find bcrypt calculators or generators from the internet if you type plain text password and get hashed password, and you should use these here.

  
Deploying the backend
If you are going to use your own server, the easiest way to deploy the Spring Boot application is to use an executable Java ARchive (JAR) file. If you use Maven, an executable JAR file can be created using the Spring Boot Maven plugin and adding the following lines of code to your pom.xml file.
  plugin>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-maven-plugin</artifactId>
<configuration>
<executable>true</executable>
</configuration>
</plugin>
  
  Next, we have to build our project using the mvn clean install command.
  
