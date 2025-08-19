# Spring Boot

## Basics of Spring Boot

### How does Spring Boot work internally?
Spring Boot works on the principle of Convention over Configuration and provides automatic setup based on what’s available in the classpath. Internally, it works like this:
1. Application Entry Point: The class annotated with `@SpringBootApplication` is the starting point. This annotation combines `@Configuration`, `@EnableAutoConfiguration`, and `@ComponentScan`.
2. Auto-Configuration: Using `@EnableAutoConfiguration`, Spring Boot looks at the dependencies in the classpath and automatically configures beans. For example, if `spring-boot-starter-web` is present, it configures DispatcherServlet, Jackson, and an embedded Tomcat server.
3. Component Scanning: Through `@ComponentScan`, it detects classes annotated with `@Component`, `@Service`, `@Repository`, or `@Controller` and registers them as beans in the application context.
4. SpringApplication.run(): This method bootstraps the application, creates and refreshes the ApplicationContext, registers beans, applies initializers, and starts the embedded server.
5. Embedded Server & Externalized Configurations: It starts an embedded web server (like Tomcat/Jetty) and reads external configurations from `application.properties` or a`pplication.yml`, environment variables, and command-line arguments.

### How does `@ComponentScan` work in Spring Boot?
@ComponentScan tells Spring where to look for Spring-managed components (beans).
If you use @SpringBootApplication, it already includes @ComponentScan. By default, it scans the package of the main class (the one with @SpringBootApplication) and all its sub-packages. Example: If the main class is in `com.example.demo`, Spring will scan `com.example.demo.*` by default.

During the startup, the Spring container scans the specified packages for classes annotated with stereotypes like:
`@Component`, 
`@Service`, 
`@Repository`, 
`@Controller` / `@RestController`

These classes are then registered as beans in the ApplicationContext.

You can customize the scan path using:
```java
@ComponentScan(basePackages = {"com.example.service", "com.example.repository"})
```

You can also use filters (includeFilters, excludeFilters) to control which classes should be included/excluded from scanning.

---

## Dependency Injection (DI)

### What is Dependency Injection, and why is it used in Spring?
Dependency Injection (DI) is a design pattern where an object’s dependencies are provided to it by an external entity (like the Spring IoC container), instead of the object creating them itself. In simple words: Instead of a class doing new to create its dependencies, Spring injects them.

DI is used in Spring because:
1. Loose Coupling - Classes depend on abstractions, not concrete implementations. Easier to swap implementations without changing code.
2. Better Testability - You can inject mock dependencies during unit testing.
3. Reusability & Maintainability - Code is cleaner and easier to maintain because wiring is handled externally.
4. Centralized COnfiguration - Dependencies are managed by the Spring IoC container via annotations or XML, not hardcoded in classes.

```java
// Without DI
public class OrderService {
    private PaymentService paymentService = new PaymentService(); // Hardcoded
}

// With DI
public class OrderService {
    private final PaymentService paymentService;

    @Autowired
    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
// Spring provides the PaymentService at runtime.
```

### What are the different types of Dependency Injection in Spring?
1. Constructor Injection
    * Dependencies are provided through the class constructor.
    * Ensures immutability because dependencies are set at object creation time.
    * Preferred approach in modern Spring (recommended by Spring team).

```java
@Component
public class OrderService {
    private final PaymentService paymentService;

    @Autowired
    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

2. Setter Injection
    * Dependencies are provided through setter methods.
    * Allows changing dependencies even after object creation.
    * Useful when a dependency is optional.

```java
@Component
public class OrderService {
    private PaymentService paymentService;

    @Autowired
    public void setPaymentService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}

```

3. Field Injection (not recommended)
    * Dependencies are injected directly into fields using @Autowired.
    * Less boilerplate, but not recommended for production code since it makes testing harder and violates immutability.

```java
@Component
public class OrderService {
    @Autowired
    private PaymentService paymentService;
}
```

---

## POJO vs Bean

### What is the difference between a POJO and a Java Bean?
POJO:
* A simple Java object without any special restrictions or requirements.
* Does not need to follow a strict convention.
* Can have any kind of fields (public, private, default).
* No specific rules for constructors, getters, setters, or serialization.

```java
public class Student {
    public String name;
    public int age;
}
```

Java Bean:
* All fields must be private.
* Must provide public getters and setters for accessing properties.
* Must have a public no-argument constructor.
* Should be serializable (implements Serializable interface).

```java
public class StudentBean implements Serializable {
    private String name;
    private int age;

    public StudentBean() {} // No-arg constructor

    public String getName() { return name; }
    public void setName(String name) { this.name = name; }

    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}
```

---

## Autowiring

### What is autowiring in Spring?
Autowiring in Spring is a feature of the Spring IoC container that allows Spring to automatically resolve and inject the required dependencies into a bean without explicit configuration. Instead of manually specifying dependencies in `applicationContext.xml` or using `@Bean` methods, Spring uses type, name, or annotations to find and inject the correct bean.

When Spring creates a bean, it looks at the fields/constructors marked with @Autowired (or other injection annotations) and automatically finds a matching bean from the container.

### What are the types of autowiring available in Spring?
Types of Autowiring in Spring:
1. ByType (@Autowired) - Injects dependency by matching the bean type
```java
@Component
public class OrderService {

    @Autowired
    private PaymentService paymentService; // injected by type
}
```
2. ByName (@Autowired + @Qualifier) - Injects dependency by bean name if multiple beans of same type exist.
```java
@Component("creditCardPayment")
public class CreditCardPaymentService implements PaymentService {}

@Component("upiPayment")
public class UpiPaymentService implements PaymentService {}

@Component
public class OrderService {

    @Autowired
    @Qualifier("upiPayment")  // injected by name
    private PaymentService paymentService;
}
```
3. Constructor Injection – Spring injects dependencies through the constructor.
```java
@Component
public class OrderService {

    private final PaymentService paymentService;

    @Autowired
    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService; // injected via constructor
    }
}
```
4. Field Injection – Directly injects into a field (not recommended for testing/immutability).
```java
@Component
public class OrderService {

    @Autowired
    private PaymentService paymentService; // injected into field
}
```
5. Setter Injection – Spring injects via setter methods.
```java
@Component
public class OrderService {

    private PaymentService paymentService;

    @Autowired
    public void setPaymentService(PaymentService paymentService) {
        this.paymentService = paymentService; // injected via setter
    }
}
```

### What is the role of the `@Qualifier` annotation in autowiring?
The @Qualifier annotation in Spring is used along with @Autowired to resolve ambiguity when multiple beans of the same type exist in the Spring container.

Without @Qualifier, Spring would not know which bean to inject and would throw an exception (NoUniqueBeanDefinitionException).

### How does `@Primary` help in resolving bean ambiguity?
When multiple beans of the same type exist in the Spring container, Spring gets confused about which bean to inject (leading to NoUniqueBeanDefinitionException). @Primary tells Spring: “If multiple beans are available, prefer this one by default.” It acts as the default bean candidate for autowiring when no @Qualifier is specified.

```java
@Component
@Primary
public class UpiPaymentService implements PaymentService {}

@Component
public class CreditCardPaymentService implements PaymentService {}

@Component
public class OrderService {
    @Autowired
    private PaymentService paymentService;  // UpiPaymentService is injected by default
}
```

---

## Spring Core Annotations

### What is the role of `@Bean` annotation in Spring?
The @Bean annotation in Spring is used to explicitly declare a bean inside a @Configuration class. It is often used when you need to create beans that are not automatically detected by component scanning (e.g., third-party libraries, complex object creation).

```java
@Configuration
public class AppConfig {

    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();   // This bean will be managed by Spring
    }
}
```
Using the above example, restTemplate() method creates a RestTemplate object. Spring registers it as a singleton bean in the ApplicationContext. And now this can be ijected anywhere using @Autowired.

Key Points:
1. @Bean methods are inside classes annotated with @Configuration (or @Component).Preferred are the classes annotated with @Configuration.
2. By default, the bean name is the method name (restTemplate in the example). You can override it.
```java
@Bean("myRestTemplate")
public RestTemplate restTemplate() { return new RestTemplate(); }
```
3. By default the scope is singleton. We can specify others using @Scope("prototype"), etc.


### How does `@Lazy` annotation improve application startup time?
By default, in Spring, singleton beans are created eagerly at application startup — meaning Spring initializes all beans when the ApplicationContext is loaded. This can slow down startup if there are many heavy beans (e.g., beans making DB connections, network calls, or large object initialization). The @Lazy annotation changes this behavior by telling Spring: Do not create this bean at startup — only create it when it is actually needed (first requested). This improves application startup time because heavy or rarely used beans are not initialized immediately.

```java
@Component
@Lazy
public class HeavyService {
    public HeavyService() {
        System.out.println("HeavyService Initialized...");
    }
}

@Component
public class OrderService {
    @Autowired
    private HeavyService heavyService;  // Initialized only when accessed
}

```

@Lazy can be applied at:
1. Class level → Marks the entire bean as lazy.
2. Injection point → Lazy-load a dependency only when required.

### What’s the trade-off of using @Lazy?
Pros:
* Faster application startup time because beans are not eagerly initialized.
* Useful for rarely used or heavy initialization beans (e.g., beans connecting to external services)

Cons:
* First-time delay → The bean is created only when accessed for the first time, so the first request may be slower.

* Hidden latency → If a lazy bean is required in a critical path, it could introduce unexpected performance bottlenecks.

* Error detection delayed → If bean creation fails (e.g., misconfiguration, missing dependency), the failure will happen later at runtime instead of during startup, making issues harder to catch early.

* Not suitable for always-needed beans → For frequently used beans (like DataSource, EntityManager), eager loading is better so the application is ready to serve requests immediately.

### What is the use of `@Value` annotation in Spring?
The @Value annotation in Spring is used to inject values into fields, method parameters, or constructor arguments directly from:
1. Property files (application.properties / application.yml)
```properties
app.name=MySpringApp
app.version=1.0.0
```
```java
@Component
public class AppConfig {

    @Value("${app.name}")
    private String appName;

    @Value("${app.version}")
    private String appVersion;

    public void printConfig() {
        System.out.println(appName + " - " + appVersion);
    }
}
```
2. Environment variables
3. System properties
4. Inline default values or expressions (SpEL - Spring Expression Language)
```java
@Value("${app.timeout:30}")  // if not found, defaults to 30
private int timeout;
@Value("#{2 * 1024}")  // SpEL
private int bufferSize;   // 2048

```

### What is the purpose of `@Profile` annotation?
The @Profile annotation in Spring is used to activate or deactivate beans/configurations based on the active environment (profile). It helps in environment-specific bean registration, so you can have different beans for dev, test, staging, and prod without changing code.

```java
public interface DataSourceConfig {
    void setup();
}

@Component
@Profile("dev")
class DevDataSourceConfig implements DataSourceConfig {
    public void setup() {
        System.out.println("Using H2 In-Memory Database for Dev");
    }
}

@Component
@Profile("prod")
class ProdDataSourceConfig implements DataSourceConfig {
    public void setup() {
        System.out.println("Using MySQL Database for Production");
    }
}
```

---

## Spring Web Annotations

### What is the difference between `@Controller` and `@RestController`?
Contoller: Marks a class as a Spring MVC Controller (used for web applications with views). Methods return a view name (HTML/JSP/Thymeleaf), not the response body. Needs @ResponseBody if you want to return JSON/XML directly.

```java
@Controller
public class HomeController {

    @GetMapping("/home")
    public String home() {
        return "home"; // returns view name "home.html" or "home.jsp"
    }

    @GetMapping("/data")
    @ResponseBody
    public String getData() {
        return "Hello from Controller"; // returns response body
    }
}
```

RestController: Specialization of @Controller + @ResponseBody. Methods return JSON/XML directly (suitable for REST APIs). No need to annotate each method with @ResponseBody.
```java
@RestController
public class ApiController {

    @GetMapping("/api/message")
    public String message() {
        return "Hello from RestController"; // returns JSON: "Hello from RestController"
    }
}
```



### What is the use of `@RequestMapping`? How does it differ from `@GetMapping`, `@PostMapping`, etc.?
@RequestMapping is the most generic and versatile annotation for mapping HTTP requests to handler methods in Spring MVC. It can be used at both class level and method level. It allows you to define:
1. URL path(s)
2. HTTP method(s) - GET, POST, PUT, PATCH, DELETE, etc
3. Consumes (request content type)
4. Produces (response content type)
5. Headers / params filtering

```java
@RestController
@RequestMapping("/api")  // Base path for all methods
public class UserController {

    @RequestMapping(value = "/users", method = RequestMethod.GET)
    public String getUsers() {
        return "List of Users";
    }

    @RequestMapping(value = "/users", method = RequestMethod.POST)
    public String createUser() {
        return "User created!";
    }
}
```
@RequestMapping is the generic and versatile mapping annotation that can map any HTTP method, while @GetMapping, @PostMapping, etc. are shorthand, specialized annotations introduced in Spring 4.3 for better readability and REST-style coding. In modern Spring Boot, @GetMapping and friends are preferred for clarity, while @RequestMapping is used when you need advanced mappings like multiple methods or header-based routing.

### Explain `@RequestBody` and `@ResponseBody` with examples.
@RequestBody maps the HTTP request body into a Java object, while @ResponseBody maps a Java object back to the HTTP response body. Together, they enable seamless JSON/XML communication in REST APIs using HttpMessageConverters.

### What is the difference between `@PathVariable` and `@RequestParam`?
| Feature             | `@PathVariable`                          | `@RequestParam`                             |
| ------------------- | ---------------------------------------- | ------------------------------------------- |
| **Source**          | From **URI path** (`/users/{id}`)        | From **query string** (`/users?name=arpan`) |
| **Use case**        | Resource identification (RESTful style)  | Filtering, sorting, optional params         |
| **Mandatory**       | Always required (unless marked optional) | Can be required or optional (with defaults) |
| **Example Request** | `/users/101`                             | `/users?name=Arpan&age=25`                  |


### How does `@ExceptionHandler` work in Spring MVC?
As the name suggest, @ExceptionHandler is used to handle exceptions thrown by controller methods. If an exception occures, don't return a 500 error, instead call this method to handle it. It improves error handling by allowing you to return meaningful responses (JSON/XML/HTML) instead of raw stack traces. 

```java
@RestController
@RequestMapping("/users")
public class UserController {

    @GetMapping("/{id}")
    public String getUser(@PathVariable int id) {
        if (id <= 0) {
            throw new IllegalArgumentException("Invalid user ID");
        }
        return "User with ID " + id;
    }

    // Handles only exceptions thrown in this controller
    @ExceptionHandler(IllegalArgumentException.class)
    public ResponseEntity<String> handleIllegalArgument(IllegalArgumentException ex) {
        return ResponseEntity
                .badRequest()
                .body("Error: " + ex.getMessage());
    }
}
```

Instead of duplicating @ExceptionHandler in every contrller, we use @ControllerAdvice.
```java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(IllegalArgumentException.class)
    public ResponseEntity<String> handleIllegalArgument(IllegalArgumentException ex) {
        return ResponseEntity
                .badRequest()
                .body("Global Error: " + ex.getMessage());
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<String> handleGeneric(Exception ex) {
        return ResponseEntity
                .status(HttpStatus.INTERNAL_SERVER_ERROR)
                .body("Something went wrong: " + ex.getMessage());
    }
}
```
### What is `@ControllerAdvice` in Spring MVC?
@ControllerAdvice is a specialized annotation in Spring used for global exception ahdnling, data binding, and model attributes across multiple controllers.

1. Global Exception Handling: Works with @ExceptionHandler to handle exceptions from any controller.
```java
@ControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(Exception.class)
    public ResponseEntity<String> handleAll(Exception ex) {
        return ResponseEntity.status(500).body("Error: " + ex.getMessage());
    }
}
```

2. Gloabl Model Attributes: Add attributes to the model that shold be available for all controllers.
```java
@ModelAttribute("appName")
public String appName() {
    return "My Spring App";
}
```

3. Global Data Binding: Customize request parameter binding for all controllers using @InitBinder.
```java
@InitBinder
public void initBinder(WebDataBinder binder) {
    binder.registerCustomEditor(Date.class, new CustomDateEditor(new SimpleDateFormat("yyyy-MM-dd"), true));
}
```

### What is the use of `@CrossOrigin` annotation in REST APIs?
@CrossOrigin is used in Spring to enable Cross-Origin Resource Sharing (CORS). By default, browsers block requests made from a different domain/port (due to Same-Origin Policy). @CrossOrigin allows REST APIs to be accessed from frontend apps running on a different domain (e.g., Angular, React, Vue).

```java
@CrossOrigin(origins = "*")  // Allow requests from all origins
@RestController
@RequestMapping("/api/users")
public class UserController {
    // All endpoints here allow CORS
}
```

Best Practice: Global Configuration
```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/api/**")
                .allowedOrigins("http://localhost:3000")
                .allowedMethods("GET", "POST", "PUT", "DELETE");
    }
}
```

---

## Spring Boot Annotations

### What does `@EnableAutoConfiguration` do?
@EnableAutoConfiguration tells Spring Boot to automatically configure beans based on the classpath and dependencies, saving developers from writing boilerplate configurations. It works by loading predefined configuration classes conditionally.

---

## Scheduling & Async

### What is the purpose of `@EnableAsync` and `@Async` annotations?
@EnableAsync - Enables Spring's asynchronous method execution capability. You put this on a @Configuration class (often the main Spring Boot application class).

@Async - Applied on a method to indicate that it should run in a seperate thread, without blocking the caller.

When Spring Boot starts, @EnableAsync tells Spring to look for @Async annotations. Any method annotated with @Async will be executed by a TaskExecutor (thread pool) instead of the main thread. The caller gets control back immediately, while the method runs asynchronously. Can return void, Future<T>, or CompletableFuture<T> for async results.

By default uses SimpleAsyncTaskExecutor (not a real thread pool). For production, define a custom ThreadPoolTaskExecutor.

```java
// Configuration
@SpringBootApplication
@EnableAsync   // Enable async support
public class MyApp {
    public static void main(String[] args) {
        SpringApplication.run(MyApp.class, args);
    }
}


// Service
@Service
public class EmailService {

    @Async
    public void sendEmail(String to) {
        System.out.println("Sending email to: " + to + " | Thread: " + Thread.currentThread().getName());
        try {
            Thread.sleep(5000); // simulate delay
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Email sent to: " + to);
    }
}


// Controller
@RestController
public class UserController {

    @Autowired
    private EmailService emailService;

    @GetMapping("/register")
    public String registerUser(@RequestParam String email) {
        emailService.sendEmail(email); // executes asynchronously
        return "Registration successful! Confirmation email will be sent.";
    }
}

```

### What is the role of `@EnableScheduling` and `@Scheduled` annotations?
@EnableScheduling - Enables Spring's scheduled task execution capability. You put this on a configuration class (commonly the main Spring Boot class).

@Scheduled - Marks a method to be executed periodically according to a fixed schedule (e.g., fixed rate, fixed delay, or CRON expression).

When the app starts, @EnableScheduling tells Spring to scan for @Scheduled annotations. Spring uses a TaskScheduler (by default `ThreadPoolTaskScheduler`) to manage background scheduled tasks. Methods annotated with @Scheduled are executed automatically at defined intervals - no need to manually invoke them.

```java
// Configuration
@SpringBootApplication
@EnableScheduling   // Enables scheduling support
public class MyApp {
    public static void main(String[] args) {
        SpringApplication.run(MyApp.class, args);
    }
}


// Scheduled Task
@Service
public class ReportService {

    // Executes every 5 seconds
    @Scheduled(fixedRate = 5000)
    public void generateReport() {
        System.out.println("Report generated at: " + LocalDateTime.now() 
                           + " | Thread: " + Thread.currentThread().getName());
    }

    // Executes 10 seconds after the previous execution completes
    @Scheduled(fixedDelay = 10000)
    public void cleanUp() {
        System.out.println("Cleanup done at: " + LocalDateTime.now());
    }

    // Executes every day at 2 AM using cron
    @Scheduled(cron = "0 0 2 * * ?")
    public void backupDatabase() {
        System.out.println("Database backup completed at: " + LocalDateTime.now());
    }
}

```

---

##  Spring Data & JPA

### What is the role of `@Transactional` annotation?
The @Transactional annotation in Spring is used to manage database transactions declaratively. It ensures that a set of database operations are executed as a single unit of work — all succeed or all fail.

Key Roles:
1. Atomicity: Ensures that all operations inside the transaction succeed. If any operation fails, all changes are rolled back.
2. Consistency: Keeps the database in a consistent state after operations.
3. Isolation: Controls how concurrent transactions interact (via isolation levels).
4. Simplified Transaction Management: Eliminates boilerplate code for begin, commit, and rollback.

```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    @Autowired
    private EmailService emailService;

    @Transactional
    public void createUser(User user) {
        // Step 1: Save user to DB
        userRepository.save(user);

        // Step 2: Send welcome email
        // If this fails, the DB save will be rolled back
        emailService.sendWelcomeEmail(user);
    }
}

```

### What is the difference between `@Id` and `@EmbeddedId`?
| Feature        | `@Id`                                                                    | `@EmbeddedId`                                                                                          |
| -------------- | ------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------ |
| **Purpose**    | Marks a single field as the **primary key** of an entity.                | Used to define a **composite primary key** by embedding an object that represents multiple key fields. |
| **Field Type** | Usually a primitive type or wrapper (e.g., `Long`, `Integer`, `String`). | Must be a **serializable class** that contains multiple fields representing the composite key.         |
| **Definition** | Applied directly to the primary key field.                               | Applied to a field of type of the **embeddable key class** annotated with `@Embeddable`.               |
| **Use Case**   | When the entity has a **single-column primary key**.                     | When the entity has a **multi-column primary key**.                                                    |

```java
@Entity
public class User {
    @Id
    private Long userId;

    private String name;
}
```

```java
@Embeddable
public class OrderId implements Serializable {
    private Long orderNumber;
    private Long productId;
}

@Entity
public class Order {
    @EmbeddedId
    private OrderId id;

    private int quantity;
}

```

### How is `@Transient` used in entity classes?
@Transient is an annotation in JPA. It marks a field in an entity class to be ignored by the persistence framework. It will not be stored in the database.

Use Cases:
1. Fields used for temporary calculations.
2. Fields not meant to be persisted, e.g., derived values, flags, or helper variables.
3. Sensitive data that should not be saved in the DB.

```java
@Entity
public class User {
    @Id
    private Long id;

    private String firstName;
    private String lastName;

    @Transient
    private String fullName; // Not persisted in the DB

    public String getFullName() {
        return firstName + " " + lastName;
    }
}
```

### Explain entity relationship annotations like `@OneToOne`, `@OneToMany`, `@ManyToOne`, and `@ManyToMany`.

1. @OneToOne - A single entity is associated with exactly one instance of another entity.
```java
@Entity
public class User {
    @Id
    private Long id;

    @OneToOne
    @JoinColumn(name = "passport_id") // foreign key in User table
    private Passport passport;
}

@Entity
public class Passport {
    @Id
    private Long id;
    private String number;
}

```

2. @OneToMany - One entity is associated with multiple instances of another entity.
```java
@Entity
public class Book {
    @Id
    private Long id;

    @OneToMany(mappedBy = "book", cascade = CascadeType.ALL)
    private List<Page> pages;
}

@Entity
public class Page {
    @Id
    private Long id;

    @ManyToOne
    @JoinColumn(name = "book_id")
    private Book book;
}
```

3. @ManyToOne - Many instances of an entity relate to one instance of another entity.

4. @ManyToMany - Multiple instances of an entity relate to multiple instances of another entity.
```java
@Entity
public class Student {
    @Id
    private Long id;

    @ManyToMany
    @JoinTable(
        name = "student_course",
        joinColumns = @JoinColumn(name = "student_id"),
        inverseJoinColumns = @JoinColumn(name = "course_id")
    )
    private List<Course> courses;
}

@Entity
public class Course {
    @Id
    private Long id;

    @ManyToMany(mappedBy = "courses")
    private List<Student> students;
}

```

Key Points:
* Cascading - Use `cascade = CascadeType.ALL` to propagate operations (save, delete) across related entities.
* Fetch Type:
    * `FetchType.LAZY` → loads related entities on demand (default for collections).
    * `FetchType.EAGER` → loads related entities immediately (default for @OneToOne and @ManyToOne).

### How does Spring Data JPA manage cascading operations?
Cascading allows operations performed on a parent entity to be automatically propagated to its related child entities. Without cascading, you would have to explicitly save, update, or delete child entities manually.

Types of Cascade Options:
* `ALL` - Applies all cascade operations.
* `PERSIST` - Saves child entities when parent is saved.
* `MERGE` - Updates child entities when parent is updated.
* `REMOVE` - Deletes child entities when parent is deleted.
* `REFRESH` - Refreshes child entities from the database.
* `DETACH` - Detaches child entities from the persistence context.


### What are the benefits and drawbacks of using Spring ORM (Spring Data JPA)?
Benefits:
* Increased Productivity:
    * Reduces boilerplate code for CRUD operations. 
    * Developers can focus on business logic rather than writing SQL.
* Simplified Data Access:
    * Uses object-oriented entities instead of relational tables.
    * Supports JPQL and derived query methods for easy queries.
* Automatic Transaction Management
    * @Transactional handles commit/rollback automatically.
* Improved Maintainability:
    * Clear separation between data access and business logic.
* Reduced Errors:
    * Automatic type conversion and exception handling prevent common mistakes.
* Integration with Spring Ecosystem:
    * Works seamlessly with dependency injection, caching, and Spring Boot features.

Drawbacks:
* Performance Overhead:
    * Generated queries may be less efficient than hand-written SQL.
    * Lazy loading can cause N+1 query problems if not handled carefully.
* Limited Flexibility: 
    * Complex queries or DB-specific optimizations may be harder to implement.
* Potential Code Bloat:
    * Overuse of annotations and generated code can make the project harder to read.
* Learning Curve:
    * Requires understanding of JPA, entity relationships, and best practices.
* Database Vendor Dependency:
    * Some advanced features may work differently across DBs.

Use ORM for complex models, faster development, and maintainability.
Use JDBC for performance-critical, simple, or legacy systems.


---

## Spring JDBC

### What is `JdbcTemplate` and what are its advantages?
JdbcTemplate is a Spring utility that simplifies JDBC operations by handling boilerplate code like connection management, statement preparation, and result set handling. It provides a consistent API for executing queries, updates, and batch operations.

Advantages:
1. Simplifies JDBC code and improves readability.
2. Automatically manages resources, preventing connection leaks.
3. Supports parameterized queries, reducing SQL injection risk.
4. Provides batch operations for better performance.
5. Translates SQLExceptions into Spring’s unchecked DataAccessException.
6. Maps results to Java objects using `RowMapper` or `BeanPropertyRowMapper`.

```java
@Autowired
private JdbcTemplate jdbcTemplate;

// Querying for a single object
User user = jdbcTemplate.queryForObject(
    "SELECT * FROM users WHERE id = ?", 
    new BeanPropertyRowMapper<>(User.class), 
    1
);

// Insert with auto-generated key
KeyHolder keyHolder = new GeneratedKeyHolder();
jdbcTemplate.update(connection -> {
    PreparedStatement ps = connection.prepareStatement(
        "INSERT INTO users(name, email) VALUES(?, ?)", 
        Statement.RETURN_GENERATED_KEYS
    );
    ps.setString(1, "John");
    ps.setString(2, "john@example.com");
    return ps;
}, keyHolder);

```

### How can you execute batch updates with Spring JDBC?
Spring JDBC provides the JdbcTemplate.batchUpdate() method to execute multiple SQL statements efficiently in a single batch. This is useful for performing bulk inserts, updates, or deletes. It reduces the number of database round-trips and improves performance.

```java
String sql = "INSERT INTO users(name, email) VALUES (?, ?)";
jdbcTemplate.batchUpdate(sql, new BatchPreparedStatementSetter() {
    @Override
    public void setValues(PreparedStatement ps, int i) throws SQLException {
        ps.setString(1, userList.get(i).getName());
        ps.setString(2, userList.get(i).getEmail());
    }

    @Override
    public int getBatchSize() {
        return userList.size();
    }
});
// Using BatchPreparedStatementSetter is ideal for dynamic data.
```

### What are some common pitfalls when using JDBC for bulk updates?
1. By default, JDBC runs in auto-commit mode, which means each update is committed immediately.This can make bulk updates very slow and inefficient. Solution - Disable auto-commit and commit manually after the batch.

2. Forgetting to close `Connection`, `Statement`, or `ResultSet` objects can lead to resource leaks and connection exhaustion. Solution - Use try-with-resources or proper finally blocks to close resources.

3. If exceptions occur in the middle of a batch, partial updates may leave the database in an inconsistent state. Solution - Handle exceptions carefully and consider rolling back the transaction.

4. Sending too many statements in a single batch can overwhelm the database or cause memory issues. Solution - Break large updates into manageable batch sizes.

5. Not using PreparedStatement for repeated updates can lead to SQL parsing overhead for each statement. Solution - Always use PreparedStatement for bulk updates.

---

## Application Configuration

### What is the role of `application.properties` / `application.yml` in Spring Boot?
The application.properties or application.yml file in Spring Boot is used to externalize configuration for the application. It allows developers to define key-value pairs or hierarchical configuration for various aspects of the application, such as:

1. Core application properties – e.g., server port, context path.
2. Data source and database settings – JDBC URLs, credentials, connection pools.
3. Caching, mail, and messaging properties.
4. Logging, security, and actuator settings.
5. Environment-specific properties – can use profiles like application-dev.yml or application-prod.yml.

---

### Explain the importance of Spring Boot’s actuator and how it enhances application monitoring and management.

The Spring Boot Actuator provides crucial operational features to a Spring Boot application, allowing for the monitoring and management of the application in production. It exposes a series of built-in endpoints delivering rich details about the application’s health, metrics, environment, and more.

For example, the /health endpoint provides basic application health information, while /metrics offers a wealth of metrics regarding the application's performance, such as JVM memory usage, thread stats, and HTTP request metrics.

To enable the Actuator, add the following dependency:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Developers can customize which endpoints are exposed and secure sensitive endpoints by modifying application properties or through security configurations.

### How do you implement caching in Spring Boot for improved performance?
Spring Boot provides easy-to-use support for caching, which can significantly improve the performance of your application by reducing the need to repeatedly compute or fetch data that hasn’t changed. To enable caching, you need to add the spring-boot-starter-cache dependency and annotate your application with @EnableCaching.

You can then use the @Cacheable annotation on methods to indicate that the result should be cached. 

```java
@Service
public class ProductService {

    @Cacheable("products")
    public Product findProductById(Long id) {
        // Method implementation to fetch product by id
        return new Product();
    }
}
```
In this example, the result of findProductById is cached under the "products" cache. Subsequent calls with the same id will fetch the result from the cache instead of executing the method again.

### What are the best practices for managing application properties and secrets in Spring Boot?

Managing application properties and secrets securely is crucial for any application. Spring Boot applications typically use application.properties or application.yml files for configuration. For sensitive data such as passwords or API keys, environment variables or Spring Cloud Config Server can be used to externalize secrets from the codebase.

A best practice is to use Spring’s @Value annotation to inject values from properties files or environment variables into your application, keeping sensitive information out of the code:
```java
@Component
public class MyService {

    @Value("${api.key}")
    private String apiKey;

    // Use the apiKey in your service methods
}
```


