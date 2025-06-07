Great! Let’s implement **API rate limiting per IP address** using **Redis** in a Spring Boot project. This helps prevent abuse like excessive login attempts or flooding API endpoints.

---

## ✅ Why Use Redis for Rate Limiting?

* **Fast**: In-memory key-value store (great for real-time checks)
* **Atomic operations**: Perfect for counters like login attempts
* **Supports expiration**: Auto-resets counters after a time period

---

## 🧠 Strategy

We’ll use:

* `INCR` to count how many times a given IP accessed an endpoint
* `EXPIRE` to reset the count after a time window

---

## 🧱 RedisRateLimiter Class

```java
package com.device.DeviceManagement.util;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.stereotype.Component;

import java.time.Duration;

@Component
public class RedisRateLimiter {

    private final StringRedisTemplate redisTemplate;
    private static final int MAX_ATTEMPTS = 5;
    private static final Duration BLOCK_TIME = Duration.ofMinutes(5);

    @Autowired
    public RedisRateLimiter(StringRedisTemplate redisTemplate) {
        this.redisTemplate = redisTemplate;
    }

    public boolean isBlocked(String ip) {
        String key = "login:attempts:" + ip;
        String value = redisTemplate.opsForValue().get(key);
        if (value == null) return false;

        int attempts = Integer.parseInt(value);
        return attempts >= MAX_ATTEMPTS;
    }

    public void recordFailedAttempt(String ip) {
        String key = "login:attempts:" + ip;
        Long count = redisTemplate.opsForValue().increment(key);
        if (count == 1) {
            redisTemplate.expire(key, BLOCK_TIME);
        }
    }

    public void resetAttempts(String ip) {
        String key = "login:attempts:" + ip;
        redisTemplate.delete(key);
    }
}
```

---

## 🧪 Example Use in Login Controller

```java
@PostMapping("/home")
public String login(@RequestParam String username,
                    @RequestParam String password,
                    HttpServletRequest request,
                    Model model,
                    HttpSession session) {
    String clientIP = request.getRemoteAddr();

    if (rateLimiter.isBlocked(clientIP)) {
        model.addAttribute("error", "Too many failed attempts. Try again in 5 minutes.");
        return "login";
    }

    if (username.equals("admin") && password.equals("admin123")) {
        session.setAttribute("loggedInUser", username);
        rateLimiter.resetAttempts(clientIP); // reset on success
        return "home";
    } else {
        rateLimiter.recordFailedAttempt(clientIP); // track failure
        model.addAttribute("error", "Invalid username or password");
        return "login";
    }
}
```

---

## 🧰 application.properties (if you use Redis password)

```properties
spring.redis.host=localhost
spring.redis.port=6379
spring.redis.password=ce18046
```

---

## ✅ What You Gain

| Feature           | Benefit                      |
| ----------------- | ---------------------------- |
| Per-IP throttling | Prevents brute-force attacks |
| Expiring counters | Self-resets after timeout    |
| Redis-backed      | Fast, centralized, scalable  |

---

Would you like to extend this to:

* Limit API requests to specific endpoints?
* Include Redis-based global bans?
* Add real-time admin monitoring?
