# neutron-pom

Maven parent POM to centralize configuration for open source contributions by Many Ions.  It provides dependency 
management for many commonly used libraries.  

It also provides a profile, maven-central-deploy, which handles all the additional build steps required for deploying to 
Sonatype's OSS Repository Hosting, via the Nexus staging plugin.

##Prerequisites

* Java 8
* [Maven](https://maven.apache.org/) - Dependency Management
    * At least version 3 is recommended.
    * ~/.m2/settings.xml
```
<settings xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 https://maven.apache.org/xsd/settings-1.0.0.xsd">

    <servers>
        <server>
            <id>ossrh</id>
            <username>... your ossrh username here ...</username>
            <password>{... your encrypted ossrh password here ...}</password>
        </server>
    </servers>

    <profiles>
        <profile>
            <id>maven-central-deploy</id>

            <activation>
                <activeByDefault>false</activeByDefault>
            </activation>

            <properties>
                <gpg.keyname>... your signing key name here ...</gpg.keyname>
                <gpg.passphrase>{... your encrypted signing key passphrase here ...}</gpg.passphrase>
            </properties>
        </profile>
    </profiles>
    
    ...
    
</settings>
```    

##Usage
To use this as your project's parent POM, include this snippet in the pom.xml for your project. 
```
<parent>
    <groupId>com.manyions</groupId>
    <artifactId>neutron-pom</artifactId>
    <version>0.0.4-SNAPSHOT</version>
</parent>
```

##Deployment

To fully prepare and deploy your project's artifact to Maven Central, use this command when building the artifact.
```
mvn clean deploy -P maven-central-deploy
``` 
