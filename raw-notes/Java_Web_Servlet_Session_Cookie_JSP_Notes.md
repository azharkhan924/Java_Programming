# Java Web – Servlet, Session, Cookie & JSP Notes

> **Style:** Hinglish + English | Revision-friendly | Interview-focused

---

## 1. Servlet Life Cycle

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

### Step 1: Loading & Instantiation

- Servlet Container servlet class ko **load** karta hai.
- Class load hone ke baad servlet ka object create hota hai.
- Normally servlet ka **one instance** create karke multiple requests handle ki jaati hain.
- Container servlet ko initialize karne ke liye `init()` call karta hai.

> **Important:** Servlet instance ko multiple concurrent requests mil sakti hain, isliye servlet instance variables ko thread-safe rakhna important hai.

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
 │ PUT → ...     │
 │ DELETE → ...  │
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

### Step 5: Garbage Collection

`destroy()` ke baad servlet instance container ke use mein nahi rehta. Agar object ke paas koi reachable reference nahi hai, to JVM ka **Garbage Collector** us object ko later reclaim kar sakta hai.

> **Important:** `destroy()` directly garbage collection nahi karta.

### Servlet Life Cycle – One-Line Revision

```text
Load Class → Create Object → init() [once]
→ service() [for requests]
→ destroy() [once]
→ eligible for Garbage Collection
```

---

# 2. HTTP is Stateless

**HTTP is a stateless protocol.**

Iska matlab: HTTP by default previous request ki user-specific state ko automatically remember nahi karta.

Example:

```text
Request 1 → Server
Request 2 → Server
Request 3 → Server
```

Server ko in requests ko same user se associate karne ke liye **state/session management techniques** use karni padti hain.

### Common Session Management Techniques

1. Cookies
2. HttpSession
3. Hidden Form Fields
4. URL Rewriting

---

# 3. Cookies

A **Cookie** small piece of data hota hai jo server response ke through browser ko diya ja sakta hai. Browser ise store karke subsequent requests mein server ko bhej sakta hai.

### Basic Flow

```text
First Request
Browser ─────────────→ Server
Browser ←───────────── Server
          Set-Cookie

Next Request
Browser ─────────────→ Server
       Cookie included
```

Server cookie ke value ke basis par client/browser ko identify ya state associate kar sakta hai.

### Important

- Cookie **client/browser side** par store hoti hai.
- Cookie ka data request ke saath server tak ja sakta hai.
- Cookies small data ke liye useful hoti hain.
- Sensitive information ko plain cookie value mein store nahi karna chahiye.
- `Secure`, `HttpOnly` aur appropriate `SameSite` settings security improve kar sakti hain.

---

## 3.1 Types of Cookies

### 1. Non-Persistent Cookie

- Iski explicit long-term expiry set nahi hoti.
- Usually browser session ke end par remove ho jaati hai.
- Example: session cookie.

### 2. Persistent Cookie

- Iske liye `Max-Age` / `Expires` set kiya ja sakta hai.
- Browser ise specified expiry tak store kar sakta hai.

> **Note:** "Persistent = multiple sessions" aur "non-persistent = one session" exam-level shortcut hai, lekin technically persistence expiry attributes se determine hoti hai.

---

## 3.2 Creating a Cookie in Servlet

### Step 1: Create Cookie

```java
Cookie ck = new Cookie("un", "azk");
```

- `"un"` → cookie name
- `"azk"` → cookie value

### Step 2: Set Expiry / Max Age

```java
ck.setMaxAge(60 * 60);
```

`setMaxAge()` mein value **seconds** mein hoti hai.

```text
60 × 60 = 3600 seconds = 1 hour
```

### Step 3: Add Cookie to Response

```java
response.addCookie(ck);
```

### Complete Example

```java
Cookie ck = new Cookie("un", "azk");
ck.setMaxAge(60 * 60);
response.addCookie(ck);
```

---

## 3.3 Reading Cookies

Request se cookies obtain karne ke liye:

```java
Cookie[] cookies = request.getCookies();
```

Then cookie name/value check kar sakte hain:

```java
if (cookies != null) {
    for (Cookie c : cookies) {
        if ("un".equals(c.getName())) {
            String username = c.getValue();
        }
    }
}
```

---

## 3.4 Cookie – Quick Points

- Client-side storage
- Small amount of data
- Browser request ke saath cookie send kar sakta hai
- `Max-Age` / `Expires` se persistence control hoti hai
- "Remember me" functionality mein commonly used
- JWT ko cookie mein store kiya ja sakta hai, but JWT khud cookie nahi hai
- Cookies ko blindly secure nahi maana ja sakta; security attributes and application design matter karte hain

> **Exam correction:** Cookies ki quantity/size unlimited nahi hoti. Browsers/domain ke practical limits hote hain.

---

# 4. Session / `HttpSession`

`HttpSession` server-side session state maintain karne ka Servlet API mechanism hai.

```java
HttpSession session = request.getSession();
```

`request.getSession()` ka default behavior `true` ke equivalent hai:

```java
request.getSession(true);
```

### `getSession(true)`

```java
HttpSession session = request.getSession(true);
```

- Existing session hai → **same session return**
- Existing session nahi hai → **new session create + return**

### `getSession(false)`

```java
HttpSession session = request.getSession(false);
```

- Existing session hai → existing session return
- Existing session nahi hai → `null`
- New session create nahi hota

---

## 4.1 Store Data in Session

```java
session.setAttribute("un", "azk");
```

- `"un"` → attribute name
- `"azk"` → attribute value

### Read Data

```java
Object value = session.getAttribute("un");
```

If String expected:

```java
String username = (String) session.getAttribute("un");
```

### Remove Data

```java
session.removeAttribute("un");
```

### Invalidate Session

```java
session.invalidate();
```

---

## 4.2 Session Information

### Creation Time

```java
long time = session.getCreationTime();
```

### Last Access Time

```java
long lastAccess = session.getLastAccessedTime();
```

### Session ID

```java
String id = session.getId();
```

---

## 4.3 Session vs Cookie

| Cookie | HttpSession |
|---|---|
| Mainly client/browser side | Server-side session state |
| Small data | Can hold more server-side state |
| Data can travel with requests | Only session identifier normally travels |
| Browser manages cookie storage | Server manages session object |
| Persistence can be configured | Session has timeout/invalidation rules |
| Example: preferences, session ID | Example: logged-in user state |

### Important Correction

Browser close hone par **session object necessarily immediately destroy nahi hota**.

Usually session ID stored in a session cookie may disappear when the browser closes, but the server-side `HttpSession` can remain until its timeout or explicit:

```java
session.invalidate();
```

---

# 5. JSP – JavaServer Pages

**JSP = JavaServer Pages**

JSP is a server-side technology used to create dynamic web pages.

JSP ko Servlet Container internally a **Servlet source/class** mein translate and compile karta hai.

### JSP Processing Flow

```text
.jsp
 ↓
Translation
 ↓
.java
 ↓
Compilation
 ↓
.class
 ↓
Class Loading / Execution
 ↓
Response
```

> Browser directly JSP ko `.java` mein convert nahi karta. Ye work server-side JSP engine/container karta hai.

---

## 5.1 Translation Phase

JSP source:

```text
.jsp → generated Java servlet source (.java)
```

Example conceptually:

```text
home.jsp
   ↓
home_jsp.java
```

## 5.2 Compilation Phase

Generated Java source:

```text
.java → .class
```

Example conceptually:

```text
home_jsp.java
   ↓
home_jsp.class
```

After that, generated servlet execute hota hai.

---

## 5.3 When Does JSP Translate/Compile Again?

Container generally JSP ko check karta hai ki page **new/modified** hai ya nahi.

- New/modified JSP → translation + compilation required
- Already compiled and unchanged → generated servlet reuse/execute kiya ja sakta hai

---

# 6. Elements of JSP

Main JSP elements:

1. Directives
2. Comments
3. Scripting Elements
4. Expressions
5. Template Text

---

# 7. Scripting Elements

Scripting elements ke 3 main types:

1. Scriptlet
2. Expression
3. Declaration

---

## 7.1 Scriptlet

### Syntax

```jsp
<%
    // Java code
%>
```

Example:

```jsp
<%
    int a = 10;
    int b = 20;
    int sum = a + b;
%>
```

Scriptlet ka Java code generated servlet ke `_jspService()` method ke andar place hota hai.

---

## 7.2 Expression

### Syntax

```jsp
<%= expression %>
```

Example:

```jsp
<%= 10 + 20 %>
```

Expression ka result response mein automatically output hota hai.

Conceptually generated servlet mein ye:

```java
out.print(10 + 20);
```

jaisa behavior produce karta hai.

---

## 7.3 Declaration

### Syntax

```jsp
<%!
    // declaration
%>
```

Example:

```jsp
<%!
    int count = 0;

    public int square(int n) {
        return n * n;
    }
%>
```

Declaration ka code generated servlet ke `_jspService()` method ke **bahar**, class-level area mein place hota hai.

### Easy Memory Trick

```text
<%  %>     → Scriptlet    → _jspService() ke andar
<%= %>     → Expression   → output / out.print(...)
<%! %>     → Declaration  → class level / _jspService() ke bahar
```

---

# 8. JSP Directives

Directives JSP container ko page ke baare mein instructions provide karti hain.

Main directives:

1. `page`
2. `include`
3. `taglib`

---

## 8.1 Page Directive

### Syntax

```jsp
<%@ page attribute="value" %>
```

Example:

```jsp
<%@ page language="java" contentType="text/html" %>
```

Common attributes:

```text
import
contentType
pageEncoding
session
errorPage
isErrorPage
```

---

## 8.2 Include Directive

### Syntax

```jsp
<%@ include file="header.jsp" %>
```

It is a **static include**.

Included file ka content JSP translation phase ke time current JSP mein include hota hai.

Example:

```jsp
<%@ include file="header.jsp" %>
```

Useful for common/static page parts like header/footer.

---

## 8.3 Taglib Directive

Custom/JSTL tag libraries use karne ke liye:

```jsp
<%@ taglib prefix="c"
           uri="http://java.sun.com/jsp/jstl/core" %>
```

> Modern Jakarta/JSTL setups mein URI/library configuration project ke version ke according different ho sakti hai.

---

# 9. HTML Comment vs JSP Comment

## HTML Comment

### Syntax

```html
<!-- This is an HTML comment -->
```

- Browser ko response mein mil sakta hai.
- Page Source mein visible ho sakta hai.
- Client-side markup ka part hai.

## JSP Comment

### Syntax

```jsp
<%-- This is a JSP comment --%>
```

- JSP container is comment ko response mein send nahi karta.
- Browser/Page Source mein visible nahi hota.

### Quick Difference

| HTML Comment | JSP Comment |
|---|---|
| `<!-- -->` | `<%-- --%>` |
| Response HTML ka part ho sakta hai | Response mein nahi bheja jata |
| Page Source mein visible ho sakta hai | Page Source mein visible nahi |
| Client-side HTML | Server-side JSP processing |

---

# 10. Client Side vs Server Side

### HTML

HTML browser/client par render hota hai.

### JavaScript

JavaScript traditionally **client-side scripting language** hai (browser context mein), although JavaScript server-side environments mein bhi run ho sakti hai, e.g. Node.js.

### JSP / Servlet

JSP aur Servlet primarily **server-side** technologies hain.

```text
Client / Browser
      ↓
   Request
      ↓
Server / Servlet Container
      ↓
Servlet / JSP
      ↓
   Response
      ↓
Client / Browser
```

---

# 11. MVC Architecture

**MVC = Model – View – Controller**

MVC application ko 3 major parts mein separate karta hai.

```text
             User / Browser
                    |
                    | Request
                    ↓
              Controller
             (Servlet)
                    |
          ┌─────────┴─────────┐
          ↓                   ↓
       Model                 View
  (Java/Service/DAO)          (JSP)
          |                   |
          ↓                   |
       Database               |
          |                   |
          └─────────┬─────────┘
                    ↓
                Response
                    ↓
               User/Browser
```

## Model

Model application ka **data + business logic** handle karta hai.

Examples:

```text
Java Classes
Service Layer
DAO
Database interaction
```

## View

User ko UI/output show karta hai.

Example:

```text
JSP
HTML
CSS
JavaScript
```

## Controller

User request receive karta hai aur request ko appropriate model/business logic tak bhejta hai.

Servlet commonly controller ka role play karta hai.

```text
Browser → Servlet → Service/DAO → Database
                    ↓
                  JSP
                    ↓
                 Browser
```

### MVC ka Main Benefit

- Separation of concerns
- Code maintain karna easy
- UI aur business logic ko separate rakhna
- Testing and scalability better organize ki ja sakti hai

---

# 12. Quick Revision Map

```text
SERVLET
│
├── Life Cycle
│   ├── Loading & Instantiation
│   ├── init()       → once
│   ├── service()    → requests
│   ├── destroy()    → once
│   └── GC           → later, if eligible
│
├── HTTP Stateless
│   └── Session Management
│       ├── Cookies
│       ├── HttpSession
│       ├── Hidden Form Field
│       └── URL Rewriting
│
├── COOKIE
│   ├── Non-persistent
│   ├── Persistent
│   ├── new Cookie()
│   ├── setMaxAge()
│   └── response.addCookie()
│
├── SESSION
│   ├── getSession(true)
│   ├── getSession(false)
│   ├── setAttribute()
│   ├── getAttribute()
│   └── invalidate()
│
└── JSP
    ├── Translation: .jsp → .java
    ├── Compilation: .java → .class
    ├── Directives
    │   ├── page
    │   ├── include
    │   └── taglib
    ├── Scripting
    │   ├── Scriptlet <% %>
    │   ├── Expression <%= %>
    │   └── Declaration <%! %>
    ├── Comments
    │   ├── HTML
    │   └── JSP
    └── MVC
        ├── Model
        ├── View
        └── Controller
```

---

# 13. Interview / Exam Important Points ⭐

1. **Who manages servlet life cycle?**  
   Servlet Container.

2. **Which method is called only once for initialization?**  
   `init()`.

3. **Which method handles requests?**  
   `service()`.

4. **Which method is called before servlet destruction?**  
   `destroy()`.

5. **Is HTTP stateful or stateless?**  
   Stateless.

6. **Where are cookies stored?**  
   Primarily in the client/browser.

7. **Where is `HttpSession` state maintained?**  
   On the server.

8. **What does `getSession(false)` do?**  
   Existing session return karta hai; otherwise `null`, without creating a new session.

9. **JSP translation:**  
   `.jsp → .java`

10. **JSP compilation:**  
    `.java → .class`

11. **Scriptlet:**  
    `<% %>`

12. **Expression:**  
    `<%= %>`

13. **Declaration:**  
    `<%! %>`

14. **JSP comment:**  
    `<%-- --%>`

15. **Main JSP directives:**  
    `page`, `include`, `taglib`

16. **MVC mein Servlet ka common role:**  
    Controller.

17. **MVC mein JSP ka common role:**  
    View.

---

# 14. One-Minute Revision

```text
Servlet Life Cycle:
Load → Instantiate → init() → service() → destroy() → GC eligible

HTTP:
Stateless

Session Management:
Cookie | HttpSession | Hidden Field | URL Rewriting

Cookie:
Client-side
new Cookie(name, value)
setMaxAge(seconds)
response.addCookie(cookie)

Session:
Server-side
request.getSession()
setAttribute()
getAttribute()
invalidate()

JSP:
.jsp → translation → .java
.java → compilation → .class

JSP Scripting:
<% %>     Scriptlet
<%= %>    Expression
<%! %>    Declaration

JSP Directives:
page | include | taglib

MVC:
Model | View | Controller
```
