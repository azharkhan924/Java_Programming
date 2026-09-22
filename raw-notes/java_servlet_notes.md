# Java Servlet

## 1. What is a Java Servlet?

A **Servlet** is a Java program that runs inside a **Servlet Container**
on a web server and is used to handle client requests and generate
responses.

### Simple Definition

> Servlet acts as a **middleman between the client/browser and the
> backend/application logic**.

``` text
Browser → Web Server / Servlet Container → Servlet → Database
                                              ↓
                                         Generate Response
                                              ↓
Browser ←────────────── HTTP Response ────────┘
```

### What does a Servlet do?

-   Receives form data
-   Processes client requests
-   Connects to the database
-   Generates dynamic HTML responses
-   Handles sessions
-   Performs authentication
-   Communicates with other servlets/resources

------------------------------------------------------------------------

# 2. Why Servlets?

Before Servlets, **CGI (Common Gateway Interface)** was commonly used
for server-side request processing.

### Problems with CGI

-   Creates a **new process for every request**
-   High memory consumption
-   Process creation makes it slower
-   Does not scale efficiently for a large number of requests

### How Servlets solve these problems

Servlets generally use a **thread-per-request model** within the
container instead of creating a new OS process for every request.

### Advantages of Servlets

-   Fast and efficient
-   Uses threads
-   Platform independent because Java is platform independent
-   Secure when properly designed and deployed
-   Scalable
-   Supports session management
-   Supports database connectivity

------------------------------------------------------------------------

# 3. Web Server vs Servlet Container

## Web Server

A **web server** receives and handles HTTP requests and sends HTTP
responses.

## Servlet Container

A **Servlet Container** is the environment that manages Servlets.

### Main responsibilities

-   Creates and manages Servlet objects
-   Manages the Servlet lifecycle
-   Maps URLs to Servlets
-   Creates/manages request and response objects
-   Handles request processing and threading
-   Calls lifecycle methods such as `init()`, `service()` and
    `destroy()`

### Example

**Apache Tomcat** provides a Servlet Container and supports Java web
applications.

> Java Servlets need a Servlet Container such as Tomcat to run as web
> components.

------------------------------------------------------------------------

# 4. Servlet Lifecycle

The main lifecycle methods are:

``` text
Servlet Loading
      ↓
   init()
      ↓
  service()
      ↓
  service()
      ↓
  service()
      ↓
 destroy()
```

## `init()`

-   Called by the container when the Servlet is initialized.
-   Called once for a Servlet instance.
-   Used for initialization work such as reading configuration.

## `service()`

-   Called by the container to process client requests.
-   For `HttpServlet`, it dispatches the request to methods such as
    `doGet()`, `doPost()`, `doPut()` and `doDelete()`.

## `destroy()`

-   Called before the Servlet is removed from service.
-   Used for cleanup work such as releasing resources.

------------------------------------------------------------------------

# 5. GenericServlet vs HttpServlet

## GenericServlet

`GenericServlet` is a protocol-independent base Servlet class.

-   It is not specific to HTTP.
-   It provides a generic Servlet implementation.
-   It can be extended when protocol-specific behavior is not required.

## HttpServlet

`HttpServlet` is designed specifically for HTTP-based web applications.

Common methods include:

-   `doGet()`
-   `doPost()`
-   `doPut()`
-   `doDelete()`
-   `doHead()`
-   `doOptions()`
-   `doTrace()`

For normal web applications, `HttpServlet` is commonly used.

------------------------------------------------------------------------

# 6. GET vs POST

## GET

Data is generally sent as part of the **URL/query string**.

Example:

``` text
/login?username=azhar
```

### Features

-   Data is visible in the URL
-   URL can be bookmarked/shared
-   Suitable for retrieving/fetching data
-   URL length limits apply
-   Should not be used for sensitive data merely because it is
    convenient

## POST

Data is sent in the **HTTP request body**.

### Features

-   Data is not placed in the URL
-   Suitable for form submission, login, registration, etc.
-   Can send larger request bodies than typical URLs
-   Better suited to sensitive form data, but **HTTPS is still
    required** for confidentiality

> POST is not automatically secure. Use HTTPS whenever sensitive data is
> transmitted.

------------------------------------------------------------------------

# 7. Request Object

The Servlet container provides an `HttpServletRequest` object containing
information about the client's request.

## Important methods

  Method                   Purpose
  ------------------------ ---------------------------------------------
  `getParameter()`         Gets a single request parameter value
  `getParameterValues()`   Gets multiple values for the same parameter
  `getParameterNames()`    Gets names of all request parameters
  `getRequestURL()`        Gets the request URL
  `getCookies()`           Gets cookies sent by the client
  `getMethod()`            Gets the HTTP method such as GET/POST
  `getSession()`           Gets or creates the HTTP session

### Example

``` java
String name = request.getParameter("name");
```

For multiple values:

``` java
String[] values = request.getParameterValues("skills");
```

------------------------------------------------------------------------

# 8. Response Object

The Servlet container provides an `HttpServletResponse` object to
generate the response.

## Important methods

  Method               Purpose
  -------------------- ------------------------------------------
  `setContentType()`   Sets the response content type
  `sendRedirect()`     Sends a redirect response to the client
  `getWriter()`        Gets a writer to send text/HTML response

### Example

``` java
response.setContentType("text/html");

PrintWriter out = response.getWriter();
out.println("<h1>Hello</h1>");
```

------------------------------------------------------------------------

# 9. Servlet Communication

**Servlet communication** means one Servlet communicates with another
Servlet/resource to:

-   Share request information
-   Transfer control
-   Reuse application logic
-   Divide application responsibilities

A single Servlet does not always need to handle the complete application
flow.

### Main approaches

1.  `RequestDispatcher`
2.  `sendRedirect()`

------------------------------------------------------------------------

# 10. RequestDispatcher

`RequestDispatcher` is used for **server-side communication** between
resources.

It provides two important methods:

-   `forward()`
-   `include()`

It can be obtained using:

``` java
RequestDispatcher rd =
    request.getRequestDispatcher("/servlet2");
```

------------------------------------------------------------------------

# 11. `forward()`

`forward()` transfers the request from one server-side resource to
another.

### Flow

``` text
Browser
   ↓
Servlet 1
   ↓
forward()
   ↓
Servlet 2
   ↓
Response
   ↓
Browser
```

### Important points

-   Server-side operation
-   Same request is forwarded
-   Same request/response objects are used
-   Browser does not make a new request
-   Browser URL remains unchanged
-   Generally faster than redirect because no second client request is
    required

### Example

``` java
RequestDispatcher rd =
    request.getRequestDispatcher("servlet2");

rd.forward(request, response);
```

------------------------------------------------------------------------

# 12. Working of `forward()`

``` text
1. Request reaches Servlet 1
2. Servlet 1 processes the data
3. Container obtains a RequestDispatcher
4. forward() is called
5. Current Servlet transfers control
6. The same request/response objects are used by Servlet 2
7. Servlet 2 generates the response
8. Response is returned to the browser
```

### Important concept

``` text
Browser → Request Object → Servlet 1
                         ↓
                       forward
                         ↓
                       Servlet 2
                         ↓
                      Response
                         ↓
                      Browser
```

> No new client request is created during forwarding, so the browser URL
> normally remains the same.

------------------------------------------------------------------------

# 13. `include()`

`include()` includes the output/content generated by another resource in
the current response.

### Example

``` java
RequestDispatcher rd =
    request.getRequestDispatcher("header");

rd.include(request, response);
```

The current Servlet can continue processing after `include()`.

### Flow

``` text
Browser
   ↓
Servlet 1
   ↓
include()
   ↓
Servlet 2 generates content
   ↓
Content is included in Servlet 1 response
   ↓
Servlet 1 continues
   ↓
Final Response
   ↓
Browser
```

### Example use

A common use is including reusable UI components:

``` text
Servlet/JSP
   ├── Header
   ├── Main Content
   └── Footer
```

------------------------------------------------------------------------

# 14. `forward()` vs `include()`

  -----------------------------------------------------------------------
  Feature                 `forward()`             `include()`
  ----------------------- ----------------------- -----------------------
  Purpose                 Transfers control       Includes another
                                                  resource's output

  Current resource        Processing is           Current resource
                          transferred             continues

  Output                  Target resource         Target output is added
                          generates the remaining to current response
                          response                

  Request                 Same request            Same request

  URL                     Usually unchanged       Usually unchanged

  Communication           Server-side             Server-side
  -----------------------------------------------------------------------

### Easy way to remember

``` text
forward() → "You handle the rest."

include() → "Give me your content; I will continue."
```

------------------------------------------------------------------------

# 15. `sendRedirect()`

`sendRedirect()` performs **client-side redirection**.

The Servlet tells the browser to make a new request to another URL.

### Flow

``` text
Browser
   ↓ Request
Servlet 1
   ↓ Redirect Response
Browser
   ↓ New Request
Servlet 2
   ↓
Response
   ↓
Browser
```

### Example

``` java
response.sendRedirect("servlet2");
```

------------------------------------------------------------------------

# 16. `forward()` vs `sendRedirect()`

  -----------------------------------------------------------------------
  Feature                 `forward()`             `sendRedirect()`
  ----------------------- ----------------------- -----------------------
  Communication           Server-side             Client-side

  Requests                Same request            New request

  Request object          Same request object     New request object

  URL                     Usually unchanged       Changes

  Speed                   Generally faster        Generally slower
                                                  because of extra
                                                  request

  Browser involvement     Browser is not aware of Browser receives
                          internal forwarding     redirect and sends new
                                                  request

  Data through request    Available               Not available in the
  attributes                                      new request

  Typical use             Internal server         Moving client to
                          transfer                another URL/resource
  -----------------------------------------------------------------------

### Easy memory trick

``` text
FORWARD  → Same request → URL unchanged → Server-side
REDIRECT → New request  → URL changes   → Client-side
```

------------------------------------------------------------------------

# 17. Request Attributes and Redirect

Request attributes belong to the **current request**.

Example:

``` java
request.setAttribute("name", "Azhar");
```

They can be accessed after a server-side `forward()`:

``` java
RequestDispatcher rd =
    request.getRequestDispatcher("servlet2");

rd.forward(request, response);
```

But they are not available in the new request created by
`sendRedirect()`.

### Why?

``` text
forward():
Request 1 → Servlet 1 → Servlet 2
             SAME REQUEST

redirect():
Request 1 → Servlet 1
               ↓
          Redirect response
               ↓
Browser creates Request 2 → Servlet 2
```

For redirect, data can be passed using URL rewriting/query parameters
when appropriate:

``` java
response.sendRedirect("s2?name=Azhar");
```

------------------------------------------------------------------------

# 18. Servlet Configuration

Hardcoding configuration values inside every Servlet creates maintenance
problems.

### Problems with hardcoding

-   Difficult to change configuration
-   Same values may need to be repeated
-   Poor maintainability
-   Configuration gets mixed with application logic

Servlets provide configuration mechanisms:

1.  `ServletConfig` → Servlet-specific configuration
2.  `ServletContext` → Application-wide configuration/data

------------------------------------------------------------------------

# 19. ServletConfig

`ServletConfig` is an object created and maintained by the Servlet
Container for **each Servlet**.

### Key point

> One Servlet has its own `ServletConfig` object.

It is mainly used to provide **Servlet-specific initialization
parameters**.

### Flow

``` text
Web Application Starts
        ↓
Container Reads web.xml
        ↓
Servlet Configuration Found
        ↓
ServletConfig Created
        ↓
Init Parameters Stored
        ↓
init() Called
        ↓
Servlet Uses Configuration
```

------------------------------------------------------------------------

# 20. ServletConfig in `web.xml`

Example:

``` xml
<servlet>
    <servlet-name>S1</servlet-name>
    <servlet-class>MyServlet</servlet-class>

    <init-param>
        <param-name>user</param-name>
        <param-value>admin</param-value>
    </init-param>
</servlet>
```

### Reading ServletConfig

``` java
ServletConfig config = getServletConfig();

String value =
    config.getInitParameter("user");
```

------------------------------------------------------------------------

# 21. ServletConfig Methods

  -----------------------------------------------------------------------
  Method                              Purpose
  ----------------------------------- -----------------------------------
  `getInitParameter()`                Gets the value of a specific
                                      initialization parameter

  `getInitParameterNames()`           Gets names of all initialization
                                      parameters

  `getServletContext()`               Gets the application-wide
                                      `ServletContext`

  `getServletName()`                  Gets the Servlet's configured name
  -----------------------------------------------------------------------

### Example

``` java
ServletConfig config = getServletConfig();

String user =
    config.getInitParameter("user");
```

------------------------------------------------------------------------

# 22. ServletContext

`ServletContext` is an **application-wide object** created by the
Servlet Container.

> It is shared among all Servlets belonging to the same web application.

### Main purpose

-   Store application-wide information
-   Share data between Servlets
-   Access application-level configuration
-   Provide access to the application environment

### Flow

``` text
Web Application Starts
        ↓
Container creates ServletContext
        ↓
Shared by all Servlets
        ↓
Servlet 1 ─┐
Servlet 2 ─┼──→ Same ServletContext
Servlet 3 ─┘
        ↓
Application Stops
        ↓
ServletContext destroyed
```

------------------------------------------------------------------------

# 23. Context Parameters in `web.xml`

Application-wide initialization parameters can be defined using
`context-param`.

``` xml
<context-param>
    <param-name>appName</param-name>
    <param-value>LearningPath</param-value>
</context-param>
```

### Reading Context Parameter

``` java
ServletContext ctx =
    getServletContext();

String appName =
    ctx.getInitParameter("appName");
```

------------------------------------------------------------------------

# 24. ServletContext Attributes

`ServletContext` also supports attributes for sharing application-level
objects/data.

### Set attribute

``` java
ServletContext ctx = getServletContext();

ctx.setAttribute("count", 100);
```

### Get attribute

``` java
Object value =
    ctx.getAttribute("count");
```

### Important methods

-   `setAttribute()`
-   `getAttribute()`
-   `removeAttribute()`

------------------------------------------------------------------------

# 25. ServletConfig vs ServletContext

  -------------------------------------------------------------------------
  Feature                 ServletConfig           ServletContext
  ----------------------- ----------------------- -------------------------
  Scope                   Servlet-specific        Application-wide

  Number                  One per Servlet         One per web application

  Shared among Servlets?  No                      Yes

  Main use                Servlet-specific        Global/application-wide
                          configuration           configuration and data

  Init parameters         `init-param`            `context-param`

  Created by              Servlet Container       Servlet Container

  Access                  `getServletConfig()`    `getServletContext()`

  Can access Context?     Yes                     Application object itself
  -------------------------------------------------------------------------

### Easy memory trick

``` text
ServletConfig  → CONFIG for ONE Servlet

ServletContext → CONTEXT for WHOLE application
```

------------------------------------------------------------------------

# 26. Init Parameter vs Attribute

  -----------------------------------------------------------------------
  Feature                 Init Parameter          Attribute
  ----------------------- ----------------------- -----------------------
  Purpose                 Configuration           Runtime data/object
                                                  sharing

  Usually defined in      `web.xml`               Java code

  Value                   Configuration value     Any object/value

  Methods                 `getInitParameter()`    `setAttribute()`,
                                                  `getAttribute()`

  Scope                   ServletConfig: one      Depends on object used
                          Servlet; Context: whole 
                          application             
  -----------------------------------------------------------------------

### Example

``` java
// Configuration
String user = config.getInitParameter("user");

// Runtime shared data
ctx.setAttribute("count", 100);
```

------------------------------------------------------------------------

# 27. When to Use ServletConfig?

Use `ServletConfig` when configuration is **specific to one Servlet**.

Examples:

-   Servlet-specific username/configuration
-   Servlet-specific file path
-   Servlet-specific initialization value
-   Any setting that should not be shared with every Servlet

### Example

``` text
LoginServlet → login-specific configuration
ReportServlet → report-specific configuration
```

Each can have its own `ServletConfig`.

------------------------------------------------------------------------

# 28. When to Use ServletContext?

Use `ServletContext` when information is required by **multiple
Servlets** or belongs to the entire application.

Examples:

-   Application name
-   Global configuration
-   Shared application-level objects
-   Common resources/data
-   Values that need to be accessed by multiple Servlets

### Example

``` text
Servlet 1 ─┐
Servlet 2 ─┼──→ ServletContext
Servlet 3 ─┘
```

------------------------------------------------------------------------

# 29. ServletConfig + ServletContext Relationship

Every Servlet has access to its own `ServletConfig`.

The ServletConfig can provide access to the common ServletContext.

``` text
Servlet
   ↓
ServletConfig
   ↓
ServletContext
   ↓
Whole Web Application
```

Example:

``` java
ServletConfig config = getServletConfig();

ServletContext context =
    config.getServletContext();
```

------------------------------------------------------------------------

# 30. Annotation-Based Servlet Configuration

Instead of configuring every Servlet in `web.xml`, annotations can be
used.

The commonly used annotation is:

``` java
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {

    @Override
    protected void doGet(
            HttpServletRequest request,
            HttpServletResponse response) {

        // Servlet logic
    }
}
```

### What does `@WebServlet` do?

It tells the Servlet Container that the class is a Servlet and provides
its URL mapping.

Example:

``` java
@WebServlet("/login")
```

Then:

``` text
http://localhost:8080/app/login
```

can map to the `LoginServlet`.

### Why use annotations?

-   Less XML configuration
-   Easier mapping
-   Configuration stays close to the Servlet class
-   Cleaner project structure

------------------------------------------------------------------------

# 31. Complete Servlet Concept Map

``` text
                         JAVA SERVLET
                              │
            ┌─────────────────┼─────────────────┐
            ↓                 ↓                 ↓
       Request/Response   Lifecycle        Configuration
            │                 │                 │
     ┌──────┴──────┐     init/service/      ┌──────┴──────┐
     ↓             ↓       destroy           ↓             ↓
 HttpServletRequest                 ServletConfig   ServletContext
 HttpServletResponse                       │             │
     │                                     │             │
 getParameter()                         One Servlet   Whole App
 setContentType()                         │             │
 sendRedirect()                           ↓             ↓
                                      init-param    context-param
```

------------------------------------------------------------------------

# 32. Quick Revision

### Servlet

> Java program that runs inside a Servlet Container to handle HTTP
> requests and generate responses.

### Servlet Container

> Manages Servlet objects, lifecycle, request/response processing and
> threading.

### Lifecycle

``` text
init() → service() → destroy()
```

### RequestDispatcher

> Server-side communication.

``` text
forward() → transfers control
include() → includes another resource's output
```

### Redirect

> Client-side communication.

``` text
sendRedirect() → new request + URL changes
```

### ServletConfig

> Configuration for **one Servlet**.

### ServletContext

> Shared object/configuration for the **whole application**.

### GET vs POST

``` text
GET  → URL/query string → Fetch/retrieve data
POST → Request body     → Submit/create/process data
```

### Most Important Memory Line

``` text
Config  = ONE Servlet
Context = WHOLE Application

Forward  = Same Request + URL unchanged
Redirect = New Request + URL changes

Include  = Include content + continue execution
```
