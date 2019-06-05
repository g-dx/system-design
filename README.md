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
  
[How To](https://spring.io/blog/2016/05/31/zero-downtime-deployment-with-a-database) - outlines the process of renaming a column in a zero downtime deployment fashion.

#### Tooling
 
  * Flyway
  * Liquibase
 
**Flyway**
 
  * [Best Practice](http://dbabullet.com/index.php/2018/03/29/best-practices-using-flyway-for-database-migrations/)
 
**Liquibase**
  
---- 

### Queueing

 TBD

---- 

### CI (build & test)

 TBD

---- 

### Deployment
  
#### Blue/Green

  * [Best Practices](https://www.blazemeter.com/blog/five-blue-green-deployment-best-practices-for-a-smooth-release) - Interesting article which gives good guidance to ensure smooth releases. Interesting points are:
 
    * Do not use DNS to perform the switch as it is too slow. Use a load balancer instead.
    * Ensure connections are drained from old env before shutdown.
    * Perform a rolling update where new servers are mixed in gradually.
    * If using IaC one the switch is complete & everything is running smoothly simply destroy the old env. Then for the next deployment rebuild it, run smoke tests, perform the load balancer switch, ensure everything is ok & destroy old env. Repeat for every deployment. 
  * [Single DB](https://techblog.bozho.net/blue-green-deployment/) - outlines working with a single DB under a blue/green deployment model. Advocates a 3 change release model for the DB ensuring backwards compatibility. Suggests uses DNS with a low TTL for the switch.

---- 

### Infrastructure as Code

Interesting tools in this space:

  * [Pulumi](https://www.pulumi.com/) - Very interesting multi-cloud tooling which builds on the actual cloud provider SDKs to support building infrastructure. Commercial use requires £££.
  * [Terraform](https://www.terraform.io/) - Hashicorp build tooling based on templating in HCL (Hashicorp Configuration Language). It has an Enterprise offerring but I'm unclear on how it actually differs.
