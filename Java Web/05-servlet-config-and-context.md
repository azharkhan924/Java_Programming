# ⚙️ ServletConfig, ServletContext & Annotations

> **Summary:** `ServletConfig` (per-servlet config) vs `ServletContext` (app-wide shared state), `<init-param>` vs `<context-param>`, Attributes vs Parameters, aur modern `@WebServlet` annotations.

---

## 1. Why External Configuration?

Agar database credentials, API keys, ya file paths ko Java class ke andar hardcode kar diya jaye:
- Har chhota change karne ke liye poora project recompile aur redeploy karna padega.
- Same database URL 10 alag Servlets me duplicate hoga.

Java Servlet do configuration objects provide karta hai:
1. **`ServletConfig`** — Sirf ek specific Servlet ke liye private settings.
2. **`ServletContext`** — Poori web application ke saare Servlets ke liye shared global settings.

---

## 2. `ServletConfig` (Servlet-Specific)

Har Servlet ke paas apna khud ka dedicated `ServletConfig` object hota hai jo Servlet Container instantiate karte waqt banata hai.

### XML Configuration (`web.xml`):
```xml
<servlet>
    <servlet-name>LoginServlet</servlet-name>
    <servlet-class>com.example.LoginServlet</servlet-class>
    
    <!-- Init Parameter for this Servlet only -->
    <init-param>
        <param-name>maxLoginAttempts</param-name>
        <param-value>5</param-value>
    </init-param>
</servlet>
```

### Accessing in Java:
```java
public class LoginServlet extends HttpServlet {
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) {
        ServletConfig config = getServletConfig();
        String maxAttempts = config.getInitParameter("maxLoginAttempts"); // "5"
        String servletName = config.getServletName(); // "LoginServlet"
    }
}
```

---

## 3. `ServletContext` (Application-Wide Shared)

Jab web application server par deploy hokar start hoti hai, container **ek single `ServletContext` object** banata hai. Ye object application band hone tak zinda rehta hai aur **application ke sabhi Servlets aur JSPs ke beech share hota hai**.

```text
                 Web Application (myapp.war) Starts
                                  │
                                  ▼
                    ┌───────────────────────────┐
                    │  Single ServletContext    │◄─── Shared by ALL Servlets!
                    │ (Global Config & State)   │
                    └─────────────┬─────────────┘
                                  │
         ┌────────────────────────┼────────────────────────┐
         ▼                        ▼                        ▼
    Servlet 1                Servlet 2                Servlet 3
(LoginServlet)            (PaymentServlet)          (AdminServlet)
```

### XML Configuration (`web.xml`):
```xml
<!-- Available to ALL Servlets in the application -->
<context-param>
    <param-name>companyEmail</param-name>
    <param-value>support@example.com</param-value>
</context-param>
```

### Accessing in Java:
```java
ServletContext context = getServletContext();
// Or via config: getServletConfig().getServletContext();

String email = context.getInitParameter("companyEmail");
```

---

## 4. `ServletContext` Attributes (Sharing Objects at Runtime)

`ServletContext` sirf string parameters read karne ke liye nahi, balki runtime par **Java objects share karne** ke liye bhi use hota hai:

```java
// Servlet 1: Hit counter update ya DB Pool save karna
ServletContext context = getServletContext();
context.setAttribute("appVisitorCount", 1050);

// Servlet 2: Kisi dusre Servlet me access karna
ServletContext context = getServletContext();
Integer visitors = (Integer) context.getAttribute("appVisitorCount");

// Attribute remove karna
context.removeAttribute("appVisitorCount");
```

---

## 5. ⚖️ Comparison Matrix: ServletConfig vs ServletContext

| Feature | `ServletConfig` | `ServletContext` |
|---------|-----------------|------------------|
| **Scope** | Single Servlet ke liye private | Poori web application ke liye shared |
| **Quantity** | One per Servlet | **Only ONE per Web Application** |
| **Configuration Tag** | `<init-param>` inside `<servlet>` | `<context-param>` under `<web-app>` |
| **Getter Method** | `getServletConfig()` | `getServletContext()` |
| **Runtime Attributes?** | ❌ Attributes support nahi karta | ✅ Supports `setAttribute()` / `getAttribute()` |
| **Lifecycle** | Servlet ke sath banta aur destroy hota hai | App start par banta hai, app stop par destroy hota hai |

### 🧠 Memory Trick:
```text
ServletConfig  = CONFIG for ONE Servlet
ServletContext = CONTEXT for WHOLE Application
```

---

## 6. ⚖️ Init Parameter vs Attribute

| Feature | Init Parameter | Attribute |
|---------|----------------|-----------|
| **Kahan define hota hai?** | `web.xml` ya `@WebServlet` annotations me | Java code me runtime par (`setAttribute`) |
| **Data Type** | Sirf **`String`** hota hai | **`Object`** (String, List, Model, Connection, etc.) |
| **Modifiable?** | Read-Only (Runtime par change nahi ho sakta) | Read-Write (Modify / Delete kiya ja sakta hai) |
| **Methods** | `getInitParameter("name")` | `setAttribute()`, `getAttribute()`, `removeAttribute()` |

---

## 7. Modern Annotation-Based Configuration (`@WebServlet`)

Java EE 6+ (Servlet 3.0+) se `web.xml` me heavy XML configuration likhne ki zaroorat nahi hai. Hum seedha Java class ke upar **`@WebServlet`** annotation use karte hain:

```java
import jakarta.servlet.annotation.WebInitParam;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;

@WebServlet(
    name = "ReportServlet",
    urlPatterns = {"/reports", "/generate-report"},
    loadOnStartup = 1,
    initParams = {
        @WebInitParam(name = "exportFormat", value = "PDF"),
        @WebInitParam(name = "pageSize", value = "A4")
    }
)
public class ReportServlet extends HttpServlet {
    // Clean & self-contained configuration!
}
```

### Advantages of `@WebServlet`:
- No XML clutter in `web.xml`
- Configuration aur code ek hi jagah rehte hain
- Fast maintenance & refactoring

---

## 🧠 Interview Quick Traps

| Trap | Answer |
|------|--------|
| Ek Servlet doosre Servlet ke `ServletConfig` ko read kar sakta hai? | ❌ Nahi! `ServletConfig` strictly private hota hai. |
| Application-wide hit counter maintain karne ke liye kya use karenge? | `ServletContext.setAttribute()`. |
| `web.xml` me `context-param` kis tag ke andar aata hai? | Root `<web-app>` tag ke directly andar (kisi `<servlet>` ke andar nahi). |
| `ServletContext` thread-safe hota hai? | ❌ Nahi! Kyunki multiple servlets/threads ek sath read/write kar sakte hain, isliye synchronization zaroori hai. |

---

[⬅️ Previous: Servlet Communication](./04-servlet-communication.md) · [📖 Back to Java Web Index](./README.md) · [Next → Session & Cookies ➡️](./06-session-management-and-cookies.md)
