# Spring Boot -- Spring Container, CRUD, Validation, Logging & Profiles

> **Topics:** Spring Container internals, Bean lifecycle, Bean scope, CRUD UPDATE & DELETE APIs, `@PathVariable`, `@RequestBody`, Validation annotations, SLF4J logging, Logback, Spring Profiles (dev/prod), Profile-based configuration

> Hinglish practical notes --- handwritten points ko priority dekar,
> missing concepts ko improve karke.

---

# 1. Spring Container

Spring Container ek **factory / manager** ki tarah kaam karta hai jo
application ke objects (Beans) ko create aur manage karta hai.

### Simple idea

```text
Spring Container
      ↓
Creates Beans
      ↓
Stores/Manages Beans
      ↓
Injects Dependencies
      ↓
Application uses Beans
```

## Bean kya hai?

**Bean = aisa object jise Spring Container create aur manage karta
hai.**

Example:

```java
@Service
public class StudentService {
}
```

`@Service` ki wajah se Spring is class ka object/bean manage kar sakta
hai.

---

# 2. Spring Application ka Startup Flow

Jab hum run karte hain:

```java
SpringApplication.run(DemoApplication.class, args);
```

Basic flow:

```text
SpringApplication.run()
        ↓
Create ApplicationContext
        ↓
Configuration / Auto-Configuration
        ↓
Component Scanning
        ↓
Beans detect
        ↓
Beans create
        ↓
Beans Container mein store
        ↓
Dependencies inject
        ↓
Application ready
```

---

# 3. Configuration + Component Scanning

Spring Boot mein:

```java
@SpringBootApplication
```

important annotation hai.

Ye Spring ko application configuration aur component scanning setup
karne mein help karta hai.

Suppose main package:

```text
com.example.demo
```

Aur uske andar:

```text
entity
service
repository
controller
```

Spring in components ko scan karke required beans create/manage karta
hai.

Common stereotype annotations:

```text
@Component
@Service
@Repository
@Controller
@RestController
```

Inhe commonly **stereotype annotations** kaha jata hai.

---

# 4. Dependency Injection (DI)

Agar `StudentService` ko `StudentRepository` chahiye:

```text
StudentService
      ↓
needs
      ↓
StudentRepository
```

Spring Container required dependency provide karta hai.

Example:

```java
@Service
public class StudentService {

    private final StudentRepository repo;

    public StudentService(StudentRepository repo) {
        this.repo = repo;
    }
}
```

Concept:

```text
Spring Container
      ↓
creates Repository Bean
      ↓
creates Service Bean
      ↓
injects Repository into Service
```

---

# 5. Multiple Beans / Bean Conflict

Agar same type ke multiple beans available hon, Spring ko decide karna
pad sakta hai ki kaunsa bean inject karna hai.

Example:

```java
@Service
class MyService {
}

@Service
class AnotherService {
}
```

Aise cases mein **`@Qualifier`** use kiya ja sakta hai.

```java
public MyController(
        @Qualifier("myService") MyService service) {
    this.service = service;
}
```

Another option:

```java
@Primary
```

`@Primary` kisi bean ko default preference de sakta hai jab multiple
candidates available hon.

---

# 6. Bean Lifecycle

Basic Bean Lifecycle:

```text
1. Bean Created
       ↓
2. Dependencies Injected
       ↓
3. Initialization
       ↓
4. Bean Ready to Use
       ↓
5. Application runs
       ↓
6. Bean Destroyed
```

Conceptually:

```text
Create
  ↓
Inject Dependencies
  ↓
Initialize
  ↓
Ready
  ↓
Destroy
```

Initialization/destruction ke liye lifecycle callbacks bhi use kiye ja
sakte hain.

Example:

```java
@PostConstruct
public void init() {
    // initialization
}

@PreDestroy
public void cleanup() {
    // cleanup
}
```

> Modern Jakarta-based Spring applications mein ye annotations
> `jakarta.annotation` package se aate hain.

---

# 7. Bean Scope

Bean scope decide karta hai ki Spring bean object ko kitne instances /
kis lifecycle ke saath manage karega.

Common scopes:

  -----------------------------------------------------------------------
  Scope                               Basic Meaning
  ----------------------------------- -----------------------------------
  `singleton`                         Container mein normally one shared
                                      instance

  `prototype`                         Injection/request ke context mein
                                      new instance creation

  `request`                           Web request scope

  `session`                           HTTP session scope

  `application`                       Web application scope
  -----------------------------------------------------------------------

### Default scope

Spring ka default bean scope:

```text
singleton
```

---

# 8. Singleton vs Request Scope

### Singleton

```text
Application
    ↓
One shared bean instance
```

### Request Scope

```text
HTTP Request 1 → Object A
HTTP Request 2 → Object B
HTTP Request 3 → Object C
```

Request-scoped bean web application mein individual HTTP request ke
lifecycle se associated hota hai.

---

# 9. Spring Basically kya karta hai?

Handwritten notes ka core idea:

> **Spring ek smart object factory/manager hai with Dependency Injection
> and Auto-Configuration.**

More accurately:

```text
Spring Container
→ Objects/Beans manage karta hai
→ Dependencies inject karta hai
→ Configuration handle karta hai
```

Spring Boot is process ko conventions aur auto-configuration ke through
easier banata hai.

---

# 10. CRUD -- UPDATE API

**UPDATE = existing data modify karna.**

Important:

> Update karte time generally pehle verify karte hain ki requested
> record exist karta hai.

Example endpoint:

```text
PUT /students/{id}
```

---

# 11. Controller -- Update API

Example:

```java
@PutMapping("/{id}")
public Student update(
        @PathVariable int id,
        @RequestBody Student s) {

    return service.updateStudent(id, s);
}
```

Request:

```text
PUT /students/1
```

Body:

```json
{
  "name": "Azhar Khan",
  "age": 22
}
```

---

# 12. `@PathVariable`

```java
@PathVariable int id
```

URL se ID extract karta hai.

Example:

```text
/students/1
```

Then:

```text
id = 1
```

---

# 13. `@RequestBody`

```java
@RequestBody Student s
```

Incoming JSON ko Java object mein convert karne mein help karta hai.

Example JSON:

```json
{
  "name": "Azhar",
  "age": 21
}
```

Concept:

```text
JSON
 ↓
Jackson
 ↓
Student Java Object
```

---

# 14. Update -- Service Layer

Typical logic:

```java
public Student updateStudent(int id, Student s) {

    Student existing = repo.findById(id)
            .orElseThrow(() ->
                new RuntimeException("Student not found"));

    existing.setName(s.getName());
    existing.setAge(s.getAge());

    return repo.save(existing);
}
```

---

# 15. Update ka Internal Flow

```text
Client
  ↓
Controller
  ↓
Service
  ↓
findById(id)
  ↓
Existing Entity
  ↓
Modify Object
  ↓
repo.save(existing)
  ↓
Database Update
```

Important:

> Yahan hum **existing object ko modify** kar rahe hain, directly new
> student create nahi kar rahe.

---

# 16. Update -- Step by Step

### Step 1: Existing record fetch

```java
repo.findById(id)
```

Internally conceptually SQL:

```sql
SELECT *
FROM student
WHERE id = 1;
```

### Step 2: Record not found

Agar ID nahi mili:

```java
orElseThrow(...)
```

Exception throw kar sakte hain.

```text
Exception
   ↓
Execution stops / error handling
```

### Step 3: Existing object modify

```java
existing.setName(s.getName());
existing.setAge(s.getAge());
```

### Step 4: Save again

```java
repo.save(existing);
```

---

# 17. `save()` kaise decide karta hai INSERT ya UPDATE?

Conceptually:

### Case 1 -- New Entity

```text
No existing ID / new entity
        ↓
INSERT
```

### Case 2 -- Existing Entity

```text
Existing entity / existing identifier
        ↓
UPDATE
```

JPA/Hibernate entity state and identifier ke basis par persistence
operation determine karta hai.

Example concept:

```sql
INSERT INTO student ...
```

vs.

```sql
UPDATE student
SET name = ?, age = ?
WHERE id = ?;
```

---

# 18. Important: Update mein Duplicate kyun nahi banana?

Wrong approach:

```java
Student newStudent = new Student();
...
repo.save(newStudent);
```

Agar existing record ka ID correctly associate nahi hua, to new row
insert ho sakti hai.

Correct basic approach:

```text
Find existing
    ↓
Modify existing
    ↓
save(existing)
```

---

# 19. DELETE API

Delete ka meaning:

> Database se existing record remove karna.

Controller:

```java
@DeleteMapping("/{id}")
public String delete(@PathVariable int id) {

    service.deleteStudent(id);

    return "Deleted";
}
```

---

# 20. Delete -- Service Layer

```java
public void deleteStudent(int id) {

    if (!repo.existsById(id)) {
        throw new RuntimeException("Student not found");
    }

    repo.deleteById(id);
}
```

Basic flow:

```text
Check ID exists
      ↓
Yes
      ↓
deleteById(id)
      ↓
Database DELETE
```

---

# 21. Delete Flow

```text
Client
  ↓
Controller
  ↓
Service
  ↓
existsById(id)
  ↓
deleteById(id)
  ↓
Database
```

Conceptual SQL:

```sql
DELETE FROM student
WHERE id = 1;
```

---

# 22. Hard Delete

**Hard Delete**:

```text
DELETE record
     ↓
Data physically removed from table
```

Meaning:

> Data normal application query se permanently gone ho sakta hai.

Use carefully, especially production systems mein.

---

# 23. Soft Delete

Real applications mein kai baar hard delete avoid kiya jata hai.

Instead of:

```sql
DELETE FROM student
WHERE id = 1;
```

we can maintain a flag:

```java
private boolean isDeleted;
```

Then:

```sql
UPDATE student
SET is_deleted = true
WHERE id = 1;
```

Record database mein remain karta hai but application use normally show
nahi karti.

---

# 24. Soft Delete kyun?

Handwritten notes ke points:

```text
Data Recovery
Audit Logs
Safety
```

Additional benefits:

-   Historical records maintain karna
-   Accidental deletion recovery
-   Business/audit requirements
-   Data traceability

---

# 25. Soft Delete ka Basic Flow

```text
Delete Request
     ↓
Service
     ↓
Find Existing
     ↓
isDeleted = true
     ↓
save()
     ↓
Database
```

Fetch queries mein:

```text
WHERE is_deleted = false
```

jaisa condition use kiya ja sakta hai.

---

# 26. REST API -- URL Design

REST API mein URL generally **resource ko represent karta hai**.

Example:

```text
/students
```

Specific student:

```text
/students/1
```

Yahan:

```text
students = Resource
1        = Specific Resource ID
```

---

# 27. REST API URL Example

Suppose:

```text
/students/1
```

Meaning:

> Student resource jiska ID `1` hai.

Method ke according operation change hota hai:

```text
GET    /students/1 → Read
PUT    /students/1 → Update
DELETE /students/1 → Delete
```

---

# 28. REST API URL ke 3 Important Parts

## 1. Specific Resource

```text
/students/1
```

Specific student.

## 2. Filters / Query Parameters

```text
/students?age=21
```

Filtering ke liye.

## 3. Full Object / Request Body

POST/PUT mein:

```json
{
  "name": "Azhar",
  "age": 21
}
```

Body mein complete/required data bheja ja sakta hai.

---

# 29. Path Variable vs Request Param vs Request Body

  Concept           Example              Purpose
  ----------------- -------------------- ---------------------
  `@PathVariable`   `/students/1`        Resource ID
  `@RequestParam`   `/students?age=21`   Query/filter values
  `@RequestBody`    JSON body            Data/object

Example:

```java
@GetMapping("/{id}")
public Student get(@PathVariable int id) {
    ...
}
```

```java
@GetMapping
public List<Student> getByAge(
        @RequestParam int age) {
    ...
}
```

```java
@PostMapping
public Student add(
        @RequestBody Student s) {
    ...
}
```

---

# 30. Validation

Validation ka purpose:

> Incoming data valid hai ya nahi, check karna.

Example request:

```json
{
  "name": "",
  "age": -5
}
```

Ye invalid data ho sakta hai.

---

# 31. Bean Validation

Dependency:

```text
Spring Boot Starter Validation
```

Example:

```java
@NotBlank
private String name;

@Min(1)
private int age;
```

Controller:

```java
@PostMapping
public Student add(
        @Valid @RequestBody Student s) {

    return service.addStudent(s);
}
```

---

# 32. Validation ka Example

```java
@NotBlank(message = "Name cannot be blank")
private String name;

@Min(value = 1, message = "Age must be positive")
private int age;
```

Invalid request par validation error response generate kiya ja sakta
hai.

---

# 33. Validation ka Flow

```text
Client
  ↓
JSON Request
  ↓
@RequestBody
  ↓
@Valid
  ↓
Validation
  ↓
Valid?
 ┌───────┴───────┐
Yes              No
 ↓                ↓
Controller       Error
 ↓
Service
```

---

# 34. `@NotBlank`

String ke liye useful:

```java
@NotBlank
private String name;
```

Blank/empty/whitespace-only values ko reject karne mein help karta hai.

Example invalid:

```json
{
  "name": ""
}
```

---

# 35. `@Min`

Numeric minimum define karta hai:

```java
@Min(1)
private int age;
```

Meaning:

```text
age >= 1
```

---

# 36. `@Size`

String/collection size validate karne ke liye:

```java
@Size(min = 2, max = 50)
private String name;
```

---

# 37. `@Email`

Email field validate karne ke liye:

```java
@Email
private String email;
```

---

# 38. `@Valid`

```java
@Valid @RequestBody Student s
```

Spring ko request body ke validation constraints apply karne ke liye
trigger karta hai.

---

# 39. Logging

**Logging = application ke behaviour/events ko record karna over time.**

Logs debugging aur monitoring mein important hain.

Example events:

```text
Request received
Student added
Student updated
Database error
Exception occurred
```

---

# 40. Why Logging?

Handwritten notes ke according:

```text
No logs
  ↓
No debugging
  ↓
No file/history
  ↓
Problems identify karna difficult
```

Production application mein logs useful hote hain for:

-   Debugging
-   Monitoring
-   Error investigation
-   Operational visibility
-   Auditing (appropriate events)

---

# 41. Log Levels

Common levels:

```text
TRACE
DEBUG
INFO
WARN
ERROR
```

Simple understanding:

### INFO

Normal important application events.

```java
log.info("Student added successfully");
```

### DEBUG

Detailed debugging information.

```java
log.debug("Processing student: {}", student.getName());
```

### ERROR

Error/exception situations.

```java
log.error("Error while saving student", e);
```

### WARN

Potential problem / unusual situation.

```java
log.warn("Student ID {} was not found", id);
```

---

# 42. SLF4J

Spring Boot applications mein commonly **SLF4J API** ke through logging
ki jaati hai.

Example:

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
```

Then:

```java
private static final Logger log =
        LoggerFactory.getLogger(StudentService.class);
```

---

# 43. Logging in Service Layer

```java
@Service
public class StudentService {

    private static final Logger log =
            LoggerFactory.getLogger(StudentService.class);

    public Student addStudent(Student s) {

        log.debug("Processing student: {}", s.getName());

        Student saved = repo.save(s);

        log.info("Student added successfully: id={}",
                saved.getId());

        return saved;
    }
}
```

---

# 44. Logging in Controller

Controller mein bhi request-related logging ho sakti hai:

```java
log.info("Received request to add student");
```

But excessive logging avoid karna chahiye.

---

# 45. Good Logging vs Bad Logging

  Good                     Avoid
  ------------------------ --------------------------
  Important events         Har line ka log
  Errors + context         Sensitive data
  Useful IDs/context       Passwords/tokens
  Debug info when needed   Huge unnecessary objects

**Never log sensitive credentials, passwords, tokens, or secrets.**

---

# 46. Logging Flow

```text
Client
   ↓
Controller
   ↓
log.info("Request received")
   ↓
Service
   ↓
log.debug("Processing...")
   ↓
Repository
   ↓
Database
   ↓
log.info("Saved successfully")
```

Error:

```text
Exception
   ↓
log.error(...)
```

---

# 47. Spring Boot Default Logging

Spring Boot provides logging setup through its logging infrastructure.

Default Spring Boot applications commonly use:

```text
SLF4J API
      ↓
Logging implementation
```

Typical Spring Boot setup uses Logback by default when included through
the standard starters.

---

# 48. Logback

Logback is a common logging implementation used by Spring Boot.

Configuration can be customized using files such as:

```text
logback-spring.xml
```

Advanced configuration can include:

-   log levels
-   console output
-   file output
-   rolling files
-   patterns

---

# 49. Profile

A **Spring Profile** application ko different environments ke according
different configuration ke saath run karne ka way hai.

Common environments:

```text
dev
test
prod
```

Example:

```text
application-dev.properties
application-prod.properties
```

---

# 50. Why Profiles?

Without profiles:

```text
Local DB
Test DB
Production DB
```

sab configuration mix ho sakti hain.

Profiles se:

```text
Development → Dev config
Testing     → Test config
Production  → Prod config
```

Use kar sakte hain.

---

# 51. Profile Configuration

Main file:

```text
application.properties
```

Profile files:

```text
application-dev.properties
application-prod.properties
```

Example:

### `application-dev.properties`

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/student_dev
```

### `application-prod.properties`

```properties
spring.datasource.url=jdbc:mysql://prod-server:3306/student_prod
```

---

# 52. Active Profile

`application.properties`:

```properties
spring.profiles.active=dev
```

Then Spring `dev` profile configuration load karega.

Concept:

```text
spring.profiles.active=dev
          ↓
application-dev.properties
```

---

# 53. Profile Flow

```text
Application Start
      ↓
Read active profile
      ↓
dev / test / prod
      ↓
Load matching configuration
      ↓
Application runs with that config
```

---

# 54. Profile Use Case

### Local Development

```text
application-dev.properties
```

contains local DB/config.

### Production

```text
application-prod.properties
```

contains production DB/config.

Important:

> Production passwords/secrets ko source code/Git repository mein
> hard-code nahi karna chahiye. Environment variables or
> secret-management solutions use karna better hai.

---

# 55. Profiles + Logging

Different environments mein logging levels different ho sakte hain.

Example:

### Dev

```properties
logging.level.com.example.demo=DEBUG
```

### Production

```properties
logging.level.com.example.demo=INFO
```

Development mein detailed logs useful ho sakte hain, while production
mein unnecessary DEBUG logs reduce kiye ja sakte hain.

---

# 56. Complete Architecture -- Current Topics

```text
                  CLIENT
                    │
                    │ HTTP
                    ↓
             ┌──────────────┐
             │  CONTROLLER  │
             │ REST API     │
             └──────┬───────┘
                    ↓
             ┌──────────────┐
             │   SERVICE    │
             │ Business     │
             │ Logic        │
             └──────┬───────┘
                    ↓
             ┌──────────────┐
             │ REPOSITORY   │
             │ Data Access  │
             └──────┬───────┘
                    ↓
             ┌──────────────┐
             │ JPA/HIBERNATE│
             └──────┬───────┘
                    ↓
                MYSQL DB
```

Spring Container:

```text
Creates & manages
Controller
Service
Repository
other Beans

        ↓

Dependency Injection
```

---

# 57. CRUD Summary

## CREATE

```text
POST /students
```

```text
Controller
 → Service
 → Repository.save()
 → INSERT
```

## READ

```text
GET /students
```

```text
Controller
 → Service
 → Repository.findAll()
 → SELECT
```

## UPDATE

```text
PUT /students/{id}
```

```text
Controller
 → Service
 → findById()
 → modify existing
 → save()
 → UPDATE
```

## DELETE

```text
DELETE /students/{id}
```

```text
Controller
 → Service
 → existsById()
 → deleteById()
 → DELETE
```

---

# 58. Full Request Lifecycle

```text
Client
  ↓
HTTP Request
  ↓
Tomcat
  ↓
Spring MVC / DispatcherServlet
  ↓
Controller
  ↓
Service
  ↓
Repository
  ↓
JPA
  ↓
Hibernate
  ↓
MySQL
  ↓
Response
  ↓
Jackson
  ↓
JSON
  ↓
Client
```

---

# 59. Quick Revision

```text
Spring Container
→ Beans create/manage karta hai

Bean
→ Spring-managed object

DI
→ Dependencies Spring provide karta hai

@Component
→ Generic Spring component

@Service
→ Business/service layer

@Repository
→ Data access layer

@RestController
→ REST API controller

@Qualifier
→ Specific bean select karne mein help

@Primary
→ Default preferred bean

Bean Scope
→ Bean lifecycle/instance behavior

PUT
→ Existing data update

@PathVariable
→ URL se value

@RequestBody
→ JSON → Java Object

Validation
→ Incoming data check

Logging
→ Application events/errors record

Profile
→ Environment-specific configuration

Dev
→ Development

Test
→ Testing

Prod
→ Production
```

---

# 60. One Mental Model

```text
Spring Boot Application
        ↓
Spring Container
        ↓
Creates Beans
        ↓
Injects Dependencies
        ↓
Controller
        ↓
Service
        ↓
Repository
        ↓
Database
```

Along the way:

```text
Validation → input check
Logging    → application visibility
Profiles   → environment configuration
Scopes     → bean lifecycle/instances
CRUD       → Create / Read / Update / Delete
```

---

# 61. Next Topics

After these basics, natural next topics:

```text
Exception Handling
      ↓
ResponseEntity
      ↓
DTO
      ↓
Global Exception Handler
      ↓
Entity Relationships
      ↓
Pagination & Sorting
      ↓
Transactions
      ↓
Spring Security
      ↓
JWT
      ↓
Testing
      ↓
Actuator
      ↓
Production Deployment
```
