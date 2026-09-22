# JSP, Directives, Scripting Elements & MVC Architecture

---

## 1. JSP – JavaServer Pages

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

### 1.1 Translation Phase

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

### 1.2 Compilation Phase

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

### 1.3 When Does JSP Translate/Compile Again?

Container generally JSP ko check karta hai ki page **new/modified** hai ya nahi.

- New/modified JSP → translation + compilation required
- Already compiled and unchanged → generated servlet reuse/execute kiya ja sakta hai

---

## 2. Elements of JSP

Main JSP elements:

1. Directives
2. Comments
3. Scripting Elements
4. Expressions
5. Template Text

---

## 3. Scripting Elements

Scripting elements ke 3 main types:

1. Scriptlet
2. Expression
3. Declaration

---

### 3.1 Scriptlet

#### Syntax

```jsp
<%
 // Java code
%>
```

#### Example

```jsp
<%
 int a = 10;
 int b = 20;
 int sum = a + b;
%>
```

Scriptlet ka Java code generated servlet ke `_jspService()` method ke andar place hota hai.

---

### 3.2 Expression

#### Syntax

```jsp
<%= expression %>
```

#### Example

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

### 3.3 Declaration

#### Syntax

```jsp
<%!
 // declaration
%>
```

#### Example

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
<% %> → Scriptlet → _jspService() ke andar
<%= %> → Expression → output / out.print(...)
<%! %> → Declaration → class level / _jspService() ke bahar
```

---

## 4. JSP Directives

Directives JSP container ko page ke baare mein instructions provide karti hain.

Main directives:

1. `page`
2. `include`
3. `taglib`

---

### 4.1 Page Directive

#### Syntax

```jsp
<%@ page attribute="value" %>
```

#### Example

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

### 4.2 Include Directive

#### Syntax

```jsp
<%@ include file="header.jsp" %>
```

It is a **static include**.

Included file ka content JSP translation phase ke time current JSP mein include hota hai.

#### Example

```jsp
<%@ include file="header.jsp" %>
```

Useful for common/static page parts like header/footer.

---

### 4.3 Taglib Directive

Custom/JSTL tag libraries use karne ke liye:

```jsp
<%@ taglib prefix="c"
 uri="http://java.sun.com/jsp/jstl/core" %>
```

> Modern Jakarta/JSTL setups mein URI/library configuration project ke version ke according different ho sakti hai.

---

## 5. HTML Comment vs JSP Comment

### HTML Comment

#### Syntax

```html
<!-- This is an HTML comment -->
```

- Browser ko response mein mil sakta hai.
- Page Source mein visible ho sakta hai.
- Client-side markup ka part hai.

### JSP Comment

#### Syntax

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

## 6. Client Side vs Server Side

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

## 7. MVC Architecture

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
 ↓ ↓
 Model View
 (Java/Service/DAO) (JSP)
 | |
 ↓ |
 Database |
 | |
 └─────────┬─────────┘
 ↓
 Response
 ↓
 User/Browser
```

### Model

Model application ka **data + business logic** handle karta hai.

Examples:

```text
Java Classes
Service Layer
DAO
Database interaction
```

### View

User ko UI/output show karta hai.

Example:

```text
JSP
HTML
CSS
JavaScript
```

### Controller

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

[Previous: Session Management & Cookies](./06-session-management-and-cookies.md) · [Back to Java Web Index](./README.md) · [Next: Quick Revision](./08-quick-revision.md)
