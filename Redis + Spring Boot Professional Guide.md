Here’s a **professional developer-level guide** for learning and using **Redis** with **Spring Boot**, which you can save in a `.md` file for your GitHub:

---

# 🚀 Redis + Spring Boot Professional Guide

## 📌 What is Redis?

Redis is an **in-memory key-value data store**, often used for:

* Caching
* Session storage
* Rate limiting
* Message brokering (Pub/Sub)
* Real-time analytics

---

## ⚙️ 1. Install Redis on Ubuntu

```bash
sudo apt update
sudo apt install redis -y
sudo systemctl enable redis-server
sudo systemctl start redis-server
```

### 🔐 Secure Redis (Optional)

Edit `/etc/redis/redis.conf`:

```ini
requirepass ce18046
bind 0.0.0.0
supervised systemd
daemonize yes
```

Restart Redis:

```bash
sudo systemctl restart redis-server
```

Test:

```bash
redis-cli -a ce18046 ping
# PONG
```

---

## 📦 2. Add Redis to Spring Boot Project

### `pom.xml`:

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

---

## 🛠 3. Configure `application.properties`

```properties
spring.redis.host=localhost
spring.redis.port=6379
spring.redis.password=ce18046
spring.cache.type=redis
```

---

## 🧠 4. Example: Caching with Redis

### 📁 `RedisConfig.java` (Optional for Custom Config)

```java
@Configuration
public class RedisConfig {
    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(factory);
        return template;
    }
}
```

---

### 📁 `UserService.java`

```java
@Service
public class UserService {
    private final Map<String, String> fakeDb = Map.of("1", "Alice", "2", "Bob");

    @Cacheable(value = "userCache", key = "#id")
    public String getUserById(String id) {
        System.out.println("Fetching from DB...");
        return fakeDb.get(id);
    }
}
```

---

### 📁 `UserController.java`

```java
@RestController
public class UserController {
    @Autowired private UserService userService;

    @GetMapping("/user/{id}")
    public String getUser(@PathVariable String id) {
        return userService.getUserById(id);
    }
}
```

---

## 🧪 Test Redis Cache

Run the app:

```bash
curl http://localhost:8080/user/1
# First call → DB
curl http://localhost:8080/user/1
# Second call → Redis
```

---

## 💡 Tips for Professional Developers

| Feature      | Usage                                                 |
| ------------ | ----------------------------------------------------- |
| 🔁 TTL       | Use `@Cacheable` with `@CacheEvict`, or configure TTL |
| 🧵 Pub/Sub   | Redis messaging between services                      |
| 📊 Analytics | Use Redis for real-time counters                      |
| ⏱ Rate limit | Redis + Lua scripts or bucket pattern                 |
| 📚 Sessions  | Spring Session backed by Redis                        |

---

## 📚 Resources

* [Redis Official Docs](https://redis.io/docs/)
* [Spring Redis Guide](https://docs.spring.io/spring-data/redis/docs/current/reference/html/)
* [Awesome Redis GitHub](https://github.com/redis/redis)

---

Would you like me to generate a complete `README.md` file for GitHub with this content, including project structure and usage examples?
