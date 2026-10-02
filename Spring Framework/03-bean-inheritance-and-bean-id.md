# Spring Framework -- Bean Inheritance, CLOB & Bean ID/Name

> **Topics:** CLOB file fetching with NamedParameterJdbcTemplate, Bean Inheritance (parent-child beans), Abstract beans, Bean ID vs Bean Name/Alias

## Complete Hinglish Notes

> Ye notes notebook ke topics ko structured + corrected form mein
> explain karte hain. Jahan notebook ka example conceptually sahi tha
> lekin syntax/behavior context-dependent tha, wahan Java/Spring ke
> correct form mein example diya gaya hai.

---

# PART A --- CLOB / File Fetching

# 1. Fetching a CLOB using `NamedParameterJdbcTemplate`

Agar database mein text data `CLOB` column mein stored hai, toh usse
`Clob` ke form mein fetch karke String mein convert kar sakte hain.

### SQL

```sql
SELECT * FROM insfile WHERE uname = :uname
```

### Example

```java
String Q =
    "SELECT * FROM insfile WHERE uname = :uname";

Map<String, Object> map = new HashMap<>();
map.put("uname", "AAA");

Clob clob = named.queryForObject(
    Q,
    map,
    (rs, rowNum) -> rs.getClob(2)
);

String s1 =
    clob.getSubString(1, (int) clob.length());

FileWriter fw =
    new FileWriter("D:\\output\\demo.java");

fw.write(s1);
fw.close();
```

### Important

`Clob` indexing generally 1-based hoti hai, isliye:

```java
clob.getSubString(1, (int) clob.length());
```ka use kiya gaya hai.

Agar query mein:

```sql
SELECT UFile FROM insfile ...
```sirf ek column select kiya hai, toh:

```java
rs.getClob("UFile")
```use karna zyada readable hai.

---

# 2. CLOB Fetch --- `BeanPropertySqlParameterSource`

Agar parameter object ke JavaBean property se lena hai, toh
`BeanPropertySqlParameterSource` use kar sakte hain.

### Model

```java
public class MyFile {

    private String uname;

    public String getUname() {
        return uname;
    }

    public void setUname(String uname) {
        this.uname = uname;
    }
}
```

### Main

```java
ApplicationContext ac =
    new ClassPathXmlApplicationContext("applicationContext.xml");

NamedParameterJdbcTemplate named =
    ac.getBean(NamedParameterJdbcTemplate.class);

MyFile file = new MyFile();
file.setUname("AAA");

String Q =
    "SELECT * FROM insfile WHERE uname = :uname";

SqlParameterSource source =
    new BeanPropertySqlParameterSource(file);

Clob clob = named.queryForObject(
    Q,
    source,
    (rs, rowNum) -> rs.getClob(2)
);

String s1 =
    clob.getSubString(1, (int) clob.length());

try (FileWriter fw =
         new FileWriter("D:\\output\\demo.java")) {

    fw.write(s1);
}
```

### Flow

```text
JavaBean
   ↓
BeanPropertySqlParameterSource
   ↓
:uname
   ↓
NamedParameterJdbcTemplate
   ↓
Database
   ↓
CLOB
   ↓
String
   ↓
FileWriter
```

---

# PART B --- Bean Inheritance

# 3. What is Bean Inheritance?

Spring Bean Inheritance ek mechanism hai jisme ek **child bean**, kisi
**parent bean** ki configuration/properties ko inherit kar sakta hai.

Isse common configuration ko baar-baar repeat karne ki zarurat nahi
padti.

### Main Idea

```text
Parent Bean
   ↓
Common configuration
   ↓
Child Bean
   ↓
Inherited + overridden/additional properties
```

### Important

Spring Bean Inheritance ko **Java class inheritance** se confuse nahi
karna chahiye.

Ye:

```java
class Student extends Person
```jaisa Java inheritance nahi hai.

Ye mainly Spring XML bean-definition configuration reuse hai.

---

# 4. Bean Inheritance --- Key Concepts

Bean inheritance ka use:

1.  Common configuration reuse karne ke liye.
2.  Repeated XML configuration avoid karne ke liye.
3.  Parent bean mein common properties define karne ke liye.
4.  Child bean mein inherited properties ko override/add karne ke liye.
5.  XML configuration ko cleaner rakhne ke liye.

### Important

Child bean:

-   parent ki properties inherit kar sakta hai;
-   apni additional properties define kar sakta hai;
-   inherited property ko override kar sakta hai.

---

# 5. Abstract Parent Bean

Parent bean ko sirf configuration template ki tarah use karna ho toh:

```xml
abstract="true"
```use kar sakte hain.

### Example

```xml
<bean id="S1"
      class="pack1.Student"
      abstract="true">

    <property name="id" value="101"/>
</bean>
```Ab:

```xml
<bean id="S2"
      class="pack1.Student"
      parent="S1">

    <property name="name" value="BBB"/>
</bean>
```Yahaan `S2` ko `S1` se:

```text
id = 101
```inherit hoga.

Aur `S2` mein:

```text
name = BBB
```add ho gaya.

---

# 6. Abstract Bean ko Directly Get Karna

Agar:

```xml
<bean id="S1"
      class="pack1.Student"
      abstract="true">
```hai, toh `S1` ko normal bean instance ki tarah directly retrieve nahi
karna chahiye.

Example:

```java
Student s =
    ac.getBean("S1", Student.class);
```par Spring bean creation-related exception throw karega because `S1`
abstract bean definition hai.

### Correct Approach

Child bean:

```xml
<bean id="S2"
      class="pack1.Student"
      parent="S1">
```retrieve karo:

```java
Student s =
    ac.getBean("S2", Student.class);
```

---

# 7. Complete Bean Inheritance Example

## Student.java

```java
package pack1;

public class Student {

    private int userRoleNumber;
    private String userName;

    public int getUserRoleNumber() {
        return userRoleNumber;
    }

    public void setUserRoleNumber(int userRoleNumber) {
        this.userRoleNumber = userRoleNumber;
    }

    public String getUserName() {
        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    @Override
    public String toString() {
        return userRoleNumber + " " + userName;
    }
}
```

## applicationContext.xml

```xml
<bean id="S1"
      class="pack1.Student"
      abstract="true">

    <property name="userRoleNumber" value="101"/>
    <property name="userName" value="AAA"/>
</bean>

<bean id="S2"
      class="pack1.Student"
      parent="S1">

    <property name="userName" value="BBB"/>
</bean>
```

### Result

`S2` ko:

```text
userRoleNumber = 101
userName       = BBB
```milega.

Because:

```text
S1:
userRoleNumber = 101
userName       = AAA

        ↓ inherit

S2:
userRoleNumber = 101
userName       = BBB
```Child ne `userName` override kar diya.

---

# 8. Parent Bean ko Abstract Rakhna Compulsory Hai?

**Nahi.**

Parent bean ko:

```xml
abstract="true"
```rakhna compulsory nahi hai.

Agar parent bean abstract nahi hai, toh:

-   parent khud bhi normal bean ke roop mein instantiate ho sakta hai;
-   child uski configuration inherit kar sakta hai.

Agar parent ka purpose sirf configuration template hai, toh
`abstract="true"` useful hai.

---

# PART C --- Bean Inheritance with Employee

# 9. Employee.java

```java
package pack1;

public class Employee {

    private int userRoleNumber;
    private String userName;
    private double salary;

    public int getUserRoleNumber() {
        return userRoleNumber;
    }

    public void setUserRoleNumber(int userRoleNumber) {
        this.userRoleNumber = userRoleNumber;
    }

    public String getUserName() {
        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    public double getSalary() {
        return salary;
    }

    public void setSalary(double salary) {
        this.salary = salary;
    }

    @Override
    public String toString() {
        return userRoleNumber + " " +
               userName + " " +
               salary;
    }
}
```

---

# 10. Student.java

```java
package pack1;

public class Student {

    private int userRoleNumber;
    private String userName;

    public int getUserRoleNumber() {
        return userRoleNumber;
    }

    public void setUserRoleNumber(int userRoleNumber) {
        this.userRoleNumber = userRoleNumber;
    }

    public String getUserName() {
        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    @Override
    public String toString() {
        return userRoleNumber + " " + userName;
    }
}
```

---

# 11. Employee Inherits Configuration from Student Bean

```xml
<bean id="S1"
      class="pack1.Student">

    <property name="userRoleNumber" value="101"/>
    <property name="userName" value="AAA"/>
</bean>

<bean id="E1"
      class="pack1.Employee"
      parent="S1">

    <property name="salary" value="50000"/>
</bean>
```

### Important Concept

Yahaan:

```text
S1 class = Student
E1 class = Employee
```different classes hain.

Phir bhi E1:

```xml
parent="S1"
```ke through S1 ki **bean configuration properties** inherit kar sakta
hai, provided the child bean's class supports those inherited
properties.

So E1 ko:

```text
userRoleNumber = 101
userName       = AAA
salary         = 50000
```milega.

> Ye Java inheritance nahi hai. `Employee extends Student` likhna
> required nahi hai. Spring bean-definition inheritance configuration
> inheritance hai.

---

# 12. Main --- Print Employee Bean

```java
ApplicationContext ac =
    new ClassPathXmlApplicationContext(
        "applicationContext.xml"
    );

Employee e =
    ac.getBean("E1", Employee.class);

System.out.println(e.getUserRoleNumber());
System.out.println(e.getUserName());
System.out.println(e.getSalary());
```

### Output

```text
101
AAA
50000.0
```

---

# PART D --- Bean ID and Bean Name

# 13. Multiple Names for One Bean

Spring mein ek bean ko multiple names/aliases diye ja sakte hain.

Example:

```xml
<bean id="S1"
      name="S2"
      class="pack1.Student">

    <property name="userName" value="AAA"/>
</bean>
```Conceptually:

```text
S1 ─────┐
        ├── same Student bean
S2 ─────┘
```Ab:

```java
Student s1 =
    ac.getBean("S1", Student.class);

Student s2 =
    ac.getBean("S2", Student.class);
```Dono same underlying bean instance ko refer karte hain in the default
singleton scope.

```java
System.out.println(s1 == s2);
```Output:

```text
true
```

---

# 14. `id` vs `name`

## Bean ID

```xml
<bean id="S1" class="pack1.Student"/>
```

`id` bean ka primary identifier hota hai.

## Bean Name / Alias

```xml
<bean id="S1"
      name="S2,S3"
      class="pack1.Student"/>
```

`S2` aur `S3` additional names/aliases ke roop mein use kiye ja sakte
hain.

### Easy Example

```text
id = S1

aliases:
S2
S3
```Access:

```java
ac.getBean("S1");
ac.getBean("S2");
ac.getBean("S3");
```

### Difference

  -----------------------------------------------------------------------
  `id`                                `name`
  ----------------------------------- -----------------------------------
  Primary bean identifier             Additional bean names/aliases
                                      define kar sakta hai

  XML bean identity ke liye commonly  Multiple names provide karne ke
  used                                liye useful

  Ek bean ka main ID                  Multiple aliases ho sakte hain
  -----------------------------------------------------------------------

> Modern Spring applications mein `@Bean`/component names and aliases
> bhi commonly use hote hain, lekin XML notes ke liye `id` + `name`
> concept important hai.

---
