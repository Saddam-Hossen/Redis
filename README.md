# Redis
---

### 📄 `install-redis.md` – Redis Installation on Ubuntu

````markdown
# 🧠 Install Redis on Ubuntu (Linux)

This guide explains how to install and verify Redis on Ubuntu-based systems.

---

## 📥 Step 1: Update Package Lists

```bash
sudo apt update
````

---

## 📦 Step 2: Install Redis

```bash
sudo apt install redis-server
```

---

## ⚙️ Step 3: Configure Redis (Optional but Recommended)

Edit the Redis config file:

```bash
sudo nano /etc/redis/redis.conf
```

* To enable password authentication, find and set:

  ```ini
  requirepass YourStrongPasswordHere
  ```

* To allow remote access (if needed):

  ```ini
  bind 0.0.0.0
  ```

Restart Redis:

```bash
sudo systemctl restart redis
```

---

## ▶️ Step 4: Enable Redis to Start on Boot

```bash
sudo systemctl enable redis
```

---

## ✅ Step 5: Verify Redis is Running

```bash
sudo systemctl status redis
```

You should see `Active: active (running)`.

---

## 🧪 Step 6: Test Redis with CLI

Start the Redis CLI:

```bash
redis-cli
```

Type:

```bash
ping
```

You should see:

```
PONG
```

---

## 📚 References

* [Official Redis Docs](https://redis.io/docs/)
* [Spring Data Redis Guide](https://spring.io/guides/gs/messaging-redis/)

---

> Created by Saddamnvn | [GitHub](https://github.com/saddamnvn)

```

---

### ✅ Next Step

Once you've created your `install-redis.md`, you can:

- Commit it to your GitHub repository
- Link it from your main `README.md`
- Share it with your team or contributors

```
