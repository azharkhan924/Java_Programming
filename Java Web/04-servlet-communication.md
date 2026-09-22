# 🔀 Servlet Communication — RequestDispatcher vs sendRedirect

> **Summary:** Inter-Servlet communication, `RequestDispatcher` (`forward()` vs `include()`), `HttpServletResponse.sendRedirect()`, complete comparison table, aur Request Attributes behavior.

---

## 1. Inter-Servlet Communication Overview

Ek web application me ek Servlet akele poora kaam nahi karta. Servlets aapas me aur JSPs ke sath communicate karte hain:
- **Server-side Transfer:** Control internal server level par ek resource se dusre resource ko transfer hota hai.
- **Client-side Transfer:** Server browser ko bolta hai ki wo khud naya request lekar kisi dusre page par jaye.

```text
                                  SERVLET COMMUNICATION
                                            │
                   ┌────────────────────────┴────────────────────────┐
                   ▼                                                 ▼
        Server-Side (Internal)                            Client-Side (External)
         RequestDispatcher                                 HttpServletResponse
        ┌──────────┴──────────┐                                      │
        ▼                     ▼                                      ▼
     forward()             include()                           sendRedirect()
 (Transfers control)  (Includes target output)            (Browser initiates NEW request)
```

---

## 2. RequestDispatcher (Server-Side)

`RequestDispatcher` interface internal server routing ke liye use hota hai.

### 📍 RequestDispatcher Kaise Milta Hai?
```java
// Option 1: Using Request object (Relative or absolute path)
RequestDispatcher rd = request.getRequestDispatcher("targetServlet");

// Option 2: Using ServletContext (Must start with leading '/')
RequestDispatcher rd = getServletContext().getRequestDispatcher("/targetServlet");
```

---

### A. `forward(request, response)` — Complete Control Handover

`forward()` current request aur response objects ko dusre Servlet/JSP ko de deta hai.

```text
Browser ──► [Request 1] ──► Servlet A ───(internal forward)───► Servlet B
   ▲                                                                │
   └─────────────────────── [Response from Servlet B] ──────────────┘
```

#### Code Example:
```java
// Servlet 1
protected void doPost(HttpServletRequest req, HttpServletResponse resp) 
        throws ServletException, IOException {
    
    // Request attribute set kiya
    req.setAttribute("userRole", "ADMIN");

    RequestDispatcher rd = req.getRequestDispatcher("dashboard.jsp");
    rd.forward(req, resp); // Dashboard JSP will generate the final view
}
```

**Key Points:**
- Browser ko pata bhi nahi chalta ki backend me control kisi aur page par gaya hai.
- **URL browser address bar me SAME rehta hai** (change nahi hota).
- **Request attributes** intact rehte hain (`Servlet B` unhe read kar sakta hai).

---

### B. `include(request, response)` — Modular Composition

`include()` kisi dusre Servlet/JSP ke output ko current response ke andar embed kar leta hai, aur control wapas caller ke paas aa jata hai.

```text
Servlet Main:
1. Generate Header banner
2. rd.include(request, response) ──► Includes output of "MenuServlet"
3. Generate Body & Footer
```

#### Code Example:
```java
RequestDispatcher header = req.getRequestDispatcher("header.jsp");
header.include(req, resp); // Header HTML gets added here

out.println("<h3>Main Content of Page</h3>");

RequestDispatcher footer = req.getRequestDispatcher("footer.jsp");
footer.include(req, resp); // Footer HTML gets added here
```

---

## 3. `sendRedirect()` (Client-Side)

`sendRedirect()` client browser ko ek **HTTP 302 (Found / Redirect)** status code aur target URL ka `Location` header bhejta hai. Browser automatically us new URL ke liye ek **brand new HTTP GET request** bhejta hai.

```text
Step 1: Browser ──► [Request 1] ──► Servlet A
Step 2: Browser ◄── [HTTP 302: Go to /login.jsp] ── Servlet A
Step 3: Browser ──► [Request 2 (New!)] ──► /login.jsp
Step 4: Browser ◄── [Response] ─── /login.jsp
```

#### Code Example:
```java
protected void doPost(HttpServletRequest req, HttpServletResponse resp) 
        throws ServletException, IOException {
    
    boolean isValid = authenticateUser(req);
    if (isValid) {
        resp.sendRedirect("dashboard.html"); // Internal redirect
    } else {
        resp.sendRedirect("https://google.com"); // External redirect possible!
    }
}
```

---

## 4. ⚖️ The Ultimate Comparison: `forward()` vs `sendRedirect()`

Ye Java Web interviews ka **#1 most asked question** hai:

| Feature | `RequestDispatcher.forward()` | `HttpServletResponse.sendRedirect()` |
|---------|-------------------------------|--------------------------------------|
| **Execution Location** | **Server-side** (Internal) | **Client-side** (Browser round-trip) |
| **Number of Requests** | **1 Request** | **2 Requests** (Initial + Redirect) |
| **Browser URL** | **Remains unchanged** (Original URL stays) | **Updates to target URL** |
| **Performance** | Faster (No network round-trip) | Slower (Extra HTTP trip from browser) |
| **Request Attributes** | ✅ **Preserved** (`request.getAttribute()`) | ❌ **Lost** (New request object created) |
| **Target Scope** | Same web application only | Same web app **YA external website** (e.g. Google) |
| **HTTP Status Code** | 200 OK (generally) | 302 Found / Moved Temporarily |

---

## 5. Request Attributes with Forward vs Redirect

```java
// In Source Servlet:
request.setAttribute("username", "Azhar");

// Case 1: forward()
rd.forward(request, response);
// In Target:
String user = (String) request.getAttribute("username"); // ✅ WORKS! Value: "Azhar"

// Case 2: sendRedirect()
response.sendRedirect("targetServlet");
// In Target:
String user = (String) request.getAttribute("username"); // ❌ Returns NULL! New request object!
```

> 💡 **Redirect me data kaise pass karein?**  
> 1. Query parameters: `response.sendRedirect("target?username=Azhar");`  
> 2. Session: `session.setAttribute("username", "Azhar");`  
> 3. Cookies

---

## 🧠 Interview Quick Traps

| Trap | Answer |
|------|--------|
| Kya `forward()` se Google.com par redirect kar sakte hain? | ❌ Nahi! `forward()` sirf usi container/web application ke andar kaam karta hai. |
| Form submission ke baad `forward()` use karna chahiye ya `redirect()`? | **Redirect (`PRG Pattern`: Post-Redirect-Get)** use karna chahiye taaki user page refresh kare toh duplicate form submit na ho! |
| `forward()` call karne ke baad agar method me aage code likha ho toh chalega? | ✅ Haan! `forward()` execute hone ke baad method ka remaining code execute hota hai, isliye common practice hai ki turant `return;` kar diya jaye. |

---

[⬅️ Previous: HttpServlet & Requests](./03-httpservlet-and-request-response.md) · [📖 Back to Java Web Index](./README.md) · [Next → Config & Context ➡️](./05-servlet-config-and-context.md)
