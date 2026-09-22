# 🚀 Servlet Basics & Architecture

> **Summary:** Java Servlet kya hai, CGI vs Servlet ka historical context, Web Server vs Servlet Container ka farq, aur Servlet architecture flow.

---

## 1. Java Servlet Kya Hai?

**Servlet** ek server-side Java program hai jo **Servlet Container (Web Container)** ke andar run hota hai. Iska main kaam client (browser) ki **HTTP requests ko receive karna, process karna aur dynamic HTTP responses generate karna** hota hai.

### 🎯 Simple Definition
> Servlet acts as a **middleman between the client/browser and the backend business logic/database**.

### 🏗️ Architecture Flow
```text
┌─────────────────┐       HTTP Request        ┌────────────────────────────────────────────────────────┐
│                 ├──────────────────────────►│ Web Server / Servlet Container (e.g. Apache Tomcat)    │
│  Client Browser │                           │  ┌───────────────┐     ┌───────────┐    ┌────────────┐ │
│                 │◄──────────────────────────┤  │ Request/Resp  │────►│  Servlet  │───►│  Database  │ │
└─────────────────┘       HTTP Response       │  └───────────────┘     └───────────┘    └────────────┘ │
                                              └────────────────────────────────────────────────────────┘
```

### 🛠️ Servlet Ke Main Kaam:
- Client ke form data (parameters) ko read karna
- User authentication & authorization check karna
- Business logic execute karna aur Database (JDBC) se connect karna
- Dynamic HTML / JSON response generate karna
- Sessions aur Cookies handle karna

---

## 2. Why Servlets? (CGI vs Servlet)

Servlets aane se pehle, server-side web development ke liye **CGI (Common Gateway Interface)** use hota tha (written in C, C++, Perl).

### ❌ Problems with CGI
1. **New Process per Request:** Har client request ke liye OS ek **naya process** create karta tha.
2. **High Memory Consumption:** 1000 requests = 1000 separate processes in RAM!
3. **Slow Response Time:** OS process creation time-consuming hota hai.
4. **Poor Scalability:** High traffic aate hi server crash ho jata tha.

### ✅ How Servlets Solve This (Thread Model)
Servlet Container ek single process me chalta hai, aur har incoming request ke liye ek **chhota lightweight Thread** allocate karta hai (**Thread-per-request model**).

```text
CGI Model:
Request 1 ──► [OS Process 1 (Heavy)]
Request 2 ──► [OS Process 2 (Heavy)]
Request 3 ──► [OS Process 3 (Heavy)]

Servlet Model:
                  ┌──► Thread 1 (Lightweight)
Container Process ┼──► Thread 2 (Lightweight)  ──► Shares Same Memory & Servlet Instance!
                  └──► Thread 3 (Lightweight)
```

---

## 3. ⚖️ Comparison: CGI vs Java Servlet

| Feature | CGI (Common Gateway Interface) | Java Servlet |
|---------|--------------------------------|--------------|
| **Execution Model** | Har request ke liye alag **OS Process** | Har request ke liye alag **Thread** |
| **Performance** | Slow (Heavy process overhead) | Fast & efficient (Lightweight threads) |
| **Memory Usage** | Bahut zyada memory consume karta hai | Shared memory, highly optimized |
| **Scalability** | Poor (heavy traffic handle nahi kar pata) | Highly scalable |
| **Platform Dependency** | Platform dependent (compiled C/Perl scripts) | Platform independent (WORA — Java bytecode) |
| **Security** | System-level access se security risk | JVM Sandbox & Container security |

---

## 4. Web Server vs Servlet Container

Web development me in do terms ka difference samajhna bohot zaroori hai:

```text
┌───────────────────────────────── Web Server ──────────────────────────────────┐
│ Handles static content (HTML, CSS, JS, Images). Listens to HTTP on Port 80.  │
│ Examples: Apache HTTP Server, Nginx                                           │
│                                                                               │
│      ┌────────────────────── Servlet Container ───────────────────────┐       │
│      │ Also known as Web Container. Executes Servlets & JSPs.        │       │
│      │ Manages Lifecycle, Multithreading, URL Mapping.                │       │
│      │ Examples: Apache Tomcat, Jetty, WildFly, GlassFish             │       │
│      └────────────────────────────────────────────────────────────────┘       │
└───────────────────────────────────────────────────────────────────────────────┘
```

### 📋 Servlet Container Ki Responsibilities:
1. **Life Cycle Management:** Servlet ka object banana (`init`), execute karna (`service`), aur destroy karna (`destroy`).
2. **Multithreading Support:** Har request ke liye automatically threads manage karna aur thread pool maintain karna.
3. **URL Mapping:** Request URL ke base par sahi Servlet identify karna (`web.xml` ya annotations ke according).
4. **Request & Response Creation:** Raw HTTP packets ko parse karke Java objects (`HttpServletRequest` aur `HttpServletResponse`) banakar Servlet ko pass karna.
5. **Security & Session Management:** Sessions maintain karna aur unauthorized access restrict karna.

---

## 5. Advantages of Java Servlets

1. **High Performance:** Thread-per-request model ki wajah se rapid response time.
2. **Platform Independent:** Ek baar likho, kisi bhi OS (Linux, Windows, macOS) aur kisi bhi compliant container (Tomcat, Jetty) par chalao.
3. **Robust & Type-Safe:** Java ka strong type-checking, exception handling, aur automatic garbage collection.
4. **Extensible:** Huge ecosystem of Java libraries, JDBC drivers, Spring, and Hibernate.

---

## 🧠 Interview Quick Traps

| Trap | Answer |
|------|--------|
| Kya Servlet container har request ke liye naya Servlet object banata hai? | ❌ **Nahi!** By default Servlet ka sirf **EK hi instance** banta hai; requests threads ke through handle hoti hain. |
| Tomcat Web Server hai ya Servlet Container? | Tomcat primarily ek **Servlet Container** hai jisme built-in HTTP Web Server bhi include hota hai. |
| CGI me process banta tha ya thread? | CGI me naya **OS Process** banta tha. |
| Servlet multithreading code developer ko manually likhna padta hai? | ❌ Nahi, Container automatically background threads manage karta hai. |

---

[📖 Back to Java Web Index](./README.md) · [Next → Servlet Lifecycle ➡️](./02-servlet-lifecycle.md)
