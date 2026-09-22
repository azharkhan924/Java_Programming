# Servlet Life Cycle

---

## 1. Servlet Life Cycle Overview

Servlet ka **life cycle Servlet Container** (e.g. Tomcat) manage karta hai.

### Main Life Cycle Methods

1. `init()`
2. `service()`
3. `destroy()`

Overall flow:

```text
Loading & Instantiation
 ↓
 init()
 ↓
Request Handling → service()
 ↓
 destroy()
 ↓
Garbage Collection
```

```text
Servlet Loading
 ↓
 init()
 ↓
 service()
 ↓
 service()
 ↓
 service()
 ↓
 destroy()
```

---

## 2. Step-by-Step Life Cycle

### Step 1: Loading & Instantiation

- Servlet Container servlet class ko **load** karta hai.
- Class load hone ke baad servlet ka object create hota hai.
- Normally servlet ka **one instance** create karke multiple requests handle ki jaati hain.
- Container servlet ko initialize karne ke liye `init()` call karta hai.

> **Important:** Servlet instance ko multiple concurrent requests mil sakti hain, isliye servlet instance variables ko thread-safe rakhna important hai.

---

### Step 2: Initialization – `init()`

`init()` servlet ko initialize karne ke liye **sirf ek baar** call hota hai, successful initialization ke baad.

Typical work:
- Database connection / resources initialize karna
- Configuration load karna
- Required resources prepare karna

#### Signature

```java
public void init() throws ServletException {
 // initialization code
}
```

Servlet API ka alternate form:

```java
public void init(ServletConfig config) throws ServletException {
 super.init(config);
}
```

---

### Step 3: Request Handling – `service()`

Jab client ki request servlet ke paas aati hai, container `service()` method ko invoke karta hai.

For `HttpServlet`, `service()` HTTP request method ke according appropriate method ko dispatch karta hai:

```text
HTTP Request
 ↓
service()
 ↓
 ┌───────────────┐
 │ GET → doGet() │
 │ POST → doPost()│
 │ PUT → ... │
 │ DELETE → ... │
 └───────────────┘
```

#### Basic signature

```java
public void service(ServletRequest req, ServletResponse res)
 throws ServletException, IOException {
 // request handling
}
```

Usually application code mein `HttpServlet` ke saath hum directly:

```java
protected void doGet(HttpServletRequest req,
 HttpServletResponse resp)
 throws ServletException, IOException {
 // GET request
}

protected void doPost(HttpServletRequest req,
 HttpServletResponse resp)
 throws ServletException, IOException {
 // POST request
}
```

---

### Step 4: Destruction – `destroy()`

Servlet ko container se remove/unload karne se pehle container `destroy()` call karta hai.

Typical cleanup:
- Database connection close karna
- Files/resources close karna
- Other resources release karna

#### Signature

```java
public void destroy() {
 // cleanup code
}
```

---

### Step 5: Garbage Collection

`destroy()` ke baad servlet instance container ke use mein nahi rehta. Agar object ke paas koi reachable reference nahi hai, to JVM ka **Garbage Collector** us object ko later reclaim kar sakta hai.

> **Important:** `destroy()` directly garbage collection nahi karta.

---

## 3. Servlet Life Cycle – One-Line Revision

```text
Load Class → Create Object → init() [once]
→ service() [for requests]
→ destroy() [once]
→ eligible for Garbage Collection
```

---

## 4. Key Interview Points

1. **Who manages the servlet life cycle?** 
 Servlet Container (e.g. Apache Tomcat).

2. **Which method is called only once for initialization?** 
 `init()`.

3. **Which method handles requests?** 
 `service()`.

4. **Which method is called before servlet destruction?** 
 `destroy()`.

5. **Does `destroy()` perform garbage collection?** 
 No. It performs cleanup; JVM Garbage Collector reclaims the object later when it is unreferenced.

---

[Previous: Servlet Basics & Architecture](./01-servlet-basics.md) · [Back to Java Web Index](./README.md) · [Next: HttpServlet, Request & Response](./03-httpservlet-and-request-response.md)
