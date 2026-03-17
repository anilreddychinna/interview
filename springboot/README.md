## Spring Boot Interview Questions & Answers

Focused Spring Boot questions with short, clear answers.

---

### 1. What is Spring Boot? How is it different from Spring MVC / Spring Framework?

**Answer:** Spring Boot is a framework that makes it easy to create standalone, production-ready Spring applications with **minimal configuration**.

- **Auto-configuration**: Configures beans automatically based on classpath and properties.
- **Starter dependencies**: Opinionated dependencies for common use-cases (`spring-boot-starter-web`, `spring-boot-starter-data-jpa`, etc.).
- **Embedded servers**: Runs with embedded Tomcat/Jetty/Undertow – no need to deploy WAR to external server.

**Difference:** Plain Spring requires more XML/Java config and manual setup; Spring Boot gives convention-over-configuration and sensible defaults.

---

### 2. What is `@SpringBootApplication`?

**Answer:** `@SpringBootApplication` is a convenience annotation that combines:

- `@Configuration` – Marks the class as a source of bean definitions.
- `@EnableAutoConfiguration` – Enables Spring Boot’s auto-configuration.
- `@ComponentScan` – Scans the package and subpackages for components (`@Component`, `@Service`, `@Repository`, `@Controller`).

**Example:**

```java
@SpringBootApplication
public class DemoApplication {
  public static void main(String[] args) {
    SpringApplication.run(DemoApplication.class, args);
  }
}
```

---

### 3. What is auto-configuration in Spring Boot? How does it work?

**Answer:** Auto-configuration **automatically configures beans** based on:

- Classes present on the **classpath**.
- Spring Boot **configuration properties**.

It uses `@EnableAutoConfiguration` and many `@Configuration` classes guarded by `@ConditionalOnClass`, `@ConditionalOnMissingBean`, etc.  
For example, if `spring-webmvc` is on the classpath, Spring Boot auto-configures a `DispatcherServlet`.

You can see enabled auto-configurations with:

```bash
java -jar app.jar --debug
```

---

### 4. What are Spring Boot Starters?

**Answer:** Starters are **predefined dependency bundles** to simplify Maven/Gradle configuration.

Examples:

- `spring-boot-starter-web` – web + JSON + validation (Spring MVC + Jackson + etc.)
- `spring-boot-starter-data-jpa` – Spring Data JPA + Hibernate
- `spring-boot-starter-test` – JUnit, Mockito, Spring Test

They help you avoid managing many individual versions manually.

---

### 5. How do you configure properties in Spring Boot?

**Answer:** Using:

- `application.properties` or `application.yml`
- Environment variables, command-line args, profiles

**Example (`application.properties`):**

```properties
server.port=8081
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=secret
```

Map properties to a class using `@ConfigurationProperties`:

```java
@ConfigurationProperties(prefix = "app")
public class AppProperties {
  private String name;
  private String version;
  // getters/setters
}
```

Enable with `@EnableConfigurationProperties(AppProperties.class)` or `@ConfigurationPropertiesScan`.

---

### 6. What is `application.properties` vs `application.yml` vs profiles?

**Answer:**

- `application.properties` / `application.yml`: Main configuration file.
- Profiles (e.g., `application-dev.yml`, `application-prod.yml`) let you define **environment-specific** configs.

Activate profile:

- Via `spring.profiles.active=dev` in properties.
- Or via command line: `--spring.profiles.active=dev`.

---

### 7. How do you create a REST API in Spring Boot?

**Answer:** Use `@RestController`, `@RequestMapping` (or `@GetMapping`, `@PostMapping`, etc.).

**Example: simple Product API**

```java
@RestController
@RequestMapping("/api/products")
public class ProductController {

  private final ProductService productService;

  public ProductController(ProductService productService) {
    this.productService = productService;
  }

  @GetMapping
  public List<ProductDto> getAll() {
    return productService.getAll();
  }

  @GetMapping("/{id}")
  public ProductDto getById(@PathVariable Long id) {
    return productService.getById(id);
  }

  @PostMapping
  public ProductDto create(@RequestBody ProductDto dto) {
    return productService.create(dto);
  }
}
```

Service layer:

```java
@Service
public class ProductService {
  public List<ProductDto> getAll() { ... }
  public ProductDto getById(Long id) { ... }
  public ProductDto create(ProductDto dto) { ... }
}
```

---

### 8. Difference between `@RestController` and `@Controller`?

**Answer:**

- `@Controller` – Typically returns **views** (JSP/Thymeleaf). Methods return view names or `ModelAndView`.
- `@RestController` – Combination of `@Controller` + `@ResponseBody`. Return values are written **directly as JSON/XML** to the HTTP response body.

Use `@RestController` for REST APIs.

---

### 8.1. What does `@RestController` do internally?

**Answer (inside view):**

- `@RestController` = `@Controller` **+** `@ResponseBody` on **every method**.
- Because of `@ResponseBody`, the **return value** of each handler method is written **directly to the HTTP response body** instead of resolving a view.
- Spring MVC uses **`HttpMessageConverter`s** to convert the Java object into JSON, XML, or text based on:
  - The **return type** (e.g., `User`, `List<User>`, `ResponseEntity<User>`),
  - The **`Accept` header** from the client (content negotiation),
  - Available converters on the classpath (e.g., Jackson for JSON).

**Flow (simplified):**

1. Request hits `DispatcherServlet`.
2. `HandlerMapping` finds your `@RestController` method.
3. Method executes and returns an object (e.g., `UserDto`).
4. Because of `@ResponseBody` (part of `@RestController`), Spring skips view resolution and passes the object to **`HttpMessageConverter`**.
5. Converter (e.g., Jackson) serializes the object to JSON and writes it to the response.

**Interview line:** “`@RestController` tells Spring MVC to treat return values as the HTTP body and use `HttpMessageConverters` (usually Jackson) to serialize objects, instead of resolving a view.”

---

### 9. How does Spring Boot handle exception handling in REST APIs?

**Answer:** You can use `@ControllerAdvice` + `@ExceptionHandler` to centralize error handling.

**Example:**

```java
@ControllerAdvice
public class GlobalExceptionHandler {

  @ExceptionHandler(ResourceNotFoundException.class)
  public ResponseEntity<ErrorResponse> handleNotFound(ResourceNotFoundException ex) {
    ErrorResponse error = new ErrorResponse("NOT_FOUND", ex.getMessage());
    return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
  }
}
```

**Interview line:** “I use `@ControllerAdvice` to keep controllers clean and handle errors consistently.”

---

### 10. What is Spring Data JPA? How does it integrate with Spring Boot?

**Answer:** Spring Data JPA simplifies data access by generating repository implementations at runtime.

- Define repository interfaces like `interface UserRepository extends JpaRepository<User, Long> {}`.
- Spring Boot auto-configures `EntityManagerFactory`, DataSource, and repositories when `spring-boot-starter-data-jpa` is on the classpath.

**Example:**

```java
public interface UserRepository extends JpaRepository<User, Long> {
  Optional<User> findByEmail(String email);
}
```

---

### 10.1. Common JPA annotations – what, where, why?

**Answer:** Frequently used JPA annotations:

| Annotation | Where used | Purpose |
|-----------|-----------|---------|
| `@Entity` | On class | Marks as JPA entity (table) |
| `@Table` | On class | Customize table name, schema, unique constraints |
| `@Id` | On field | Primary key |
| `@GeneratedValue` | On `@Id` | Primary-key generation strategy (IDENTITY, SEQUENCE, AUTO, TABLE) |
| `@Column` | On field | Customize column name, length, nullable, unique |
| `@Transient` | On field | Not persisted to DB |
| `@OneToOne`, `@OneToMany`, `@ManyToOne`, `@ManyToMany` | On relationships | Define entity relationships |
| `@JoinColumn` | On field | Foreign key column mapping |
| `@Enumerated` | On enum field | How to store enum (STRING / ORDINAL) |
| `@Lob` | On field | Large objects (CLOB/BLOB) |
| `@Temporal` (old), `@CreationTimestamp`, `@UpdateTimestamp` (Hibernate) | On date/time | Store timestamps; audit columns |

**Example:**

```java
@Entity
@Table(name = "users")
public class User {

  @Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  private Long id;

  @Column(nullable = false, length = 100)
  private String name;

  @Column(unique = true, nullable = false)
  private String email;

  @Enumerated(EnumType.STRING)
  private Role role;

  @OneToMany(mappedBy = "user")
  private List<Order> orders;
}
```

**Interview line:** “I use JPA annotations to describe how my Java model maps to tables/columns and relationships.”

---

### 10.2. `JpaRepository` / `CrudRepository` / `Repository` – what’s the difference?

**Answer:**

- `Repository` – **Marker** interface. Base for Spring Data repositories; usually not extended directly.
- `CrudRepository<T, ID>` – Basic CRUD: `save`, `findById`, `findAll`, `deleteById`, etc.
- `JpaRepository<T, ID>` – Extends `PagingAndSortingRepository` and `CrudRepository`, adds:
  - Pagination and sorting
  - JPA-specific methods like `flush()`, `saveAndFlush()`, `getOne()` (older) / `getReferenceById()` (newer).

**Most projects directly use `JpaRepository`** because it provides all CRUD + paging + JPA features.

**Example:**

```java
public interface ProductRepository extends JpaRepository<Product, Long> {
  List<Product> findByCategory(String category);

  @Query("select p from Product p where p.price > :minPrice")
  List<Product> findExpensive(@Param("minPrice") BigDecimal minPrice);
}
```

---

### 10.3. What methods are available in `CrudRepository` / `JpaRepository`?

**Answer:** Common methods (interview-ready list):

- `save(S entity)` / `saveAll(Iterable<S>)`
- `findById(ID id)` / `findAll()`
- `deleteById(ID id)` / `deleteAll()`
- `count()`, `existsById(ID id)`
- From `PagingAndSortingRepository`: `findAll(Pageable pageable)`, `findAll(Sort sort)`
- From `JpaRepository`: `flush()`, `saveAndFlush(entity)`, `deleteInBatch(Iterable<T>)`, etc.

---

### 11. How do you connect Spring Boot to a database?

**Answer:**

1. Add dependency: `spring-boot-starter-data-jpa` + driver (e.g., MySQL).
2. Configure properties (`spring.datasource.*`, `spring.jpa.*`).
3. Create `@Entity` classes and `JpaRepository` interfaces.

**Example (`application.properties`):**

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=secret
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

---

### 12. What is `@Entity` and `@Id` in JPA?

**Answer:**

- `@Entity`: Marks a class as a JPA entity mapped to a database table.
- `@Id`: Marks the primary key field of the entity.

**Example:**

```java
@Entity
public class User {
  @Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  private Long id;

  private String name;
  private String email;
}
```

---

### 12.1. What is `JdbcTemplate`? When would you use it instead of JPA?

**Answer:** `JdbcTemplate` is a helper class that simplifies **plain JDBC** code:

- Handles connection/statement creation and closing.
- Helps execute SQL queries and map `ResultSet` rows to objects.

Use it when:

- You need **fine-grained control** over SQL.
- You want **better performance** on very large, simple queries.
- You’re working with **legacy schemas** that don’t map cleanly to entities.

**Example:**

```java
@Repository
public class UserJdbcRepository {

  private final JdbcTemplate jdbcTemplate;

  public UserJdbcRepository(JdbcTemplate jdbcTemplate) {
    this.jdbcTemplate = jdbcTemplate;
  }

  public List<User> findActiveUsers() {
    String sql = "SELECT id, name, email FROM users WHERE active = true";
    return jdbcTemplate.query(sql, (rs, rowNum) -> {
      User u = new User();
      u.setId(rs.getLong("id"));
      u.setName(rs.getString("name"));
      u.setEmail(rs.getString("email"));
      return u;
    });
  }
}
```

---

### 12.2. How to fetch data from a big table and improve performance?

**Answer (key strategies):**

- **Use pagination** instead of loading everything at once.
- Fetch only **required columns** (projections).
- Use **indexes** on frequently filtered columns.
- For reporting, consider **read replicas** or a separate analytics DB.

**Example – JPA pagination:**

```java
Page<Product> page = productRepository.findAll(PageRequest.of(0, 50));
List<Product> first50 = page.getContent();
```

**Example – streaming with JdbcTemplate (process row by row):**

```java
String sql = "SELECT id, name FROM big_table";
jdbcTemplate.query(sql, rs -> {
  while (rs.next()) {
    // process each row, do not keep all in memory
  }
});
```

**Interview line:** “For huge tables, I never load everything into memory. I use pagination or streaming and ensure proper DB indexes.”

---

### 12.3. How can we use multiple data sources in Spring Boot?

**Answer:** Configure **two `DataSource` beans** and mark one as `@Primary`, then configure separate `EntityManagerFactory` and `TransactionManager` for each, or use one with JPA and one with plain `JdbcTemplate`.

**High-level steps:**

1. Define multiple sets of properties (e.g., `spring.datasource.primary.*`, `spring.datasource.secondary.*`).
2. Create config classes:
   - `@Bean` `DataSource` for each.
   - `@Bean` `LocalContainerEntityManagerFactoryBean` for each JPA data source.
   - `@EnableJpaRepositories` with different `basePackages` per data source.

**Interview summary:** “Multiple data sources = multiple `DataSource` + multiple entity managers + repository packages pointing to each.”

---

### 12.4. What is batch processing in Spring (Spring Batch)? How can it help with big data loads?

**Answer:** **Spring Batch** is a framework for **batch jobs**: reading, processing, and writing large volumes of data in chunks.

- Defines **Job** → **Steps** → **ItemReader**, **ItemProcessor**, **ItemWriter**.
- Reads data in **chunks** (e.g., 1000 records), processes them, and writes them out, which uses memory efficiently.

**When to use:** When you need scheduled ETL jobs, large imports/exports (e.g., processing millions of rows from DB or CSV).

**Interview line:** “For big data loads, I use Spring Batch with chunk-oriented processing instead of manual loops, so I get restartability, monitoring, and good performance.”

---

### 12.5. Handling 1 million+ records: API, batch jobs, S3 upload (interview Q&A)

Interview questions about **large volume**: APIs returning or accepting huge datasets, batch processing millions of records, and uploading to S3 (or similar).

---

#### Q. How would you design an API to return 1 million records?

**Answer:**

- **Never return 1M rows in one response** – it blows memory and timeouts.
- **Pagination**: Return **pages** (e.g., `GET /api/items?page=0&size=1000`). Client requests page by page.
- **Cursor-based pagination**: For very large or changing datasets, use a **cursor** (e.g., last id or token) instead of page number so results are stable.
- **Streaming** (advanced): Use **streaming response** (e.g., `StreamingResponseBody`, SSE, or chunked transfer) so the server sends records as they’re read (from DB or Kafka), without loading all into memory.
- **Async + file export**: For “give me everything” use cases, trigger a **background job** (e.g., Spring Batch), write result to a file, then provide a **download link** (or upload to S3 and return the link).

**Interview line:** “I’d use pagination or cursor-based pagination for APIs; for full exports I’d use a batch job and then provide a download or S3 link.”

---

#### Q. How do you process 1 million records in a batch job (e.g., from DB or CSV) in Spring?

**Answer:**

- Use **Spring Batch** with **chunk-oriented** processing:
  - **ItemReader**: Read in chunks (e.g., 1000 rows via JPA pagination, JdbcTemplate streaming, or CSV reader).
  - **ItemProcessor**: Transform/filter each item.
  - **ItemWriter**: Write in chunks (e.g., to DB, file, or S3).
- **Chunk size**: Tune (e.g., 500–2000) to balance memory and throughput.
- **Never load 1M rows into memory at once**; process and release each chunk.
- Use **partitioning** (optional) to split the job across multiple threads or JVMs for more parallelism.

**Interview line:** “I use Spring Batch with a chunked reader/processor/writer so we never hold 1M records in memory; we read, process, and write in chunks.”

---

#### Q. How do you upload 1 million records (or a large file) to S3 from a Java/Spring application?

**Answer:**

- **Large single file**:
  - Use **multipart upload**: split the file into parts (e.g., 5–100 MB), upload parts in parallel, then complete the multipart upload. AWS SDK supports this (`TransferManager` or low-level multipart APIs).
- **Many records (e.g., 1M rows)**:
  - **Don’t** build one huge payload in memory. Either:
    - **Batch in chunks** (e.g., 10k records), write each chunk to a file or buffer, then upload that chunk as one S3 object (or one part of a multipart upload), then process the next chunk; or
    - **Stream** records (e.g., from Kafka or DB), write to a temporary file or buffer, and when the chunk size is reached, upload to S3 and clear the buffer.
- **Async**: Run the upload in a **background thread or async job** (e.g., `@Async`, or a Spring Batch step) so the API responds quickly and uploads in the background.
- **Idempotency**: Use deterministic S3 keys (e.g., date + batch index + partition) so retries don’t create duplicates.

**Interview line:** “For large files I use S3 multipart upload; for millions of records I process in chunks and upload each chunk as an object or as part of a multipart upload, usually in a background job.”

---

#### Q. How would you handle an API that receives 1 million records (e.g., bulk upload)?

**Answer:**

- **Don’t** accept 1M records in one request body (memory and timeouts).
- **Chunked upload**: Client sends **chunks** (e.g., 1000–10000 per request); server appends to a temporary store or queue (e.g., file, Kafka, or DB). After all chunks received, trigger a batch job to process.
- **Or stream**: Client streams the payload (chunked transfer); server reads the stream and writes to Kafka or to temporary storage, then processes via a batch job.
- **Validation**: Validate per chunk; return errors per chunk or a summary so the client can retry failed chunks.

**Interview line:** “I’d design a chunked or streaming upload API, write chunks to Kafka or temp storage, then process in a batch job rather than loading 1M records in one request.”



**Answer:** Spring Boot Actuator exposes **production-ready endpoints** to monitor and manage the application.

Examples:

- `/actuator/health` – health status
- `/actuator/info` – custom info
- `/actuator/metrics` – application metrics

Enable by adding `spring-boot-starter-actuator` and configuring in properties:

```properties
management.endpoints.web.exposure.include=health,info,metrics
```

---

### 14. How do you secure a Spring Boot REST API?

**Answer:** Use **Spring Security** with `spring-boot-starter-security`.

Basic steps:

- Define a `SecurityFilterChain` bean with `HttpSecurity`.
- Configure authentication (in-memory, JDBC, UserDetailsService, JWT).

**Example (very basic):**

```java
@Configuration
@EnableMethodSecurity(prePostEnabled = true, securedEnabled = true)
public class SecurityConfig {

  @Bean
  public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    http
      .csrf().disable()
      .authorizeHttpRequests(auth -> auth
        .requestMatchers("/api/public/**").permitAll()
        .anyRequest().authenticated()
      )
      .httpBasic();
    return http.build();
  }
}
```

---

### 14.1. What is Authentication vs Authorization?

**Answer:**

- **Authentication**: *“Who are you?”* – Verifying the **identity** of the user (login: username/password, token, OAuth, etc.).
- **Authorization**: *“What are you allowed to do?”* – Checking the **permissions/roles** of an already authenticated user (e.g., admin vs user).

**Interview line:** “First we authenticate the user (identity), then for each request we authorize their access (roles/permissions).”

---

### 14.2. What is JWT? How does JWT-based authentication work in Spring Boot?

**Answer:** **JWT (JSON Web Token)** is a compact, signed token format used to securely transmit claims (like user id, roles).

**Structure:** `header.payload.signature`

- **Header** – type + algorithm (e.g., HS256).
- **Payload** – claims (e.g., `sub`, `roles`, `exp`).
- **Signature** – HMAC or RSA signature to prevent tampering.

**Basic flow with Spring Boot:**

1. **Login (authenticate)**  
   - Client sends username/password to `/auth/login`.  
   - Server validates credentials (e.g., with `UserDetailsService`).  
   - If valid, server generates a JWT containing user id and roles and returns it to the client.

2. **Use token on each request**  
   - Client sends the token in `Authorization: Bearer <jwt>` header.
   - A **JWT filter** (or authentication filter) in Spring Security:
     - Extracts the token.
     - Validates signature + expiration.
     - Reads username/roles from claims.
     - Builds an `Authentication` object and sets it in `SecurityContext`.

3. **Authorization**  
   - When the controller method is called, annotations like `@PreAuthorize("hasRole('ADMIN')")` or URL rules in `HttpSecurity` check the roles from the `SecurityContext` to allow/deny access.

**Simple pseudo-example (filter-style):**

```java
public class JwtAuthFilter extends OncePerRequestFilter {

  @Override
  protected void doFilterInternal(HttpServletRequest request,
                                  HttpServletResponse response,
                                  FilterChain filterChain) throws ServletException, IOException {
    String header = request.getHeader("Authorization");
    if (header != null && header.startsWith("Bearer ")) {
      String token = header.substring(7);
      // validate token, extract username/roles
      String username = jwtService.extractUsername(token);
      if (jwtService.isValid(token) && SecurityContextHolder.getContext().getAuthentication() == null) {
        UserDetails userDetails = userDetailsService.loadUserByUsername(username);
        UsernamePasswordAuthenticationToken auth =
          new UsernamePasswordAuthenticationToken(userDetails, null, userDetails.getAuthorities());
        SecurityContextHolder.getContext().setAuthentication(auth);
      }
    }
    filterChain.doFilter(request, response);
  }
}
```

**Interview summary:** “JWT is a signed token we issue after login; on each request a filter validates it and sets the authenticated user in the security context, and then authorization rules check roles/authorities.”

---

### 14.3. Common Spring Security annotations for authorization (`@PreAuthorize`, roles, permissions)

**Answer:** Spring Security uses **method-level annotations** with **SpEL expressions** to control access.

#### `@PreAuthorize` – check **before** method execution

**Where:** On service or controller methods.

**Why:** Enforce role/permission checks in code, not just via URL patterns.

**Examples:**

```java
@PreAuthorize("hasRole('ADMIN')")
public void deleteUser(Long id) { ... }

@PreAuthorize("hasAnyRole('ADMIN','MANAGER')")
public List<User> getAllUsers() { ... }

@PreAuthorize("hasAuthority('PRODUCT_READ')")
public Product getProduct(Long id) { ... }
```

**Notes:**

- `hasRole('ADMIN')` expects authority `ROLE_ADMIN` internally.
- Prefer `hasAuthority('SOME_PERMISSION')` when you use granular permissions.

---

#### `@PostAuthorize` – check **after** method execution

**Where:** On methods where you need to look at the **returned object** to decide access.

```java
@PostAuthorize("returnObject.owner == authentication.name")
public Order getOrder(Long id) { ... }
```

**Why:** Useful when authorization depends on the data itself (resource ownership).

---

#### `@Secured` and `@RolesAllowed`

**`@Secured`** (Spring-specific):

```java
@Secured("ROLE_ADMIN")
public void adminOnlyTask() { ... }
```

**`@RolesAllowed`** (JSR-250 standard):

```java
@RolesAllowed({"ROLE_ADMIN", "ROLE_MANAGER"})
public void adminOrManagerTask() { ... }
```

**When to use:** Simple role-based checks. For more complex expressions, prefer `@PreAuthorize`.

---

#### Common expression helpers (inside `@PreAuthorize` / `@PostAuthorize`)

- `hasRole('ADMIN')` / `hasAnyRole('ADMIN','USER')`
- `hasAuthority('USER_READ')` / `hasAnyAuthority('USER_READ','USER_WRITE')`
- `isAuthenticated()` / `isAnonymous()` / `isRememberMe()`
- `principal` or `authentication` – current user:
  - `principal.username`
  - `authentication.name`
  - `authentication.authorities`

**Example combining user and path variable:**

```java
@PreAuthorize("#username == authentication.name or hasRole('ADMIN')")
public User getProfile(String username) { ... }
```

---

**Interview summary:** “I enable method security and use `@PreAuthorize` / `@PostAuthorize`, `@Secured`, `@RolesAllowed` with expressions like `hasRole`, `hasAuthority`, `hasPermission`, and checks on `authentication` to enforce authorization in the service layer.”

---

### 15. How do you test Spring Boot applications?

**Answer:**

- Use `spring-boot-starter-test` which includes JUnit, Mockito, Spring Test, etc.
- For integration tests, use `@SpringBootTest` to load the full context.
- For slice tests: `@WebMvcTest`, `@DataJpaTest`, etc.

**Example:**

```java
@SpringBootTest
class DemoApplicationTests {

  @Test
  void contextLoads() {
  }
}
```

---

### 16. What is the difference between `@Component`, `@Service`, `@Repository`?

**Answer:** All are stereotypes for Spring-managed beans:

- `@Component`: Generic component (base stereotype).
- `@Service`: Service layer – business logic.
- `@Repository`: DAO layer – data access, also translates persistence exceptions to Spring’s `DataAccessException`.

In practice, they behave similarly for component scanning; names express intent.

---

### 17. How do SOLID principles apply to a Product API in Spring Boot?

**Answer:** SOLID is a set of **5 object-oriented design principles**. We can apply them directly to a simple Product API.

---

#### S – Single Responsibility Principle

**Definition:** A class should have **one reason to change** (one responsibility).

**In Product API:** Split responsibilities:

- `ProductController` → handles HTTP and request/response mapping.
- `ProductService` → business logic (validation, orchestration).
- `ProductRepository` → data access.

```java
@RestController
@RequestMapping("/api/products")
public class ProductController {

  private final ProductService productService;

  public ProductController(ProductService productService) {
    this.productService = productService;
  }

  @PostMapping
  public ProductDto create(@RequestBody CreateProductRequest request) {
    return productService.createProduct(request);
  }
}

@Service
public class ProductService {

  private final ProductRepository productRepository;

  public ProductService(ProductRepository productRepository) {
    this.productRepository = productRepository;
  }

  public ProductDto createProduct(CreateProductRequest request) {
    Product entity = new Product(request.getName(), request.getPrice());
    Product saved = productRepository.save(entity);
    return ProductDto.from(saved);
  }
}
```

---

#### O – Open/Closed Principle

**Definition:** Classes should be **open for extension, closed for modification**.

**In Product API:** Use interfaces and strategy patterns so you can extend behavior **without editing existing classes**.

Example: pricing strategy.

```java
public interface PricingStrategy {
  BigDecimal calculatePrice(Product product);
}

@Service
public class DefaultPricingStrategy implements PricingStrategy {
  public BigDecimal calculatePrice(Product product) {
    return product.getBasePrice();
  }
}

@Service
public class ProductService {
  private final PricingStrategy pricingStrategy;
  // inject implementation via constructor
}
```

To add a new strategy (e.g., discount pricing), create another `PricingStrategy` implementation and wire it with configuration, instead of modifying `ProductService`.

---

#### L – Liskov Substitution Principle

**Definition:** Subtypes should be usable **wherever** their base type is expected, **without breaking behavior**.

**In Product API:** If `SpecialProduct` extends `Product`, it should still behave like a normal `Product` when used by the service or controller.

```java
public class Product {
  public BigDecimal getPrice() { ... }
}

public class SpecialProduct extends Product {
  @Override
  public BigDecimal getPrice() {
    // add special logic but still returns a valid price
  }
}
```

Any code using `Product` (e.g., `ProductService`) should work correctly if it receives a `SpecialProduct`.

---

#### I – Interface Segregation Principle

**Definition:** Prefer **small, specific interfaces** instead of large “fat” ones.

**In Product API:** Instead of one repository interface with many unrelated operations, split responsibilities.

```java
public interface ProductReadRepository {
  Optional<Product> findById(Long id);
  List<Product> findByCategory(String category);
}

public interface ProductWriteRepository {
  Product save(Product product);
  void deleteById(Long id);
}
```

Your service depends only on the interfaces it actually needs (read-only vs read-write).

---

#### D – Dependency Inversion Principle

**Definition:** High-level modules should not depend on low-level modules; both should depend on **abstractions**.

**In Product API:** `ProductService` depends on **interfaces**, not concrete classes.

```java
public interface ProductPort {
  Product save(Product product);
  Optional<Product> findById(Long id);
}

@Repository
public class JpaProductRepository implements ProductPort {
  private final SpringDataProductRepository repo;
  // delegate calls to Spring Data repository
}

@Service
public class ProductService {
  private final ProductPort productPort;

  public ProductService(ProductPort productPort) {
    this.productPort = productPort;
  }
}
```

Now you can plug in another implementation (e.g., using `JdbcTemplate` or calling an external service) **without changing** `ProductService`.

**Interview summary:** “In my Product API, I keep controller/service/repository separated (S), use strategies and interfaces for extension (O, D), ensure subclasses behave like parents (L), and keep interfaces small and focused (I).”

---

### 18. What is the Spring bean life cycle in Spring Boot?

**Answer:** A Spring bean goes through well-defined phases from creation to destruction. In Spring Boot the core life cycle is the same as in Spring.

**High-level steps:**

1. **Instantiation** – Container creates the bean (calls constructor).
2. **Populate properties** – Injects dependencies (`@Autowired` / constructor injection / `@Value`).
3. **`BeanNameAware`, `BeanFactoryAware`, etc.** (optional) – If the bean implements these *Aware* interfaces, Spring calls the corresponding methods.
4. **`BeanPostProcessor` (before init)** – Any registered `BeanPostProcessor` can modify the bean before initialization.
5. **Initialization callbacks** – In order:
   - `@PostConstruct` method (if present),
   - `InitializingBean.afterPropertiesSet()` (if implemented),
   - Custom init method specified in config (`initMethod`).
6. **Bean is ready to use** – Application code can now use it.
7. **Context shutdown / bean destruction**:
   - `@PreDestroy` method (if present),
   - `DisposableBean.destroy()` (if implemented),
   - Custom destroy method.

**Simple example with annotations (most common in Spring Boot):**

```java
@Component
public class MyBean {

  public MyBean() {
    System.out.println("Constructor: bean created");
  }

  @PostConstruct
  public void init() {
    System.out.println("@PostConstruct: after dependencies are set");
  }

  @PreDestroy
  public void destroy() {
    System.out.println("@PreDestroy: before bean is destroyed");
  }
}
```

**Interview line:** “In Spring Boot I mostly use `@PostConstruct` for initialization and `@PreDestroy` for cleanup, but under the hood the container still does instantiation → dependency injection → post-processors → init callbacks → destruction on context shutdown.”

---
