# Java Servlet Basics & Architecture

---

## 1. What is a Java Servlet?

A **Servlet** is a Java program that runs inside a **Servlet Container** on a web server and is used to handle client requests and generate responses.

### Simple Definition

> Servlet acts as a **middleman between the client/browser and the backend/application logic**.

```text
Browser → Web Server / Servlet Container → Servlet → Database
 ↓
 Generate Response
 ↓
Browser ←────────────── HTTP Response ────────┘
```

### What does a Servlet do?

- Receives form data
- Processes client requests
- Connects to the database
- Generates dynamic HTML responses
- Handles sessions
- Performs authentication
- Communicates with other servlets/resources

---

## 2. Why Servlets?

Before Servlets, **CGI (Common Gateway Interface)** was commonly used for server-side request processing.

### Problems with CGI

- Creates a **new process for every request**
- High memory consumption
- Process creation makes it slower
- Does not scale efficiently for a large number of requests

### How Servlets solve these problems

Servlets generally use a **thread-per-request model** within the container instead of creating a new OS process for every request.

```text
CGI Model:
Request 1 ──► [OS Process 1 (Heavy)]
Request 2 ──► [OS Process 2 (Heavy)]
Request 3 ──► [OS Process 3 (Heavy)]

Servlet Model:
 ┌──► Thread 1 (Lightweight)
Container Process ┼──► Thread 2 (Lightweight) ──► Shares Same Memory & Servlet Instance
 └──► Thread 3 (Lightweight)
```

### Advantages of Servlets

- Fast and efficient
- Uses threads
- Platform independent because Java is platform independent
- Secure when properly designed and deployed
- Scalable
- Supports session management
- Supports database connectivity

---

## 3. Comparison: CGI vs Java Servlet

| Feature | CGI (Common Gateway Interface) | Java Servlet |
|---|---|---|
| Execution Model | Separate OS process for every request | Lightweight thread for every request |
| Performance | Slower due to process creation overhead | Fast and efficient |
| Memory Usage | High memory consumption | Shared memory, lower overhead |
| Scalability | Poor scalability under high load | Highly scalable |
| Platform Dependency | Often platform dependent | Platform independent |
| Security | System-level process security concerns | Container-managed security sandbox |

---

## 4. Web Server vs Servlet Container

### Web Server

A **web server** receives and handles HTTP requests and sends HTTP responses (primarily static content like HTML, CSS, JS, images).

### Servlet Container

A **Servlet Container** (also known as a Web Container) is the environment that manages Servlets.

#### Main responsibilities:

- Creates and manages Servlet objects
- Manages the Servlet lifecycle
- Maps URLs to Servlets
- Creates/manages request and response objects
- Handles request processing and threading
- Calls lifecycle methods such as `init()`, `service()`, and `destroy()`

#### Example:

**Apache Tomcat** provides a Servlet Container and supports Java web applications.

> Java Servlets need a Servlet Container such as Tomcat to run as web components.

---

## 5. Summary & Key Points

- A Servlet is a server-side Java component managed by a Servlet Container.
- Unlike CGI which creates a process per request, Servlets use a thread-per-request model.
- By default, the Servlet Container creates a single instance of each Servlet and handles concurrent requests using threads.
- Web Servers handle HTTP communication; Servlet Containers manage the execution and lifecycle of Servlets and JSPs.

---

[Back to Java Web Index](./README.md) · [Next: Servlet Lifecycle](./02-servlet-lifecycle.md)
