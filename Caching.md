To **use Redis as a caching layer in Spring Boot**, you can avoid hitting the database for frequently accessed data like user info, product listings, etc. Here's a **complete example** to show you **how to set up and use Redis caching**.

---

### ✅ **1. Add Dependencies**

In your `pom.xml` (for Maven):

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```

---

### ✅ **2. Configure Redis in `application.properties`**

```properties
spring.cache.type=redis
spring.redis.host=localhost
spring.redis.port=6379
spring.redis.password=ce18046
```

---

### ✅ **3. Enable Caching in Spring Boot**

In your main class:

```java
@SpringBootApplication
@EnableCaching
public class DeviceManagementApplication {
    public static void main(String[] args) {
        SpringApplication.run(DeviceManagementApplication.class, args);
    }
}
```

---

### ✅ **4. Example: Caching a User from the Database**

#### Entity class:

```java
@Entity
public class User {
    @Id
    private Long id;
    private String username;
    private String email;

    // getters and setters
}
```

#### Repository:

```java
public interface UserRepository extends JpaRepository<User, Long> {
}
```

#### Service with Caching:

```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    @Cacheable(value = "userCache", key = "#id")
    public User getUserById(Long id) {
        System.out.println("Fetching user from DB...");
        return userRepository.findById(id).orElse(null);
    }

    @CacheEvict(value = "userCache", key = "#id")
    public void deleteUser(Long id) {
        userRepository.deleteById(id);
    }

    @CachePut(value = "userCache", key = "#user.id")
    public User updateUser(User user) {
        return userRepository.save(user);
    }
}
```

---

### ✅ **5. Use in Controller**

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

    @Autowired
    private UserService userService;

    @GetMapping("/{id}")
    public ResponseEntity<User> getUser(@PathVariable Long id) {
        User user = userService.getUserById(id);
        return ResponseEntity.ok(user);
    }
}
```

---

### 🔍 **Result**

* First request hits the database.
* Next requests for the same user **come from Redis cache**.
* Faster, lighter on DB, and ideal for **high-traffic endpoints**.

---

Would you like an example that also **expires the cache** after a fixed time (TTL)?
