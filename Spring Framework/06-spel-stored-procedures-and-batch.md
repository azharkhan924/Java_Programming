# Spring Framework -- SpEL, Stored Procedures & Batch Processing

> **Topics:** Stored Procedures (IN/OUT/INOUT), SimpleJdbcCall, CallableStatement, Statement types, `batchUpdate()`, `BatchPreparedStatementSetter`, Named Parameter batch, Complete CRUD project, SpEL (Spring Expression Language), SpEL operators, type access, bean references

---

# 21. Stored Procedure

**Stored Procedure** database ke andar stored SQL statements ka reusable
block/program hota hai.

Isse database mein create karke application se baar-baar call kiya ja
sakta hai.

### Advantages

-   Reusable SQL logic
-   Database-side centralization
-   Complex operations encapsulate kar sakte hain
-   Application code simpler ho sakta hai
-   Database-level permissions/control possible
-   Appropriate use cases mein network round trips reduce ho sakte hain

---

# 22. MySQL Stored Procedure Syntax

MySQL mein `DELIMITER` use karke procedure define kar sakte hain:

```sql
DELIMITER //

CREATE PROCEDURE procedure_name()
BEGIN

    -- SQL statements

END //

DELIMITER ;
```

`DELIMITER` MySQL client ko batata hai ki procedure definition ka end
normal `;` ke bajay kisi aur delimiter, jaise `//`, se identify karna
hai.

---

# 23. Types of Stored Procedure Parameters

## 23.1 IN Parameter

Input value procedure ke andar jaati hai.

```sql
DELIMITER //

CREATE PROCEDURE getStudent(
    IN p_urno VARCHAR(30)
)
BEGIN

    SELECT *
    FROM marks
    WHERE urno = p_urno;

END //

DELIMITER ;
```

---

## 23.2 OUT Parameter

Procedure result ko output parameter ke through bahar provide karta hai.

```sql
DELIMITER //

CREATE PROCEDURE getStudentName(
    IN p_urno VARCHAR(30),
    OUT p_name VARCHAR(30)
)
BEGIN

    SELECT uname
    INTO p_name
    FROM marks
    WHERE urno = p_urno;

END //

DELIMITER ;
```

---

## 23.3 INOUT Parameter

Input bhi deta hai aur procedure us value ko modify karke output bhi kar
sakta hai.

```sql
DELIMITER //

CREATE PROCEDURE updateMarks(
    INOUT p_marks INT
)
BEGIN

    SET p_marks = p_marks + 10;

END //

DELIMITER ;
```

---

# 24. Calling Stored Procedure with SimpleJdbcCall

Spring ka:

```java
SimpleJdbcCall
```stored procedure/function calls ko simplify karne ke liye use kiya ja
sakta hai.

Example:

```java
ApplicationContext app =
    new ClassPathXmlApplicationContext(
        "configs/ApplicationContext.xml");

DriverManagerDataSource dataSource =
    (DriverManagerDataSource)
        app.getBean("dataSource");

SimpleJdbcCall simpleJdbcCall =
    new SimpleJdbcCall(dataSource)
        .withProcedureName("insertStudent");

Map<String, Object> map =
    new HashMap<>();

map.put("N1", 1000);
map.put("N2", "AAA");
map.put("N3", 30);
map.put("N4", 40);
map.put("N5", 50);

simpleJdbcCall.execute(map);

System.out.println("Inserted");
```

---

# 25. DriverManagerDataSource Bean for SimpleJdbcCall

```xml
<bean id="dataSource"
      class="org.springframework.jdbc.datasource.DriverManagerDataSource">

    <property name="driverClassName"
              value="com.mysql.cj.jdbc.Driver"/>

    <property name="url"
              value="jdbc:mysql://localhost/springdb"/>

    <property name="username"
              value="root"/>

    <property name="password"
              value="root"/>

</bean>
```

---

# 26. Statement Types

JDBC mein commonly teen statement types discuss kiye jaate hain:

```text
Statement
PreparedStatement
CallableStatement
```

---

## 26.1 Statement

Static SQL execute karne ke liye.

```java
Statement st =
    connection.createStatement();

st.executeUpdate(
    "INSERT INTO student VALUES (101, 'Azhar')"
);
```

---

## 26.2 PreparedStatement

Parameterized SQL ke liye.

```java
PreparedStatement ps =
    connection.prepareStatement(
        "INSERT INTO student VALUES (?, ?)"
    );

ps.setInt(1, 101);
ps.setString(2, "Azhar");

ps.executeUpdate();
```

### Benefits

-   Parameters separately bind hote hain
-   Repeated execution ke liye useful
-   Proper parameter binding SQL injection risk ko reduce karne mein
    help karti hai

---

## 26.3 CallableStatement

Stored procedure/function ko call karne ke liye.

```java
CallableStatement cs =
    connection.prepareCall(
        "{call insertStudent(?, ?, ?, ?, ?)}"
    );

cs.setInt(1, 1000);
cs.setString(2, "AAA");
cs.setInt(3, 30);
cs.setInt(4, 40);
cs.setInt(5, 50);

cs.execute();
```

---

# 27. Student Model for Stored Procedure

```java
public class Student {

    private String urno;
    private String uname;
    private String physics;
    private String chemistry;
    private String maths;

    public String getUrno() {
        return urno;
    }

    public void setUrno(String urno) {
        this.urno = urno;
    }

    public String getUname() {
        return uname;
    }

    public void setUname(String uname) {
        this.uname = uname;
    }

    public String getPhysics() {
        return physics;
    }

    public void setPhysics(String physics) {
        this.physics = physics;
    }

    public String getChemistry() {
        return chemistry;
    }

    public void setChemistry(String chemistry) {
        this.chemistry = chemistry;
    }

    public String getMaths() {
        return maths;
    }

    public void setMaths(String maths) {
        this.maths = maths;
    }
}
```

---

# 28. Stored Procedure for Student Insertion

```sql
DELIMITER //

CREATE PROCEDURE insertStudent(
    IN p_urno VARCHAR(30),
    IN p_uname VARCHAR(30),
    IN p_physics VARCHAR(30),
    IN p_chemistry VARCHAR(30),
    IN p_maths VARCHAR(30)
)
BEGIN

    INSERT INTO marks
    VALUES (
        p_urno,
        p_uname,
        p_physics,
        p_chemistry,
        p_maths
    );

END //

DELIMITER ;
```

---

# 29. BeanPropertySqlParameterSource

`BeanPropertySqlParameterSource` JavaBean ki properties ko named SQL
parameters se bind karne mein help karta hai.

Example:

```java
Student student = new Student();

student.setUrno("101");
student.setUname("Azhar");
student.setPhysics("90");
student.setChemistry("85");
student.setMaths("88");

SqlParameterSource source =
    new BeanPropertySqlParameterSource(student);
```Ab query ke named parameters bean properties ke names se match karne
chahiye:

```sql
INSERT INTO marks
(urno, uname, physics, chemistry, maths)
VALUES
(:urno, :uname, :physics, :chemistry, :maths)
```

---

# 30. SimpleJdbcCall + BeanPropertySqlParameterSource

```java
SimpleJdbcCall call =
    new SimpleJdbcCall(dataSource)
        .withProcedureName("insertStudent");

SqlParameterSource source =
    new BeanPropertySqlParameterSource(student);

Map<String, Object> out =
    call.execute(source);

System.out.println(out);
```

### Flow

```text
Student Object
      |
      v
BeanPropertySqlParameterSource
      |
      v
Named Parameters
      |
      v
SimpleJdbcCall
      |
      v
Stored Procedure
      |
      v
Database
```

---

# 31. CallableStatement Through JdbcTemplate

`JdbcTemplate` ke `call()` method ke through low-level
`CallableStatement` creation/control bhi kiya ja sakta hai.

Basic form:

```java
Map<String, Object> result =
    jdbcTemplate.call(
        callableStatementCreator,
        declaredParameters
    );
```

`CallableStatementCreator` ko lambda se create kar sakte hain:

```java
CallableStatementCreator creator =
    connection -> {

        CallableStatement call =
            connection.prepareCall(
                "{call selectStudent()}"
            );

        return call;
    };
```

---

# 32. Fetching Data Using CallableStatement

Suppose MySQL procedure:

```sql
DELIMITER //

CREATE PROCEDURE selectStudent()
BEGIN

    SELECT *
    FROM ins_marks;

END //

DELIMITER ;
```Spring `JdbcTemplate.call()` mein ResultSet ko retrieve karne ke liye
`SqlReturnResultSet` declare karna useful hai.

Example:

```java
ApplicationContext app =
    new ClassPathXmlApplicationContext(
        "applicationContext.xml");

DriverManagerDataSource dataSource =
    (DriverManagerDataSource)
        app.getBean("dataSource");

JdbcTemplate jdbcTemplate =
    new JdbcTemplate(dataSource);

List<SqlParameter> params =
    new ArrayList<>();

params.add(
    new SqlReturnResultSet(
        "students",
        (rs, rowNum) -> {

            Student s = new Student();

            s.setUrno(rs.getString("urno"));
            s.setUname(rs.getString("uname"));
            s.setPhysics(rs.getString("physics"));
            s.setChemistry(rs.getString("chemistry"));
            s.setMaths(rs.getString("maths"));

            return s;
        }
    )
);

Map<String, Object> result =
    jdbcTemplate.call(
        connection -> {

            CallableStatement call =
                connection.prepareCall(
                    "{call selectStudent()}"
                );

            return call;
        },
        params
    );

System.out.println(result);
```

### Important

`prepareCall()`:

```java
connection.prepareCall(...)
```

`CallableStatement` object create karta hai.

---

# 33. CallableStatement with IN + OUT Parameter

Suppose procedure:

```sql
DELIMITER //

CREATE PROCEDURE selectStudent3(
    IN p_urno VARCHAR(30),
    OUT p_name VARCHAR(30)
)
BEGIN

    SELECT uname
    INTO p_name
    FROM ins_marks
    WHERE urno = p_urno;

END //

DELIMITER ;
```Spring:

```java
ApplicationContext app =
    new ClassPathXmlApplicationContext(
        "applicationContext.xml");

DriverManagerDataSource dataSource =
    (DriverManagerDataSource)
        app.getBean("dataSource");

JdbcTemplate jdbcTemplate =
    new JdbcTemplate(dataSource);

List<SqlParameter> params =
    new ArrayList<>();

params.add(
    new SqlParameter(
        "N1",
        Types.VARCHAR
    )
);

params.add(
    new SqlOutParameter(
        "N2",
        Types.VARCHAR
    )
);

Map<String, Object> result =
    jdbcTemplate.call(

        connection -> {

            CallableStatement call =
                connection.prepareCall(
                    "{call selectStudent3(?, ?)}"
                );

            call.setString(1, "101");

            call.registerOutParameter(
                2,
                Types.VARCHAR
            );

            return call;
        },

        params
    );

System.out.println(result);
```

### Flow

```text
N1 → IN parameter → 101
N2 → OUT parameter → Student name
```

---

# 34. CallableStatement --- Important Methods

```java
connection.prepareCall(...)
```

→ `CallableStatement` create karta hai.

```java
call.setString(1, "101");
```

→ IN parameter set karta hai.

```java
call.registerOutParameter(
    2,
    Types.VARCHAR
);
```

→ OUT parameter register karta hai.

```java
call.execute();
```

→ Procedure execute karta hai.

---

# 35. JdbcTemplate `batchUpdate()` --- Positional Parameters

Agar multiple rows insert karni hain, one-by-one `update()` call karne
ki jagah `batchUpdate()` use kar sakte hain.

Query:

```java
String q =
    "INSERT INTO ins_marks " +
    "VALUES (?, ?, ?, ?, ?)";
```Multiple argument arrays:

```java
List<Object[]> list =
    new ArrayList<>();

list.add(
    new Object[] {
        "101",
        "AAA",
        "30",
        "40",
        "50"
    }
);

list.add(
    new Object[] {
        "102",
        "BBB",
        "35",
        "45",
        "55"
    }
);

list.add(
    new Object[] {
        "103",
        "CCC",
        "40",
        "50",
        "60"
    }
);
```Execute:

```java
int[] x =
    jdbcTemplate.batchUpdate(
        q,
        list
    );

for (int i : x) {
    System.out.println(i);
}
```

### Concept

```text
SQL Query
   +
List<Object[]>
   ↓
batchUpdate()
   ↓
Multiple rows
```Each `Object[]` ek row ke positional parameter values represent karta
hai.

---

# 36. Batch Update Using Student Objects

Student class:

```java
public class Student {

    private String urno;
    private String uname;
    private String physics;
    private String chemistry;
    private String maths;

    // constructors
    // getters
    // setters
}
```Students:

```java
List<Student> list =
    new ArrayList<>();

list.add(
    new Student(
        "101",
        "CCC",
        "10",
        "20",
        "30"
    )
);

list.add(
    new Student(
        "102",
        "BBB",
        "15",
        "25",
        "35"
    )
);

list.add(
    new Student(
        "103",
        "AAA",
        "20",
        "30",
        "40"
    )
);
```Now convert each Student into an `Object[]`:

```java
List<Object[]> list2 =
    new ArrayList<>();

for (int i = 0; i < list.size(); i++) {

    Student s = list.get(i);

    list2.add(
        new Object[] {
            s.getUrno(),
            s.getUname(),
            s.getPhysics(),
            s.getChemistry(),
            s.getMaths()
        }
    );
}
```Execute:

```java
String q =
    "INSERT INTO ins_marks " +
    "VALUES (?, ?, ?, ?, ?)";

int[] x =
    jdbcTemplate.batchUpdate(
        q,
        list2
    );

for (int i : x) {
    System.out.println(i);
}
```

### Concept

```text
List<Student>
     ↓
Convert Student → Object[]
     ↓
List<Object[]>
     ↓
batchUpdate()
     ↓
Database
```

---

# 37. Batch Update Using `BatchPreparedStatementSetter`

Large batches ke liye Spring ka `BatchPreparedStatementSetter` bhi use
kar sakte hain.

```java
String q =
    "INSERT INTO ins_marks " +
    "VALUES (?, ?, ?, ?, ?)";

int[] result =
    jdbcTemplate.batchUpdate(
        q,
        list,
        list.size(),
        (ps, student) -> {

            ps.setString(1, student.getUrno());
            ps.setString(2, student.getUname());
            ps.setString(3, student.getPhysics());
            ps.setString(4, student.getChemistry());
            ps.setString(5, student.getMaths());
        }
    );
```

> API signatures version ke according overloaded ho sakte hain; the
> `BatchPreparedStatementSetter` form is useful when values are already
> held as domain objects.

---

# 38. Named Parameter Batch Update --- Map

`NamedParameterJdbcTemplate` ke saath multiple named-parameter rows ke
liye `SqlParameterSource[]` use karna convenient hai.

```java
String q =
    "INSERT INTO ins_marks " +
    "(urno, uname, physics, chemistry, maths) " +
    "VALUES (:urno, :uname, :physics, :chemistry, :maths)";
```Create maps:

```java
Map<String, Object> m1 =
    new HashMap<>();

m1.put("urno", "101");
m1.put("uname", "CCC");
m1.put("physics", "10");
m1.put("chemistry", "20");
m1.put("maths", "30");

Map<String, Object> m2 =
    new HashMap<>();

m2.put("urno", "102");
m2.put("uname", "BBB");
m2.put("physics", "15");
m2.put("chemistry", "25");
m2.put("maths", "35");

Map<String, Object> m3 =
    new HashMap<>();

m3.put("urno", "103");
m3.put("uname", "AAA");
m3.put("physics", "20");
m3.put("chemistry", "30");
m3.put("maths", "40");
```Convert:

```java
SqlParameterSource[] batch = {

    new MapSqlParameterSource(m1),
    new MapSqlParameterSource(m2),
    new MapSqlParameterSource(m3)
};
```Execute:

```java
NamedParameterJdbcTemplate namedJdbcTemplate =
    new NamedParameterJdbcTemplate(dataSource);

int[] result =
    namedJdbcTemplate.batchUpdate(
        q,
        batch
    );

for (int i : result) {
    System.out.println(i);
}
```

---

# 39. Named Parameter Batch Update --- `BeanPropertySqlParameterSource`

Agar data `Student` objects mein already hai, then
`BeanPropertySqlParameterSource` aur cleaner approach provide karta hai.

```java
List<Student> students =
    new ArrayList<>();

students.add(
    new Student("101", "CCC", "10", "20", "30")
);

students.add(
    new Student("102", "BBB", "15", "25", "35")
);

students.add(
    new Student("103", "AAA", "20", "30", "40")
);
```Create parameter sources:

```java
SqlParameterSource[] batch =
    new SqlParameterSource[students.size()];

for (int i = 0; i < students.size(); i++) {

    batch[i] =
        new BeanPropertySqlParameterSource(
            students.get(i)
        );
}
```Query:

```java
String q =
    "INSERT INTO ins_marks " +
    "(urno, uname, physics, chemistry, maths) " +
    "VALUES (:urno, :uname, :physics, :chemistry, :maths)";
```Execute:

```java
NamedParameterJdbcTemplate namedJdbcTemplate =
    new NamedParameterJdbcTemplate(dataSource);

int[] result =
    namedJdbcTemplate.batchUpdate(
        q,
        batch
    );
```

### Main idea

```text
Student object
     ↓
BeanPropertySqlParameterSource
     ↓
Named parameters
     ↓
NamedParameterJdbcTemplate
     ↓
batchUpdate()
```

---

# 40. Complete Named Parameter Insert with Student Bean

```java
Student s1 =
    new Student();

s1.setUrno("101");
s1.setUname("Azhar");
s1.setPhysics("90");
s1.setChemistry("85");
s1.setMaths("88");

NamedParameterJdbcTemplate j =
    new NamedParameterJdbcTemplate(dataSource);

String q =
    "INSERT INTO ins_marks " +
    "(urno, uname, physics, chemistry, maths) " +
    "VALUES (:urno, :uname, :physics, :chemistry, :maths)";

SqlParameterSource source =
    new BeanPropertySqlParameterSource(s1);

int x =
    j.update(q, source);

if (x > 0) {
    System.out.println("Inserted");
}
```

---

# 41. Complete CRUD Project Structure

```text
src
│
├── config
│   └── applicationContext.xml
│
├── controller
│   ├── StudentController.java
│   └── StudentControllerImpl.java
│
├── dao
│   ├── StudentDAO.java
│   └── StudentDAOImpl.java
│
├── maindemo
│   └── MainDemo.java
│
├── mydata
│   └── Student.java
│
└── service
    ├── StudentService.java
    └── StudentServiceImpl.java
```

---

# 42. Student.java

```java
package mydata;

public class Student {

    private String userRollNumber;
    private String userName;
    private String userPhysics;
    private String userChemistry;
    private String userMaths;

    public Student() {
    }

    public Student(
            String userRollNumber,
            String userName,
            String userPhysics,
            String userChemistry,
            String userMaths) {

        this.userRollNumber = userRollNumber;
        this.userName = userName;
        this.userPhysics = userPhysics;
        this.userChemistry = userChemistry;
        this.userMaths = userMaths;
    }

    public String getUserRollNumber() {
        return userRollNumber;
    }

    public void setUserRollNumber(String userRollNumber) {
        this.userRollNumber = userRollNumber;
    }

    public String getUserName() {
        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    public String getUserPhysics() {
        return userPhysics;
    }

    public void setUserPhysics(String userPhysics) {
        this.userPhysics = userPhysics;
    }

    public String getUserChemistry() {
        return userChemistry;
    }

    public void setUserChemistry(String userChemistry) {
        this.userChemistry = userChemistry;
    }

    public String getUserMaths() {
        return userMaths;
    }

    public void setUserMaths(String userMaths) {
        this.userMaths = userMaths;
    }

    @Override
    public String toString() {
        return "Student{" +
                "userRollNumber='" + userRollNumber + '\'' +
                ", userName='" + userName + '\'' +
                ", userPhysics='" + userPhysics + '\'' +
                ", userChemistry='" + userChemistry + '\'' +
                ", userMaths='" + userMaths + '\'' +
                '}';
    }
}
```

---

# 43. StudentDAO

```java
package dao;

import mydata.Student;

public interface StudentDAO {

    Student searchStudentDAO(String userRollNumber);

    String addStudentDAO(Student s);

    String updateStudentDAO(Student s);

    String deleteStudentDAO(String userRollNumber);
}
```

---

# 44. StudentDAOImpl

DataSource aur JdbcTemplate ko globally declare kar sakte hain:

```java
private DriverManagerDataSource dataSource;
private JdbcTemplate jdbcTemplate;

public void setDataSource(
        DriverManagerDataSource dataSource) {

    this.dataSource = dataSource;
}

public void setJdbcTemplate(
        JdbcTemplate jdbcTemplate) {

    this.jdbcTemplate = jdbcTemplate;
}
```Isse har method mein repeatedly DataSource/JdbcTemplate create nahi
karna padega.

---

## Add

```java
@Override
public String addStudentDAO(Student s) {

    String q =
        "INSERT INTO ins_marks " +
        "VALUES (?, ?, ?, ?, ?)";

    Object[] values = {

        s.getUserRollNumber(),
        s.getUserName(),
        s.getUserPhysics(),
        s.getUserChemistry(),
        s.getUserMaths()
    };

    int rowCount =
        jdbcTemplate.update(q, values);

    String status;

    if (rowCount > 0) {
        status = "success";
    } else {
        status = "failure";
    }

    return status;
}
```

---

## Delete

```java
@Override
public String deleteStudentDAO(
        String userRollNumber) {

    Student student =
        searchStudentDAO(userRollNumber);

    if (student == null) {
        return "Data not exist";
    }

    String q =
        "DELETE FROM ins_marks " +
        "WHERE userRollNumber = ?";

    int rowCount =
        jdbcTemplate.update(
            q,
            userRollNumber
        );

    if (rowCount > 0) {
        return "success";
    } else {
        return "failure";
    }
}
```

> Actual database column name exactly table schema ke according hona
> chahiye. Agar column `urno` hai, query mein `urno` use karein.

---

# 45. StudentService

```java
package service;

import mydata.Student;

public interface StudentService {

    Student searchStudentService(
        String userRollNumber
    );

    String addStudentService(Student s);

    String updateStudentService(Student s);

    String deleteStudentService(
        String userRollNumber
    );
}
```

---

# 46. StudentServiceImpl

```java
package service;

import dao.StudentDAO;
import mydata.Student;

public class StudentServiceImpl
        implements StudentService {

    private StudentDAO studentDAO;

    public void setStudentDAO(
            StudentDAO studentDAO) {

        this.studentDAO = studentDAO;
    }

    @Override
    public String addStudentService(Student s) {

        String status =
            studentDAO.addStudentDAO(s);

        return status;
    }

    @Override
    public Student searchStudentService(
            String userRollNumber) {

        return studentDAO.searchStudentDAO(
            userRollNumber
        );
    }

    @Override
    public String updateStudentService(Student s) {

        return studentDAO.updateStudentDAO(s);
    }

    @Override
    public String deleteStudentService(
            String userRollNumber) {

        return studentDAO.deleteStudentDAO(
            userRollNumber
        );
    }
}
```

---

# 47. StudentController Interface

```java
package controller;

public interface StudentController {

    void searchStudentController();

    void addStudentController();

    void updateStudentController();

    void deleteStudentController();
}
```

---

# 48. StudentControllerImpl

```java
package controller;

import mydata.Student;
import service.StudentService;

public class StudentControllerImpl
        implements StudentController {

    private StudentService studentService;

    public void setStudentService(
            StudentService studentService) {

        this.studentService = studentService;
    }

    @Override
    public void addStudentController() {

        Student s = new Student();

        s.setUserRollNumber("101");
        s.setUserName("Azhar");
        s.setUserPhysics("90");
        s.setUserChemistry("85");
        s.setUserMaths("88");

        String status =
            studentService.addStudentService(s);

        if (status.equals("success")) {
            System.out.println("Success");
        } else {
            System.out.println(status);
        }
    }

    @Override
    public void searchStudentController() {

        String srno = "101";

        Student student =
            studentService.searchStudentService(srno);

        if (student != null) {

            System.out.println(
                "User Roll Number : "
                + student.getUserRollNumber()
            );

            System.out.println(
                "User Name : "
                + student.getUserName()
            );

            System.out.println(
                "Physics : "
                + student.getUserPhysics()
            );

            System.out.println(
                "Chemistry : "
                + student.getUserChemistry()
            );

            System.out.println(
                "Maths : "
                + student.getUserMaths()
            );

        } else {

            System.out.println("Data not exist");
        }
    }

    @Override
    public void updateStudentController() {

        Student s = new Student();

        s.setUserRollNumber("101");
        s.setUserName("Updated Name");
        s.setUserPhysics("95");
        s.setUserChemistry("92");
        s.setUserMaths("94");

        String status =
            studentService.updateStudentService(s);

        if (status.equals("success")) {
            System.out.println("Success");
        } else {
            System.out.println(status);
        }
    }

    @Override
    public void deleteStudentController() {

        String srno = "101";

        String status =
            studentService.deleteStudentService(srno);

        if (status.equals("success")) {
            System.out.println("Success");
        } else {
            System.out.println(status);
        }
    }
}
```

---

# 49. applicationContext.xml --- Complete Project

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- DataSource -->

    <bean id="dataSource"
          class="org.springframework.jdbc.datasource.DriverManagerDataSource">

        <property name="driverClassName"
                  value="com.mysql.cj.jdbc.Driver"/>

        <property name="url"
                  value="jdbc:mysql://localhost/springdb"/>

        <property name="username"
                  value="root"/>

        <property name="password"
                  value="root"/>

    </bean>


    <!-- JdbcTemplate -->

    <bean id="jdbcTemplate"
          class="org.springframework.jdbc.core.JdbcTemplate">

        <constructor-arg ref="dataSource"/>

    </bean>


    <!-- DAO -->

    <bean id="studentDAO"
          class="dao.StudentDAOImpl">

        <property name="dataSource"
                  ref="dataSource"/>

        <property name="jdbcTemplate"
                  ref="jdbcTemplate"/>

    </bean>


    <!-- Service -->

    <bean id="studentService"
          class="service.StudentServiceImpl">

        <property name="studentDAO"
                  ref="studentDAO"/>

    </bean>


    <!-- Controller -->

    <bean id="studentController"
          class="controller.StudentControllerImpl">

        <property name="studentService"
                  ref="studentService"/>

    </bean>

</beans>
```

---

# 50. MainDemo --- Calling Four Methods

```java
package maindemo;

import controller.StudentController;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MainDemo {

    public static void main(String[] args) {

        ApplicationContext app =
            new ClassPathXmlApplicationContext(
                "config/applicationContext.xml"
            );

        StudentController controller =
            (StudentController)
                app.getBean("studentController");

        // Add
        controller.addStudentController();

        // Search
        controller.searchStudentController();

        // Update
        controller.updateStudentController();

        // Search after update
        controller.searchStudentController();

        // Delete
        controller.deleteStudentController();

        // Search after delete
        controller.searchStudentController();
    }
}
```

---

# 51. SpEL --- Spring Expression Language

**SpEL = Spring Expression Language**

SpEL Spring applications mein runtime expressions evaluate karne ke liye
use hoti hai.

Basic syntax:

```text
#{expression}
```Example:

```xml
value="#{10 + 20}"
```Output:

```text
30
```

---

# 52. SpEL Static Method Example

Student class:

```java
package pack1;

public class Student {

    public Student() {
    }

    public Student(String s) {
        System.out.println(s);
    }

    public static String show() {
        return "SWT";
    }
}
```XML:

```xml
<bean id="s1"
      class="pack1.Student">

    <constructor-arg
        value="#{T(pack1.Student).show()}"/>

</bean>
```Output:

```text
SWT
```

### Why `static`?

Class name ke through method directly access kar rahe hain:

```text
T(pack1.Student).show()
```Isliye `show()` static hona chahiye.

---

# 53. SpEL Instance Data Access

Suppose:

```java
public class Student {

    public String id = "101";

}
```Instance field ko access karne ke liye object/reference required hoga.

SpEL mein object create karke:

```xml
<constructor-arg
    value="#{new pack1.Student().id}"/>
```Yahan:

```text
new pack1.Student()
```new object create karta hai.

---

# 54. SpEL Bean Reference

Suppose bean:

```xml
<bean id="student"
      class="pack1.Student"/>
```Then another bean/expression can reference it:

```xml
<constructor-arg
    value="#{student.id}"/>
```Yahan `student` Spring bean ka reference hai.

---

# 55. SpEL Arithmetic Expressions

```xml
value="#{10 + 20}"
```Output:

```text
30
```More examples:

```xml
value="#{10 - 20}"
```Output:

```text
-10
```

```xml
value="#{10 * 20}"
```Output:

```text
200
```

```xml
value="#{20 / 10}"
```Output:

```text
2
```

---

# 56. SpEL Relational Operators

```xml
value="#{10 == 20}"
```Output:

```text
false
```

```xml
value="#{10 != 20}"
```Output:

```text
true
```

```xml
value="#{10 < 20}"
```Output:

```text
true
```

```xml
value="#{10 > 20}"
```Output:

```text
false
```

### XML Important Point

XML attribute mein raw `<` character problem create kar sakta hai.

Isliye safer form:

```xml
value="#{10 lt 20}"
```or XML escape:

```xml
value="#{10 &lt; 20}"
```Similarly:

```xml
value="#{10 gt 20}"
```or:

```xml
value="#{10 &gt; 20}"
```

---

# 57. SpEL Boolean Expressions

```xml
value="#{true && false}"
```Output:

```text
false
```

```xml
value="#{true || false}"
```Output:

```text
true
```

```xml
value="#{!false}"
```Output:

```text
true
```

```xml
value="#{!true}"
```Output:

```text
false
```

---

# 58. SpEL Type Operator `T()`

SpEL mein:

```text
T(TypeName)
```type/class ko refer karta hai.

Example:

```xml
value="#{T(pack1.Student)}"
```Yeh `pack1.Student` class/type ko refer karta hai.

---

# 59. SpEL `Math.max()`

```xml
value="#{T(java.lang.Math).max(10, 20)}"
```Output:

```text
20
```Important syntax:

```text
T(java.lang.Math).max(10,20)
```

---

# 60. SpEL `Byte.MAX_VALUE`

```xml
value="#{T(java.lang.Byte).MAX_VALUE}"
```Output:

```text
127
```Because Java `byte` ki maximum value:

```text
127
```

---

# 61. SpEL String Method

Suppose:

```java
String abc = "hello";
```SpEL mein instance method call conceptually:

```text
#{abc.toUpperCase()}
```Agar bean/reference ke context mein:

```xml
value="#{student.name.toUpperCase()}"
```to name ko uppercase mein convert kiya ja sakta hai, provided
`student.name` valid bean/property reference ho.

---

# 62. SpEL Quick Revision

  Expression                          Output
  ----------------------------------- ---------
  `#{10 + 20}`                        `30`
  `#{10 == 20}`                       `false`
  `#{10 != 20}`                       `true`
  `#{10 lt 20}`                       `true`
  `#{10 gt 20}`                       `false`
  `#{true && false}`                  `false`
  `#{true || false}`                  `true`
  `#{!false}`                         `true`
  `#{T(java.lang.Math).max(10,20)}`   `20`
  `#{T(java.lang.Byte).MAX_VALUE}`    `127`

---

# 63. JdbcTemplate --- Main Operations

```text
update()
query()
queryForObject()
queryForList()
batchUpdate()
call()
```

### `update()`

INSERT / UPDATE / DELETE

### `query()`

Multiple rows retrieve karna.

### `queryForObject()`

Single result/object retrieve karna.

### `batchUpdate()`

Multiple updates/inserts ko batch mein execute karna.

### `call()`

Stored procedure / CallableStatement based operations.

---

# 64. Stored Procedure + JdbcTemplate Flow

```text
Java Application
      |
      v
JdbcTemplate.call()
      |
      v
CallableStatementCreator
      |
      v
connection.prepareCall()
      |
      v
CallableStatement
      |
      v
Stored Procedure
      |
      v
MySQL Database
```

---

# 65. JdbcTemplate `call()` Basic Pattern

```java
List<SqlParameter> params =
    new ArrayList<>();

Map<String, Object> result =
    jdbcTemplate.call(

        connection -> {

            CallableStatement call =
                connection.prepareCall(
                    "{call selectStudent()}"
                );

            return call;
        },

        params
    );
```

### Important

```java
connection.prepareCall(...)
```

`CallableStatement` ka object create karta hai.

---

# 66. Complete Stored Procedure Examples --- Revision

### Insert

```sql
DELIMITER //

CREATE PROCEDURE insertStudent(
    IN p_urno VARCHAR(30),
    IN p_uname VARCHAR(30),
    IN p_physics VARCHAR(30),
    IN p_chemistry VARCHAR(30),
    IN p_maths VARCHAR(30)
)
BEGIN

    INSERT INTO marks
    VALUES (
        p_urno,
        p_uname,
        p_physics,
        p_chemistry,
        p_maths
    );

END //

DELIMITER ;
```

### Select

```sql
DELIMITER //

CREATE PROCEDURE selectStudent()
BEGIN

    SELECT *
    FROM ins_marks;

END //

DELIMITER ;
```

### Select with IN + OUT

```sql
DELIMITER //

CREATE PROCEDURE selectStudent3(
    IN p_urno VARCHAR(30),
    OUT p_name VARCHAR(30)
)
BEGIN

    SELECT uname
    INTO p_name
    FROM ins_marks
    WHERE urno = p_urno;

END //

DELIMITER ;
```

---

# 67. Final Concept Map

```text
                    SPRING
                       |
        +--------------+--------------+
        |              |              |
       MVC          Spring JDBC       SpEL
        |              |              |
   Controller      DataSource      #{...}
   Service         JdbcTemplate    T(...)
   DAO             RowMapper       Methods
   Model           update()        Operators
   View            query()
                   batchUpdate()
                   call()
                       |
                       v
                Stored Procedure
                       |
              +--------+--------+
              |        |        |
              IN      OUT     INOUT
```

---

# 68. JDBC Parameter Comparison

```text
Positional Parameters
        |
        v
        ?
        |
        v
JdbcTemplate
```

```text
Named Parameters
        |
        v
      :name
        |
        v
NamedParameterJdbcTemplate
```

```text
JavaBean Properties
        |
        v
BeanPropertySqlParameterSource
        |
        v
Named Parameters
```

---

# 69. Batch Processing Comparison

### Positional + Object\[\]

```java
jdbcTemplate.batchUpdate(
    sql,
    List<Object[]>
);
```

### Named + Map

```java
namedJdbcTemplate.batchUpdate(
    sql,
    SqlParameterSource[]
);
```

### Named + Student Bean

```java
new BeanPropertySqlParameterSource(student)
```Then:

```java
namedJdbcTemplate.batchUpdate(
    sql,
    batchSources
);
```

---

# 70. Interview Quick Revision

### MVC

-   Controller = request entry point
-   Service = business logic
-   DAO/Repository = database access
-   Model = application/domain data
-   View = presentation

### Spring

-   IoC container manages beans
-   `@Component` = generic component
-   `@Repository` = DAO
-   `@Service` = service
-   `@Controller` = MVC controller
-   `@Autowired` = dependency injection

### JDBC

-   `DriverManagerDataSource` = simple DataSource, no connection pool
-   `JdbcTemplate` = simplifies JDBC
-   `update()` = INSERT/UPDATE/DELETE
-   `query()` = SELECT
-   `RowMapper` = ResultSet row → Java object
-   `batchUpdate()` = multiple operations
-   `call()` = stored procedure/CallableStatement

### Parameters

```text
?name?          → positional placeholder: ?
:name            → named parameter
```

### Stored Procedure

```text
IN     → input
OUT    → output
INOUT  → input + output
```

### CallableStatement

```java
connection.prepareCall(...)
```

### SpEL

```text
#{...}
```Examples:

```text
#{10 + 20}                       → 30
#{10 == 20}                      → false
#{10 != 20}                      → true
#{true && false}                 → false
#{true || false}                 → true
#{!false}                        → true
#{T(java.lang.Math).max(10,20)}  → 20
#{T(java.lang.Byte).MAX_VALUE}   → 127
```
