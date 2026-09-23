# Spring Framework – Core Architecture, IoC Container & Bean Scopes

> **Focus:** Rod Johnson background, IoC & DI concepts, Eclipse project setup, First Spring Bean program, `ABC.xml`, `ApplicationContext` vs `BeanFactory`, Container hierarchy, Reflection & private constructors, Spring Exceptions, DI types (Constructor, Setter, Field), `getBean()` overloads, Bean Scopes (Singleton vs Prototype), and 4 Scope combinations.

## 1. Introduction to Spring Framework

Spring is a Java framework whose core concepts include **Inversion of
Control (IoC)** and **Dependency Injection (DI)**.

It helps in developing Java applications that are:

-   loosely coupled,
-   easier to modify,
-   easier to maintain,
-   easier to test.

### Main idea

Normally, the developer creates objects:

```java
Student s = new Student();
```

With Spring, the **IoC container** takes responsibility for creating and
managing Spring beans.

```java
Student s = context.getBean(Student.class);
```

So:

```text
Developer
   ↓
creates/manages objects
```

becomes:

```text
Spring IoC Container
   ↓
creates/manages Spring beans
```

---


# 2. Spring Project --- Basic Setup

For a basic XML-based Spring project in Eclipse:

```text
File
 ↓
New
 ↓
Java Project
 ↓
Create project
```

Then:

```text
src
 ↓
New
 ↓
Package
```

For our example:

```text
src
 ├── pack1
 │    └── Student.java
 │
 ├── pack2
 │    └── ABC.xml
 │
 └── pack3
      └── MainDemo.java
```

### Package responsibilities

  Package   File              Purpose
  --------- ----------------- ---------------------------
  `pack1`   `Student.java`    Bean class
  `pack2`   `ABC.xml`         Spring configuration file
  `pack3`   `MainDemo.java`   Main class

Spring JAR/dependencies can be added through:

```text
Project
 → Properties
 → Java Build Path
 → Libraries
 → Classpath
 → Add External JARs
```

---


# 3. First Spring Bean Program

We will create a very basic `Student` bean.

## Student.java

```java
package pack1;

public class Student {

    private int id;
    private String name;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

Here:

```text
Student
   ↓
Bean class
```

The class contains:

```java
int id;
String name;
```

along with their getter and setter methods.

---


# 4. Spring Configuration File --- ABC.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="S1" class="pack1.Student">

        <property name="id" value="101"/>

        <property name="name" value="AAA"/>

    </bean>

</beans>
```

## Understanding the bean tag

```xml
<bean id="S1" class="pack1.Student">
```

### `id`

```xml
id="S1"
```

`S1` is the bean's identifier/name.

### `class`

```xml
class="pack1.Student"
```

This tells Spring which class's object should be created.

Therefore:

```text
bean id = S1
        ↓
class = pack1.Student
        ↓
Spring creates Student object
```

---


# 5. Property Tag

Inside the bean:

```xml
<property name="id" value="101"/>
<property name="name" value="AAA"/>
```

Spring uses the corresponding setter methods.

Conceptually:

```xml
<property name="id" value="101"/>
```

results in:

```java
setId(101);
```

and:

```xml
<property name="name" value="AAA"/>
```

results in:

```java
setName("AAA");
```

So the `property` tag is used for setter-based configuration/injection.

---


# 6. MainDemo.java

```java
package pack3;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import pack1.Student;

public class MainDemo {

    public static void main(String[] args) {

        ApplicationContext app =
            new ClassPathXmlApplicationContext("pack2/ABC.xml");

        Student s1 =
            app.getBean("S1", Student.class);

        System.out.println(s1.getId());
        System.out.println(s1.getName());
    }
}
```

### Output

```text
101
AAA
```

---


# 7. Understanding `ApplicationContext`

```java
ApplicationContext app =
    new ClassPathXmlApplicationContext("pack2/ABC.xml");
```

`ApplicationContext` is an interface:

```java
org.springframework.context.ApplicationContext
```

`ClassPathXmlApplicationContext` is a commonly used implementation:

```java
org.springframework.context.support.ClassPathXmlApplicationContext
```

Conceptually:

```text
ApplicationContext
        ↑
        |
ClassPathXmlApplicationContext
```

---


# 8. What Happens When `ClassPathXmlApplicationContext` Is Created?

When this is executed:

```java
new ClassPathXmlApplicationContext("pack2/ABC.xml");
```

Spring creates the application context/container and processes the
configuration.

Basic flow:

```text
ClassPathXmlApplicationContext
          ↓
Read ABC.xml
          ↓
Read bean definitions
          ↓
Create/manage configured beans
          ↓
Store/manage beans in the IoC container
          ↓
Application can request beans
```

For normal eager singleton beans, Spring creates the bean while the
application context is being initialized.

---


# 9. `getBean()`

After the container is created:

```java
Student s1 =
    app.getBean("S1", Student.class);
```

The developer asks the container for the bean named `S1`.

Conceptually:

```text
Developer
   ↓
getBean("S1")
   ↓
IoC Container
   ↓
S1 bean
   ↓
Student object/reference
```

The developer does not need to manually create the Spring-managed object
using:

```java
new Student();
```

---


# 10. Java GC vs Spring Container

In Core Java, the **Garbage Collector (GC)** is responsible for
reclaiming memory from objects that are no longer reachable.

Spring has a different responsibility.

### Java GC

```text
Unreachable object
       ↓
Garbage Collector
       ↓
Memory reclamation
```

### Spring IoC Container

```text
Bean definition
       ↓
Create/configure bean
       ↓
Manage bean
       ↓
Provide bean to application
```

So do not confuse:

```text
GC → memory management
Spring IoC → object/bean creation and dependency management
```

---


# 11. Bean Concept

A Spring-managed object is called a **bean**.

Example:

```xml
<bean id="S1"
      class="pack1.Student"/>
```

Here Spring creates and manages an object of:

```java
pack1.Student
```

That object is a Spring bean.

---


# 12. Meaning of Bean ID and Class

Consider:

```xml
<bean id="S1"
      class="pack1.Student"/>
```

### Bean ID

```text
S1
```

It identifies the bean inside the container.

### Class

```text
pack1.Student
```

It specifies the class whose object Spring should create.

So:

```text
S1
 ↓
pack1.Student object
```

---


# 13. IoC --- Inversion of Control

Without Spring:

```java
Student s = new Student();
```

The developer controls object creation.

With Spring:

```java
Student s =
    app.getBean(Student.class);
```

The Spring container controls creation and management of the bean.

Therefore:

> **Inversion of Control means that control over object creation and
> dependency management is transferred from the developer/application
> code to the IoC container.**

---


# 14. IoC Container

The Spring IoC container is responsible for tasks such as:

-   creating beans,
-   configuring beans,
-   injecting dependencies,
-   managing bean lifecycle,
-   providing beans to application code.

Basic flow:

```text
Configuration
     ↓
IoC Container
     ↓
Bean Creation
     ↓
Dependency Injection
     ↓
Bean Management
     ↓
getBean()
```

---


# 15. Types of IoC Container

At the basic level, two important Spring container abstractions are:

1.  `BeanFactory`
2.  `ApplicationContext`

---


# 16. BeanFactory

`BeanFactory` is present in:

```java
org.springframework.beans.factory.BeanFactory
```

It provides the basic IoC container functionality.

### Characteristics

-   Basic IoC container.
-   Supports dependency injection.
-   Supports lazy creation of singleton beans.
-   Provides the fundamental bean-management functionality.

---


# 17. ApplicationContext

`ApplicationContext` is a more feature-rich container abstraction.

It provides bean-management functionality along with additional features
such as:

-   event publication,
-   message/resource support,
-   resource loading,
-   annotation integration,
-   easier application-level configuration.

Common implementations include:

```text
ClassPathXmlApplicationContext
FileSystemXmlApplicationContext
AnnotationConfigApplicationContext
```

---


# 18. BeanFactory vs ApplicationContext

| Point | BeanFactory | ApplicationContext |
| --- | --- | --- |
  ----------------------- ----------------------- -----------------------
| Type | Basic container | Advanced |
                          interface               application-context
                                                  interface

| Dependency Injection | Yes | Yes |

  Lazy singleton creation Supported               Supported

| Features | Basic | More features |

| Typical use | Basic/low-level | Most Spring |
                          container               applications


### Important

Do not memorize the oversimplified statement:

```text
BeanFactory = old
ApplicationContext = new
```

A better understanding is:

```text
BeanFactory
→ fundamental IoC container contract

ApplicationContext
→ richer container built on top of bean-factory functionality
```

---


# 19. Container Hierarchy --- Basic View

A simplified conceptual view is:

```text
BeanFactory
     ↑
ApplicationContext
     ↑
ConfigurableApplicationContext
     ↑
ClassPathXmlApplicationContext
```

Another XML-based implementation is:

```text
FileSystemXmlApplicationContext
```

For Java configuration:

```text
AnnotationConfigApplicationContext
```

> Older notes may mention `XmlBeanFactory`. It is a legacy/deprecated
> API and should not be treated as the modern recommended XML container.

---


# 20. BeanFactory and Lazy Creation

A basic characteristic of `BeanFactory` is that singleton beans can be
created lazily.

Conceptually:

```text
Bean definition loaded
       ↓
Bean not necessarily created immediately
       ↓
getBean()
       ↓
Bean created
```

Whereas the normal `ApplicationContext` behavior is to eagerly create
singleton beans during context initialization unless lazy initialization
is configured.

---


# 21. Reflection and Private Constructor

Consider:

```java
public class Student {

    private Student() {
        System.out.println("Private constructor");
    }
}
```

Normally:

```java
Student s = new Student();
```

cannot be used outside the class because the constructor is private.

Reflection can inspect the constructor:

```java
Constructor<Student> constructor =
    Student.class.getDeclaredConstructor();
```

Then, where reflective access is permitted:

```java
constructor.setAccessible(true);

Student s =
    constructor.newInstance();
```

---


# 22. Reflection Example

```java
import java.lang.reflect.Constructor;

public class Demo {

    private Demo() {
        System.out.println("Private constructor");
    }

    public static void main(String[] args)
            throws Exception {

        Constructor<Demo> c =
            Demo.class.getDeclaredConstructor();

        c.setAccessible(true);

        Demo obj =
            c.newInstance();
    }
}
```

Output:

```text
Private constructor
```

### Flow

```text
Private Constructor
       ↓
Reflection API
       ↓
getDeclaredConstructor()
       ↓
setAccessible(true)
       ↓
newInstance()
       ↓
Object created
```

> Modern Java module/access rules can restrict reflective access in some
> situations. `setAccessible(true)` should therefore not be understood
> as an unconditional bypass of every access restriction.

---


# 23. Important Spring Exceptions

## 23.1 `BeanDefinitionStoreException`

This is a general bean-definition/configuration loading exception.

It can occur when Spring cannot load/process a bean definition or
configuration resource.

For example, if the XML configuration file cannot be found or loaded,
the failure can be represented by:

```text
BeanDefinitionStoreException
```

with a lower-level resource exception as the cause.

---


# 24. Wrong XML Resource

Example:

```java
ApplicationContext app =
    new ClassPathXmlApplicationContext("wrong.xml");
```

If the resource cannot be found/loaded, a configuration loading failure
can occur, commonly involving:

```text
BeanDefinitionStoreException
```

with an underlying resource/file-related cause.

---


# 25. Wrong Bean Class

Suppose:

```xml
<bean id="S1"
      class="pack1.WrongStudent"/>
```

If the class cannot be loaded, Spring can throw:

```text
CannotLoadBeanClassException
```

with a lower-level:

```text
ClassNotFoundException
```

as the cause in the relevant case.

---


# 26. `BeanDefinitionParsingException`

If the Spring bean definition XML is malformed or cannot be parsed
according to the expected configuration structure, a parsing-related
exception can occur:

```text
BeanDefinitionParsingException
```

Example category:

```text
Invalid/malformed XML bean configuration
```

---


# 27. `BeanInstantiationException`

This can occur when Spring cannot instantiate a configured bean class.

For example, trying to instantiate an interface directly:

```xml
<bean id="A1"
      class="P1.Address"/>
```

when:

```java
public interface Address {
}
```

cannot work because an interface has no concrete object that Spring can
instantiate.

Use a concrete implementation instead:

```xml
<bean id="A1"
      class="P1.OfficeAddress"/>
```

---


# 28. Dependency Injection --- Meaning

Suppose:

```java
public class Student {

    private Address address;

}
```

`Student` needs an `Address`.

Therefore:

```text
Address
   ↓
Dependency of Student
```

### Dependency

A dependency is an object/functionality that another class requires.

### Injection

Injection means supplying that dependency from outside the dependent
class.

So:

> **Dependency Injection means providing an object's required
> dependencies from an external source rather than making the object
> create those dependencies itself.**

---


# 29. Why Dependency Injection?

Without DI:

```java
public class Student {

    private Address address =
        new Address();

}
```

`Student` is tightly coupled to `Address`.

With DI:

```java
public class Student {

    private Address address;

    public Student(Address address) {
        this.address = address;
    }
}
```

The dependency is supplied from outside.

Therefore:

```text
Tight coupling
      ↓
Reduced through DI
      ↓
Loose coupling
```

---


# 30. Types of Dependency Injection

Three commonly discussed types:

```text
1. Constructor Injection
2. Setter Injection
3. Field Injection
```

---


# 31. Constructor Injection

Dependency is supplied through the constructor.

```java
public class Student {

    private final Address address;

    public Student(Address address) {
        this.address = address;
    }
}
```

### Advantages

-   Dependency is available immediately.
-   Good for mandatory dependencies.
-   Makes required dependencies explicit.
-   Works naturally with `final` references.
-   Encourages immutable object design.
-   Easy to test using normal Java constructors.

---


# 32. Setter Injection

Dependency is supplied through a setter method.

```java
public class Student {

    private Address address;

    public void setAddress(Address address) {
        this.address = address;
    }
}
```

### Advantages

-   Useful for optional dependencies.
-   Dependency can be configured after object creation.
-   Dependency can be changed later if the design allows it.

---


# 33. Field Injection

Dependency is injected directly into a field.

```java
public class Student {

    @Autowired
    private Address address;
}
```

### Characteristics

-   Less boilerplate.
-   Easy to write.
-   Dependency is less visible in the constructor.
-   Makes constructor-based immutability harder.
-   Testing without Spring can require additional setup/reflection.

---


# 34. Constructor vs Setter vs Field Injection

| Point | Constructor Injection | Setter Injection | Field Injection |
|---|---|---|---|
| **Definition** | Dependency provided through constructor | Dependency provided through setter method | Dependency injected directly into field via reflection |
| **Injection Time** | During object instantiation | After object creation | During bean population |
| **Mandatory Dependency** | Best suited (cannot instantiate without it) | Possible but requires explicit checks | Less explicit |
| **Optional Dependency** | Less convenient (needs multiple constructors) | Well suited | Possible |
| **Object State** | Dependency available immediately; object always initialized | Can be incomplete after construction | Can be incomplete until reflection injection |
| **Mutability** | Can use `final` reference (immutable) | Dependency can be changed later | Usually mutable |
| **Testing** | Very easy (can pass mock objects in constructor) | Easy (can use setter to pass mock) | Harder (requires reflection or Spring context) |
| **Readability** | Very explicit | Explicit | Less explicit |
| **Recommendation** | Preferred for required dependencies | Good for optional/configurable dependencies | Avoided in modern Spring code |

---


# 35. `getBean()` Method

Spring's `ApplicationContext` provides overloaded `getBean()` methods.

---


## 35.1 `T getBean(Class<T> requiredType)`

```java
Student s =
    app.getBean(Student.class);
```

Here:

```text
T
↓
Generic return type

Class<T>
↓
Generic type parameter
```

The method returns a bean matching the requested type.

---


## 35.2 `Object getBean(String name)`

```java
Object obj =
    app.getBean("S1");
```

The bean is requested by name.

---


## 35.3 `T getBean(String name, Class<T> requiredType)`

```java
Student s =
    app.getBean("S1", Student.class);
```

This specifies:

```text
Bean name → S1
Type      → Student
```

---


## 35.4 `Object getBean(String name, Object... args)`

```java
Object obj =
    app.getBean("S1", 101, "AAA");
```

Here:

```java
Object... args
```

is a variable-argument parameter.

This form is relevant to prototype bean creation where explicit
arguments are supplied to the bean's creation mechanism.

---


# 36. Singleton Scope

By default, Spring beans have **singleton scope**.

Example:

```xml
<bean id="S1"
      class="pack1.Student"
      scope="singleton"/>
```

For a singleton bean:

```text
One bean instance
per Spring IoC container
```

Example:

```java
Student s1 =
    app.getBean("S1", Student.class);

Student s2 =
    app.getBean("S1", Student.class);

System.out.println(s1 == s2);
```

Output:

```text
true
```

---


# 37. Prototype Scope

To use prototype scope:

```xml
<bean id="S1"
      class="pack1.Student"
      scope="prototype"/>
```

Every request for the prototype bean results in a new instance.

Example:

```java
Student s1 =
    app.getBean("S1", Student.class);

Student s2 =
    app.getBean("S1", Student.class);

System.out.println(s1 == s2);
```

Output:

```text
false
```

Conceptually:

```text
getBean()
   ↓
Student #1

getBean()
   ↓
Student #2

getBean()
   ↓
Student #3
```

---


# 38. Singleton vs Prototype

| Point | Singleton | Prototype |
|---|---|---|
| **Instance Count** | One instance per Spring container | New instance created on each request |
| **Creation** | Normally during context initialization (eager) | Only when requested via `getBean()` (lazy) |
| **`getBean()`** | Always returns the same instance | Always returns a new instance |
| **Object Creation Overhead** | Low (created once) | Higher (created repeatedly) |
| **Memory Usage** | Fewer instances in memory | More instances created in memory |
| **Destruction Management** | Container manages complete lifecycle including `@PreDestroy` / destruction callbacks | Container does NOT manage complete destruction lifecycle after returning prototype |


### Important

Prototype does **not** mean:

```text
Spring destroys prototype automatically after use.
```

Spring creates and configures the prototype, but the container generally
does not manage its complete destruction lifecycle after returning it to
the caller.

---


# 39. Student and Address Scope Example

Consider:

```text
Student
   ↓
Address
```

Student depends on Address.

We can create four scope combinations:

```text
Case 1 → Student Singleton + Address Prototype
Case 2 → Student Prototype + Address Singleton
Case 3 → Student Prototype + Address Prototype
Case 4 → Student Singleton + Address Singleton
```

---


# 40. Case 1 --- Student Singleton + Address Prototype

### ABC.xml

```xml
<bean id="A1"
      class="P1.Address"
      scope="prototype"/>

<bean id="S1"
      class="P1.Student"
      scope="singleton">

    <property name="address"
              ref="A1"/>

</bean>
```

### Important behavior

Student:

```text
singleton
```

Address:

```text
prototype
```

When the singleton Student is created, its Address dependency is
resolved and injected.

So the singleton Student normally holds that injected Address reference.

```text
ApplicationContext starts
        ↓
Student singleton created
        ↓
Address prototype created for injection
        ↓
Student stores Address reference
```

### Important

Calling a method on the same singleton Student does **not automatically
create another Address prototype**.

If you need a new Address every time a method is called, a
lookup/provider mechanism is required.

This is the problem solved by **Lookup Method Injection**.

---


# 41. Case 2 --- Student Prototype + Address Singleton

### ABC.xml

```xml
<bean id="A1"
      class="P1.Address"
      scope="singleton"/>

<bean id="S1"
      class="P1.Student"
      scope="prototype">

    <property name="address"
              ref="A1"/>

</bean>
```

Flow:

```text
Address
   ↓
Singleton
   ↓
One Address object

Student getBean()
   ↓
Student #1
   ↓
same Address

Student getBean()
   ↓
Student #2
   ↓
same Address
```

So:

```text
Student → new each time
Address → same object
```

---


# 42. Case 3 --- Both Prototype

```xml
<bean id="A1"
      class="P1.Address"
      scope="prototype"/>

<bean id="S1"
      class="P1.Student"
      scope="prototype">

    <property name="address"
              ref="A1"/>

</bean>
```

Flow:

```text
getBean(S1)
    ↓
Student #1
    ↓
Address #1

getBean(S1)
    ↓
Student #2
    ↓
Address #2
```

Both are prototype beans.

---


# 43. Case 4 --- Both Singleton

```xml
<bean id="A1"
      class="P1.Address"
      scope="singleton"/>

<bean id="S1"
      class="P1.Student"
      scope="singleton">

    <property name="address"
              ref="A1"/>

</bean>
```

Flow:

```text
Student → one object
Address → one object
```

Within the same Spring container, repeated `getBean()` calls return the
same Student and Address instances.

---


# 44. Four Cases --- Quick Revision

| Case | Student Scope | Address Scope | Student Object | Address Object Injected |
|---|---|---|---|---|
| **Case 1** | Singleton | Prototype | Same instance every `getBean()` | Same Address instance (injected once at creation)! |
| **Case 2** | Prototype | Singleton | New Student instance every `getBean()` | Same shared Address instance |
| **Case 3** | Prototype | Prototype | New Student instance every `getBean()` | New Address instance for each Student |
| **Case 4** | Singleton | Singleton | Same Student instance | Same shared Address instance |

# 45. Why Prototype Inside Singleton Creates a Problem

Suppose:

```text
Student = Singleton
Address = Prototype
```

Requirement:

```text
student.getAddress()
      ↓
Address #1

student.getAddress()
      ↓
Address #2

student.getAddress()
      ↓
Address #3
```

Normal injection does not do this automatically.

The prototype dependency is normally resolved when the singleton is
created.

Therefore:

```text
Singleton
   ↓
holds one injected prototype reference
```

To obtain a **fresh prototype instance whenever required**, mechanisms
such as:

```text
Lookup Method Injection
ObjectProvider
Provider
```

can be used.

The detailed Lookup Method Injection section comes next.
