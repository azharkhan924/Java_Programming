# Maven -- Build Tool & Dependency Management

> **Topics:** What is Maven, POM.xml, Maven lifecycle, Local/Central/Remote repositories, JAR/WAR/POM packaging, SNAPSHOT vs Release, Plugins, Scopes, Maven vs Gradle

---

# PART K --- Maven

# 55. Maven Kya Hai?

**Apache Maven** Java projects ke liye ek build automation aur
dependency management tool hai.

Maven ka use:

-   project build karne;
-   dependencies manage/download karne;
-   compile karne;
-   tests run karne;
-   JAR/WAR package banane;
-   plugins execute karne;
-   project information/configuration maintain karne ke liye hota hai.

Maven project ka central configuration file:

```text
pom.xml
```

---

# 56. POM

**POM = Project Object Model**

`pom.xml` ek XML file hai jisme project ki configuration define hoti
hai.

Common information:

```text
Group ID
Artifact ID
Version
Packaging
Dependencies
Plugins
Build configuration
Repositories
Properties
```Example:

```xml
<project>

    <modelVersion>4.0.0</modelVersion>

    <groupId>demo</groupId>

    <artifactId>maven-demo-1</artifactId>

    <version>1.0-SNAPSHOT</version>

    <dependencies>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>...</version>
        </dependency>

    </dependencies>

</project>
```

---

# 57. Maven Project Creation --- Basic Flow

IDE mein typical Maven project creation:

```text
File
 ↓
New
 ↓
Maven Project
 ↓
Next
 ↓
Choose Archetype
 ↓
Quickstart archetype
 ↓
Group Id = demo
 ↓
Artifact Id = maven-demo-1
 ↓
Finish
```Common Maven quickstart archetype historically uses coordinates such as:

```text
org.apache.maven.archetypes
maven-archetype-quickstart
```Exact archetype version IDE/Maven environment ke according change ho
sakta hai.

---

# 58. Typical Maven Directory Structure

```text
maven-demo-1/
│
├── pom.xml
│
└── src/
    ├── main/
    │   ├── java/
    │   │   └── demo/
    │   │       └── App.java
    │   │
    │   └── resources/
    │
    └── test/
        ├── java/
        └── resources/
```Maven convention-based structure use karta hai.

---

# 59. Maven Dependency Management

Traditional Java project mein:

```text
Download JAR
 ↓
Project Build Path
 ↓
Add JAR
```Maven mein:

```xml
<dependency>
    <groupId>...</groupId>
    <artifactId>...</artifactId>
    <version>...</version>
</dependency>
```Then Maven dependency resolve karta hai.

---

# 60. Does Maven Download JAR Every Time?

**Nahi.**

Maven normally pehle local repository check karta hai.

Flow:

```text
pom.xml
   ↓
Dependency required?
   ↓
Local Repository check
   ↓
Found?
 ┌───────┴────────┐
 YES              NO
 ↓                 ↓
Use local       Remote repository
                 ↓
               Download
                 ↓
             Local repository
                 ↓
               Use it
```Maven documentation states that Maven first attempts to use a local
copy; if it is not available, it downloads from a remote repository.
citeturn0search3turn0search10

---

# 61. Maven JAR Kab Dobara Download Kar Sakta Hai?

Normal **release dependency** ke case mein agar required artifact local
repository mein available hai, Maven generally us local copy ko reuse
karta hai.

Download ho sakta hai jab:

1.  Artifact local repository mein present nahi hai.
2.  Different version required hai.
3.  Local artifact/cache delete ho gaya.
4.  Repository/update policy ke according remote check required ho.
5.  `SNAPSHOT` dependency ka newer version available ho.
6.  User explicitly dependency update force kare.
7.  Repository/mirror configuration change ho.

Maven repository settings mein update policies such as `always`, `daily`
(default), `interval:X`, and `never` exist karte hain.
citeturn0search4

---

# 62. Local Repository

Maven ka local repository generally user home ke andar hota hai.

Linux/macOS:

```text
~/.m2/repository
```Windows:

```text
C:\Users\<username>\.m2\repository
```Yahaan downloaded artifacts store hote hain.

Example:

```text
.m2/
└── repository/
    └── org/
        └── springframework/
            └── spring-context/
                └── ...
```

---

# 63. Maven Repositories

Main concepts:

## 1. Local Repository

Developer ke machine par.

Purpose:

```text
Downloaded dependencies
Cached artifacts
Installed local projects
```

---

## 2. Central Repository

Maven Central ek major public repository hai jahan many Java artifacts
available hote hain.

Maven default repository configuration central repository ko use karti
hai. citeturn0search3

---

## 3. Remote Repository

Local machine ke bahar repository.

Examples:

```text
Maven Central
Company Nexus
Company Artifactory
Other configured repositories
```Remote repository se artifacts download kiye ja sakte hain aur
appropriate permissions ke saath deploy/upload bhi kiye ja sakte hain.
citeturn0search10

---

# 64. Repository Flow

```text
POM
 ↓
Local Repository
 ↓
Artifact available?
 ├── YES → use it
 │
 └── NO
      ↓
Remote Repository
      ↓
Download
      ↓
Local Repository
      ↓
Use dependency
```

---

# 65. Maven Dependency Coordinates

A dependency commonly identify hoti hai:

```text
groupId
artifactId
version
```Example:

```xml
<dependency>

    <groupId>org.springframework</groupId>

    <artifactId>spring-context</artifactId>

    <version>...</version>

</dependency>
```Conceptually:

```text
groupId     → organization/project group
artifactId  → library/project name
version     → required version
```

---

# 66. Maven Lifecycle

Maven has three built-in lifecycles:

```text
1. default
2. clean
3. site
```Official Maven lifecycle documentation defines separate phases for these
lifecycles. citeturn0search7

---

# 67. Default Lifecycle

Important phases:

```text
validate
initialize
generate-sources
process-sources
generate-resources
process-resources
compile
process-classes
generate-test-sources
process-test-sources
generate-test-resources
process-test-resources
test-compile
test
prepare-package
package
pre-integration-test
integration-test
post-integration-test
verify
install
deploy
```Beginner ke liye most important:

```text
validate
compile
test
package
install
deploy
```

---

# 68. `mvn compile`

Command:

```bash
mvn compile
```Source code compile karta hai.

Typical result:

```text
src/main/java
      ↓
compile
      ↓
target/classes
```

---

# 69. `mvn test`

```bash
mvn test
```Test source compile karke tests run karta hai.

```text
src/test/java
      ↓
test compile
      ↓
tests execute
```

---

# 70. `mvn package`

```bash
mvn package
```Project ko package karta hai.

Example:

```text
Java source
 ↓
compile
 ↓
test
 ↓
package
 ↓
target/maven-demo-1-1.0-SNAPSHOT.jar
```Packaging type ke according output ho sakta hai:

```text
JAR
WAR
EAR
```

---

# 71. `mvn install`

```bash
mvn install
```Important point:

`install` lifecycle mein `package` se pehle ki required phases bhi
execute hoti hain, then generated artifact ko local Maven repository
mein install karta hai.

Flow:

```text
compile
 ↓
test
 ↓
package
 ↓
install
 ↓
~/.m2/repository
```Agar project:

```text
groupId = demo
artifactId = maven-demo-1
version = 1.0-SNAPSHOT
```hai, toh artifact local repository ke corresponding coordinate path mein
install ho sakta hai.

---

# 72. `mvn clean`

```bash
mvn clean
```Previous build ke generated output ko remove karta hai.

Commonly:

```text
target/
```remove hota hai.

Important:

```text
clean ≠ local Maven repository delete
```

`mvn clean` normally project ka `target` output clean karta hai;
`.m2/repository` ko delete nahi karta.

---

# 73. `mvn clean package`

Common command:

```bash
mvn clean package
```Flow:

```text
clean
 ↓
compile
 ↓
test
 ↓
package
```Isse clean build package generate hota hai.

---

# 74. `mvn clean install`

```bash
mvn clean install
```Flow:

```text
clean
 ↓
compile
 ↓
test
 ↓
package
 ↓
install into local repository
```

---

# 75. Maven Site Lifecycle

Third lifecycle:

```text
site
```Related phases:

```text
pre-site
site
post-site
site-deploy
```Purpose:

Project documentation/site generate aur deploy karna.

---

# 76. Maven Lifecycle Rule

Agar command:

```bash
mvn package
```run karte ho, Maven sirf `package` phase nahi karta.

Woh `package` se pehle required phases ko sequence mein execute karta
hai.

Conceptually:

```text
validate
 ↓
...
 ↓
compile
 ↓
test
 ↓
package
```Isi tarah:

```bash
mvn install
```package tak ke required phases ke baad install phase execute karta hai.

---

# 77. Maven Packaging Types

## JAR

**Java ARchive**

Common Java library/application packaging.

```text
target/app.jar
```

---

## WAR

**Web Application Archive**

Traditional Java web applications ke liye.

```text
target/app.war
```Tomcat jaise servlet container mein deploy kiya ja sakta hai.

---

## POM

Packaging:

```xml
<packaging>pom</packaging>
```Parent/multi-module Maven projects mein commonly useful.

Is case mein project ka main artifact POM hota hai.

---

## EAR

**Enterprise Archive**

Java EE/Jakarta EE enterprise application packaging ke liye
historically/common enterprise deployment packaging.

```text
target/app.ear
```

---

# 78. `package` vs `install`

## `package`

Artifact banata hai:

```text
target/app.jar
```or:

```text
target/app.war
```

## `install`

Artifact banane ke baad usse **local repository** mein install bhi karta
hai.

```text
package
   ↓
target/app.jar

install
   ↓
target/app.jar
   +
~/.m2/repository/...
```Easy trick:

```text
package = package bana
install  = package bana + local repository mein daal
```

---

# 79. Maven Snapshot

Example:

```xml
<version>1.0-SNAPSHOT</version>
```

`SNAPSHOT` generally indicates a development version that may change
over time.

Example:

```text
1.0-SNAPSHOT
```Development phase mein repeatedly updated artifact represent kar sakta
hai.

Release:

```text
1.0
```generally fixed/released version ko represent karta hai.

---

# 80. SNAPSHOT Dependency

Suppose:

```text
Project A
depends on
Project B : 1.0-SNAPSHOT
```Project B ka newer snapshot remote repository mein available ho sakta
hai.

Maven snapshot metadata/update policy ke according remote repository ko
check/update kar sakta hai.

Maven documentation specifically notes that remote downloading can be
triggered for a SNAPSHOT when a newer snapshot is available.
citeturn0search10

---

# 81. Release vs SNAPSHOT

  Release                           SNAPSHOT
  --------------------------------- ------------------------------
  Stable/released version           Development version
  Example `1.0`                     Example `1.0-SNAPSHOT`
  Normally immutable expectation    Can change over time
  Consumers expect fixed artifact   Development updates possible

---

# 82. Maven Build Flow --- Complete

```text
Developer writes Java code
          ↓
        pom.xml
          ↓
   Maven reads project
          ↓
   Resolve dependencies
          ↓
 Local repository check
          ↓
If missing → remote repository
          ↓
 Download + cache locally
          ↓
       Compile
          ↓
         Test
          ↓
       Package
          ↓
     JAR/WAR/etc.
          ↓
        Install
          ↓
 Local Maven Repository
```

---

# 83. Maven and External JAR Files

Traditional:

```text
JAR download
 ↓
Build path
 ↓
Manual management
```Maven:

```text
pom.xml
 ↓
dependency coordinates
 ↓
Maven resolves dependency
 ↓
download if needed
 ↓
local repository
 ↓
classpath
```Benefits:

-   Manual JAR handling reduced.
-   Dependency versions centrally declared.
-   Transitive dependencies can be resolved.
-   Build reproducibility improved.
-   Standard project structure.
-   Plugins integrate with lifecycle.

---

# 84. Multiple JDK Versions Problem

Notebook mein point:

```text
Multiple JDKs installed
 ↓
Maven/IDE wrong JDK pick kar raha hai
 ↓
Compilation error
```Important:

Maven itself Java compiler nahi hai; Maven Java/JDK environment ko use
karta hai to run Maven and compile the project.

Check:

```bash
java -version
```and:

```bash
mvn -version
```

`mvn -version` se Maven ke saath use ho raha Java version bhi check kar
sakte ho.

### Better approach

Blindly saare JDK delete karna mandatory solution nahi hai.

Better:

```text
JAVA_HOME
PATH
IDE Project SDK
Maven JDK
```correctly configure karo.

Agar project Java 21 target karta hai, ensure Maven/IDE/compiler
configuration Java 21 ke saath consistent ho.

---

# 85. Maven Plugins

Maven ka actual build work plugins ke through hota hai.

Examples:

```text
maven-compiler-plugin
maven-surefire-plugin
maven-jar-plugin
maven-war-plugin
maven-clean-plugin
```Lifecycle phases plugins/goals ko execute karte hain.

Conceptually:

```text
mvn compile
   ↓
compiler plugin
   ↓
Java compilation
```

---

# 86. Maven Dependency Transitivity

Suppose:

```text
Your Project
    ↓
Spring Library
    ↓
Another required Library
```Maven dependency graph ko resolve karke required transitive dependencies
bhi la sakta hai, unless exclusions/scopes change that behavior.

Isliye manually har dependent JAR download karne ki need reduce hoti
hai.

---

# 87. Maven Scopes --- Basic

Common dependency scopes:

```text
compile
provided
runtime
test
system
import
```Most common beginner examples:

### compile

Default scope; compile and runtime classpath mein available.

### provided

Compile ke liye available, lekin runtime environment provide karega.

Example:

```text
Servlet API
```traditional servlet deployment context mein.

### runtime

Runtime ke liye needed, compile ke liye generally not needed.

### test

Sirf tests ke liye.

Example:

```xml
<scope>test</scope>
```

---

# 88. Maven and Gradle

  Maven                                      Gradle
  ------------------------------------------ ----------------------------------
  XML-based `pom.xml`                        Groovy/Kotlin DSL
  Convention-based                           Highly flexible
  Mature ecosystem                           Modern build tooling
  Standard lifecycle                         Task-oriented build model
  XML can be verbose                         Build scripts often more concise
  Strong dependency management               Strong dependency management
  Easy to understand for standard projects   Powerful customization
  Large enterprise usage                     Widely used modern projects

### Simple Difference

```text
Maven
→ XML + lifecycle + conventions

Gradle
→ DSL + tasks + highly programmable builds
```Neither is universally "better"; choice depends on project requirements,
team preference, ecosystem and build complexity.

---

# 89. Why Maven?

Main reasons:

### 1. Dependency Management

```text
No manual JAR downloading
```

### 2. Standard Project Structure

```text
src/main/java
src/main/resources
src/test/java
```

### 3. Build Automation

```text
compile
test
package
install
deploy
```

### 4. Reproducible Builds

Project dependencies/configuration POM mein declared hoti hain.

### 5. Plugins

Different build tasks plugins ke through automate kiye ja sakte hain.

### 6. Team Collaboration

Same project:

```text
Git clone
 ↓
pom.xml
 ↓
mvn build
```Team members dependency setup manually repeat nahi karte.

---

# 90. Maven `package` vs `install` vs `deploy`

```text
compile
  ↓
test
  ↓
package
  ↓
install
  ↓
deploy
```

### package

Artifact:

```text
target/
```mein generate.

### install

Artifact:

```text
local repository
```mein install.

### deploy

Artifact:

```text
remote repository
```mein publish/upload, provided repository credentials/configuration
permit it.

---

# 91. Maven Commands --- Quick Revision

```bash
mvn validate
```Project configuration validate.

```bash
mvn compile
```Main source compile.

```bash
mvn test
```Tests run.

```bash
mvn package
```JAR/WAR etc. package.

```bash
mvn install
```Artifact local repository mein install.

```bash
mvn clean
```Previous build output clean.

```bash
mvn clean package
```Clean + package.

```bash
mvn clean install
```Clean + build + test + package + local install.

```bash
mvn deploy
```Artifact remote repository mein deploy.

---

# 92. Maven Repository Quick Revision

```text
LOCAL
  ↓
Your computer
  ↓
~/.m2/repository
```

```text
CENTRAL
  ↓
Public Maven repository
```

```text
REMOTE
  ↓
Any configured external repository
  ↓
Company Nexus / Artifactory / Central etc.
```

---

# 93. Does `mvn clean` Delete Dependencies?

**No.**

```bash
mvn clean
```generally project ke generated build output ko clean karta hai:

```text
target/
```It does not normally delete:

```text
~/.m2/repository
```Agar `.m2/repository` manually delete kar doge, toh future build mein
Maven ko required dependencies dobara download karni pad sakti hain.

---

# 94. Complete Spring Transaction + Maven Mental Model

```text
Maven
 │
 ├── Downloads Spring/JDBC dependencies
 ├── Manages dependency versions
 ├── Compiles project
 ├── Runs tests
 └── Packages application
          │
          ▼
Spring
 │
 ├── DataSource
 ├── JdbcTemplate
 ├── Service
 ├── AOP
 └── TransactionManager
          │
          ▼
@Transactional
          │
          ▼
Transaction Interceptor
          │
          ▼
Database Transaction
          │
     ┌────┴────┐
   Success    Failure
     │           │
   COMMIT      ROLLBACK
```

---

# 95. Exam-Oriented Definitions

### Bean Inheritance

Spring Bean Inheritance is a mechanism in which a child bean definition
inherits configuration/properties from a parent bean definition,
reducing duplicate XML configuration.

### `abstract="true"`

Marks a bean definition as an abstract configuration template that
should not be instantiated as a normal bean.

### Bean ID

The primary identifier of a Spring bean definition in XML.

### Bean Name

A bean can have additional names/aliases through the `name` attribute.

### Transaction

A logical unit of database work that is committed or rolled back
according to transaction rules.

### ACID

```text
A → Atomicity
C → Consistency
I → Isolation
D → Durability
```

### AOP

Aspect-Oriented Programming separates cross-cutting concerns such as
logging, security and transaction management from core business logic.

### `@Transactional`

Spring annotation used to declare transactional semantics for a class or
method.

### `update()`

JdbcTemplate method generally used to execute a single INSERT, UPDATE or
DELETE operation and return an affected-row/update count.

### `batchUpdate()`

JdbcTemplate method used to execute a batch of similar update
operations, improving efficiency for bulk database work.

### `BatchPreparedStatementSetter`

Spring callback interface used to set values for each item in a JDBC
batch through a `PreparedStatement`.

### Maven

Apache Maven is a build automation and dependency management tool for
Java projects.

### POM

Project Object Model; `pom.xml` contains Maven project configuration
such as coordinates, dependencies, plugins, repositories and build
settings.

### Local Repository

Local cache/repository on the developer's machine where Maven stores
downloaded and locally installed artifacts.

### Maven Central

A major public Maven repository from which Maven can resolve many Java
artifacts.

### `package`

Builds the project's distributable artifact such as a JAR or WAR.

### `install`

Builds the artifact and installs it into the local Maven repository.

### SNAPSHOT

A development version that may change over time, such as:

```text
1.0-SNAPSHOT
```

---

# 96. Final Quick Revision --- Most Important Points

```text
Bean Inheritance
→ Reuse Spring bean configuration
→ Not Java inheritance
→ parent + child
→ abstract parent can act as template

Bean ID / Name
→ id = primary identifier
→ name = additional names/aliases

Transaction
→ logical unit of DB work
→ commit / rollback
→ ACID

@Transactional
→ declarative transaction
→ usually applied through Spring AOP proxy
→ RuntimeException/Error rollback by default

AOP
→ cross-cutting concerns
→ logging/security/transaction etc.

update()
→ one update statement

batchUpdate()
→ batch of similar updates

BatchPreparedStatementSetter
→ custom PreparedStatement parameter setting for batch

Maven
→ build + dependency management
→ pom.xml
→ local + remote repositories
→ compile → test → package → install → deploy

package
→ artifact in target/

install
→ artifact + local repository

SNAPSHOT
→ development version that can change

Maven does NOT download every JAR every time
→ local repository first
→ remote download if needed
→ snapshots may be checked/updated according to policy
```

## Official references used for verification

-   Spring transaction management and `@Transactional` behavior: Spring
    Framework reference. citeturn0search0turn0search1
-   Spring AOP-based declarative transaction implementation: Spring
    Framework reference. citeturn0search0
-   Maven lifecycle phases: Apache Maven documentation.
    citeturn0search7
-   Maven repository/dependency resolution: Apache Maven documentation.
    citeturn0search3turn0search10
-   Maven repository update policies: Apache Maven settings
    documentation. citeturn0search4
