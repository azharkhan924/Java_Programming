# Hibernate & JPA -- Entity Mapping & Annotations

> **Topics:** `@Entity`, `@Table`, `@Column`, `@Id`, `@GeneratedValue`, `@Temporal`, `@Transient`, `@Lob`, `@CreationTimestamp`, `@UpdateTimestamp`, Persistence Context, Dirty Checking, Entity lifecycle (Transient/Persistent/Detached/Removed), Complete Hibernate application flow, Database independence

---

# 31. `@Transient`

Used when a Java field should **not** be persisted.

Example:

```java
@Transient
private double temporaryValue;
```

Hibernate/JPA ignores this field for database persistence.

### Why needed?

Use it for:

-   Calculated values
-   Temporary values
-   Runtime-only data
-   UI/helper values
-   Values that should not be stored in DB

Example:

```java
public double getFinalMarks() {
    return marks + bonus;
}
```

If the calculated value does not need a DB column:

```java
@Transient
private double finalMarks;
```

---

# 32. `@Temporal`

`@Temporal` is associated with the older Java date/time types such as:

```java
java.util.Date
java.util.Calendar
```

It tells JPA how to map a legacy date/time value.

Values:

```java
TemporalType.DATE
TemporalType.TIME
TemporalType.TIMESTAMP
```

Example:

```java
@Temporal(TemporalType.DATE)
private Date dateOfBirth;
```

### Meaning

  Type          Meaning
  ------------- -------------
  `DATE`        Date only
  `TIME`        Time only
  `TIMESTAMP`   Date + time

> For modern Java time types such as `LocalDate`, `LocalTime`, and
> `LocalDateTime`, `@Temporal` is generally not needed.

---

# 33. `@Enumerated`

Used to persist Java enums.

Example:

```java
public enum Status {
    ACTIVE,
    INACTIVE
}
```

Entity:

```java
@Enumerated(EnumType.STRING)
private Status status;
```

Database can store:

```text
ACTIVE
INACTIVE
```

### Two common strategies

#### `EnumType.ORDINAL`

Stores numeric ordinal:

```text
ACTIVE   → 0
INACTIVE → 1
```

This can be dangerous if enum order changes.

#### `EnumType.STRING`

Stores enum name:

```text
ACTIVE
INACTIVE
```

Usually safer because changing enum declaration order does not change
the stored meaning.

Recommended example:

```java
@Enumerated(EnumType.STRING)
private Status status;
```

---

# 34. `@Lob`

LOB = Large Object.

Used for large database values.

Examples:

-   Large text
-   Documents
-   Images
-   Binary data

Types:

```text
CLOB → Character Large Object
BLOB → Binary Large Object
```

Example:

```java
@Lob
private String description;
```

or:

```java
@Lob
private byte[] document;
```

---

# 35. `@CreationTimestamp`

Hibernate-specific annotation.

It can automatically populate the entity's creation timestamp.

Example:

```java
@CreationTimestamp
private LocalDateTime createdAt;
```

When the entity is initially persisted:

```text
createdAt = current date/time
```

This is useful for:

-   Record creation time
-   Audit information
-   Created-at fields

---

# 36. `@UpdateTimestamp`

Hibernate-specific annotation.

It automatically updates a timestamp when the entity is updated.

Example:

```java
@UpdateTimestamp
private LocalDateTime updatedAt;
```

Conceptually:

```text
Create entity
    ↓
createdAt = time of creation

Update entity
    ↓
updatedAt = modification time
```

---

# 37. Big Entity Example

The following example combines the major annotations discussed above.

```java
package com.entity;

import jakarta.persistence.*;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

import java.time.LocalDateTime;
import java.util.Date;

@Entity
@Table(name = "student_info")
public class Student {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(
        name = "stu_name",
        nullable = false,
        unique = true,
        length = 50
    )
    private String name;

    @Column(name = "age")
    private int age;

    @Enumerated(EnumType.STRING)
    private Status status;

    @Temporal(TemporalType.DATE)
    private Date dateOfBirth;

    @Lob
    private String description;

    @Transient
    private double temporaryMarks;

    @CreationTimestamp
    private LocalDateTime createdAt;

    @UpdateTimestamp
    private LocalDateTime updatedAt;

    public Student() {
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public Status getStatus() {
        return status;
    }

    public void setStatus(Status status) {
        this.status = status;
    }

    public Date getDateOfBirth() {
        return dateOfBirth;
    }

    public void setDateOfBirth(Date dateOfBirth) {
        this.dateOfBirth = dateOfBirth;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public double getTemporaryMarks() {
        return temporaryMarks;
    }

    public void setTemporaryMarks(double temporaryMarks) {
        this.temporaryMarks = temporaryMarks;
    }

    public LocalDateTime getCreatedAt() {
        return createdAt;
    }

    public LocalDateTime getUpdatedAt() {
        return updatedAt;
    }
}
```

Enum:

```java
public enum Status {
    ACTIVE,
    INACTIVE
}
```

### What each annotation does

```text
@Entity
   ↓
Marks class as persistent entity

@Table
   ↓
Customizes table name

@Id
   ↓
Primary key / entity identifier

@GeneratedValue
   ↓
Automatically generates ID

@Column
   ↓
Customizes column

@Enumerated
   ↓
Maps enum

@Temporal
   ↓
Maps legacy Date/Calendar type

@Lob
   ↓
Maps large object

@Transient
   ↓
Field is not stored

@CreationTimestamp
   ↓
Creation time

@UpdateTimestamp
   ↓
Last modification time
```

---

# 38. Persistence

## What is Persistence?

Persistence means storing data so that it survives beyond the current
program execution.

Example:

```java
Student s = new Student();
```

This object initially exists only in memory.

When it is saved to the database:

```java
session.persist(s);
```

the object's state becomes persistent.

Conceptually:

```text
Java Memory
   ↓
Student Object
   ↓
Persistence
   ↓
Database
```

---

# 39. Persistent Context

## What is Persistence Context?

A **persistence context** is the managed environment in which the
persistence provider keeps track of entity instances and their state.

In simple terms:

> It is like an internal management area associated with a persistence
> session where Hibernate/JPA keeps track of managed entity objects.

It helps Hibernate know:

-   Which entity is managed
-   Which entity was loaded
-   Which entity was changed
-   Which entity is new
-   Which entity was removed
-   Which changes need synchronization with the database

---

# 40. Persistence Context Example

```java
Student s = session.get(Student.class, 101);
```

Hibernate loads the student.

Conceptually:

```text
Database
   ↓
SELECT ...
   ↓
Student object
   ↓
Persistence Context
```

Now Hibernate manages that object.

If we do:

```java
s.setName("Rahul");
```

we don't necessarily need to write:

```sql
UPDATE student SET name = 'Rahul' WHERE id = 101;
```

Hibernate can detect the change through **dirty checking** and
synchronize it during flush/transaction commit.

---

# 41. Dirty Checking

Dirty checking means Hibernate detects changes made to managed entities.

Example:

```java
Student s = session.get(Student.class, 101);

s.setName("Rahul");

tx.commit();
```

No manual UPDATE query is written.

Conceptually:

```text
Load Student
     ↓
Persistence Context tracks Student
     ↓
Original state remembered
     ↓
s.setName("Rahul")
     ↓
State changed
     ↓
Dirty Checking
     ↓
UPDATE SQL generated
     ↓
JDBC
     ↓
Database
```

This is one of Hibernate's major advantages over manual JDBC object
mapping.

---

# 42. Entity Lifecycle

A JPA entity can conceptually move through states:

```text
        new
         |
         | persist()
         ↓
     MANAGED
       /   \
      /     \
 detach    remove
    ↓         ↓
DETACHED   REMOVED
```

Common states:

### Transient

New object not managed by persistence context.

```java
Student s = new Student();
```

### Managed / Persistent

Entity is associated with persistence context.

```java
session.persist(s);
```

or after:

```java
session.get(Student.class, id);
```

### Detached

Entity was previously managed but is no longer associated with the
current persistence context.

For example, after closing the session:

```java
session.close();
```

### Removed

Entity is marked for deletion.

```java
session.remove(s);
```

---

# 43. Persistence Context and Database Synchronization

Important idea:

> Hibernate primarily works with entity objects and synchronizes their
> state with the database.

Flow:

```text
Application
    ↓
Entity Object
    ↓
Persistence Context
    ↓
Hibernate tracks state
    ↓
Flush
    ↓
SQL generated
    ↓
JDBC
    ↓
Database
```

The persistence context is not the database itself.

It is not simply a permanent copy of every database row.

It is the provider's managed context for entity instances.

---

# 44. Hibernate CRUD

CRUD means:

```text
C → Create
R → Read
U → Update
D → Delete
```

---

# 45. Create

Traditional Hibernate:

```java
session.save(student);
```

Modern JPA-style:

```java
session.persist(student);
```

Example:

```java
Student s = new Student();

s.setName("Azhar");
s.setAge(22);

session.persist(s);

tx.commit();
```

Conceptual flow:

```text
Student object
      ↓
session.persist()
      ↓
Persistence Context
      ↓
Hibernate generates INSERT
      ↓
JDBC
      ↓
MySQL
```

---

# 46. Read

```java
Student s = session.get(Student.class, 101);

System.out.println(s.getName());
```

`get()` is an immediate lookup operation. If the entity is not found, it
returns `null`.

---

# 47. `get()` vs `load()`

### `get()`

```java
Student s = session.get(Student.class, 101);
```

Generally:

-   Immediately obtains/fetches the entity.
-   Returns `null` if the entity does not exist.

### `load()`

```java
Student s = session.load(Student.class, 101);
```

In older Hibernate APIs, `load()` can use a proxy and defer database
access.

If the entity does not exist, accessing the proxy can result in an
exception.

> Modern Hibernate/JPA code generally prefers `get()` or JPA's `find()`
> depending on the API and desired behavior.

JPA equivalent:

```java
Student s = entityManager.find(Student.class, 101);
```

---

# 48. Update

```java
Student s = session.get(Student.class, 101);

s.setName("Rahul");

tx.commit();
```

No manual UPDATE SQL is required.

Hibernate performs dirty checking.

Conceptual flow:

```text
get()
 ↓
Managed entity
 ↓
setName()
 ↓
Dirty checking
 ↓
UPDATE generated
 ↓
commit()
```

---

# 49. Delete

```java
Student s = session.get(Student.class, 101);

session.remove(s);

tx.commit();
```

Hibernate generates the appropriate DELETE SQL.

---

# 50. Complete CRUD Flow

```text
CREATE
persist()
   ↓
INSERT

READ
get()/find()
   ↓
SELECT

UPDATE
modify managed object
   ↓
dirty checking
   ↓
UPDATE

DELETE
remove()
   ↓
DELETE
```

---

# 51. Complete Traditional Hibernate Program

```java
package com.main;

import com.entity.Student;
import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.cfg.Configuration;

public class App {

    public static void main(String[] args) {

        Configuration cfg = new Configuration();

        cfg.configure();

        SessionFactory sf = cfg.buildSessionFactory();

        Session session = sf.openSession();

        Transaction tx = session.beginTransaction();

        Student s = new Student();

        s.setName("Azhar");
        s.setAge(22);

        session.persist(s);

        tx.commit();

        session.close();

        sf.close();

        System.out.println("Student saved");
    }
}
```

---

# 52. What Happens Internally?

```java
Configuration cfg = new Configuration();
```

Creates a configuration object.

```java
cfg.configure();
```

Loads configuration.

```java
SessionFactory sf = cfg.buildSessionFactory();
```

Builds heavyweight SessionFactory and initializes Hibernate
infrastructure.

```java
Session session = sf.openSession();
```

Opens a Session/persistence context.

```java
Transaction tx = session.beginTransaction();
```

Begins transaction.

```java
session.persist(s);
```

Makes entity managed and schedules persistence.

```java
tx.commit();
```

Flushes required changes and commits the transaction.

```java
session.close();
```

Closes session.

```java
sf.close();
```

Closes SessionFactory and releases its resources.

---

# 53. Hibernate Internal Flow

For saving:

```text
Student Object
      ↓
session.persist()
      ↓
Persistence Context
      ↓
Hibernate ORM
      ↓
SQL generated
      ↓
JDBC
      ↓
JDBC Driver
      ↓
MySQL
```

For reading:

```text
session.get()
      ↓
Persistence Context
      ↓
Hibernate
      ↓
SQL SELECT
      ↓
JDBC
      ↓
Database
      ↓
Row
      ↓
Student Object
```

---

# 54. Hibernate Does Not Directly Work Like JDBC

With JDBC, developer commonly thinks:

```text
SQL
 ↓
ResultSet
 ↓
Manually create object
```

With Hibernate:

```text
Entity
 ↓
ORM metadata
 ↓
Hibernate
 ↓
SQL
 ↓
Database
 ↓
Hibernate maps result
 ↓
Entity
```

Hibernate abstracts much of the manual mapping.

---

# 55. `Configuration` vs `SessionFactory` vs `Session`

  ---------------------------------------------------------------------
  Component                          Main Responsibility
  ---------------------------------- ----------------------------------
  Configuration                      Reads/prepares configuration

  SessionFactory                     Creates/manages sessions and
                                     Hibernate infrastructure

  Session                            Performs persistence operations
                                     and manages persistence context

  Transaction                        Defines transactional unit of work
  ---------------------------------------------------------------------

Simple memory trick:

```text
Configuration
   ↓
"How should Hibernate be configured?"

SessionFactory
   ↓
"Give me a Session."

Session
   ↓
"Perform database/persistence work."

Transaction
   ↓
"Make this unit of work atomic."
```

---

# 56. Modern JPA Style

Modern JPA code usually works with:

```text
EntityManagerFactory
       ↓
EntityManager
       ↓
Persistence Context
       ↓
Database
```

Hibernate provides an implementation underneath.

Example:

```java
EntityManager em =
    entityManagerFactory.createEntityManager();

EntityTransaction tx = em.getTransaction();

tx.begin();

em.persist(student);

tx.commit();

em.close();
```

Hibernate-native API:

```java
Session
```

JPA API:

```java
EntityManager
```

Do not unnecessarily mix the two APIs in one example.

---

# 57. Hibernate vs JPA API

### Hibernate-native

```java
Session session;
SessionFactory sessionFactory;
```

### JPA

```java
EntityManager entityManager;
EntityManagerFactory entityManagerFactory;
```

Conceptually:

```text
JPA API
   ↓
Hibernate Provider
   ↓
JDBC
   ↓
Database
```

---

# 58. Why Hibernate is Called an ORM Framework

Because it maps:

```text
Java Class
     ↓
Database Table

Java Object
     ↓
Database Row

Java Field
     ↓
Database Column

Object Relationship
     ↓
Foreign Key / Join Table
```

It lets developers think primarily in terms of entities and objects
instead of manually converting every row.

---

# 59. Important Hibernate Features

### 1. ORM

Maps objects to relational tables.

### 2. Automatic SQL generation

Generates SQL for many CRUD operations.

### 3. Persistence Context

Tracks managed entity objects.

### 4. Dirty Checking

Detects changes in managed entities.

### 5. Lazy Loading

Loads some associated data only when required.

### 6. Eager Loading

Loads associated data immediately according to the mapping/fetch plan.

### 7. Caching

Hibernate supports first-level caching through the persistence context
and can also support second-level/query caching depending on
configuration.

### 8. Relationship Mapping

Supports:

```text
@OneToOne
@OneToMany
@ManyToOne
@ManyToMany
```

### 9. HQL/JPQL

Allows object-oriented query approaches instead of writing raw SQL for
every operation.

---

# 60. First-Level Cache

The persistence context associated with a Hibernate Session acts as a
first-level cache.

Example:

```java
Student s1 = session.get(Student.class, 101);
Student s2 = session.get(Student.class, 101);
```

Within the same persistence context, Hibernate can avoid unnecessarily
loading the same managed entity again.

Important:

```text
Session / Persistence Context
        ↓
First-Level Cache
```

The first-level cache is associated with the Session/persistence context
and is not a global application-wide cache.

---

# 61. Lazy Loading

Lazy loading means related data is loaded only when required.

Example concept:

```java
student.getDepartment();
```

Hibernate may defer loading the department until it is accessed,
depending on mapping and fetch strategy.

Benefits:

-   Avoid unnecessary data
-   Reduce initial query cost
-   Useful for large object graphs

But lazy loading must be used with awareness of
Session/persistence-context boundaries.

---

# 62. Common Errors

## 62.1 No suitable driver

Possible cause:

```text
MySQL JDBC driver dependency missing
```

or driver configuration problem.

---

## 62.2 Access denied

Usually caused by:

-   Wrong username
-   Wrong password
-   Database permissions

---

## 62.3 Database connection failure

Possible causes:

-   MySQL server not running
-   Wrong host
-   Wrong port
-   Wrong database name
-   Network/configuration issue

---

## 62.4 Table not created

Possible causes:

-   Wrong entity mapping
-   Entity not discovered/registered
-   Schema-generation setting
-   Database permissions
-   Wrong database selected

---

## 62.5 Entity not found / not mapped

Possible causes:

-   Missing `@Entity`
-   Entity not discovered/registered
-   Incorrect package/class mapping
-   Configuration problem

---

## 62.6 No identifier specified for entity

Cause:

Missing:

```java
@Id
```

Every normal JPA entity needs an identifier.

---

## 62.7 No default constructor

Cause:

No suitable no-argument constructor.

Solution:

```java
public Student() {
}
```

---

# 63. `hbm2ddl.auto` Summary

  Value           Purpose
  --------------- ------------------------------------------
  `create`        Recreates schema on startup
  `update`        Attempts schema update
  `create-drop`   Creates at startup and drops at shutdown
  `validate`      Validates schema
  `none`          No automatic schema action

**Important:** Be careful with `create` and `create-drop` because they
can result in data loss.

---

# 64. `@Entity` + `@Table` Small Example

```java
@Entity
@Table(name = "student_details")
public class Student {

    @Id
    private int id;

    private String name;

    public Student() {
    }
}
```

Meaning:

```text
Student Java class
       ↓
student_details table
       ↓
id → primary key
name → column
```

---

# 65. Complete Annotation Cheat Sheet

  Annotation             Purpose
  ---------------------- ------------------------------------
  `@Entity`              Marks class as persistent entity
  `@Table`               Customizes table mapping
  `@Id`                  Defines primary key
  `@GeneratedValue`      Automatic ID generation
  `@Column`              Customizes column
  `@Transient`           Excludes field from persistence
  `@Temporal`            Maps legacy Date/Calendar type
  `@Enumerated`          Maps enum
  `@Lob`                 Maps large object
  `@CreationTimestamp`   Automatically tracks creation time
  `@UpdateTimestamp`     Automatically tracks update time

---

# 66. Annotation Memory Trick

```text
@Entity
   ↓
Who are you?
   ↓
Persistent Entity

@Table
   ↓
Which table?

@Id
   ↓
Which unique row identifier?

@GeneratedValue
   ↓
Who generates ID?

@Column
   ↓
Which column and what constraints?

@Transient
   ↓
Don't store this field.

@Temporal
   ↓
How should old Date/Calendar be stored?

@Enumerated
   ↓
How should enum be stored?

@Lob
   ↓
Large data

@CreationTimestamp
   ↓
When was it created?

@UpdateTimestamp
   ↓
When was it modified?
```

---

# 67. Full Conceptual Flow

```text
                JDBC Problems
                     ↓
       Too much boilerplate / manual SQL
                     ↓
       Manual object-row conversion
                     ↓
       Object-Relational Mismatch
                     ↓
                    ORM
                     ↓
                 Hibernate
                     ↓
              JPA Specification
                     ↓
       Hibernate = JPA Provider
                     ↓
             Entity Mapping
                     ↓
        Persistence Context
                     ↓
              Dirty Checking
                     ↓
             SQL Generation
                     ↓
                  JDBC
                     ↓
              JDBC Driver
                     ↓
                Database
```

---

# 68. Full Hibernate Application Flow

```text
Application starts
        ↓
Configuration
        ↓
hibernate.cfg.xml
        ↓
Properties + Entity Metadata
        ↓
SessionFactory
        ↓
Session
        ↓
Transaction
        ↓
Entity Object
        ↓
Persistence Context
        ↓
persist/get/modify/remove
        ↓
Hibernate ORM
        ↓
Dirty Checking / SQL generation
        ↓
JDBC
        ↓
JDBC Driver
        ↓
Database
        ↓
Commit
```

---

# 69. Practical Save Example

```java
Configuration cfg = new Configuration();
cfg.configure();

SessionFactory sf = cfg.buildSessionFactory();

Session session = sf.openSession();

Transaction tx = session.beginTransaction();

Student student = new Student();

student.setName("Azhar");
student.setAge(22);

session.persist(student);

tx.commit();

session.close();
sf.close();
```

### Explanation

```text
new Student()
     ↓
Transient object

persist()
     ↓
Managed entity

Persistence Context
     ↓
Hibernate tracks it

commit()
     ↓
Flush

INSERT SQL
     ↓
JDBC
     ↓
Database
```

---

# 70. Practical Read Example

```java
Transaction tx = session.beginTransaction();

Student student =
        session.get(Student.class, 101);

System.out.println(student.getName());

tx.commit();
```

Conceptually:

```text
get()
 ↓
Persistence Context checks managed entity
 ↓
If needed, SELECT query
 ↓
Database
 ↓
Student object
```

---

# 71. Practical Update Example

```java
Transaction tx = session.beginTransaction();

Student student =
        session.get(Student.class, 101);

student.setName("Rahul");

tx.commit();
```

No manual:

```sql
UPDATE ...
```

is required.

Hibernate uses dirty checking.

---

# 72. Practical Delete Example

```java
Transaction tx = session.beginTransaction();

Student student =
        session.get(Student.class, 101);

session.remove(student);

tx.commit();
```

Conceptually:

```text
Managed entity
      ↓
remove()
      ↓
Removed state
      ↓
DELETE SQL
      ↓
Database
```

---

# 73. Important: `save()` vs `persist()`

Older/native Hibernate code may use:

```java
session.save(student);
```

Modern JPA-style Hibernate code commonly uses:

```java
session.persist(student);
```

JPA standard:

```java
entityManager.persist(student);
```

For learning modern persistence, understand `persist()` as the standard
JPA operation.

---

# 74. Hibernate Configuration Flow

```text
new Configuration()
        ↓
Empty configuration object
        ↓
configure()
        ↓
Reads hibernate.cfg.xml
        ↓
Loads properties
        ↓
Loads mappings / entity metadata
        ↓
buildSessionFactory()
        ↓
SessionFactory created
        ↓
openSession()
        ↓
Session created
```

---

# 75. Configuration Does Not Mean Connection Is Permanently Open

Important distinction:

```java
Configuration cfg = new Configuration();
```

does not mean:

```text
Permanent DB connection opened
```

It creates/configures the bootstrap object.

```java
cfg.configure();
```

loads configuration.

```java
cfg.buildSessionFactory();
```

initializes the SessionFactory and its persistence infrastructure.

Actual database interaction happens when persistence operations require
it.

---

# 76. Why `SessionFactory` Is Heavy

SessionFactory:

-   Builds metadata
-   Processes mappings
-   Initializes ORM infrastructure
-   Manages Session creation
-   May initialize caches/services
-   Represents a major application-level resource

Therefore, applications generally do not create a new SessionFactory for
every database operation.

Typical idea:

```text
Application
     ↓
One/shared SessionFactory
     ↓
Many Sessions
```

---

# 77. Session and Persistence Context

In Hibernate:

```text
Session
  ↓
Persistence Context
  ↓
Managed Entities
```

The Session is the Hibernate API object through which you interact with
the persistence context.

In JPA:

```text
EntityManager
  ↓
Persistence Context
  ↓
Managed Entities
```

---

# 78. Important Concept: Hibernate Works With Objects

JDBC mindset:

```text
Write SQL
   ↓
Get ResultSet
   ↓
Read columns
   ↓
Create Java object
```

Hibernate mindset:

```text
Work with Entity
   ↓
Hibernate mapping
   ↓
Hibernate generates SQL
   ↓
Database
```

This is the central advantage of ORM abstraction.

---

# 79. What Hibernate Automates

Hibernate can automate/reduce:

```text
Object ↔ Row mapping
SQL generation
Relationship mapping
Entity state tracking
Dirty checking
Persistence operations
Some connection/resource management
Caching mechanisms
Lazy loading
```

It does **not** mean that developers never need to understand:

-   SQL
-   Database design
-   Transactions
-   Indexes
-   Constraints
-   Query optimization
-   Connection management
-   Fetch strategies

A Java developer using Hibernate should still understand databases and
SQL.

---

# 80. Database Independence

ORM can improve database portability because application code can rely
more on:

```text
Entity model
+
ORM API
+
Dialect/provider
```

instead of embedding database-specific SQL everywhere.

However:

> Hibernate does not magically make every application completely
> database-independent. Native SQL, database-specific functions, schema
> differences and vendor-specific behavior can still introduce database
> coupling.

---

# 81. Hibernate and Spring Boot

In Spring Boot applications, many Hibernate objects are configured
automatically.

You normally do not manually write:

```java
Configuration cfg = new Configuration();
SessionFactory sf = cfg.buildSessionFactory();
```

Spring Boot commonly configures:

```text
DataSource
Hibernate
JPA
EntityManagerFactory
Transactions
```

for you.

Typical architecture:

```text
Spring Boot
     ↓
Spring Data JPA
     ↓
JPA
     ↓
Hibernate
     ↓
JDBC
     ↓
Database
```

This is why Hibernate fundamentals remain important even when working
with Spring Boot.

---

# 82. Traditional Hibernate vs Modern Spring Boot

### Traditional learning setup

```text
Configuration
   ↓
SessionFactory
   ↓
Session
   ↓
Transaction
   ↓
Hibernate
```

### Modern Spring Boot/JPA setup

```text
Spring Boot
   ↓
Spring Data JPA
   ↓
EntityManager
   ↓
Hibernate
   ↓
JDBC
   ↓
Database
```

The abstraction changes, but the fundamental ORM concepts remain.

---

# 83. Final Revision Sheet

## JDBC

```text
Low-level API
Manual SQL
Manual mapping
More boilerplate
Direct database interaction
```

## ORM

```text
Object Relational Mapping
Class → Table
Object → Row
Field → Column
```

## Hibernate

```text
ORM framework/provider
Implements JPA
Uses JDBC underneath
Generates SQL
Tracks entities
Dirty checking
Caching
Lazy loading
Relationships
```

## JPA

```text
Specification/standard
Defines ORM rules and APIs
Not an implementation
```

## Persistence

```text
Saving entity state to database
```

## Entity

```text
Persistent Java class
@Entity
@Id required
No-argument constructor
```

## Persistence Context

```text
Managed environment for entity objects
Tracks entity state
Supports identity management
Dirty checking
Synchronizes changes with DB
```

## Configuration

```text
Reads/prepares configuration
```

## SessionFactory

```text
Heavyweight
Creates Sessions
Usually shared/reused
```

## Session

```text
Persistence operations
Persistence context
```

## Transaction

```text
Unit of work
commit()
rollback()
```

---

# 84. One-Line Interview Definitions

### What is ORM?

ORM is a technique that maps object-oriented entities to relational
database structures.

### What is Hibernate?

Hibernate is a Java ORM framework/provider that maps entities to
relational databases and simplifies persistence.

### What is JPA?

JPA is a Java persistence specification that defines standard APIs and
rules for ORM.

### Is JPA a framework?

No. JPA is a specification/standard.

### Is Hibernate JPA?

Hibernate is an ORM implementation/provider that supports the JPA
specification.

### What is Object-Relational Impedance Mismatch?

It is the mismatch between object-oriented data models and relational
database models.

### What is Persistence?

Persistence means storing data so that it survives beyond the current
program execution.

### What is an Entity?

An entity is a persistent Java class whose instances are managed by the
persistence provider and mapped to database data.

### Why is `@Id` required?

It defines the entity's unique identifier, allowing Hibernate/JPA to
identify and manage database rows.

### What is Persistence Context?

It is the managed environment in which Hibernate/JPA tracks entity
instances and their state.

### What is Dirty Checking?

Dirty checking is Hibernate's mechanism for detecting changes in managed
entities and synchronizing those changes with the database.

### What does `@Transient` do?

It tells the persistence provider not to persist that field.

### What does `@Table` do?

It customizes the database table mapping of an entity.

### What does `@Column` do?

It customizes how a field maps to a database column.

### What does `@GeneratedValue` do?

It specifies how an entity identifier is automatically generated.

### What does `@Enumerated` do?

It specifies how a Java enum is persisted.

### What does `@Lob` do?

It maps a field to a large database object such as CLOB/BLOB data.

### What does `@CreationTimestamp` do?

It automatically records the entity creation timestamp when
supported/configured by Hibernate.

### What does `@UpdateTimestamp` do?

It automatically records the entity's update/modification timestamp when
supported/configured by Hibernate.

---

# 85. Final Mental Model

Remember Hibernate with this single picture:

```text
             JAVA APPLICATION
                    |
                    v
              Entity/Object
                    |
                    v
          Persistence Context
                    |
                    v
               Hibernate
                    |
        +-----------+-----------+
        |                       |
        v                       v
   ORM Mapping             Dirty Checking
        |                       |
        +-----------+-----------+
                    |
                    v
              SQL Generation
                    |
                    v
                  JDBC
                    |
                    v
             JDBC Driver
                    |
                    v
               DATABASE
```

And remember the core relationship:

```text
JPA = Standard / Rules
Hibernate = Implementation / Provider
JDBC = Low-level database connectivity
ORM = Object ↔ Relational mapping technique
```

The most important conceptual chain is:

```text
Java Object
    ↓
Entity
    ↓
Persistence Context
    ↓
Hibernate
    ↓
SQL
    ↓
JDBC
    ↓
Database
```

---
