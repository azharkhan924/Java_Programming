# Java Web Quick Revision

---

## 1. Quick Revision Map

```text
SERVLET
│
├── Life Cycle
│ ├── Loading & Instantiation
│ ├── init() → once
│ ├── service() → requests
│ ├── destroy() → once
│ └── GC → later, if eligible
│
├── HTTP Stateless
│ └── Session Management
│ ├── Cookies
│ ├── HttpSession
│ ├── Hidden Form Field
│ └── URL Rewriting
│
├── COOKIE
│ ├── Non-persistent
│ ├── Persistent
│ ├── new Cookie()
│ ├── setMaxAge()
│ └── response.addCookie()
│
├── SESSION
│ ├── getSession(true)
│ ├── getSession(false)
│ ├── setAttribute()
│ ├── getAttribute()
│ └── invalidate()
│
└── JSP
 ├── Translation: .jsp → .java
 ├── Compilation: .java → .class
 ├── Directives
 │ ├── page
 │ ├── include
 │ └── taglib
 ├── Scripting
 │ ├── Scriptlet <% %>
 │ ├── Expression <%= %>
 │ └── Declaration <%! %>
 ├── Comments
 │ ├── HTML
 │ └── JSP
 └── MVC
 ├── Model
 ├── View
 └── Controller
```

---

## 2. Complete Servlet Concept Map

```text
 JAVA SERVLET
 │
 ┌─────────────────┼─────────────────┐
 ↓ ↓ ↓
 Request/Response Lifecycle Configuration
 │ │ │
 ┌──────┴──────┐ init/service/ ┌──────┴──────┐
 ↓ ↓ destroy ↓ ↓
 HttpServletRequest ServletConfig ServletContext
 HttpServletResponse │ │
 │ │ │
 getParameter() One Servlet Whole App
 setContentType() │ │
 sendRedirect() ↓ ↓
 init-param context-param
```

---

## 3. Crucial Comparison Tables

### Forward vs SendRedirect

| Feature | RequestDispatcher.forward() | HttpServletResponse.sendRedirect() |
|---|---|---|
| Communication Type | Server-side internal transfer | Client-side HTTP redirect |
| Number of Requests | 1 Request | 2 Requests |
| Browser URL | Unchanged | Changes to new target URL |
| Speed | Faster (processed internally) | Slower (extra browser roundtrip) |
| Request Attributes | Preserved | Lost (new request object created) |
| Target Resource Scope | Within the same web application | Any internal resource or external URL |

---

### ServletConfig vs ServletContext

| Feature | ServletConfig | ServletContext |
|---|---|---|
| Scope | Servlet-specific | Application-wide |
| Number | One per Servlet | One per web application |
| Shared among Servlets? | No | Yes |
| Main use | Servlet-specific configuration | Global/application-wide configuration and data |
| Init parameters | `init-param` in `web.xml` | `context-param` in `web.xml` |
| Created by | Servlet Container | Servlet Container |
| Access method | `getServletConfig()` | `getServletContext()` |
| Runtime sharing | No attribute storage | Supports `setAttribute()` / `getAttribute()` |

---

### Cookie vs HttpSession

| Feature | Cookie | HttpSession |
|---|---|---|
| Storage Location | Client / Browser side | Server-side memory |
| Storage Capacity | Limited (~4KB per cookie) | Larger (limited by server heap memory) |
| Data Transmitted | Sent with every matching request | Only Session ID (e.g. `JSESSIONID`) transmitted |
| Security | Lower (stored on client, can be inspected) | Higher (sensitive data stays on server) |
| Data Types | Strings only | Any Java Object |
| Persistence | Managed by `Max-Age` / `Expires` | Controlled by server timeout / `invalidate()` |

---

### JSP Scripting Elements

| Syntax | Name | Placement in Generated Servlet |
|---|---|---|
| `<% ... %>` | Scriptlet | Inside `_jspService()` method (local code/variables) |
| `<%= ... %>` | Expression | Inside `_jspService()` as `out.print(...)` |
| `<%! ... %>` | Declaration | Outside `_jspService()` at class level (instance variables/methods) |

---

## 4. Interview & Exam Important Points

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

## 5. One-Minute Revision

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
<% %> Scriptlet
<%= %> Expression
<%! %> Declaration

JSP Directives:
page | include | taglib

MVC:
Model | View | Controller
```

### Most Important Memory Lines

```text
Config = ONE Servlet
Context = WHOLE Application

Forward = Same Request + URL unchanged
Redirect = New Request + URL changes

Include = Include content + continue execution
```

---

[Previous: JSP, Directives, Scripting Elements & MVC Architecture](./07-jsp-basics-and-mvc.md) · [Back to Java Web Index](./README.md) · [Home / Root README](../README.md)
