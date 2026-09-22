# Session Management & Cookies

---

## 1. HTTP is Stateless

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

## 2. Cookies

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

### 2.1 Types of Cookies

#### 1. Non-Persistent Cookie

- Iski explicit long-term expiry set nahi hoti.
- Usually browser session ke end par remove ho jaati hai.
- Example: session cookie.

#### 2. Persistent Cookie

- Iske liye `Max-Age` / `Expires` set kiya ja sakta hai.
- Browser ise specified expiry tak store kar sakta hai.

> **Note:** "Persistent = multiple sessions" aur "non-persistent = one session" exam-level shortcut hai, lekin technically persistence expiry attributes se determine hoti hai.

---

### 2.2 Creating a Cookie in Servlet

#### Step 1: Create Cookie

```java
Cookie ck = new Cookie("un", "azk");
```

- `"un"` → cookie name
- `"azk"` → cookie value

#### Step 2: Set Expiry / Max Age

```java
ck.setMaxAge(60 * 60);
```

`setMaxAge()` mein value **seconds** mein hoti hai.

```text
60 × 60 = 3600 seconds = 1 hour
```

#### Step 3: Add Cookie to Response

```java
response.addCookie(ck);
```

#### Complete Example

```java
Cookie ck = new Cookie("un", "azk");
ck.setMaxAge(60 * 60);
response.addCookie(ck);
```

---

### 2.3 Reading Cookies

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

### 2.4 Cookie – Quick Points

- Client-side storage
- Small amount of data
- Browser request ke saath cookie send kar sakta hai
- `Max-Age` / `Expires` se persistence control hoti hai
- "Remember me" functionality mein commonly used
- JWT ko cookie mein store kiya ja sakta hai, but JWT khud cookie nahi hai
- Cookies ko blindly secure nahi maana ja sakta; security attributes and application design matter karte hain

> **Exam correction:** Cookies ki quantity/size unlimited nahi hoti. Browsers/domain ke practical limits hote hain.

---

## 3. Session / `HttpSession`

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

### 3.1 Store Data in Session

```java
session.setAttribute("un", "azk");
```

- `"un"` → attribute name
- `"azk"` → attribute value

#### Read Data

```java
Object value = session.getAttribute("un");
```

If String expected:

```java
String username = (String) session.getAttribute("un");
```

#### Remove Data

```java
session.removeAttribute("un");
```

#### Invalidate Session

```java
session.invalidate();
```

---

### 3.2 Session Information

#### Creation Time

```java
long time = session.getCreationTime();
```

#### Last Access Time

```java
long lastAccess = session.getLastAccessedTime();
```

#### Session ID

```java
String id = session.getId();
```

---

### 3.3 Session vs Cookie

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

[Previous: ServletConfig & ServletContext](./05-servlet-config-and-context.md) · [Back to Java Web Index](./README.md) · [Next: JSP, Directives, Scripting Elements & MVC Architecture](./07-jsp-basics-and-mvc.md)
