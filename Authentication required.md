You're getting the error:

```
(error) NOAUTH Authentication required.
```

This means Redis is now password protected (`requirepass ce18046`), and you must authenticate before issuing commands.

---

### ✅ To Authenticate in `redis-cli`

At the `redis-cli` prompt, type:

```bash
auth ce18046
```

Then try:

```bash
ping
```

You should see:

```
PONG
```

---

### 🧠 Explanation

* `auth ce18046`: Tells Redis to authenticate your CLI session using the password.
* `ce18046` by itself was interpreted as a **command**, which caused the error:

  > `(error) ERR unknown command 'ce18046'`

---

### ✅ For Persistent Access in `redis-cli`

You can connect and authenticate in one command:

```bash
redis-cli -a ce18046
```

> 🔒 Be careful when using `-a` in scripts or history—it exposes the password.

---

If you're now ready to connect from Spring Boot or test Redis operations, let me know!
