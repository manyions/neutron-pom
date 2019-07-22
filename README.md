# neutron-pom

Maven parent POM to centralize configuration for open source contributions by Many Ions.  It provides dependency 
management for many commonly used libraries.  

It also provides a profile, maven-central-deploy, which handles all the additional build steps required for deploying to 
Sonatype's OSS Repository Hosting, via the Nexus staging plugin.

##Prerequisites

* [Java](https://adoptopenjdk.net/) - at least 1.8 is required. AdoptOpenJDK is recommended for its LTS extended support.
* [Maven](https://maven.apache.org/) - at least version 3 is required
* [GnuPG](https://central.sonatype.org/pages/working-with-pgp-signatures.html) - used to cryptographically sign the build artifacts
* [Jira account at Sonatype](https://central.sonatype.org/pages/ossrh-guide.html#initial-setup) - used to access the OSS Repository Hosting

###Maven User Configuration
~/.m2/settings.xml

This example shows the necessary server and profile configuration required for deployment to the staging repo.  Your 
file may include additional configuration as well. 

WARNING: Never include un-encrypted passwords in cleartext files.  Follow the steps in this guide to encrypt your 
passwords, [Maven Password Encryption](https://maven.apache.org/guides/mini/guide-encryption.html).
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
            </properties>
        </profile>
    </profiles>
    
    ...
    
</settings>
```    

##Usage
To use this as your project's parent POM, include this snippet in your project's pom.xml. 
```
<parent>
    <groupId>com.manyions</groupId>
    <artifactId>neutron-pom</artifactId>
    <version>0.0.4-SNAPSHOT</version>
</parent>
```

##Deployment

To fully prepare and deploy your project's artifact to Maven Central's staging repo, use this command when building the artifact.
```
mvn clean deploy -P maven-central-deploy
```

GPG will then prompt you for the passphrase for the key defined in the <gpg.keyname> property in your settings.xml.  After 
the passphrase is entered, the artifacts are signed and deployed to the staging repo.  

The Nexus Staging plugin's autoReleaseAfterClose parameter is set to false, so releases from the staging repo require manual approval.

##Automating Deployment Steps

In order to release artifacts automatically from the staging repo, instead of having to manually review, define this in
the properties element of your project's pom.xml
```
<nexus.staging.maven.plugin.autoReleaseAfterClose>true</nexus.staging.maven.plugin.autoReleaseAfterClose>
```

In order to prevent the GPG passphrase prompt from being presented, and automatically supply the passphrase during code 
signing, include the following server configuration snippet in your settings.xml.
```
<server>
    <id>gpg.passphrase</id>
    <passphrase>{... your encrypted code signing passhrase here ...}</passphrase>
</server>
```

If your system has GPG 2.1.x or greater installed, you may also need to configure gpg with an additional command line 
option. To do this, add the following snippet to your project's pom.xml.
```
<profile>
    <id>maven-central-deploy</id>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-gpg-plugin</artifactId>
                <version>${maven.gpg.plugin.version}</version>

                <executions>
                    <execution>
                        <id>sign-artifacts</id>
                        <phase>verify</phase>

                        <goals>
                            <goal>sign</goal>
                        </goals>

                        <configuration>
                            <gpgArguments>
                                <arg>--pinentry-mode</arg>
                                <arg>loopback</arg>
                            </gpgArguments>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</profile>
```
