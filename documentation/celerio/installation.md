---
layout: english
title: Celerio Guide - Installation
---

Celerio Installation
====================

Requirements
------------

### JDK 1.8 or above

Celerio and the generated code require at least JDK 1.8.0_25 to compile and run.

*Download and install the latest JDK from [http://java.oracle.com](http://java.oracle.com)*

Once installed, make sure you can run `javac` from the command line.
Open a command line and run:

	javac –version

You should get a result similar to this:

    javac 1.8.0_25

### Maven 3

Celerio and the generated code require Maven 3.

*Download and install Maven 3 from [http://maven.apache.org](http://maven.apache.org)*

Once installed, make sure you can run Maven from the command line.
Open a command line and run:

	mvn –v

You should get a result containing a line similar to this:

    Apache Maven 3.3.3 (7994120775791599e205a5524ec3e0dfe41d4a06; 2015-04-22T13:57:37+02:00)

### Source control system (optional)

Celerio leverages source control (SVN, CVS, GIT) usage to provide features
allowing the user to take control over certain generated file.

Before generating such file, Celerio always check if a file of the same
name exists on disk and is present under source control.

Celerio assumes that files under source control should not be
overwritten arbitrarily.

To really take advantage of Celerio's collision features, you must use a
source-control system for the project you intent to generate with Celerio.

Celerio
-------

Celerio is distributed as a Maven plugin. It is available on Jaxio's Maven repository, not on Maven central.

For this reason, you must declare this repository in your Maven `~/.m2/settings.xml` file.

### Declare Jaxio's Maven repository in settings.xml

{% highlight xml %}
<settings xmlns="http://maven.apache.org/POM/4.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
    <!-- ... -->
    <profiles>
        <!-- ... -->
        <profile>
            <id>celerio</id>
            <repositories>
                <repository>
                    <id>jaxio-repository</id>
                    <url>http://maven.jaxio.com/repository</url>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                    <snapshots>
                        <enabled>false</enabled>
                    </snapshots>
                </repository>
            </repositories>
            <pluginRepositories>
                <pluginRepository>
                    <id>jaxio-repository</id>
                    <url>http://maven.jaxio.com/repository</url>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                    <snapshots>
                        <enabled>false</enabled>
                    </snapshots>
                </pluginRepository>
            </pluginRepositories>
        </profile>
        <!-- ... -->
    <profiles>
    <!-- ... -->
</settings>
{% endhighlight %}


### Bootstrap a project with Celerio

Type the following command in a console:

    mvn -Pcelerio com.jaxio.celerio:bootstrap-maven-plugin:4.0.0:bootstrap

Then follow the instructions...

### Branding file...

The first time Celerio is executed it creates the file `.celerio/branding.properties` in your home directory.

To change the default root package of the generated source code, you can open this file and change property:

    root_package=com.jaxio
