# HttpServlet, Request and Response

---

## 1. The Servlet Hierarchy

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

### GenericServlet vs HttpServlet

#### GenericServlet

`GenericServlet` is a protocol-independent base Servlet class.

- It is not specific to HTTP.
- It provides a generic Servlet implementation.
- It can be extended when protocol-specific behavior is not required.

#### HttpServlet

`HttpServlet` is designed specifically for HTTP-based web applications.

Common methods include:

- `doGet()`
- `doPost()`
- `doPut()`
- `doDelete()`
- `doHead()`
- `doOptions()`
- `doTrace()`

For normal web applications, `HttpServlet` is commonly used.

| Feature | GenericServlet | HttpServlet |
|---|---|---|
| Class Type | `abstract class` | `abstract class` |
| Package | `jakarta.servlet` | `jakarta.servlet.http` |
| Protocol Support | Protocol-independent (HTTP, FTP, SMTP etc.) | HTTP-specific only |
| Main Method | `service(ServletRequest, ServletResponse)` | `service()` overrides and delegates to `doGet()`, `doPost()` etc. |
| Request/Response Objects | `ServletRequest` & `ServletResponse` | `HttpServletRequest` & `HttpServletResponse` |
| Real-World Usage | Rarely used directly | Standard for web applications |

---

## 2. GET vs POST

### GET

Data is generally sent as part of the **URL/query string**.

Example:

```text
/login?username=azhar
```

#### Features

- Data is visible in the URL
- URL can be bookmarked/shared
- Suitable for retrieving/fetching data
- URL length limits apply
- Should not be used for sensitive data merely because it is convenient

### POST

Data is sent in the **HTTP request body**.

#### Features

- Data is not placed in the URL
- Suitable for form submission, login, registration, etc.
- Can send larger request bodies than typical URLs
- Better suited to sensitive form data, but **HTTPS is still required** for confidentiality

> **Important:** POST is not automatically secure. Use HTTPS whenever sensitive data is transmitted.

### Comparison Matrix: GET vs POST

| Feature | HTTP GET | HTTP POST |
|---|---|---|
| Data Transmission | Sent in URL / query string (`?key=val`) | Sent inside HTTP Request Body |
| Data Visibility | Visible in URL and browser history | Hidden from URL |
| Primary Purpose | Fetching / reading data (idempotent) | Submitting / modifying data |
| Bookmarking | Can be bookmarked and cached | Cannot be bookmarked or safely cached |
| Data Length | Limited by browser/server URL limits | Virtually unlimited body size |

---

## 3. Request Object (`HttpServletRequest`)

The Servlet container provides an `HttpServletRequest` object containing information about the client's request.

### Important Methods

| Method | Purpose |
|---|---|
| `getParameter(String name)` | Gets a single request parameter value |
| `getParameterValues(String name)` | Gets multiple values for the same parameter |
| `getParameterNames()` | Gets names of all request parameters |
| `getRequestURL()` | Gets the request URL |
| `getCookies()` | Gets cookies sent by the client |
| `getMethod()` | Gets the HTTP method such as GET/POST |
| `getSession()` | Gets or creates the HTTP session |
| `getAttribute(String name)` | Gets a request-scoped attribute |
| `setAttribute(String name, Object o)` | Sets a request-scoped attribute |

### Example

Single value:

```java
String name = request.getParameter("name");
```

Multiple values (e.g. checkboxes):

```java
String[] values = request.getParameterValues("skills");
```

---

## 4. Response Object (`HttpServletResponse`)

The Servlet container provides an `HttpServletResponse` object to generate the response.

### Important Methods

| Method | Purpose |
|---|---|
| `setContentType(String type)` | Sets the response content type (e.g. `text/html`, `application/json`) |
| `getWriter()` | Gets a PrintWriter to send character/HTML text response |
| `getOutputStream()` | Gets an OutputStream for binary data (images, PDFs) |
| `sendRedirect(String location)` | Sends a redirect response to the client |
| `addCookie(Cookie cookie)` | Adds a cookie to the response header |
| `setStatus(int sc)` | Sets the HTTP status code (e.g. 200, 404, 500) |

### Example

```java
response.setContentType("text/html");

PrintWriter out = response.getWriter();
out.println("<h1>Hello from Servlet</h1>");
```

---

[Previous: Servlet Life Cycle](./02-servlet-lifecycle.md) · [Back to Java Web Index](./README.md) · [Next: Servlet Communication](./04-servlet-communication.md)
