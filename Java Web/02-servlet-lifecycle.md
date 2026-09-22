# 🔄 Servlet Lifecycle — Deep Dive

> **Summary:** Servlet ke 5 lifecycle phases (`Loading`, `init()`, `service()`, `destroy()`, `GC`), method signatures, `load-on-startup`, aur thread-safety implications.

---

## 1. Servlet Lifecycle Overview

Servlet ka lifecycle **developer nahi, balki Servlet Container (Tomcat)** manage karta hai. Ek Servlet object create hone se lekar destroy hone tak 5 major steps se guzarta hai:

```text
               ┌────────────────────────────────────────────────────────┐
               │              1. Loading & Instantiation                │
               │         (Class loader loads .class, creates object)     │
               └───────────────────────────┬────────────────────────────┘
                                           │ (Called ONCE)
                                           ▼
               ┌────────────────────────────────────────────────────────┐
               │                 2. init(ServletConfig)                 │
               │            (One-time initialization & config)          │
               └───────────────────────────┬────────────────────────────┘
                                           │
                                           ▼
               ┌────────────────────────────────────────────────────────┐
         ┌────►│             3. service(request, response)              │◄────┐
         │     │         (Called EVERY TIME a request arrives)          │     │
         └─────┴───────────────────────────┬────────────────────────────┴─────┘
                       (Request Thread 1, Request Thread 2...)
                                           │ (Container stops / unloads)
                                           ▼
               ┌────────────────────────────────────────────────────────┐
               │                      4. destroy()                      │
               │         (Clean-up: close DB connections, files)        │
               └───────────────────────────┬────────────────────────────┘
                                           │ (Called ONCE)
                                           ▼
               ┌────────────────────────────────────────────────────────┐
               │                 5. Garbage Collection                  │
               │        (JVM GC cleans up unreachable servlet memory)   │
               └────────────────────────────────────────────────────────┘
```

---

## 2. The 5 Lifecycle Steps

### Step 1: Loading & Instantiation
- Container Servlet class ki `.class` file ko memory me load karta hai.
- Container no-arg constructor call karke **single instance** instantiate karta hai.
- **Kab hota hai?** By default, jab **first request** aati hai (Lazy loading), ya fir application startup par (Pre-loading via `load-on-startup`).

---

### Step 2: Initialization — `init()`
- Instance banne ke baad container turant `init()` method call karta hai.
- Ye method poore lifecycle me **sirf ek baar** execute hota hai.
- Purpose: Database connection initialize karna, heavy resources load karna, ya `ServletConfig` parameters read karna.

#### Method Signature:
```java
public void init(ServletConfig config) throws ServletException
```
Ya `GenericServlet` me simplified version:
```java
public void init() throws ServletException
```

---

### Step 3: Request Handling — `service()`
- Har incoming request ke liye container ek naya thread create karta hai aur `service()` method call karta hai.
- Ye method **har request par baar-baar call hota hai**.
- Request type (`GET`, `POST`, `PUT`, `DELETE`) inspect karke appropriate handler ko delegate karta hai (e.g., `doGet()`, `doPost()`).

#### Method Signature:
```java
public void service(ServletRequest req, ServletResponse res) 
        throws ServletException, IOException
```

---

### Step 4: Destruction — `destroy()`
- Jab application un-deploy hoti hai, ya web server stop/restart hota hai, container `destroy()` method call karta hai.
- Ye method bhi poore lifecycle me **sirf ek baar** execute hota hai.
- Purpose: Open resources clean karna (closing database connections, stopping background threads, flushing streams).

#### Method Signature:
```java
public void destroy()
```

---

### Step 5: Garbage Collection (GC)
- `destroy()` complete hone ke baad, Servlet object container ke references se detach ho jata hai.
- JVM ka Garbage Collector jab chahe is object ko heap se delete karke memory reclaim karta hai.

---

## 3. 🚀 Lazy Loading vs Pre-Loading (`<load-on-startup>`)

By default, Servlet ka object pehli request aane par banta hai (**Lazy Loading**). Isse pehle user ko thoda delay (first-request lag) face karna pad sakta hai.

Agar aap chahte ho ki server start hote hi Servlet instantiate ho jaye, toh **`load-on-startup`** use karo:

### In `web.xml`:
```xml
<servlet>
    <servlet-name>MyStartupServlet</servlet-name>
    <servlet-class>com.example.MyStartupServlet</servlet-class>
    <load-on-startup>1</load-on-startup> <!-- Positive value = Load at server startup -->
</servlet>
```

### In Annotations (`@WebServlet`):
```java
@WebServlet(urlPatterns = "/home", loadOnStartup = 1)
public class HomeServlet extends HttpServlet {
    // ...
}
```

> 💡 **Priority Rule:** Lower integer value = Higher priority! `<load-on-startup>1</load-on-startup>` pehle load hoga `<load-on-startup>2</load-on-startup>` se.

---

## 4. ⚠️ Servlet Thread Safety (Golden Concept)

Interviewers ka favorite question: *"Are Servlets Thread-Safe?"*

> 🚨 **Answer:** By default, **Servlets are NOT thread-safe!**

### Kyu?
- Container Servlet ka **sirf ek object** create karta hai.
- 100 concurrent requests aane par **100 threads usi ek object ke methods ko ek sath execute karte hain**.
- Agar aap Servlet me **instance variable** declare karoge, toh multiple threads us variable ko ek sath modify karenge aur **Race Condition / Data Inconsistency** create hogi!

```java
public class UnsafeServlet extends HttpServlet {
    private int counter = 0; // ⚠️ DANGER: Shared across all client threads!

    protected void doGet(HttpServletRequest req, HttpServletResponse res) {
        counter++; // NOT ATOMIC! Data race condition!
    }
}
```

### 🛡️ How to ensure Thread Safety?
1. **Never use instance variables for user state.** Always use local variables inside `doGet()` / `doPost()` (local variables stack memory me thread-isolated hote hain).
2. Agar instance variable zaroori ho, toh `AtomicInteger` ya synchronization use karo.

---

## 🧠 Interview Quick Traps

| Question / Trap | Correct Answer |
|-----------------|----------------|
| `init()` kitni baar call hota hai? | Pure lifecycle me **sirf ek baar**. |
| `service()` kitni baar call hota hai? | **Har incoming client request par**. |
| `destroy()` call hone se object memory se delete ho jata hai? | ❌ Nahi, cleanup code chalta hai; memory reclamation JVM Garbage Collector karta hai. |
| Developer direct `init()` call kar sakta hai? | Technically yes, lekin container lifecycle break ho jayegi. Developer ko kabhi manually lifecycle methods call nahi karne chahiye. |
| Negative `load-on-startup` ka kya matlab hai? | Container pehli request aane par hi Servlet load karega (Lazy loading). |

---

[⬅️ Previous: Servlet Basics](./01-servlet-basics.md) · [📖 Back to Java Web Index](./README.md) · [Next → HttpServlet & Requests ➡️](./03-httpservlet-and-request-response.md)
