# ⚡ Java Web — Master Quick Revision & Cheat Sheet

> **Summary:** Ek single master cheat sheet jisme Servlets, Sessions, Cookies, JSP, aur MVC ke saare comparison tables, rapid-fire interview questions aur golden rules consolidated hain.

---

## 1. 📊 Topic-Wise Summary Matrix

| # | Topic | Core Concept | Golden Rule |
|---|-------|--------------|-------------|
| **01** | **Servlet Basics** | Middleman between browser & backend | Replaced CGI's heavy process model with lightweight thread-per-request model. |
| **02** | **Lifecycle** | `init()` ➔ `service()` ➔ `destroy()` | `init()` and `destroy()` run once; `service()` runs on every request. Servlets are NOT thread-safe by default! |
| **03** | **HttpServlet** | HTTP-specific request handling | GET is idempotent and URL-based; POST is for forms and body-based payloads. |
| **04** | **Communication** | `forward()` vs `sendRedirect()` | Forward = server-side, 1 request, same URL. Redirect = client-side, 2 requests, URL changes. |
| **05** | **Config & Context** | Configuration management | `ServletConfig` = ONE servlet (`init-param`). `ServletContext` = WHOLE app (`context-param`). |
| **06** | **Session & Cookies** | Overcoming HTTP statelessness | Cookies store strings in browser (4KB max). `HttpSession` stores objects in server memory. |
| **07** | **JSP & MVC** | HTML-centric view technology | JSP translates to Servlet (`_jspService`). MVC: Controller (Servlet) ➔ Model (JavaBean) ➔ View (JSP). |

---

## 2. ⚖️ Crucial Comparison Tables

### A. Forward vs SendRedirect (The Interview Favorite ⭐)

| Feature | `RequestDispatcher.forward()` | `HttpServletResponse.sendRedirect()` |
|---------|-------------------------------|--------------------------------------|
| **Type** | Server-side internal transfer | Client-side HTTP redirect (302) |
| **Requests** | **1 Request** | **2 Requests** |
| **Browser URL** | **Unchanged** | **Changes to new URL** |
| **Speed** | Faster (internal) | Slower (extra browser roundtrip) |
| **Request Attributes** | ✅ Preserved | ❌ Lost (New request object created) |
| **Scope** | Same web application only | Any external URL (e.g. `google.com`) |

---

### B. ServletConfig vs ServletContext

| Feature | `ServletConfig` | `ServletContext` |
|---------|-----------------|------------------|
| **Scope** | One specific Servlet | Whole web application (All Servlets & JSPs) |
| **Quantity** | One per Servlet instance | **Exactly ONE per Web App** |
| **Configuration** | `<init-param>` | `<context-param>` |
| **Runtime Sharing** | No attribute support | Supports `setAttribute()` / `getAttribute()` |

---

### C. Cookies vs HttpSession

| Feature | Cookies | `HttpSession` |
|---------|---------|---------------|
| **Location** | Client Browser | Server RAM / Heap |
| **Security** | Low (Inspectable & editable) | High (Only `JSESSIONID` shared) |
| **Capacity** | ~4 KB | Limited only by server memory |
| **Data Types** | Strings / Text only | Any Java Object (`User`, `List`, `Map`) |
| **Persistence** | Persistent (via `setMaxAge`) or Session | Expires after inactivity (default 30 mins) |

---

### D. JSP Scripting Elements

| Tag | Name | Translated Location | Example |
|-----|------|---------------------|---------|
| `<% ... %>` | **Scriptlet** | Inside `_jspService()` | `<% int x = 10; %>` |
| `<%= ... %>` | **Expression** | `out.print(...)` inside `_jspService()` | `<%= x + 5 %>` (NO semicolon!) |
| `<%! ... %>` | **Declaration** | Class-level (Outside `_jspService()`) | `<%! int count = 0; %>` |

---

## 3. 🎯 Top 20 Rapid-Fire Interview Questions

1. **Who manages the Servlet Lifecycle?**  
   ➔ Servlet Container (e.g., Apache Tomcat).
2. **How many instances of a Servlet are created by default?**  
   ➔ Exactly **ONE** instance per servlet declaration (Singleton-style within container).
3. **Are Servlets Thread-Safe?**  
   ➔ **No.** Multiple request threads access the same instance concurrently. Avoid mutable instance variables!
4. **How to pre-load a Servlet at startup?**  
   ➔ Using `<load-on-startup>1</load-on-startup>` or `@WebServlet(loadOnStartup = 1)`.
5. **What is the difference between `getSession(true)` and `getSession(false)`?**  
   ➔ `getSession(true)` creates a new session if none exists; `getSession(false)` returns `null` if none exists.
6. **How do you destroy a session during logout?**  
   ➔ `session.invalidate()`.
7. **What is the default session timeout in Tomcat?**  
   ➔ **30 minutes**.
8. **Can `forward()` redirect to Google.com?**  
   ➔ No, `forward()` works only within the current web container. Use `sendRedirect()` for external URLs.
9. **Why is `int` returned by `read()` instead of `byte`?**  
   ➔ Because byte is signed (-128 to 127), and `read()` needs to return `-1` to signal End-Of-File (EOF).
10. **How do you delete a Cookie?**  
    ➔ Set its max age to 0: `cookie.setMaxAge(0); response.addCookie(cookie);`.
11. **What is the role of the Controller in MVC?**  
    ➔ Servlet acts as the Controller: parses request, calls Model, updates request attributes, and forwards to View.
12. **Which JSP element does NOT use a semicolon?**  
    ➔ Expression tag `<%= ... %>`.
13. **What is the difference between HTML comments and JSP comments?**  
    ➔ `<!-- -->` is sent to browser and visible in View Source; `<%-- --%>` is stripped on server and hidden.
14. **What happens during JSP Translation Phase?**  
    ➔ `.jsp` file is translated into a Java source file (`.java` Servlet).
15. **What is a Marker Interface?**  
    ➔ An interface with 0 methods (e.g., `Cloneable`, `Serializable`).
16. **How to enable append mode in `FileOutputStream`?**  
    ➔ Pass `true` in constructor: `new FileOutputStream("file.txt", true)`.
17. **What is the difference between `getMethods()` and `getDeclaredMethods()` in Reflection?**  
    ➔ `getMethods()` returns all public methods (including inherited); `getDeclaredMethods()` returns all declared methods of that class only (including private).
18. **Why is Enum Singleton the best implementation?**  
    ➔ It is immune to reflection attacks, handles serialization automatically, and is thread-safe by design.
19. **What is the Post-Redirect-Get (PRG) pattern?**  
    ➔ Handling form POST submissions with a redirect to GET, preventing duplicate form submissions on page refresh.
20. **Can you call both `getWriter()` and `getOutputStream()` on the same response?**  
    ➔ **No.** It throws `IllegalStateException`.

---

[⬅️ Previous: JSP & MVC](./07-jsp-basics-and-mvc.md) · [📖 Back to Java Web Index](./README.md) · [🏠 Home / Root README](../README.md)
