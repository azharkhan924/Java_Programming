# Spring Framework -- MVC Architecture & Layered Project

> **Topics:** MVC pattern (Model-View-Controller), MVC request flow, Spring project structure (6 packages), Stereotype annotations (`@Component`, `@Repository`, `@Service`, `@Controller`), Annotation-based project setup, applicationContext.xml

> **Language:** Hinglish\
> **Approach:** Concept + handwritten-note flow + practical Java/XML/SQL
> examples\
> **Important:** Examples are corrected for valid Spring/JDBC syntax
> while keeping the same concepts and learning sequence.

---

# 1. MVC Architecture

MVC = **Model + View + Controller**

MVC application ko separate layers mein divide karta hai so that each
layer has a clear responsibility.

## 1.1 Controller Layer

**Controller = Entry Point**

Controller ka main kaam request receive karna aur appropriate service ko
call karna hota hai.

### Responsibilities

-   Accept HTTP request
-   Validate request format
-   JSON/request data ko Java object mein convert karna
-   Service layer ko call karna
-   Result receive karna
-   HTTP response return karna

> Controller ko generally **thin** rakhna chahiye. Business logic
> Controller mein nahi rakhna chahiye.

---

## 1.2 View Layer

**View layer = Data display karne ke liye responsible**

Examples:

-   JSP
-   HTML
-   Thymeleaf

REST API applications mein traditional View layer ki jagah Controller
directly JSON response return kar sakta hai.

---

## 1.3 Model Layer

Model application ke data aur domain behavior ko represent karta hai.

### Model mein kya ho sakta hai?

-   Application data
-   Business rules
-   State
-   Domain behavior
-   Entity/domain classes
-   Validation rules
-   Calculations
-   State management

Example:

```java
public class Student {

    private int id;
    private String name;

    // getters and setters
}
```

---

# 2. MVC Flow

Typical layered application:

```text
Client / Browser
       |
       v
   Controller
       |
       v
    Service
       |
       v
      DAO
       |
       v
    Database
       |
       v
      DAO
       |
       v
    Service
       |
       v
   Controller
       |
       v
 View / JSON Response
```

### Steps

1.  Request Controller ke paas jaati hai.
2.  Controller Service ko call karta hai.
3.  Service business logic/process karta hai.
4.  Service DAO/Repository ko call karta hai.
5.  DAO database se interaction karta hai.
6.  Result wapas Service → Controller ko milta hai.
7.  Controller response View/client ko return karta hai.

> Simplified notes mein "Controller calls Model" likha ho sakta hai,
> lekin layered Spring application mein commonly Controller → Service →
> DAO/Repository flow use hota hai.

---

# 3. Basic Spring Project Structure

```text
src
├── model
│   └── Student.java
│
├── dao
│   └── StudentDAO.java
│
├── service
│   └── StudentService.java
│
├── controller
│   └── StudentController.java
│
├── contextdemo
│
└── maindemo
    └── MainDemo.java
```

---

# 4. Spring Stereotype Annotations

```java
@Component
@Repository
@Service
@Controller
```Annotation      Typical Role
  --------------- ----------------------------------
  `@Component`    Generic Spring-managed component
  `@Repository`   DAO / persistence layer
  `@Service`      Service / business layer
  `@Controller`   MVC Controller
  `@Autowired`    Dependency injection

`@Repository`, `@Service` and `@Controller` are specialized stereotype
annotations built on the component model.

---

# 5. Basic Annotation Example

## Student.java

```java
package model;

import org.springframework.stereotype.Component;

@Component
public class Student {

    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

## StudentDAO.java

```java
package dao;

import model.Student;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Repository;

@Repository
public class StudentDAO {

    @Autowired
    private Student student;

    public Student getStudent() {
        return student;
    }

    public void setStudent(Student student) {
        this.student = student;
    }
}
```

## StudentService.java

```java
package service;

import dao.StudentDAO;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class StudentService {

    @Autowired
    private StudentDAO studentDAO;

    public StudentDAO getStudentDAO() {
        return studentDAO;
    }

    public void setStudentDAO(StudentDAO studentDAO) {
        this.studentDAO = studentDAO;
    }
}
```

## StudentController.java

```java
package controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import service.StudentService;

@Controller
public class StudentController {

    @Autowired
    private StudentService service;

    public StudentService getService() {
        return service;
    }

    public void setService(StudentService service) {
        this.service = service;
    }
}
```

---

# 6. applicationContext.xml --- Component Scan

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       https://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan
        base-package="model,dao,service,controller"/>

</beans>
```

---

# 7. Why Four Annotations?

### Question

**Why do we use `@Component`, `@Repository`, `@Service`, and
`@Controller`?**

### Answer

Ye annotations classes ko Spring IoC container ke managed beans ke roop
mein register karne ke liye use hoti hain.

```text
@Component   → generic component
@Repository  → DAO/persistence component
@Service     → business/service component
@Controller  → MVC controller
```

### Important

`@Autowired` ka role different hai.

```java
@Autowired
private StudentService service;
```

`@Autowired` dependency inject karne ke liye use hota hai.

---
