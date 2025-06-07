
---

# RedisConfig Custom Configuration in Spring Boot

This document explains the purpose, setup, and usage of a **custom Redis configuration** in a Spring Boot application using Spring Data Redis.

---

## Overview

Spring Boot provides auto-configuration for Redis, but when you want more control over serialization or connection settings, a custom configuration class is recommended.

The custom `RedisConfig` class defines a `RedisTemplate` bean with:

* **String serialization for keys** (human-readable keys)
* **JSON serialization for values** using `GenericJackson2JsonRedisSerializer` (for interoperability and readability)
* Proper initialization and bean management

---

## Why Customize RedisTemplate?

* **Control over serialization:** Default Redis serialization uses Java serialization, which is not human-readable and can cause compatibility issues.
* **Performance & interoperability:** JSON serialization helps to debug stored data easily and supports different clients/languages.
* **Avoid deprecated APIs:** Newer Spring versions deprecate some methods; this config uses recommended serializers.

---

## RedisConfig.java

```java
package com.device.DeviceManagement.Config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.GenericJackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.StringRedisSerializer;

@Configuration
public class RedisConfig {

    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(factory);

        // Use String serializer for keys and hash keys
        StringRedisSerializer stringSerializer = new StringRedisSerializer();
        template.setKeySerializer(stringSerializer);
        template.setHashKeySerializer(stringSerializer);

        // Use JSON serializer for values and hash values
        GenericJackson2JsonRedisSerializer jsonSerializer = new GenericJackson2JsonRedisSerializer();
        template.setValueSerializer(jsonSerializer);
        template.setHashValueSerializer(jsonSerializer);

        template.afterPropertiesSet();
        return template;
    }
}
```

---

## How to Use

Inject `RedisTemplate<String, Object>` into your services or repositories to interact with Redis.

Example:

```java
@Service
public class CacheService {

    private final RedisTemplate<String, Object> redisTemplate;

    public CacheService(RedisTemplate<String, Object> redisTemplate) {
        this.redisTemplate = redisTemplate;
    }

    public void saveData(String key, Object value) {
        redisTemplate.opsForValue().set(key, value);
    }

    public Object getData(String key) {
        return redisTemplate.opsForValue().get(key);
    }
}
```

---

## Notes

* Ensure Redis server is running and accessible.
* Customize serializers if you want to use different serialization mechanisms.
* This config is compatible with Spring Boot 3.x and above.
* If you want to use caching annotations (`@Cacheable`), configure `CacheManager` separately.

---

## References

* [Spring Data Redis Reference](https://docs.spring.io/spring-data/redis/docs/current/reference/html/#redis.templates)
* [Spring Boot Redis Auto-Configuration](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-redis)
* [GenericJackson2JsonRedisSerializer JavaDoc](https://docs.spring.io/spring-data/redis/docs/current/api/org/springframework/data/redis/serializer/GenericJackson2JsonRedisSerializer.html)

---
