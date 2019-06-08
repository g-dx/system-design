# SDLC

## Contents

TBD

### System Architecture

  * [General Primer](https://github.com/donnemartin/system-design-primer) - A rather large collection of resources on system design. Good general discussion of pros vs. cons. Covers example architectures for AWS, PasteBin, etc while also explaining:
    * Persistence - relational vs. noSQL vs. graph
    * Caching - levels & types
    * CAP Theorem - basics
    * Network - loadbalancing, REST, TCP, UDP

----

### Source Code Layout

#### Mono-Repository

  * [Awesome Monorepo](https://github.com/korfuri/awesome-monorepo) - Lots of good information about monorepos, the argument for them & how to work with them.
  * [Advantages of Monorepos](https://danluu.com/monorepo/) - arguments for by Dan Luu.
  * [Gitlab Monorepo (11.0)](https://github.com/BastiPaeltz/gitlab-ci-monorepo) - outlines how to build only parts of the a monorepository using some hacks.
  * [Gitlab Monorepo (11.4+)](https://gitlab.com/gitlab-org/gitlab-ce/issues/19232) - supports `changes` key to only run tasks when paths are changed.
  * [Building Monorepos with Gradle](https://www.javaadvent.com/2018/12/building-monorepo-projects-with-gradle.html) - explains how to use `Composite Builds` [\[1\]](https://blog.gradle.org/introducing-composite-builds), [\[2\]](https://docs.gradle.org/current/userguide/composite_builds.html) to only build part of the repository super quickly.
  * [Determining Changes Files In Jenkins](https://stackoverflow.com/questions/6260383/how-to-get-list-of-changed-files-since-last-build-in-jenkins-hudson/9473207#9473207) - a technique for Jenkins to understand which parts of the repository to build.
    
#### Multiple Repositories

TBD

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

##### Gradle

  * [Performance Guide](https://guides.gradle.org/performance/) - covers lots of aspects to improve and maintain performance of builds.
  * [Incremental Compiler](https://blog.gradle.org/incremental-compiler-avoidance) - article which explains the difference the incremental compiler makes to large repo builds.
  * **Annotation Processors & Incremental Builds** ([1](https://github.com/gradle/gradle/issues/1320), [2](https://github.com/gradle/gradle/issues/5277)) - details the API support that Gradle provides for annotation processing & libraries which have adopted this.
  * [Large Multi-Project](https://github.com/gradle/performance-comparisons/tree/master/large-multiproject) - taken from the benchmarking code in the article above.
  * [Build Cache](https://docs.gradle.org/current/userguide/build_cache.html) - describes the build cache architecture (local & remote) & how to configure & debug tasks.

##### Maven

  TBD 
  
#### DAG (Directed Acyclic Graph)
 
 * Bazel
 * Buck
 
---- 

### Service Architecture

#### Microservices

  * [Microservices & Monorepos](http://blog.shippable.com/ci/cd-of-microservices-using-mono-repos) - explains how to build, test & deploy only services which have changed using the Shippable infrastructure (looks a lot like Gitlab CI files)

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

### Releases

  * [Continuous Delivery with Maven & Docker](https://phauer.com/2016/version-numbers-continuous-delivery-maven-docker/) - describes a versioning scheme which builds all `master` branch artifacts with a dynamic version of `${commit time}-${commit hash}`. This eliminates the need for managing tags in Git & makes every commit "release ready" by supporting a unique identifier.
  * [Maven Release Plugin: Dead & Buried](https://axelfontaine.com/blog/dead-burried.html) - final article in a series which highlights the problems of attempting to do continuous delivery with Maven builds. It highlights v3.2.1 of the `maven` which allows the `pom.xml` version number to be set by a property defined on the commandline, e.g `mvn deplog -DVERSION=$BUILD_NUMBER`. This in conjunction with pushing a new git tag with the `VERSION` number means running the release plugin is not necessary at all.

---- 

### Testing

#### e2e

  * [Test Pyramid](https://martinfowler.com/bliki/TestPyramid.html) - describes the basic pyramid, cautions its inversion (the icecream cone) generally makes a case _against_ writing many e2e tests due to brittleness, determinisim & maintainability.
  * [Making End-to-End Tests Work](https://www.symphonious.net/2015/04/30/making-end-to-end-tests-work/) - describes e2e acceptance tests for the LMAX trading platform & their importance. Also has a number of excellent linked articles on [test isolation](https://www.symphonious.net/2014/04/12/testinglmax-test-isolation/), [test result storage](https://www.symphonious.net/2014/03/25/testinglmax-test-results-database/) & [control of time during tests](https://www.symphonious.net/2014/04/01/testinglmax-time-travel-and-the-tardis/).

#### Frameworks

  * [JUnit](https://junit.org/junit5/) - Standard Java test framework.
  * [Spock](http://spockframework.org/) - Groovy test framework. Very comprehensive support for a number of different testing styles (mocking, stubbing, table driven, etc)
  * [Mockito](https://site.mockito.org/) - Standard stubbing & mocking library.
  * [AssertJ](http://joel-costigliola.github.io/assertj/) - Provides comprehensive assertion support. Excellent alternative to Junit assertions. 

#### Database

  * [H2](https://h2database.com/html/main.html) - a fast, in memory database which can be used for unit tests.
  
#### Queuing

#### Cloud Integrations

  * [LocalStack](https://localstack.cloud/) - provides testing stubs for key AWS services such as SQS, SNS, etc. Basic edition is free & seems to be available as a JAR on Maven Central [here](https://search.maven.org/artifact/cloud.localstack/localstack-utils/0.1.22/jar). Normal operation is as a Docker container to run locally to aid development & then support easy running in your CI environment to enable testing. This is easy to do in Gitlab CI by using the [`services` key](https://docs.gitlab.com/ee/ci/docker/using_docker_images.html#accessing-the-services)

---- 

### RDBMS

#### Migrations & Refactoring
  
[Zero Downtime Deployment With a Database](https://spring.io/blog/2016/05/31/zero-downtime-deployment-with-a-database) - describes the process of renaming a column in a zero downtime deployment fashion by splitting the work across multiple releases & deployments.

#### Tooling
 
  * Flyway
  * Liquibase
 
**Flyway**
 
  * [Best Practice for Database Migrations](http://dbabullet.com/index.php/2018/03/29/best-practices-using-flyway-for-database-migrations/)
 
**Liquibase**
  
---- 

### Queueing

 TBD

---- 

### CI/CD (Continous Integration/Delivery/Deployment)

 * [Continuous Integration, Delivery, and Deployment with GitLab](https://about.gitlab.com/2016/08/05/continuous-integration-delivery-and-deployment-with-gitlab/) - describes the differences between the 3 terms & gives a good overview of how Gitlab CI can address these.
 * [Anatomy of a Delivery Pipeline](http://www.informit.com/articles/article.aspx?p=1621865&seqNum=1) - a chapter from a book on continuous delivery which describes the general benefits, principals & general outline of a continuous delivery pipeline. There are good descriptions of what should happen at each stage.
 
#### Gitlab

  * [Scaling Gitlab CI](http://blog.thehumangeo.com/gitlab-autoscale-runners.html) - interesting article explaining the use of the Runner in Docker Machine mode to support auto-scaling of EC2 instances to meet build demand.
  * [AutoScale CI Runners](https://about.gitlab.com/2017/11/23/autoscale-ci-runners/) - similar to the article above explaining how to use the Runner in Docker Machine mode with EC2 spot instances to lower costs.

---- 

### Deployment
  
#### Blue/Green

  * [Best Practices for Smooth Releases](https://www.blazemeter.com/blog/five-blue-green-deployment-best-practices-for-a-smooth-release) - Interesting article which gives good guidance to ensure smooth releases. Interesting points are:
 
    * Do not use DNS to perform the switch as it is too slow. Use a load balancer instead.
    * Ensure connections are drained from old env before shutdown.
    * Perform a rolling update where new servers are mixed in gradually.
    * If using IaC one the switch is complete & everything is running smoothly simply destroy the old env. Then for the next deployment rebuild it, run smoke tests, perform the load balancer switch, ensure everything is ok & destroy old env. Repeat for every deployment. 
  * [Blue/Green Deployement With a Single Underlying Database](https://techblog.bozho.net/blue-green-deployment/) - outlines working with a single DB under a blue/green deployment model. Advocates a 3 change release model for the DB ensuring backwards compatibility. Suggests uses DNS with a low TTL for the switch.

---- 
### Networking

  * [Introduction to Modern Network Load Balancing and Proxying](https://blog.envoyproxy.io/introduction-to-modern-network-load-balancing-and-proxying-a57f6ff80236) - describes how L4 & L7 loadbalancers works & standard features. Also explains typically topologies such as middle (Nginx, HAProxy, Apache), edge (Traefik, HAProxy, Caddy, Envoy, etc), client library (Finagle, Hystrix) & side car (a.k.a "service mesh") 
  * [Application Proxy/Gateway Comparison](https://gist.github.com/StevenACoffman/acf1133da6c5ff5226c0f6eb8fbd8132) - a ranking of various opensource solutions by Github stars (!)
  * [AWS Single VPC Design](https://aws.amazon.com/answers/networking/aws-single-vpc-design/) - describes general best practice for network design inside a VPC on AWS.

---- 

### Infrastructure as Code

Interesting tools in this space:

  * [Pulumi](https://www.pulumi.com/) - Very interesting multi-cloud tooling which builds on the actual cloud provider SDKs to support building infrastructure. Commercial use requires £££.
  * [Terraform](https://www.terraform.io/) - Hashicorp build tooling based on templating in HCL (Hashicorp Configuration Language). It has an Enterprise offerring but I'm unclear on how it actually differs.
  
#### Testing

  * [Top 3 Terraform Testing Strategies](https://www.contino.io/insights/top-3-terraform-testing-strategies-for-ultra-reliable-infrastructure-as-code) - describes 3 approaches to testing IaC, basically, manual review of Terraform plan, integration test via Inspec (see below) or unit test via [RSpec](https://relishapp.com/rspec/) (I suspect the author comes from a Chef background).
  * [Terratest](https://github.com/gruntwork-io/terratest) - a library to write tests for Terraform configuration. It does this by actually creating all resources on smaller instances, running tests & then destroying everything. Tests are written in Go. [This article introduces it](https://blog.gruntwork.io/open-sourcing-terratest-a-swiss-army-knife-for-testing-infrastructure-code-5d883336fcd5) and explains some of the issues IaC teams have without tests.
  * [Kitchen-Terraform](https://github.com/newcontext-oss/kitchen-terraform) - a set of Kitchen plugins to allow writing InSpec tests for infrastructure.

#### Example Usage

 * [Shippable Terraform Code](https://github.com/Shippable/infra) - a repo containing all the infrastructure code for https://www.shippable.com/ (I assume just the website)

----

### Emerging Tools & Best Practice

 * [ThoughtWorks Tech Radar](https://www.thoughtworks.com/radar) - published bi-anually & provides recommendations on whether a language, tool or framework is considered ready for adoption by their consultants.
 * ["Awesome Lists"](https://github.com/sindresorhus/awesome#readme) - a collection of deeper discussions on topics like security, networking & other misc topics.

----

### Misc

  * [http4K](https://www.http4k.org/) - a pure Kotlin library for making & receiving HTTP traffic. Aims to be highly testable & it contains only 2 primitives: the `handler` & `filter`
