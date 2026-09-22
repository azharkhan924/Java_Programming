# 📨 HttpServlet, Requests & Responses

> **Summary:** Servlet class hierarchy (`Servlet` ➔ `GenericServlet` ➔ `HttpServlet`), GET vs POST comparison, `HttpServletRequest` aur `HttpServletResponse` ke essential methods aur practical code.

---

## 1. The Servlet Hierarchy

Java Web me Servlet architecture ek clean object-oriented hierarchy follow karta hai:

```text
               <<interface>>
                  Servlet (jakarta.servlet / javax.servlet)
                     ▲
                     │ implements
             GenericServlet (Abstract Class)
              (Protocol-Independent, handles any protocol)
                     ▲
                     │ extends
               HttpServlet (Abstract Class)
              (Protocol-Specific: Optimized for HTTP protocol)
                     ▲
                     │ extends
             Your Custom Servlet (e.g. MyServlet)
```

### ⚖️ GenericServlet vs HttpServlet

| Feature | GenericServlet | HttpServlet |
|---------|----------------|-------------|
| **Class Type** | `abstract class` | `abstract class` |
| **Package** | `jakarta.servlet` | `jakarta.servlet.http` |
| **Protocol Support** | Protocol-independent (HTTP, FTP, SMTP etc.) | **HTTP-specific** only |
| **Main Method** | `service(ServletRequest, ServletResponse)` | `service()` override karke `doGet()`, `doPost()` etc. me delegate karta hai |
| **Request/Response Objects** | Protocol-independent `ServletRequest` | HTTP-specific `HttpServletRequest` & `HttpServletResponse` |
| **Real-World Usage** | Rarely used directly | **Almost 100% of web apps use HttpServlet** |

---

## 2. HTTP Methods: GET vs POST

Web applications me client se server par data bhejne ke do sabse popular tareeqe hain:

```text
GET Request:
URL: http://example.com/search?query=java&page=1
Data visible in URL address bar! (Query Parameters)

POST Request:
URL: http://example.com/login
Data hidden inside HTTP Request Body!
```

### 📊 Comparison Matrix: GET vs POST

| Feature | HTTP GET | HTTP POST |
|---------|----------|-----------|
| **Data Transmission** | URL me query string ke roop me (`?key=val`) | HTTP **Request Body** ke andar |
| **Data Visibility** | URL bar aur browser history me visible | URL me visible nahi hota (safer) |
| **Security** | Insecure (passwords kabhi GET se na bhejein) | Secure for sensitive data (with HTTPS) |
| **Data Size Limit** | Browser URL length limit (~2048 characters) | No standard size limit (files/images ke liye) |
| **Caching & Bookmarking** | Cached & can be bookmarked | Cannot be bookmarked, not cached |
| **Idempotency** | **Idempotent** (multiple calls produce same result) | **Non-Idempotent** (each call creates new resource) |
| **Servlet Handler** | `doGet(request, response)` | `doPost(request, response)` |

---

## 3. `HttpServletRequest` Essential Methods

`HttpServletRequest` client ke HTTP request packet ko represent karta hai.

```java
// 1. Single form field value read karna
String username = request.getParameter("username");

// 2. Multi-select (Checkboxes) ke values read karna
String[] hobbies = request.getParameterValues("hobby");

// 3. Request Metadata
String method = request.getMethod();            // e.g. "GET" or "POST"
String uri = request.getRequestURI();           // e.g. "/myapp/login"
String query = request.getQueryString();        // e.g. "lang=en&theme=dark"

// 4. Client Information
String clientIp = request.getRemoteAddr();      // Client IP address
String userAgent = request.getHeader("User-Agent"); // Browser info

// 5. Attributes (Server-side forwarding ke liye)
request.setAttribute("userObj", user);
Object val = request.getAttribute("userObj");

// 6. Session & Cookies
HttpSession session = request.getSession();
Cookie[] cookies = request.getCookies();
```

---

## 4. `HttpServletResponse` Essential Methods

`HttpServletResponse` server se client browser ko bheje jane wale response packet ko represent karta hai.

```java
// 1. Content Type set karna (MIME Type)
response.setContentType("text/html;charset=UTF-8"); // Web page ke liye
// response.setContentType("application/json");     // REST API ke liye

// 2. Writing text output (HTML / JSON)
PrintWriter out = response.getWriter();
out.println("<h1>Hello from Servlet!</h1>");

// 3. Binary output (Images, PDF, Files)
// ServletOutputStream os = response.getOutputStream();

// 4. Redirection to another URL
response.sendRedirect("welcome.jsp");

// 5. Setting HTTP Headers & Status Codes
response.setStatus(HttpServletResponse.SC_OK);          // 200
response.sendError(HttpServletResponse.SC_NOT_FOUND);   // 404
response.addCookie(new Cookie("user", "azhar"));
```

> ⚠️ **Golden Rule:** Ek hi response me `getWriter()` aur `getOutputStream()` dono ek sath call **nahi** kar sakte! (`IllegalStateException` aayega).

---

## 5. Complete Practical Example

```java
import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

@WebServlet("/greet")
public class GreetServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) 
            throws ServletException, IOException {
        
        resp.setContentType("text/html");
        PrintWriter out = resp.getWriter();

        String name = req.getParameter("name");
        if (name == null || name.trim().isEmpty()) {
            name = "Guest";
        }

        out.println("<html><body>");
        out.println("<h2>Welcome, " + name + "!</h2>");
        out.println("<p>Request handled via HTTP GET.</p>");
        out.println("</body></html>");
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) 
            throws ServletException, IOException {
        // Delegates or handles POST form submissions
        doGet(req, resp);
    }
}
```

---

## 🧠 Interview Quick Traps

| Trap | Answer |
|------|--------|
| `getParameter()` checkbox ke saare selected values de sakta hai? | ❌ Nahi, sirf first value dega. Saare values ke liye `getParameterValues()` use karo. |
| Parameter exist na kare toh `getParameter()` kya return karta hai? | **`null`** return karta hai. |
| `getWriter()` aur `getOutputStream()` me farq? | `getWriter()` character streams (text/html) ke liye hai, `getOutputStream()` binary data (images/pdf) ke liye. |
| Kya `doGet()` ke andar `doPost()` ya vice versa call kar sakte hain? | ✅ Haan! Form processing consolidate karne ke liye developers aksar `doPost()` me `doGet(req, resp)` call kar dete hain. |

---

[⬅️ Previous: Servlet Lifecycle](./02-servlet-lifecycle.md) · [📖 Back to Java Web Index](./README.md) · [Next → Servlet Communication ➡️](./04-servlet-communication.md)
