# 🍪 Session Management & Cookies

> **Summary:** HTTP is Stateless, 4 Session tracking techniques, Cookies deep dive (Persistent vs Non-Persistent), `HttpSession` deep dive (`getSession(true)` vs `getSession(false)`), Session lifecycle, aur Cookies vs Session comparison.

---

## 1. HTTP is Stateless — The Fundamental Problem

**HTTP protocol Stateless hota hai.**
Iska matlab: Server client ke previous requests ko yaad nahi rakhta.
- Jab aap Login karte ho ➔ Server request process karta hai.
- Agle second jab aap "My Profile" click karte ho ➔ Server ke liye aap ek bilkul naye anjaan user ho! 🤦‍♂️

**Session Management** wo mechanism hai jisse server multiple requests ke dauran ek user ki pehchan (state) maintain rakhta hai (e.g. Shopping Cart, User Authentication).

```text
HTTP Statelessness:
Request 1: "Hi, I am Rahul, password is 123" ──► Server: "Authenticated!"
Request 2: "Show my bank balance"           ──► Server: "Who are you?! I don't remember!"
```

---

## 2. 4 Common Session Management Techniques

| Technique | Where State is Stored? | Mechanism |
|-----------|------------------------|-----------|
| **1. Cookies** | **Client (Browser)** | Small text data stored in browser and sent in request headers |
| **2. HttpSession** | **Server Memory** | Server assigns a unique `JSESSIONID` token to the client |
| **3. Hidden Form Fields** | HTML Page | `<input type="hidden" name="userId" value="101"/>` |
| **4. URL Rewriting** | URL Address | `dashboard.jsp;jsessionid=4A9B2...` |

---

## 3. Deep Dive: Cookies

**Cookie** ek chhota sa piece of data (key-value pair) hota hai jo server response header ke through browser ko bhejta hai, aur browser har subsequent request me use wapas server ko bhejta hai.

```text
Browser                                                          Server
   │                      1. First Request                          │
   ├───────────────────────────────────────────────────────────────►│
   │                                                                │ Creates Cookie
   │           2. Response + Header: Set-Cookie: user=Azhar         │
   │◄───────────────────────────────────────────────────────────────┤
   │ Stores in cookie storage                                       │
   │                                                                │
   │           3. Next Request + Header: Cookie: user=Azhar         │
   ├───────────────────────────────────────────────────────────────►│ Server recognizes user!
```

### A. Non-Persistent vs Persistent Cookies

| Type | Expiry Behavior | Storage Location | Creation Syntax |
|------|-----------------|------------------|-----------------|
| **Non-Persistent (Session Cookie)** | Browser close hote hi delete ho jata hai | Browser RAM / Memory | `setMaxAge()` set **nahi** karte (default negative) |
| **Persistent Cookie** | Expiry time tak hard drive me store rehta hai | Browser Disk Storage | `cookie.setMaxAge(60 * 60 * 24);` (e.g. 24 hours) |

### B. Working with Cookies in Java

#### Creating & Sending a Cookie:
```java
// Step 1: Create cookie object
Cookie userCookie = new Cookie("userRole", "Admin");

// Step 2: Set expiry in seconds (Optional: makes it persistent)
userCookie.setMaxAge(60 * 60 * 24); // 24 hours

// Step 3: Security flags (Best practices)
userCookie.setHttpOnly(true); // Prevents JavaScript XSS theft!
userCookie.setSecure(true);   // Transmitted only over HTTPS

// Step 4: Add to response header
response.addCookie(userCookie);
```

#### Reading Cookies from Request:
```java
Cookie[] cookies = request.getCookies();

if (cookies != null) {
    for (Cookie c : cookies) {
        if ("userRole".equals(c.getName())) {
            String role = c.getValue();
            System.out.println("Role: " + role);
        }
    }
}
```

#### Deleting a Cookie:
Browser se cookie delete karne ke liye uski `maxAge` ko `0` karke dobara response me add kar do:
```java
Cookie c = new Cookie("userRole", "");
c.setMaxAge(0); // 0 means delete immediately!
response.addCookie(c);
```

---

## 4. Deep Dive: `HttpSession`

`HttpSession` state ko **Server ki memory me** store karta hai. Server har client ke liye ek unique token generate karta hai jise **`JSESSIONID`** kehte hain, aur ye ID client ko cookie ke roop me bhej di jaati hai.

### ❓ `getSession(true)` vs `getSession(false)` (Super Important!)

```java
// Default / getSession() / getSession(true):
HttpSession session = request.getSession(true);
```
- Agar user ka session already exist karta hai, toh **existing session return karega**.
- Agar session exist nahi karta (naya user hai), toh **brand-new session create karke return karega**.

```java
// getSession(false):
HttpSession session = request.getSession(false);
```
- Agar user ka session pehle se chal raha hai, toh **existing session return karega**.
- Agar user logged in nahi hai ya session exist nahi karta, toh **`null` return karega** (naya session create NAHI karega).
- *Best Use Case:* Authentication check karne ke liye!

```java
// Checking Login State:
HttpSession session = request.getSession(false);
if (session == null || session.getAttribute("currentUser") == null) {
    response.sendRedirect("login.jsp"); // User not logged in!
    return;
}
```

---

### Managing Data inside Session

```java
HttpSession session = request.getSession();

// 1. Store data
session.setAttribute("user", new User("Azhar", "Admin"));

// 2. Retrieve data (Needs typecast)
User user = (User) session.getAttribute("user");

// 3. Remove single attribute
session.removeAttribute("user");

// 4. Destroy / Logout entire session
session.invalidate(); // All attributes wiped out!
```

### Session Lifecycle & Configuration
- **Default Timeout:** Tomcat me by default session **30 minutes** inactive rehne ke baad expire ho jata hai.
- **Configuring Timeout in `web.xml` (in minutes):**
```xml
<session-config>
    <session-timeout>15</session-timeout> <!-- 15 minutes -->
</session-config>
```
- **Configuring Programmatically (in seconds):**
```java
session.setMaxInactiveInterval(15 * 60); // 15 minutes
```

---

## 5. ⚖️ Grand Comparison: Cookies vs HttpSession

| Feature | Cookies | HttpSession |
|---------|---------|-------------|
| **Storage Location** | **Client / Browser** | **Server Memory** |
| **Data Types Allowed** | Only Text / Strings | Any Java Object (`User`, `List`, `Map`) |
| **Storage Capacity** | Max ~4 KB per cookie | Server RAM capacity (much larger) |
| **Security** | Low (User can inspect/modify in browser) | **High** (Client only has Session ID) |
| **Browser Dependency** | Disabled if user blocks cookies | Can fallback to URL Rewriting |
| **Traffic Overhead** | Transmitted with EVERY HTTP request | Only Session ID string transmitted |

---

## 🧠 Interview Quick Traps

| Trap | Answer |
|------|--------|
| Agar browser me cookies disabled hon toh kya `HttpSession` kaam karega? | Direct nahi karega, lekin **URL Rewriting (`response.encodeURL()`)** ke through kaam kar sakta hai! |
| `request.getSession(false)` kab use karna chahiye? | User logged-in hai ya nahi check karne ke liye, taaki unauthorized user ke liye faltu session create na ho. |
| Logout button click karne par kaunsa method call karna chahiye? | `session.invalidate()`. |
| Cookie me password store karna safe hai? | ❌ Bilkul nahi! Cookies client-side plain-text hoti hain. |
| Cookie ka size limit kitna hota hai? | Approximately **4 KB**. |

---

[⬅️ Previous: Config & Context](./05-servlet-config-and-context.md) · [📖 Back to Java Web Index](./README.md) · [Next → JSP & MVC ➡️](./07-jsp-basics-and-mvc.md)
