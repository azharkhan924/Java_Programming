# Spring Boot -- Project Setup & REST API

> **Topics:** API concepts, HTTP methods, REST API, Spring Initializr, Project structure, `@SpringBootApplication`, Layered architecture (Entity/Repository/Service/Controller), JPA & Hibernate, CRUD endpoints (GET/POST/PUT/DELETE), `@PathVariable`, Persistence, Tomcat, IoC, Constructor Injection, Entity vs DTO

> **Style:** Hinglish + practical notes\
> **Topic:** Spring Boot basics se lekar MySQL database ke saath REST
> API banane tak\
> **Example Project:** `Student Management API`

---

# 1. API kya hoti hai?

**API = Application Programming Interface**

Simple words mein:

> API frontend aur backend ke beech communication ka bridge hai.

Example:

```text
Client / Frontend
       ↓ HTTP Request
      API
       ↓
    Backend
       ↓
   Database
       ↑
    Response
       ↑
      API
       ↑
     Client
```

### Basic idea

Client request bhejta hai:

```text
POST /students
```

Server request ko process karta hai aur response deta hai.

Usually data **JSON** format mein exchange hota hai.

Example JSON:

```json
{
  "name": "Azhar",
  "age": 21
}
```

---

# 2. HTTP Methods

REST API mein commonly ye methods use hote hain:

  Method     Purpose
  ---------- ------------------------------
  `GET`      Data fetch/read karna
  `POST`     New data insert/create karna
  `PUT`      Existing data update karna
  `DELETE`   Data remove/delete karna

### Example

```text
GET     /students       → saare students fetch
POST    /students       → new student add
PUT     /students/1    → student update
DELETE  /students/1    → student delete
```

---

# 3. REST API kya hai?

**REST = Representational State Transfer**

REST ek architectural style hai jiske rules follow karke HTTP-based APIs
design ki jaati hain.

Example:

```text
POST /students
```

ka meaning hai:

> Students resource mein ek new student create karo.

REST API mein generally resources ko nouns ki tarah represent kiya jata
hai:

```text
/students
/students/1
/courses
/courses/10
```

---

# 4. Example: Student Add karna

Suppose client ye request bhejta hai:

```http
POST /students
Content-Type: application/json
```

Body:

```json
{
  "name": "Azhar",
  "age": 21
}
```

Backend:

```text
Controller
    ↓
Service
    ↓
Repository
    ↓
Database
```

Database mein student save hota hai.

Then server response bhejta hai:

```json
{
  "id": 1,
  "name": "Azhar",
  "age": 21
}
```

---

# 5. Step 1 -- Project Setup

Spring Boot project start karne ke liye **Spring Initializr** use kar
sakte hain.

Spring Initializr ek **project generator tool** hai.

Ye khud application run nahi karta.

Iska kaam:

-   Project structure generate karna
-   Dependencies add karna
-   Build configuration create karna
-   Basic Spring Boot setup ready karna

Without Spring Initializr hume manually:

-   folder structure banana
-   dependencies configure karna
-   build file configure karna
-   main class banana

etc. karna padta.

With Spring Initializr:

```text
Select options
     ↓
Generate
     ↓
Ready Spring Boot Project
```

---

# 6. Spring Initializr mein basic options

Commonly:

```text
Project: Maven
Language: Java
Spring Boot: suitable stable version
Packaging: Jar
Java: installed/supported version
```

Example dependencies:

-   Spring Web
-   Spring Data JPA
-   MySQL Driver
-   Lombok (optional)

### Hamare Student API ke liye

```text
Spring Web
Spring Data JPA
MySQL Driver
```

enough hain.

---

# 7. Generated Project Structure

Typical structure:

```text
student-api/
│
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com.example.demo/
│   │   │       ├── DemoApplication.java
│   │   │       ├── entity/
│   │   │       │   └── Student.java
│   │   │       ├── repository/
│   │   │       │   └── StudentRepository.java
│   │   │       ├── service/
│   │   │       │   └── StudentService.java
│   │   │       └── controller/
│   │   │           └── StudentController.java
│   │   │
│   │   └── resources/
│   │       └── application.properties
│   │
│   └── test/
│
└── pom.xml
```

### Important files

**`pom.xml`**

Maven dependencies aur project configuration.

**`application.properties`**

Database aur application configuration.

**`DemoApplication.java`**

Spring Boot application ka main entry point.

---

# 8. Main Spring Boot Class

Example:

```java
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

### `@SpringBootApplication`

Ye main Spring Boot annotation hai.

Internally ye major Spring functionality ko enable karta hai, including:

-   Configuration
-   Component scanning
-   Auto-configuration

### `SpringApplication.run()`

Spring Boot application ko start karta hai aur Spring context create
karta hai.

---

# 9. Layered Architecture

Hamare project ko layers mein divide karenge:

```text
┌─────────────────────────┐
│   Controller Layer      │
│       (API)             │
└───────────┬─────────────┘
            ↓
┌─────────────────────────┐
│     Service Layer       │
│    (Business Logic)     │
└───────────┬─────────────┘
            ↓
┌─────────────────────────┐
│   Repository Layer      │
│ (Database Communication)│
└───────────┬─────────────┘
            ↓
┌─────────────────────────┐
│       Database          │
│         MySQL           │
└─────────────────────────┘
```

Aur:

```text
Entity Layer
   ↓
Database table mapping
```

---

# 10. Layer 1 -- Entity Layer

## Entity Layer = Database Mapping

Entity class Java object ko database table ke saath map karti hai.

Example:

### `Student.java`

```java
package com.example.demo.entity;

import jakarta.persistence.*;

@Entity
public class Student {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;

    private String name;
    private int age;

    // getters and setters
}
```

---

# 11. `@Entity`

```java
@Entity
```

Spring/JPA ko batata hai:

> Ye Java class database ki ek table ko represent karegi.

Conceptually:

```text
Student class
      ↓
Student table
```

---

# 12. `@Id`

```java
@Id
private int id;
```

`@Id` field ko entity ka **primary key** mark karta hai.

Example:

```text
id
--
1
2
3
```

Har student ko uniquely identify karne ke liye ID use hoti hai.

---

# 13. `@GeneratedValue`

```java
@GeneratedValue(strategy = GenerationType.IDENTITY)
```

Iska use ID ko automatically generate/increment karne ke liye hota hai.

Example:

```text
Student 1 → id = 1
Student 2 → id = 2
Student 3 → id = 3
```

Database generated ID handle karta hai.

---

# 14. Entity → Table Mapping

Agar class hai:

```java
public class Student {

    private int id;
    private String name;
    private int age;
}
```

Conceptually table:

  Column   Type
  -------- ---------
  id       INT
  name     VARCHAR
  age      INT

Entity object aur relational table ke beech mapping **JPA/Hibernate**
handle karta hai.

---

# 15. JPA kya hai?

**JPA = Java Persistence API**

JPA Java mein relational database ke saath kaam karne ke liye standard
specification hai.

Important:

> JPA khud implementation nahi hai.

Spring Boot projects mein commonly **Hibernate** JPA ka implementation
hota hai.

Flow:

```text
Java Entity
    ↓
JPA
    ↓
Hibernate
    ↓
SQL
    ↓
MySQL
```

---

# 16. Hibernate kya karta hai?

Hibernate ORM framework hai.

**ORM = Object Relational Mapping**

Simple meaning:

```text
Java Object
    ↕
Database Table
```

Hibernate Java objects ko database records ke saath map/manage karne
mein help karta hai.

Isliye hume har basic operation ke liye manually SQL likhne ki zarurat
nahi padti.

---

# 17. Layer 2 -- Repository Layer

Repository ka kaam:

> Database se communication karna.

Example:

### `StudentRepository.java`

```java
package com.example.demo.repository;

import org.springframework.data.jpa.repository.JpaRepository;
import com.example.demo.entity.Student;

public interface StudentRepository
        extends JpaRepository<Student, Integer> {

}
```

---

# 18. `JpaRepository`

```java
JpaRepository<Student, Integer>
```

Yahan:

```text
Student → Entity type
Integer → ID ka type
```

Spring Data JPA hume already kaafi ready-made methods provide karta hai.

Examples:

```java
save()
findAll()
findById()
deleteById()
existsById()
```

Isliye basic CRUD ke liye SQL manually likhne ki zarurat nahi padti.

---

# 19. Repository ka role

```text
Service
   ↓
Repository
   ↓
JPA / Hibernate
   ↓
MySQL
```

Repository database-related operations ko handle karta hai.

Controller ko directly database ke saath communicate nahi karna chahiye.

---

# 20. Layer 3 -- Service Layer

## Service Layer = Business Logic

Service layer mein application ki business logic rakhi jaati hai.

Example:

### `StudentService.java`

```java
package com.example.demo.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import com.example.demo.entity.Student;
import com.example.demo.repository.StudentRepository;

import java.util.List;

@Service
public class StudentService {

    @Autowired
    private StudentRepository repo;

    public Student addStudent(Student s) {
        return repo.save(s);
    }

    public List<Student> getAllStudents() {
        return repo.findAll();
    }
}
```

---

# 21. `@Service`

```java
@Service
```

Spring ko batata hai:

> Ye class service/business logic layer ka component hai.

Spring is class ka object manage karta hai.

---

# 22. `@Autowired`

```java
@Autowired
private StudentRepository repo;
```

Spring automatically required dependency provide karta hai.

Concept:

```text
StudentService needs StudentRepository
             ↓
          Spring
             ↓
Repository object provide
```

Is process ko **Dependency Injection (DI)** kehte hain.

---

# 23. Service mein Business Logic

Example:

```java
public Student addStudent(Student s) {

    // business logic can be applied here

    return repo.save(s);
}
```

Future mein yahin:

-   validation
-   calculations
-   rules
-   conditions
-   multiple repository calls
-   transaction-related logic

add ki ja sakti hai.

---

# 24. Layer 4 -- Controller Layer

## Controller = API Layer

Controller client ki HTTP requests receive karta hai.

Example:

### `StudentController.java`

```java
package com.example.demo.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import com.example.demo.entity.Student;
import com.example.demo.service.StudentService;

import java.util.List;

@RestController
@RequestMapping("/students")
public class StudentController {

    @Autowired
    private StudentService service;

    @PostMapping
    public Student add(@RequestBody Student s) {
        return service.addStudent(s);
    }

    @GetMapping
    public List<Student> getAll() {
        return service.getAllStudents();
    }
}
```

---

# 25. `@RestController`

```java
@RestController
```

Ye class ko REST controller banata hai.

Matlab:

> Ye class HTTP requests handle karegi aur generally response body
> return karegi.

---

# 26. `@RequestMapping`

```java
@RequestMapping("/students")
```

Ye controller ka base URL define karta hai.

Example:

```text
/students
```

Then:

```java
@PostMapping
```

means:

```text
POST /students
```

And:

```java
@GetMapping
```

means:

```text
GET /students
```

---

# 27. `@PostMapping`

```java
@PostMapping
```

POST request handle karta hai.

Use case:

> New student create karna.

---

# 28. `@RequestBody`

```java
public Student add(@RequestBody Student s)
```

Client JSON bhejta hai:

```json
{
  "name": "Azhar",
  "age": 21
}
```

`@RequestBody` incoming JSON ko Java `Student` object mein convert karne
mein help karta hai.

---

# 29. Jackson

Spring Boot mein JSON conversion commonly **Jackson** ke through hota
hai.

Example:

```text
JSON
 ↓
Jackson
 ↓
Student Java Object
```

Response ke time reverse:

```text
Student Java Object
 ↓
Jackson
 ↓
JSON
```

Example Java object:

```java
Student {
    id = 1,
    name = "Azhar",
    age = 21
}
```

Response:

```json
{
  "id": 1,
  "name": "Azhar",
  "age": 21
}
```

---

# 30. Complete POST Request Flow

Suppose Postman se request aayi:

```text
POST http://localhost:8080/students
```

Body:

```json
{
  "name": "Azhar",
  "age": 21
}
```

Internally flow:

```text
Client / Postman
       ↓
HTTP POST Request
       ↓
Tomcat / Spring Boot
       ↓
@RestController
       ↓
@PostMapping
       ↓
@RequestBody
       ↓
Jackson
       ↓
Student Object
       ↓
Service
       ↓
Repository
       ↓
JPA
       ↓
Hibernate
       ↓
SQL
       ↓
MySQL
```

---

# 31. Response Flow

Database se data save/fetch hone ke baad:

```text
MySQL
   ↓
Hibernate
   ↓
JPA
   ↓
Repository
   ↓
Service
   ↓
Controller
   ↓
Jackson
   ↓
JSON Response
   ↓
Postman / Client
```

---

# 32. Why Controller → Service → Repository?

Controller se directly repository call karna technically possible hai,
but layered architecture better separation provide karti hai.

### If Controller directly uses Repository:

```text
Controller
    ↓
Repository
    ↓
Database
```

Problems:

-   Business logic controller mein aa sakti hai
-   Separation of concerns kam hota hai
-   Code maintain karna difficult ho sakta hai
-   Testing difficult ho sakti hai

### Better:

```text
Controller
    ↓
Service
    ↓
Repository
```

### Main benefits

-   Separation of concerns
-   Maintainability
-   Reusability
-   Scalability
-   Easier testing

---

# 33. Connect to Database

API database ke bina bhi temporarily data process kar sakti hai, lekin
persistent storage ke liye database required hota hai.

Example:

```text
Client
  ↓
API
  ↓
Database
```

Agar application restart ho aur data sirf memory mein tha, to data lose
ho sakta hai.

Database persistent storage provide karta hai.

---

# 34. MySQL Database Create karna

MySQL mein:

```sql
CREATE DATABASE testdb;
```

Database name application configuration se match hona chahiye.

Example:

```text
Database = testdb
```

---

# 35. `application.properties`

Location:

```text
src/main/resources/application.properties
```

Example:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/testdb
spring.datasource.username=root
spring.datasource.password=root

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

> Username/password apne local MySQL setup ke according change karna
> hai.

---

# 36. Important Database Properties

### Database URL

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/testdb
```

Meaning:

```text
localhost → database same machine par hai
3306      → MySQL ka common port
testdb    → database name
```

### Username

```properties
spring.datasource.username=root
```

### Password

```properties
spring.datasource.password=root
```

Actual password environment ke according hoga.

---

# 37. `ddl-auto=update`

```properties
spring.jpa.hibernate.ddl-auto=update
```

Development environment mein Hibernate entity ke according database
schema ko update kar sakta hai.

Example:

```text
Entity mein field add
        ↓
Hibernate schema update kar sakta hai
```

### Common values

```text
none
validate
update
create
create-drop
```

⚠️ Production mein `update/create/create-drop` ko blindly use nahi karna
chahiye. Production schema changes ke liye controlled database migration
tools/processes preferred hote hain.

---

# 38. `show-sql=true`

```properties
spring.jpa.show-sql=true
```

Hibernate jo SQL generate karta hai, usse console/logs mein dekhne mein
help milti hai.

Useful for learning/debugging.

Example:

```sql
select
    s1_0.id,
    s1_0.age,
    s1_0.name
from student s1_0
```

---

# 39. Application Run karna

Spring Boot application run karne par:

```text
SpringApplication.run()
        ↓
Spring Context
        ↓
Tomcat starts
        ↓
Application ready
```

Usually local development mein URL:

```text
http://localhost:8080
```

---

# 40. Postman se API Test karna

Postman API testing ke liye useful tool hai.

### POST request

```text
POST http://localhost:8080/students
```

Body:

```text
raw
JSON
```

```json
{
  "name": "Azhar",
  "age": 21
}
```

Send karo.

Expected response:

```json
{
  "id": 1,
  "name": "Azhar",
  "age": 21
}
```

---

# 41. GET Request

```text
GET http://localhost:8080/students
```

Expected response:

```json
[
  {
    "id": 1,
    "name": "Azhar",
    "age": 21
  }
]
```

---

# 42. CRUD ka Complete Idea

CRUD:

```text
C → Create
R → Read
U → Update
D → Delete
```

### Create

```text
POST /students
```

### Read

```text
GET /students
GET /students/{id}
```

### Update

```text
PUT /students/{id}
```

### Delete

```text
DELETE /students/{id}
```

---

# 43. Example CRUD Endpoints

  Operation   HTTP Method   URL
  ----------- ------------- ---------------
  Create      POST          `/students`
  Get all     GET           `/students`
  Get one     GET           `/students/1`
  Update      PUT           `/students/1`
  Delete      DELETE        `/students/1`

---

# 44. PUT Example

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

# 45. `@PathVariable`

```java
@PathVariable int id
```

URL se value read karta hai.

Example:

```text
/students/10
```

Then:

```java
id = 10
```

---

# 46. DELETE Example

```java
@DeleteMapping("/{id}")
public void delete(@PathVariable int id) {
    service.deleteStudent(id);
}
```

Request:

```text
DELETE /students/1
```

Student with ID `1` delete ho jayega.

---

# 47. Important Spring Boot Annotations -- Quick Revision

  Annotation                 Purpose
  -------------------------- ---------------------------------
  `@SpringBootApplication`   Main Spring Boot configuration
  `@Entity`                  Java class ko JPA entity banana
  `@Id`                      Primary key
  `@GeneratedValue`          ID generation
  `@Service`                 Service layer component
  `@RestController`          REST API controller
  `@RequestMapping`          Base URL mapping
  `@GetMapping`              GET request
  `@PostMapping`             POST request
  `@PutMapping`              PUT request
  `@DeleteMapping`           DELETE request
  `@RequestBody`             JSON request body → Java object
  `@PathVariable`            URL se value lena
  `@Autowired`               Dependency injection

---

# 48. Full Project Architecture

```text
                     CLIENT
                 (Postman / Frontend)
                         │
                         │ HTTP + JSON
                         ↓
                ┌─────────────────┐
                │   CONTROLLER    │
                │    REST API     │
                └────────┬────────┘
                         ↓
                ┌─────────────────┐
                │     SERVICE     │
                │ Business Logic  │
                └────────┬────────┘
                         ↓
                ┌─────────────────┐
                │   REPOSITORY    │
                │   Data Access   │
                └────────┬────────┘
                         ↓
                ┌─────────────────┐
                │   JPA/HIBERNATE │
                └────────┬────────┘
                         ↓
                ┌─────────────────┐
                │      MYSQL      │
                │    Database     │
                └─────────────────┘
```

---

# 49. Full Internal Flow -- POST Request

```text
POSTMAN
   │
   │ JSON
   ↓
Tomcat / Spring Boot
   ↓
@RestController
   ↓
@PostMapping
   ↓
@RequestBody
   ↓
Jackson
   ↓
Student Java Object
   ↓
StudentService
   ↓
StudentRepository
   ↓
JpaRepository
   ↓
JPA
   ↓
Hibernate
   ↓
SQL
   ↓
MySQL
```

---

# 50. Full Internal Flow -- GET Request

```text
Client
  ↓
GET /students
  ↓
Controller
  ↓
Service
  ↓
Repository
  ↓
JPA / Hibernate
  ↓
MySQL
  ↓
Database Records
  ↓
Entity Objects
  ↓
Service
  ↓
Controller
  ↓
Jackson
  ↓
JSON Response
  ↓
Client
```

---

# 51. Important Concept -- Persistence

**Persistence** ka simple meaning:

> Data ko application ke lifecycle ke beyond permanently store karna.

Example:

```text
Application Memory
      ↓
Temporary data
```

vs.

```text
MySQL Database
      ↓
Persistent data
```

Agar application restart ho jaye, MySQL mein stored data normally
available rahega.

---

# 52. Tomcat ka Role

Spring Boot ke web application mein embedded server commonly **Tomcat**
hota hai.

Flow:

```text
Client
  ↓
HTTP Request
  ↓
Tomcat
  ↓
Spring MVC
  ↓
Controller
```

Isliye alag se traditional Tomcat server install/configure karna zaroori
nahi hota for a typical Spring Boot embedded-Tomcat setup.

---

# 53. Spring MVC ka Basic Role

Spring Web MVC HTTP request ko controller tak route karne mein help
karta hai.

Basic idea:

```text
HTTP Request
      ↓
DispatcherServlet
      ↓
Controller
      ↓
Response
```

Spring Boot web application mein request processing ka important part
**DispatcherServlet** handle karta hai.

---

# 54. Dependency Injection -- Basic Idea

Suppose:

```java
StudentService
```

ko:

```java
StudentRepository
```

chahiye.

Instead of manually:

```java
StudentRepository repo = new StudentRepository();
```

Spring dependency manage kar sakta hai.

```text
Spring Container
      ↓
Creates / manages beans
      ↓
Injects required dependency
```

Isse classes loosely coupled aur easier to manage hoti hain.

---

# 55. Bean kya hota hai?

Spring ke context mein **Bean** ek object hai jise Spring IoC container
create/manage karta hai.

Examples:

```text
StudentService
StudentRepository
Controller
```

Spring annotations/configuration ke through in objects ko manage kar
sakta hai.

---

# 56. IoC

**IoC = Inversion of Control**

Normal Java mein:

```text
Developer
   ↓
Object create karta hai
```

Spring mein:

```text
Spring Container
   ↓
Object create/manage karta hai
```

Ye concept Dependency Injection se closely related hai.

---

# 57. Constructor Injection -- Better Practice

Notes mein `@Autowired` field injection use kiya gaya hai:

```java
@Autowired
private StudentRepository repo;
```

Learning ke liye ye samajhna useful hai, but production code mein
generally **constructor injection** prefer ki jaati hai.

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

Benefits:

-   Dependency explicit hoti hai
-   `final` use kar sakte hain
-   Testing easier hoti hai
-   Object incomplete state mein create nahi hota

---

# 58. Entity vs DTO

Starting project mein directly Entity ko request/response mein use karna
simple hai.

Lekin larger applications mein DTO use karna better ho sakta hai.

```text
Client
  ↓
DTO
  ↓
Service
  ↓
Entity
  ↓
Repository
  ↓
Database
```

**DTO = Data Transfer Object**

DTO ka purpose API ke data contract ko database entity se separate
rakhna hai.

Ye concept later Spring Boot development mein important hoga.

---

# 59. Validation -- Next Step

API mein incoming data validate karna important hai.

Example:

```text
name empty nahi hona chahiye
age positive honi chahiye
email valid hona chahiye
```

Spring Boot mein Bean Validation use ki ja sakti hai.

Example:

```java
@NotBlank
private String name;

@Min(1)
private int age;
```

Controller:

```java
public Student add(@Valid @RequestBody Student s)
```

---

# 60. HTTP Status Codes -- Basic

API sirf data return nahi karti; HTTP status code bhi return karti hai.

Common codes:

  Code    Meaning
  ------- -----------------------
  `200`   OK
  `201`   Created
  `204`   No Content
  `400`   Bad Request
  `401`   Unauthorized
  `403`   Forbidden
  `404`   Not Found
  `500`   Internal Server Error

Example:

```text
POST /students
        ↓
201 Created
```

when a resource is successfully created.

---

# 61. API Design -- Basic Rules

Good REST API ke liye:

### Use nouns

```text
/students
```

instead of:

```text
/getStudents
```

### Use HTTP methods properly

```text
GET    /students
POST   /students
PUT    /students/1
DELETE /students/1
```

### Keep responsibilities separate

```text
Controller → HTTP/API
Service → Business Logic
Repository → Database Access
Entity → Data/DB Mapping
```

---

# 62. Starting Project -- Checklist

```text
[ ] Spring Initializr se project create
[ ] Spring Web add
[ ] Spring Data JPA add
[ ] MySQL Driver add
[ ] Project import/run
[ ] Entity create
[ ] Repository create
[ ] Service create
[ ] Controller create
[ ] MySQL database create
[ ] application.properties configure
[ ] Application run
[ ] Postman se POST test
[ ] Postman se GET test
[ ] CRUD complete
```

---

# 63. Quick Revision -- Ek Line Mein

```text
Spring Initializr
→ Project generate karta hai

Spring Boot
→ Application ko quickly build/run karne mein help karta hai

Spring Web
→ Web/REST API banane ke liye

JPA
→ Persistence ka standard specification

Hibernate
→ JPA implementation / ORM framework

MySQL
→ Persistent relational database

Entity
→ Java class ↔ Database table

Repository
→ Database communication

Service
→ Business logic

Controller
→ API/HTTP request handling

Jackson
→ JSON ↔ Java Object conversion

Tomcat
→ HTTP requests ko receive/serve karne wala embedded web server

Postman
→ API testing
```

---

# 64. One-Page Mental Model

Agar poora topic ek flow mein yaad rakhna ho:

```text
             SPRING BOOT REST API

Client / Postman
       │
       │ HTTP + JSON
       ↓
Controller
(@RestController)
       │
       ↓
Service
(@Service)
       │
       ↓
Repository
(JpaRepository)
       │
       ↓
JPA
       │
       ↓
Hibernate
       │
       ↓
MySQL
       │
       ↓
Database
```

### Request side

```text
JSON → Jackson → Java Object
```

### Database side

```text
Java Entity → JPA/Hibernate → SQL → MySQL
```

### Response side

```text
Java Object → Jackson → JSON
```

---

# 65. Final Concept

Spring Boot REST API ka basic architecture:

> **Client request bhejta hai → Controller request receive karta hai →
> Service business logic handle karti hai → Repository database se
> communicate karta hai → JPA/Hibernate SQL/database interaction handle
> karta hai → response wapas Controller ke through client ko JSON mein
> milta hai.**

Ye basic architecture samajh aane ke baad Spring Boot mein next topics
naturally aate hain:

```text
CRUD
 ↓
Validation
 ↓
Exception Handling
 ↓
DTO
 ↓
ResponseEntity
 ↓
Relationships
 ↓
Pagination & Sorting
 ↓
Spring Security
 ↓
JWT Authentication
 ↓
Testing
 ↓
Deployment
```
