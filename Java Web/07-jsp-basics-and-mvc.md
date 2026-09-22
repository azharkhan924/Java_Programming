# 📄 JSP Basics, Directives & MVC Architecture

> **Summary:** JavaServer Pages (JSP) lifecycle (Translation ➔ Compilation ➔ Execution), Scripting elements (`<% %>`, `<%= %>`, `<%! %>`), Directives (`page`, `include`, `taglib`), Comments, aur MVC Pattern (Model-View-Controller).

---

## 1. JSP Kya Hai aur Servlet se Kaise Alag Hai?

Servlet me HTML likhna bohot messy hota tha (`out.println("<html><body...")`).
**JSP (JavaServer Pages)** ek server-side view technology hai jo HTML-centric hoti hai — isme HTML ke andar Java code embed kiya jata hai.

```text
Servlet  = Java Code ke andar HTML (Controller logic ke liye best)
JSP      = HTML Code ke andar Java (Presentation / UI ke liye best)
```

> 💡 **Under the Hood:** Container har JSP page ko internally ek **Servlet me hi convert (translate)** karta hai!

---

## 2. JSP Lifecycle (The Under-the-Hood Process)

Jab client browser kisi `.jsp` page ko request karta hai:

```text
index.jsp
    │
    ▼ 1. Translation Phase (Only on 1st request or file change)
index_jsp.java (A standard Java Servlet source file)
    │
    ▼ 2. Compilation Phase (javac)
index_jsp.class (Bytecode)
    │
    ▼ 3. Class Loading & Instantiation
Servlet Object in Container
    │
    ▼ 4. jspInit() (Called ONCE)
    │
    ▼ 5. _jspService(request, response) (Called EVERY request!)
    │
    ▼ 6. jspDestroy() (Called on server shutdown)
```

### ❓ Kab Dubara Translation/Compilation Hoti Hai?
- JSP **sirf pehli request par** translate aur compile hota hai.
- Uske baad subsequent requests seedha pre-compiled `.class` file ke `_jspService()` ko execute karti hain (Very fast!).
- Agar developer JSP file modify karta hai, toh container timestamp compare karke automatically re-translate kar leta hai.

---

## 3. JSP Scripting Elements

JSP me Java code embed karne ke 3 main scripting elements hain:

```text
┌─────────────────┬──────────────────┬────────────────────────────────────────────────────────┐
│ Tag Syntax      │ Name             │ Kahan Place Hota Hai? (Generated Servlet me)           │
├─────────────────┼──────────────────┼────────────────────────────────────────────────────────┤
│ <% ... %>       │ Scriptlet        │ _jspService() method ke ANDAR (Local variables)        │
│ <%= ... %>      │ Expression       │ out.print(...) bankar _jspService() ke ANDAR           │
│ <%! ... %>      │ Declaration      │ _jspService() method ke BAHAR (Class-level variables)  │
└─────────────────┴──────────────────┴────────────────────────────────────────────────────────┘
```

### A. Scriptlet `<% ... %>`
Method ke andar execute hone wala normal Java code (loops, if-else, calculations):
```jsp
<%
    int count = 10;
    if (count > 5) {
        System.out.println("Greater than 5");
    }
%>
```

### B. Expression `<%= ... %>`
Values ko direct HTML response me render karta hai. **Iske aakhir me semicolon (`;`) NAHI lagate!**
```jsp
<h3>Welcome, <%= request.getParameter("name") %>!</h3>
<p>Sum of 10 + 20 is: <%= 10 + 20 %></p>
```

### C. Declaration `<%! ... %>`
Class level par variables aur methods define karne ke liye:
```jsp
<%!
    // Class-level instance variable (Shared across all requests!)
    int globalHits = 0;

    // Helper method
    public int square(int x) {
        return x * x;
    }
%>
```

---

## 4. JSP Directives

Directives JSP container ko page-level instructions deti hain:

```jsp
<%@ directiveName attribute="value" %>
```

### 1. Page Directive (`<%@ page ... %>`)
Page ki global settings define karta hai:
```jsp
<%@ page language="java" 
         contentType="text/html; charset=UTF-8"
         import="java.util.List, java.util.ArrayList"
         errorPage="errorHandler.jsp"
         isErrorPage="false" %>
```

### 2. Include Directive (`<%@ include ... %>`) — Static Include
Translation phase par hi specified file ka content current page me paste ho jata hai:
```jsp
<%@ include file="navbar.jsp" %>
<!-- Great for common Header / Footer -->
```

### 3. Taglib Directive (`<%@ taglib ... %>`)
JSTL (JSP Standard Tag Library) ya custom tags use karne ke liye:
```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```

---

## 5. ⚖️ HTML Comment vs JSP Comment

| Feature | HTML Comment | JSP Comment |
|---------|--------------|-------------|
| **Syntax** | `<!-- This is HTML comment -->` | `<%-- This is JSP comment --%>` |
| **Response me jaata hai?** | ✅ Haan, client browser ko bheja jata hai | ❌ Nahi, server par hi discard ho jata hai |
| **View Source me dikhta hai?** | ✅ Browser "Page Source" me visible hota hai | ❌ View Source me **kabhi nahi dikhta** |
| **Sensitivity** | Insecure for secret logic comments | Safe for developer-only notes |

---

## 6. MVC Architecture (Model - View - Controller)

Enterprise web applications me code clean aur maintainable rakhne ke liye **MVC Pattern** use kiya jata hai:

```text
                  Browser / Client
                    │          ▲
            Request │          │ HTML Response
                    ▼          │
               ┌───────────────────────┐
               │      Controller       │
               │   (Java Servlet)      │
               └───────────┬───────────┘
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
    ┌─────────────────┐         ┌─────────────────┐
    │      Model      │         │      View       │
    │ (JavaBean/DAO)  │         │   (JSP Page)    │
    └────────┬────────┘         └─────────────────┘
             │                           ▲
             ▼                           │ Forwards Data (Request Scope)
         Database ───────────────────────┘
```

### Roles Breakdown:
1. **Controller (`Servlet`):**
   - User request ko receive karta hai.
   - Form inputs validate karta hai.
   - Model (Service/DAO) ko call karta hai.
   - Result data ko `request.setAttribute("data", result)` me store karta hai.
   - View (JSP) ko `rd.forward(request, response)` se control handover karta hai.

2. **Model (`Java POJO / Service / DAO`):**
   - Business logic aur Database calculations perform karta hai.
   - UI / Presentation se completely independent hota hai.

3. **View (`JSP / HTML`):**
   - Model se mile data ko user-friendly UI me display karta hai.
   - Isme koi heavy business logic ya DB query nahi honi chahiye.

### 🌟 Why MVC?
- **Separation of Concerns:** Designer JSP par kaam kar sakta hai bina Java code tode.
- **Maintainability:** Business logic change karne par UI disturb nahi hoti.
- **Testability:** Controller aur Model ko independent unit test kiya ja sakta hai.

---

## 🧠 Interview Quick Traps

| Trap | Answer |
|------|--------|
| JSP file direct run hoti hai? | ❌ Nahi, pehle `.java` (Servlet) me translate aur fir `.class` me compile hoti hai. |
| `<%= 5 + 5; %>` me kya error hai? | Expression element me **semicolon (`;`) nahi lagate**! (`out.print(5 + 5;);` ban kar syntax error dega). |
| `<%-- comment --%>` browser ke page source me dikhta hai? | ❌ Kabhi nahi! Ye server par hi strip ho jata hai. |
| MVC me database connectivity kahan honi chahiye? | **Model layer** me (DAO/Repository), Controller ya JSP me kabhi nahi! |

---

[⬅️ Previous: Session & Cookies](./06-session-management-and-cookies.md) · [📖 Back to Java Web Index](./README.md) · [Next → Master Quick Revision ➡️](./08-quick-revision.md)
