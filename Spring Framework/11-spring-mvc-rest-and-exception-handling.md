# Spring MVC -- REST APIs & Exception Handling

> **Topics:** Spring MVC (DispatcherServlet, HandlerMapping, HandlerAdapter), Complete request lifecycle, `@PathVariable`, `@RequestParam`, `@RequestBody`, `@RestController` vs `@Controller`, Interceptors vs Filters, `@ExceptionHandler`, `@ControllerAdvice`, `@RestControllerAdvice`, Custom exceptions, Validation + Exception handling, Error Response DTO

## 1. Spring MVC

**Spring MVC** ek web framework hai jo **Servlet API ke upar** build hua hai aur incoming HTTP requests ko organized way me handle karta hai.

### MVC ka meaning

Spring MVC application ko 3 main parts me divide karta hai:

- **Model** → application ka data/state.
- **View** → data ko user ko kaise present karna hai.
- **Controller** → request receive karta hai, decide karta hai kya karna hai, aur response/data return karta hai.

Spring Boot me Spring MVC ka setup mostly auto-configure ho jata hai.

---

# 2. Front Controller Pattern

Spring MVC **Front Controller pattern** follow karta hai.

Iska main component:

```text
DispatcherServlet
```Instead of multiple separate entry points, normally requests ek central entry point — `DispatcherServlet` — se pass hoti hain.

### Basic flow

```text
Client
   ↓
Tomcat / Servlet Container
   ↓
DispatcherServlet
   ↓
HandlerMapping
   ↓
HandlerAdapter
   ↓
Controller
   ↓
Service
   ↓
Repository
   ↓
Database
```Response reverse direction me aata hai.

---

# 3. Servlet Container

**Tomcat** ek Servlet Container hai.

Iska kaam:

- incoming TCP/HTTP connection accept karna
- HTTP request ko parse karna
- `HttpServletRequest` / `HttpServletResponse` objects banana
- Servlet ko request forward karna
- response ko client tak bhejna

Spring Boot me embedded Tomcat commonly use hota hai.

---

# 4. DispatcherServlet

`DispatcherServlet` Spring MVC ka **front controller** hai.

Iski responsibilities:

1. HTTP request receive karna.
2. Appropriate handler/controller find karna.
3. `HandlerAdapter` ke through controller method invoke karwana.
4. Controller ka result process karna.
5. REST response ko JSON me convert karwana.
6. Exceptions ko appropriate exception handling mechanism tak bhejna.
7. Final HTTP response client ko return karna.

> Simple way: **DispatcherServlet decides "request ko kahan aur kaise process karna hai".**

---

# 5. HandlerMapping

`HandlerMapping` ka kaam hai:

> Given request ke liye **kaunsa handler/controller method execute hoga?**

Example:

```java
@GetMapping("/users")
public List<User> getUsers() {
    return service.getUsers();
}
```Mapping internally roughly:

```text
GET + /users
      ↓
UserController.getUsers()
```

### Startup time

Application start hote time Spring controllers scan karta hai aur request mappings ka internal registry/table prepare karta hai.

Isliye har request par controller annotations ko from scratch scan karna necessary nahi hota.

---

# 6. HandlerAdapter

`HandlerAdapter` ka kaam hai selected handler ko **actually invoke karna**.

Flow:

```text
HandlerMapping
      ↓
finds handler
      ↓
HandlerAdapter
      ↓
invokes controller method
```Different handler types ke liye different adapters ho sakte hain.

Simple idea:

> **HandlerMapping finds WHAT to call; HandlerAdapter knows HOW to call it.**

---

# 7. Complete Request Lifecycle

Example:

```http
GET /users/101
```

### Step 1 — Client

Client HTTP request send karta hai.

```text
GET http://localhost:8080/users/101
```

### Step 2 — Tomcat

Tomcat request receive karta hai aur HTTP request ko Java request objects me parse karta hai.

### Step 3 — DispatcherServlet

Tomcat request ko Spring MVC ke `DispatcherServlet` tak forward karta hai.

### Step 4 — HandlerMapping

DispatcherServlet HandlerMapping se poochta hai:

> Is URL + HTTP method ko kaunsa handler handle karega?

Suppose:

```java
@GetMapping("/users/{id}")
public User getUser(@PathVariable int id) {
    ...
}
```HandlerMapping controller method identify kar leta hai.

### Step 5 — HandlerAdapter

DispatcherServlet suitable HandlerAdapter select karta hai.

HandlerAdapter controller method ke arguments resolve karta hai, jaise:

- `@PathVariable`
- `@RequestParam`
- `@RequestBody`
- `@RequestHeader`

Then controller method invoke hota hai.

### Step 6 — Controller

Controller generally request ko service layer tak forward karta hai.

```java
@GetMapping("/users/{id}")
public User getUser(@PathVariable int id) {
    return service.getUser(id);
}
```

### Step 7 — Service

Business logic service layer me hota hai.

```java
public User getUser(int id) {
    return repo.findById(id).orElseThrow(...);
}
```

### Step 8 — Repository

Repository database se data fetch karta hai.

```text
Repository → Database
```

### Step 9 — Result back

```text
Database
 ↓
Repository
 ↓
Service
 ↓
Controller
 ↓
HandlerAdapter
 ↓
DispatcherServlet
 ↓
HTTP Response
 ↓
Client
```

---

# 8. `@PathVariable`

URL ke andar se value extract karne ke liye.

```java
@GetMapping("/students/{id}")
public Student getStudent(@PathVariable int id) {
    return service.getStudent(id);
}
```Request:

```text
GET /students/101
```Then:

```text
id = 101
```

---

# 9. `@RequestParam`

Query parameter read karne ke liye.

```java
@GetMapping("/students")
public Student getStudent(@RequestParam int id) {
    ...
}
```Request:

```text
/students?id=101
```Here:

```text
id = 101
```

---

# 10. `@RequestBody`

Request body ke JSON ko Java object me convert karne ke liye.

```java
@PostMapping("/students")
public Student addStudent(@RequestBody Student student) {
    return service.addStudent(student);
}
```JSON:

```json
{
  "name": "Azhar",
  "age": 21
}
```Spring/Jackson JSON ko `Student` object me deserialize karta hai.

---

# 11. `@RestController`

```java
@RestController
public class StudentController {
}
```Conceptually:

```java
@Controller
@ResponseBody
```

`@RestController` ka use REST APIs me commonly hota hai.

Controller method ka returned object generally response body ke through JSON/XML representation me convert hota hai.

---

# 12. `@Controller` vs `@RestController`

### `@Controller`

Traditional MVC / view-based applications me useful.

```java
@Controller
public class StudentController {

    @GetMapping("/students")
    public String students() {
        return "students";
    }
}
```Here `"students"` ek view name ho sakta hai.

### `@RestController`

REST API ke liye:

```java
@RestController
public class StudentController {

    @GetMapping("/students")
    public Student students() {
        return student;
    }
}
```Returned object response body me serialize hota hai.

---

# 13. REST Response Conversion

Agar controller:

```java
@RestController
public class UserController {

    @GetMapping("/user")
    public User getUser() {
        return user;
    }
}
```return karta hai:

```java
User
```to Spring MVC `HttpMessageConverter` ke through object ko JSON representation me convert kar sakta hai.

Example:

```json
{
  "id": 101,
  "name": "Azhar"
}
```

---

# 14. Interceptors

Interceptor request processing ke around additional logic execute karne ke liye use hota hai.

Typical flow:

```text
Request
  ↓
Interceptor.preHandle()
  ↓
Controller
  ↓
Interceptor.postHandle()
  ↓
Response processing
  ↓
Interceptor.afterCompletion()
```

### `preHandle()`

Controller method execute hone se pehle.

Use cases:

- authentication checks
- request logging
- permission checks

### `postHandle()`

Controller method ke baad, response complete hone se pehle.

### `afterCompletion()`

Request processing complete hone ke baad.

Use cases:

- cleanup
- final logging
- resource release

---

# 15. Interceptor vs Filter

### Filter

Servlet/container level par kaam karta hai.

```text
Client
 ↓
Filter
 ↓
DispatcherServlet
 ↓
Spring MVC
```Filter Spring MVC ke outside bhi operate kar sakta hai.

### Interceptor

Spring MVC ke andar request handling lifecycle ka part hai.

```text
DispatcherServlet
 ↓
Interceptor
 ↓
Controller
```

---

# 16. Exception Handling in Spring MVC

Exception handling ka purpose:

> Application me exception aane par proper, controlled HTTP response return karna instead of exposing raw errors.

Example:

```java
public Student getStudent(int id) {
    return repo.findById(id)
            .orElseThrow(() ->
                new StudentNotFoundException("Student not found"));
}
```Agar exception properly handle nahi hui, client ko unwanted error response mil sakta hai.

---

# 17. `@ExceptionHandler`

`@ExceptionHandler` kisi specific exception ko handle karne ke liye use hota hai.

### Controller-level handling

```java
@RestController
public class StudentController {

    @GetMapping("/students/{id}")
    public Student getStudent(@PathVariable int id) {
        return service.getStudent(id);
    }

    @ExceptionHandler(StudentNotFoundException.class)
    public ResponseEntity<String> handleStudentNotFound(
            StudentNotFoundException ex) {

        return ResponseEntity
                .status(HttpStatus.NOT_FOUND)
                .body(ex.getMessage());
    }
}
```Agar isi controller ke request processing me `StudentNotFoundException` throw hoti hai, ye handler execute ho sakta hai.

### Important

`@ExceptionHandler` ka scope normally us controller ke exception handling tak hota hai jahan method defined hai.

---

# 18. `@ControllerAdvice`

`@ControllerAdvice` centralized exception handling provide karta hai.

Instead of har controller me same:

```java
@ExceptionHandler(...)
```likhne ke, ek common class bana sakte hain.

```java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(StudentNotFoundException.class)
    public ResponseEntity<String> handleStudentNotFound(
            StudentNotFoundException ex) {

        return ResponseEntity
                .status(HttpStatus.NOT_FOUND)
                .body(ex.getMessage());
    }
}
```Ye multiple controllers ke liye common handling provide kar sakta hai.

---

# 19. `@RestControllerAdvice`

`@RestControllerAdvice` REST APIs ke liye convenient annotation hai.

Conceptually:

```java
@ControllerAdvice
@ResponseBody
```Example:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(StudentNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleStudentNotFound(
            StudentNotFoundException ex) {

        ErrorResponse error =
                new ErrorResponse(
                    "STUDENT_NOT_FOUND",
                    ex.getMessage()
                );

        return ResponseEntity
                .status(HttpStatus.NOT_FOUND)
                .body(error);
    }
}
```REST API me structured JSON error response easily return kiya ja sakta hai.

---

# 20. Exception Handling Scope

Simple hierarchy:

```text
@ExceptionHandler
       ↓
Controller-specific handling

@ControllerAdvice
       ↓
Global/shared MVC exception handling

@RestControllerAdvice
       ↓
Global/shared REST API exception handling
```Agar same exception ke liye controller-level aur global handlers available hon, Spring exception-resolution rules ke according suitable handler choose karta hai.

---

# 21. Custom Exception

Application-specific exception bana sakte hain:

```java
public class StudentNotFoundException
        extends RuntimeException {

    public StudentNotFoundException(String message) {
        super(message);
    }
}
```Service:

```java
public Student getStudent(int id) {

    return repo.findById(id)
            .orElseThrow(() ->
                new StudentNotFoundException(
                    "Student not found with id: " + id
                ));
}
```Global handler:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(StudentNotFoundException.class)
    public ResponseEntity<String> handle(
            StudentNotFoundException ex) {

        return ResponseEntity
                .status(HttpStatus.NOT_FOUND)
                .body(ex.getMessage());
    }
}
```

---

# 22. Multiple Exceptions

Ek handler multiple exception types ke liye bhi define kiya ja sakta hai.

```java
@ExceptionHandler({
    IllegalArgumentException.class,
    NumberFormatException.class
})
public ResponseEntity<String> handleBadRequest(Exception ex) {

    return ResponseEntity
            .badRequest()
            .body(ex.getMessage());
}
```

---

# 23. Validation + Exception Handling

DTO validation:

```java
public class StudentDTO {

    @NotBlank
    private String name;

    @Min(1)
    private int age;
}
```Controller:

```java
@PostMapping("/students")
public Student add(
        @Valid @RequestBody StudentDTO student) {

    return service.add(student);
}
```Invalid request par validation-related exception generate ho sakti hai.

Global handler me validation errors ko clean API response me convert kiya ja sakta hai.

Example concept:

```json
{
  "name": "Name is required",
  "age": "Age must be greater than 0"
}
```

---

# 24. Common HTTP Status Codes for Exceptions

| Situation | Status |
|---|---:|
| Resource not found | `404 NOT_FOUND` |
| Invalid request/data | `400 BAD_REQUEST` |
| Authentication required/failed | `401 UNAUTHORIZED` |
| Access denied | `403 FORBIDDEN` |
| Conflict | `409 CONFLICT` |
| Unexpected server error | `500 INTERNAL_SERVER_ERROR` |

Status code exception ke actual meaning/use case ke according choose karna chahiye.

---

# 25. Error Response DTO

Production APIs me raw string ki jagah structured error object useful hota hai.

```java
public class ErrorResponse {

    private String code;
    private String message;

    // constructor, getters, setters
}
```Response:

```json
{
  "code": "STUDENT_NOT_FOUND",
  "message": "Student not found with id: 101"
}
```

---

# 26. Spring MVC Request Lifecycle — Short Revision

```text
Client
  ↓
Tomcat
  ↓
DispatcherServlet
  ↓
HandlerMapping
  ↓
HandlerAdapter
  ↓
Controller
  ↓
Service
  ↓
Repository
  ↓
Database
  ↑
Repository
  ↑
Service
  ↑
Controller
  ↑
HandlerAdapter
  ↑
DispatcherServlet
  ↑
HTTP Response
  ↑
Client
```

### If exception occurs

```text
Controller / Service / Repository
          ↓
       Exception
          ↓
Exception Resolution
          ↓
@ExceptionHandler /
@ControllerAdvice /
@RestControllerAdvice
          ↓
HTTP Error Response
```

---

# 27. Interview One-Liners

**DispatcherServlet:**  
Spring MVC ka front controller aur central request dispatcher.

**HandlerMapping:**  
Request URL + HTTP method ko appropriate handler se map karta hai.

**HandlerAdapter:**  
Selected handler/controller method ko invoke karne ka mechanism provide karta hai.

**@PathVariable:**  
URL path se value extract karta hai.

**@RequestParam:**  
Query parameter se value read karta hai.

**@RequestBody:**  
HTTP request body ko Java object me bind/deserialise karta hai.

**@ExceptionHandler:**  
Specific controller/exception-handling scope me exception handle karta hai.

**@ControllerAdvice:**  
Multiple controllers ke liye centralized MVC exception handling provide karta hai.

**@RestControllerAdvice:**  
REST controllers ke liye centralized exception handling + response body support provide karta hai.

**Filter:**  
Servlet/container level request processing.

**Interceptor:**  
Spring MVC request lifecycle ke andar pre/post processing.

---

# 28. Easy Mental Model

```text
DispatcherServlet
      |
      | "Kisko call karna hai?"
      ↓
HandlerMapping
      |
      | "Ye controller/method hai"
      ↓
HandlerAdapter
      |
      | "Isko kaise invoke karna hai?"
      ↓
Controller
      |
      ↓
Service
      |
      ↓
Repository
      |
      ↓
Database
```Exception aaye:

```text
Exception
   ↓
@ExceptionHandler
   OR
@ControllerAdvice
   OR
@RestControllerAdvice
   ↓
Clean Error Response
```
