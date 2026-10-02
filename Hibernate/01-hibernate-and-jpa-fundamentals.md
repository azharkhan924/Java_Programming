# Hibernate & JPA -- Fundamentals

> **Topics:** JDBC problems, Object-Relational Impedance Mismatch, ORM concept, Hibernate architecture, JPA specification, JDBC vs Hibernate, SessionFactory, Session, Configuration, Maven setup, First Hibernate project, CRUD operations

## JDBC → ORM → Hibernate → JPA → CRUD → Entity Mapping → Persistence Context

> **Goal:** These notes explain why Hibernate/ORM is needed, how
> Hibernate works internally, how JPA relates to Hibernate, how to
> configure a Hibernate project, perform CRUD operations, and map Java
> entities to relational database tables using important annotations.

---

# 1. Problems with JDBC

JDBC (Java Database Connectivity) allows Java applications to
communicate with relational databases, but in a large application,
writing everything directly with JDBC creates a lot of repetitive work.

### Major problems

1.  **Too much boilerplate code**

    -   Create connection
    -   Create statement/prepared statement
    -   Execute query
    -   Read `ResultSet`
    -   Convert data
    -   Close resources

2.  **Manual SQL writing**

    ``` sql
    SELECT * FROM student;
    INSERT INTO student VALUES (...);
    UPDATE student SET name = ...;
    DELETE FROM student WHERE id = ...;
    ```

3.  **Manual object conversion**

    Database returns rows, but Java works with objects.

    ``` text
    DB Row → Java Object
    Java Object → DB Row
    ```

    JDBC does not automatically perform complete ORM mapping.

4.  **Connection handling**

    -   Open connections
    -   Close connections
    -   Handle connection failures
    -   Manage resources

5.  **Exception handling**

    -   A lot of JDBC code contains repetitive `SQLException` handling.

6.  **Hard maintenance**

    -   SQL is scattered throughout the application.
    -   Changing database structure can require changes in many places.

7.  **Database-dependent code**

    -   SQL syntax and database-specific features can make code tightly
        coupled to a particular DB.

8.  **Large application becomes messy**

    -   DAO classes can contain large amounts of SQL and mapping code.
    -   Relationships become harder to manage manually.

9.  **Relationships are manual**

    -   One-to-one
    -   One-to-many
    -   Many-to-one
    -   Many-to-many

    JDBC does not automatically convert these relationships into Java
    object relationships.

10. **No automatic object lifecycle management** JDBC primarily deals
    with SQL statements and rows, not with the lifecycle of Java
    objects.

11. **Caching is not provided as an ORM feature** Hibernate provides
    caching mechanisms that can reduce unnecessary database access.

12. **Lazy loading is not an ORM feature of plain JDBC** Hibernate can
    load related data only when required.

13. **Transaction management is not ORM-level automatic** JDBC has
    transaction APIs, but the developer has to manage them explicitly.

---

# 2. The Real Problem: Object-Relational Impedance Mismatch

Java is an **object-oriented** language.

A relational database works with:

-   Tables
-   Rows
-   Columns
-   Primary keys
-   Foreign keys
-   SQL

Java works with:

-   Classes
-   Objects
-   Variables/fields
-   Inheritance
-   Object identity
-   Object references
-   Graph navigation

Therefore:

```text
Java Object-Oriented World
        ↓
   MISMATCH
        ↓
Relational Database World
```

This is called:

## Object-Relational Impedance Mismatch

### Definition

Object-relational impedance mismatch is the fundamental mismatch between
how an object-oriented language such as Java models data as objects,
inheritance, identity, relationships and object graphs, and how a
relational database models data using tables, rows, columns, keys and
SQL.

ORM frameworks such as Hibernate help solve this mismatch.

---

# 3. ORM

## What is ORM?

**ORM = Object Relational Mapping**

ORM is a programming technique that maps Java objects/classes to
relational database structures.

It provides a bridge between:

```text
Object-Oriented World
          ↕
         ORM
          ↕
Relational Database World
```

### Basic mapping

  Java                        Database
  --------------------------- ----------------------------
  Class                       Table
  Object                      Row
  Instance variable / field   Column
  Object identity             Primary Key
  Object reference            Foreign Key / Relationship
  Object collection           Related rows

### Example

Java:

```java
public class Student {
    private int id;
    private String name;
}
```

Database:

```text
student
+----+--------+
| id | name   |
+----+--------+
| 1  | Azhar  |
+----+--------+
```

ORM understands that:

```text
Student class → student table

Student object → student row

id field → id column

name field → name column
```

---

# 4. Five Important Object-Relational Mismatches

Different explanations classify these slightly differently, but the
commonly discussed ORM mismatches include:

1.  Granularity
2.  Inheritance
3.  Identity
4.  Associations
5.  Navigation

---

## 4.1 Granularity Mismatch

Java can represent data using many fine-grained objects/classes, while a
relational database stores information in tables and columns.

Example:

```java
class Address {
    String city;
    String state;
}
```

The database may store:

```text
student
+----+------+-------+
| id | city | state |
+----+------+-------+
```

ORM decides how the object structure maps to the relational structure.

---

## 4.2 Inheritance Mismatch

Java supports inheritance:

```java
class Person { }

class Student extends Person { }

class Teacher extends Person { }
```

Relational databases do not have Java-style class inheritance directly.

ORM provides strategies to represent inheritance in tables.

---

## 4.3 Identity Mismatch

Java objects have object identity.

```java
Student s1 = new Student();
Student s2 = new Student();
```

The database identifies rows primarily through primary keys.

```text
Student object identity
        ↓
Database primary key
```

ORM manages this mapping.

---

## 4.4 Association Mismatch

Java can directly represent object references:

```java
student.setAddress(address);
```

A relational database represents relationships using:

-   Foreign keys
-   Join tables
-   SQL joins

ORM maps object relationships to relational relationships.

---

## 4.5 Navigation Mismatch

Java can navigate an object graph:

```java
student.getDepartment().getCollege();
```

A relational database normally requires SQL queries and joins to obtain
related information.

ORM can provide mechanisms such as:

-   Lazy loading
-   Fetching
-   Joins
-   Relationship mappings

---

# 5. Is JDBC Always Worse Than Hibernate?

**No.**

Hibernate is not automatically better for every situation.

### JDBC can be useful when:

-   SQL needs to be highly optimized or precisely controlled.
-   The application is SQL-heavy.
-   A simple query requires minimal abstraction.
-   You need database-specific SQL features.
-   You want direct control over JDBC behavior.

### Hibernate is useful when:

-   The application has many entities.
-   Relationships are complex.
-   CRUD operations are frequent.
-   Object mapping is repetitive.
-   You want ORM abstraction.
-   You want features such as dirty checking, caching and lazy loading.

The correct choice depends on the application's requirements.

---

# 6. Hibernate

## What is Hibernate?

Hibernate is a Java ORM framework/provider.

It maps Java objects/entities to relational database tables and reduces
the amount of JDBC and SQL mapping code developers need to write
manually.

### Hibernate provides

-   ORM
-   Object-relational mapping
-   Automatic SQL generation for many operations
-   Relationship management
-   Persistence management
-   Dirty checking
-   Lazy/eager fetching mechanisms
-   Caching
-   Transaction integration
-   HQL/JPQL-style query support
-   Database dialect support

### Simplified idea

```text
Java Object
    ↓
Hibernate
    ↓
SQL
    ↓
JDBC
    ↓
Database
```

Important:

> Hibernate does **not** replace JDBC internally. Hibernate commonly
> uses JDBC underneath to communicate with the database.

---

# 7. JPA

## What is JPA?

**JPA = Java Persistence API**

JPA is a **specification/standard**, not a concrete ORM implementation.

It defines rules, annotations and APIs for persistence and ORM.

Examples:

```java
@Entity
@Table
@Id
@Column
@GeneratedValue
```

and JPA APIs such as:

```java
EntityManager
EntityManagerFactory
```

### JPA vs Hibernate

```text
JPA       → Specification / Rules / Standard

Hibernate → Implementation / Provider
```

A simple analogy:

```text
JPA       = Rules
Hibernate = Implementation of those rules
```

Other JPA providers exist as well; Hibernate is one of the widely used
providers.

> Modern Jakarta Persistence is the successor of the API historically
> known as JPA.

---

# 8. Why JPA + Hibernate?

Using JPA APIs with Hibernate as the provider gives:

-   Cleaner code
-   Less vendor-specific code
-   Standard annotations
-   Easier relationship mapping
-   Less repetitive persistence code
-   Database portability
-   Easy integration with Spring/Spring Boot
-   Enterprise-standard persistence programming model

Usually, application code can be written against JPA APIs while
Hibernate provides the implementation underneath.

---

# 9. JDBC vs Hibernate

  ---------------------------------------------------------------------
  JDBC                               Hibernate
  ---------------------------------- ----------------------------------
  Low-level database API             ORM framework/provider

  SQL usually written manually       SQL often generated automatically

  Manual `ResultSet` mapping         Object-relational mapping

  More boilerplate                   Less boilerplate

  Manual relationship handling       Relationship mapping

  No built-in ORM persistence        Persistence context
  context                            

  No ORM dirty checking              Dirty checking

  Manual resource handling           Higher-level abstraction

  Direct JDBC programming            Uses JDBC internally

  Developer has more direct SQL      Framework manages more persistence
  control                            details
  ---------------------------------------------------------------------

---

# 10. Hibernate Architecture

A simplified architecture:

```text
Java Application
       ↓
JPA / Hibernate API
       ↓
Hibernate ORM
       ↓
JDBC
       ↓
JDBC Driver
       ↓
Database
```

For MySQL:

```text
Java Application
       ↓
Hibernate
       ↓
JDBC
       ↓
MySQL Connector/J
       ↓
MySQL Database
```

Hibernate sits between application code and the database and manages
ORM/persistence communication.

---

# 11. Core Hibernate Components

## 11.1 Configuration

`Configuration` is traditionally used to bootstrap/configure Hibernate.

It can:

-   Read configuration
-   Load database properties
-   Load mappings/metadata
-   Prepare the Hibernate environment

Example:

```java
Configuration cfg = new Configuration();
```

At this point:

```text
Configuration object created
        ↓
Configuration file not necessarily loaded yet
```

Then:

```java
cfg.configure();
```

By default, Hibernate looks for:

```text
hibernate.cfg.xml
```

---

# 12. What Happens During `configure()`?

```java
Configuration cfg = new Configuration();
cfg.configure();
```

Conceptually:

```text
cfg.configure()
      ↓
Find hibernate.cfg.xml
      ↓
Read XML
      ↓
Load database properties
      ↓
Load mappings/entity metadata
      ↓
Prepare configuration/metadata
```

`configure()` does not mean that a permanent database connection is now
open.

It prepares the configuration for creating the `SessionFactory`.

---

# 13. SessionFactory

```java
SessionFactory sf = cfg.buildSessionFactory();
```

`SessionFactory` is a heavyweight, thread-safe factory used to create
Hibernate sessions.

It is normally created once for an application/database configuration
and reused.

### Responsibilities

-   Hold Hibernate configuration/metadata
-   Create `Session` objects
-   Coordinate Hibernate's persistence infrastructure
-   Participate in caching/configuration infrastructure

### Important

```text
Configuration
      ↓
SessionFactory
      ↓
Session
```

`SessionFactory` is heavyweight.

`Session` is comparatively lightweight and normally represents a unit of
interaction with the persistence context.

---

# 14. Session

A Hibernate `Session` is used to perform persistence operations.

Examples:

```java
session.persist(student);
session.get(Student.class, 101);
session.remove(student);
```

A Session manages a persistence context and tracks managed entities.

---

# 15. Transaction

A transaction groups database operations into a unit of work.

Example:

```java
Transaction tx = session.beginTransaction();

session.persist(student);

tx.commit();
```

If an operation fails, the transaction can be rolled back.

```java
tx.rollback();
```

### Why transaction?

To maintain consistency and atomicity.

Example:

```text
Operation 1 ✓
Operation 2 ✓
Operation 3 ✗
```

A transaction allows the unit of work to be rolled back when
appropriate.

---

# 16. Hibernate Configuration File

Typical location in a Maven project:

```text
src/
 └── main/
     ├── java/
     └── resources/
         └── hibernate.cfg.xml
```

Default file:

```text
hibernate.cfg.xml
```

Custom configuration:

```java
cfg.configure("myconfig.xml");
```

---

# 17. Example `hibernate.cfg.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE hibernate-configuration PUBLIC
        "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
        "https://hibernate.sourceforge.net/hibernate-configuration-3.0.dtd">

<hibernate-configuration>

    <session-factory>

        <property name="connection.driver_class">
            com.mysql.cj.jdbc.Driver
        </property>

        <property name="connection.url">
            jdbc:mysql://localhost:3306/college
        </property>

        <property name="connection.username">
            root
        </property>

        <property name="connection.password">
            root
        </property>

        <property name="dialect">
            org.hibernate.dialect.MySQLDialect
        </property>

        <property name="show_sql">
            true
        </property>

        <property name="hbm2ddl.auto">
            update
        </property>

        <mapping class="com.entity.Student"/>

    </session-factory>

</hibernate-configuration>
```

> Exact DTD URLs and dialect class names can vary by Hibernate version.
> In modern Hibernate versions, configuration conventions have evolved,
> especially with Jakarta Persistence and Hibernate 6+.

---

# 18. Important Hibernate Configuration Properties

## 18.1 `connection.driver_class`

```xml
<property name="connection.driver_class">
    com.mysql.cj.jdbc.Driver
</property>
```

Specifies the JDBC driver class.

For MySQL Connector/J:

```text
com.mysql.cj.jdbc.Driver
```

Without the required JDBC driver dependency, the application cannot
communicate with MySQL.

---

## 18.2 `connection.url`

Example:

```text
jdbc:mysql://localhost:3306/college
```

Breakdown:

```text
jdbc       → JDBC protocol
mysql      → Database type
localhost  → Database server
3306       → MySQL default port
college    → Database name
```

---

## 18.3 `connection.username`

```xml
<property name="connection.username">
    root
</property>
```

Database username.

---

## 18.4 `connection.password`

```xml
<property name="connection.password">
    root
</property>
```

Database password.

---

## 18.5 Dialect

```xml
<property name="dialect">
    ...
</property>
```

Dialect tells Hibernate how to generate SQL appropriate for the target
database.

Think:

```text
Hibernate
   ↓
Which DB am I targeting?
   ↓
How should SQL be generated?
```

Exact dialect configuration depends on Hibernate version and whether
automatic dialect detection is being used.

---

## 18.6 `show_sql`

```xml
<property name="show_sql">true</property>
```

Shows generated SQL in the console.

Useful for:

-   Debugging
-   Learning
-   Understanding ORM behavior
-   Checking generated SQL

---

## 18.7 `hbm2ddl.auto`

Controls schema-generation/update behavior.

Common values:

### `create`

Creates/recreates the schema when the SessionFactory starts.

**Warning:** Can destroy existing schema/data depending on
database/schema behavior.

### `update`

Attempts to update the schema to match mappings without intentionally
dropping existing tables.

Useful for development, but should be used carefully.

### `create-drop`

Creates the schema when the SessionFactory starts and drops it when the
SessionFactory closes.

Useful mainly for testing/development.

### `validate`

Validates that the existing schema matches the mappings.

It does not create/update tables.

### `none`

No automatic schema action.

> For production systems, schema migration tools such as Flyway or
> Liquibase are commonly preferred over relying on `hbm2ddl.auto` for
> schema evolution.

---

# 19. Mapping Entity Classes

Example:

```xml
<mapping class="com.entity.Student"/>
```

This tells Hibernate that the `Student` class is part of the persistence
model when using this traditional configuration approach.

With modern JPA/Spring Boot setups, entities are commonly discovered
through scanning rather than being listed manually in XML.

---

# 20. Maven Dependencies

Typical dependencies for a traditional Hibernate + MySQL project
include:

### Hibernate ORM

Provides Hibernate ORM functionality such as:

-   Session
-   SessionFactory
-   ORM mapping
-   HQL
-   Persistence mechanisms
-   Caching infrastructure

### MySQL Connector/J

Provides JDBC connectivity to MySQL.

```text
Java/Hibernate
      ↓
JDBC API
      ↓
MySQL Connector/J
      ↓
MySQL
```

### JPA/Jakarta Persistence API

Provides standard persistence annotations/APIs such as:

```java
@Entity
@Table
@Id
@Column
@GeneratedValue
```

Exact artifact names depend on whether the project uses older
`javax.persistence` or modern `jakarta.persistence`.

---

# 21. Project Structure

Example:

```text
HibernateProject/
│
├── src/
│   └── main/
│       ├── java/
│       │   ├── com/
│       │   │   ├── entity/
│       │   │   │   └── Student.java
│       │   │   │
│       │   │   └── main/
│       │   │       └── App.java
│       │   │
│       │
│       └── resources/
│           └── hibernate.cfg.xml
│
└── pom.xml
```

---

# 22. Entity

An **entity** is a Java class whose instances are persisted to a
database and whose class is mapped to a database table.

Example:

```java
@Entity
public class Student {
    private int id;
    private String name;
}
```

`@Entity` tells the ORM provider that this class participates in
persistence.

---

# 23. Entity Requirements

A typical entity should:

1.  Be annotated with `@Entity`.
2.  Have an identifier using `@Id`.
3.  Have a no-argument constructor (usually required by the persistence
    specification/provider).
4.  Generally not be `final`.
5.  Use fields/properties that Hibernate/JPA can access.
6.  Have a proper identifier strategy.

### Why ID is mandatory

Hibernate needs an identifier to uniquely identify an entity.

It needs the ID to:

-   Find a row
-   Update a row
-   Delete a row
-   Track an entity
-   Determine entity identity

Example:

```java
@Id
private int id;
```

Database:

```text
student
+-----+--------+
| id  | name   |
+-----+--------+
| 101 | Azhar  |
+-----+--------+
```

Here `id = 101` identifies the entity.

---

# 24. No-Argument Constructor

Hibernate/JPA needs an accessible no-argument constructor for entity
instantiation according to the persistence model.

Example:

```java
@Entity
public class Student {

    @Id
    private int id;

    private String name;

    public Student() {
    }
}
```

Without a suitable no-argument constructor, entity instantiation can
fail.

---

# 25. `@Entity`

```java
@Entity
public class Student {
}
```

Meaning:

> This class is a persistent entity managed by the ORM provider.

Hibernate/JPA uses entity metadata to understand how the class maps to
database data.

---

# 26. Default Table Mapping

If no custom table name is supplied, the provider typically derives the
table name from the entity name according to its naming rules.

Example:

```java
@Entity
public class Student {
}
```

Can map to a table such as:

```text
Student
```

Exact physical naming can depend on the ORM provider and naming
strategy.

---

# 27. `@Table`

Used to customize table mapping.

Example:

```java
@Entity
@Table(name = "student_info")
public class Student {
}
```

Now:

```text
Java Entity
    ↓
Student
    ↓
student_info table
```

### Why use `@Table`?

Suppose the existing database has:

```text
student_info
```

but your Java class is:

```java
Student
```

Then:

```java
@Table(name = "student_info")
```

maps them correctly.

---

# 28. `@Id`

`@Id` identifies the primary key.

```java
@Id
private int id;
```

Database:

```text
id → Primary Key
```

### Why is it important?

Hibernate needs a unique identifier to:

-   Track entity identity
-   Fetch entity
-   Update entity
-   Delete entity
-   Manage persistence context

Without an identifier, a normal JPA entity mapping is invalid.

---

# 29. `@GeneratedValue`

Used for automatic identifier generation.

```java
@Id
@GeneratedValue
private Long id;
```

Common strategies:

```text
AUTO
IDENTITY
SEQUENCE
TABLE
```

---

## 29.1 `GenerationType.AUTO`

Provider chooses an appropriate strategy.

```java
@GeneratedValue(strategy = GenerationType.AUTO)
```

---

## 29.2 `GenerationType.IDENTITY`

Database identity/auto-increment mechanism.

Example in MySQL:

```java
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
```

Database can generate:

```text
1
2
3
4
...
```

---

## 29.3 `GenerationType.SEQUENCE`

Uses a database sequence where supported.

```java
@GeneratedValue(strategy = GenerationType.SEQUENCE)
```

Common with databases that support sequences.

---

## 29.4 `GenerationType.TABLE`

Uses a table-based mechanism for identifier generation.

```java
@GeneratedValue(strategy = GenerationType.TABLE)
```

Less commonly chosen in modern applications.

---

# 30. `@Column`

Used to customize column mapping.

Example:

```java
@Column(name = "stu_name")
private String name;
```

This maps:

```text
Java field: name
       ↓
DB column: stu_name
```

---

## `@Column` Attributes

Example:

```java
@Column(
    name = "stu_name",
    nullable = false,
    unique = true,
    length = 50
)
private String name;
```

### `name`

Custom column name.

### `nullable`

```java
nullable = false
```

Column should not accept `NULL` according to generated schema
constraints.

### `unique`

```java
unique = true
```

Requests a uniqueness constraint in generated schema.

### `length`

```java
length = 50
```

Defines length for applicable string columns in generated schema.

---
