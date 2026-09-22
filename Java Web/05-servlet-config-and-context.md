# ServletConfig & ServletContext

---

## 1. Servlet Configuration

Hardcoding configuration values inside every Servlet creates maintenance problems.

### Problems with hardcoding

- Difficult to change configuration
- Same values may need to be repeated
- Poor maintainability
- Configuration gets mixed with application logic

Servlets provide configuration mechanisms:

1. `ServletConfig` → Servlet-specific configuration
2. `ServletContext` → Application-wide configuration/data

---

## 2. ServletConfig

`ServletConfig` is an object created and maintained by the Servlet Container for **each Servlet**.

### Key point

> One Servlet has its own `ServletConfig` object.

It is mainly used to provide **Servlet-specific initialization parameters**.

### Flow

```text
Web Application Starts
        ↓
Container Reads web.xml
        ↓
Servlet Configuration Found
        ↓
ServletConfig Created
        ↓
Init Parameters Stored
        ↓
init() Called
        ↓
Servlet Uses Configuration
```

### ServletConfig in `web.xml`

Example:

```xml
<servlet>
    <servlet-name>S1</servlet-name>
    <servlet-class>MyServlet</servlet-class>

    <init-param>
        <param-name>user</param-name>
        <param-value>admin</param-value>
    </init-param>
</servlet>
```

### Reading ServletConfig

```java
ServletConfig config = getServletConfig();
String value = config.getInitParameter("user");
```

### ServletConfig Methods

| Method | Purpose |
|---|---|
| `getInitParameter(String name)` | Gets the value of a specific initialization parameter |
| `getInitParameterNames()` | Gets names of all initialization parameters |
| `getServletContext()` | Gets the application-wide `ServletContext` |
| `getServletName()` | Gets the Servlet's configured name |

---

## 3. ServletContext

`ServletContext` is an **application-wide object** created by the Servlet Container.

> It is shared among all Servlets belonging to the same web application.

### Main purpose

- Store application-wide information
- Share data between Servlets
- Access application-level configuration
- Provide access to the application environment

### Flow

```text
Web Application Starts
        ↓
Container creates ServletContext
        ↓
Shared by all Servlets
        ↓
Servlet 1 ─┐
Servlet 2 ─┼──→ Same ServletContext
Servlet 3 ─┘
        ↓
Application Stops
        ↓
ServletContext destroyed
```

### Context Parameters in `web.xml`

Application-wide initialization parameters can be defined using `context-param`.

```xml
<context-param>
    <param-name>appName</param-name>
    <param-value>LearningPath</param-value>
</context-param>
```

### Reading Context Parameter

```java
ServletContext ctx = getServletContext();
String appName = ctx.getInitParameter("appName");
```

### ServletContext Attributes

`ServletContext` also supports attributes for sharing application-level objects/data.

```java
ServletContext ctx = getServletContext();

// Set attribute
ctx.setAttribute("count", 100);

// Get attribute
Object value = ctx.getAttribute("count");

// Remove attribute
ctx.removeAttribute("count");
```

---

## 4. ServletConfig vs ServletContext

| Feature | ServletConfig | ServletContext |
|---|---|---|
| Scope | Servlet-specific | Application-wide |
| Number | One per Servlet | One per web application |
| Shared among Servlets? | No | Yes |
| Main use | Servlet-specific configuration | Global/application-wide configuration and data |
| Init parameters | `init-param` | `context-param` |
| Created by | Servlet Container | Servlet Container |
| Access | `getServletConfig()` | `getServletContext()` |
| Can access Context? | Yes | Application object itself |

### Easy memory trick

```text
ServletConfig  → CONFIG for ONE Servlet
ServletContext → CONTEXT for WHOLE application
```

---

## 5. Init Parameter vs Attribute

| Feature | Init Parameter | Attribute |
|---|---|---|
| Purpose | Configuration | Runtime data/object sharing |
| Usually defined in | `web.xml` | Java code |
| Value | Configuration value (String) | Any object/value (`Object`) |
| Methods | `getInitParameter()` | `setAttribute()`, `getAttribute()` |
| Scope | ServletConfig: one Servlet; Context: whole application | Depends on scope object used |

### Example

```java
// Configuration
String user = config.getInitParameter("user");

// Runtime shared data
ctx.setAttribute("count", 100);
```

---

## 6. When to Use What?

### When to Use ServletConfig?

Use `ServletConfig` when configuration is **specific to one Servlet**.

Examples:
- Servlet-specific username/configuration
- Servlet-specific file path
- Servlet-specific initialization value
- Any setting that should not be shared with every Servlet

### When to Use ServletContext?

Use `ServletContext` when information is required by **multiple Servlets** or belongs to the entire application.

Examples:
- Application name
- Global configuration
- Shared application-level objects
- Common resources/data
- Values that need to be accessed by multiple Servlets

---

## 7. Annotation-Based Servlet Configuration

Instead of configuring every Servlet in `web.xml`, annotations can be used.

The commonly used annotation is:

```java
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) {
        // Servlet logic
    }
}
```

### What does `@WebServlet` do?

It tells the Servlet Container that the class is a Servlet and provides its URL mapping.

Example:

```java
@WebServlet("/login")
```

Then `http://localhost:8080/app/login` can map to `LoginServlet`.

### Why use annotations?

- Less XML configuration
- Easier mapping
- Configuration stays close to the Servlet class
- Cleaner project structure

---

[Previous: Servlet Communication](./04-servlet-communication.md) · [Back to Java Web Index](./README.md) · [Next: Session Management & Cookies](./06-session-management-and-cookies.md)
