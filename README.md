# System Design

## Topics

### Source Code Layout

  * Mono-Repository
    - [Building Monorepos with Gradle](https://www.javaadvent.com/2018/12/building-monorepo-projects-with-gradle.html)
    -
  * Multiple Repositories

---- 

### Development Model

 * [Trunk-based Development](https://trunkbaseddevelopment.com/)
 * [Git Flow](https://nvie.com/posts/a-successful-git-branching-model/)
 * [One Flow](https://www.endoflineblog.com/oneflow-a-git-branching-model-and-workflow)
 * [Successful branching model considered harmful](https://barro.github.io/2016/02/a-succesful-git-branching-model-considered-harmful/)
 
*Important Ideas:*

 - Linear History (i.e. no merge commits). Makes the log considerably more useful.
 - No long lived branches other than master.

---- 

### Build Tooling

[Difference between recursive & DAG](https://trunkbaseddevelopment.com/monorepos/).

#### Recursive
 
 * Gradle
   - Composite Builds [\[1\]](https://blog.gradle.org/introducing-composite-builds), [\[2\]](https://docs.gradle.org/current/userguide/composite_builds.html)
 * Maven
 
#### DAG (Directed Acyclic Graph)
 
 * Bazel
 * Buck
 
---- 

### Languages

TBD

---- 

### Packaging

  * WAR - Standard Java EE packaging format.
  * Fat JAR (with embedded servlet) - Spring Boot, etc.
  * [jarpackager]() - bundles JRE, JDK & platform native script to execute
  * [jlink (Java 9)](https://steveperkins.com/using-java-9-modularization-to-ship-zero-dependency-native-apps/) - bundles JRE, _only required modules of the JDK_ & platform native script to execute
  * [Docker]() - 
  * [VM]() - 

The following table shows encapsulation for a "standard web service".

|           | WAR | Fat JAR | `jarpackager` | `jlink`          | Docker | VM |
|-----------|-----|---------|---------------|------------------|--------|----|
| App Code  | X   | X       | X             | X                | X      | X  |
| Webserver |     | X       | X             | X                | X      | X  |
| Ext Conf  |     | X       | (See note 1)  | (See note 1)     | X      | X  |
| JDK       |     |         | X             | X (reduced size) | X      | X  |
| JVM       |     |         | X             | X                | X      | X  |
| Ext Svcs  |     |         | (See note 2)  | (See note 2)     | X      | X  |
| OS        |     |         |               |                  | X      | X  |
| Kernel    |     |         |               |                  |        | X  |
  
(1) - Possible if service is released as a ZIP with extra configuration in appropriate subdir.
(2) - Again, possible but tricker than using Docker 

The above table assumes that environment specific configuration will be plugged in via env vars on start up or resolved 
dynamically by the app on startup.

---- 

### Testing

* JUnit - Standard Java test framework.
* Spock - Groovy test framework. Very comprehensive support for a number of different testing styles (mocking, stubbing, table driven, etc)
* Mockito - Standard stubbing & mocking library.
* AssertJ - Provides comprehensive assertion support. Excellent alternative to Junit assertions. 

---- 

### RDBMS

 #### Migrations & Refactoring
 
 - Flyway
 - Liquibase

*How To*

 [This article](https://spring.io/blog/2016/05/31/zero-downtime-deployment-with-a-database) outlines the process of renaming a column in a 
 zero downtime deployment fashion.

---- 

### Queueing

 TBD

---- 

### CI (build & test)

 TBD

---- 

### Deployment
  
  * Blue/Green
  * 
