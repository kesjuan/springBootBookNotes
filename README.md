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


