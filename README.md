# neutron-pom

Maven parent POM to centralize configuration for open source contributions by Many Ions.  It provides dependency 
management for many commonly used libraries.  

It also provides a profile, maven-central-deploy, which handles all the additional build steps required for deploying to 
Sonatype's OSS Repository Hosting, via the Nexus staging plugin.

##Prerequisites

* Java 8
* Maven 3
    * ~/.m2/settings.xml

##Usage
To use this as your project's parent POM, include this snippet in the pom.xml for your project. 
```
<parent>
    <groupId>com.manyions</groupId>
    <artifactId>neutron-pom</artifactId>
    <version>0.0.4-SNAPSHOT</version>
</parent>
```

To fully prepare and deploy your project's artifact to Maven Central, use this command when building the artifact.
```
mvn clean deploy -P maven-central-deploy
``` 
