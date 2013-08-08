---
layout: doc
title: Celerio Guide - Installation
---

Installation
============

Requirements
------------

### JDK 6 or above

Celerio and the generated code require at least JDK 1.6.0_24 to compile and run.
*Download and install the latest JDK from [](http://java.oracle.com)*

Once installed, make sure you can run `javac` from the command line.
Open a command line and run:

	javac –version

You should get a result similar to this:

    javac 1.6.0_37

### Maven 2

Celerio and the generated code require Maven 2.

*Download and install Maven 2 from [](http://maven.apache.org)*

Once installed, make sure you can run Maven 2 from the command line.
Open a command line and run:

	mvn –v

You should get a result similar to this:

    Maven version: 2.0.9
    Java version: 1.6.0_18
    OS name: "windows xp" version: "5.1" arch: "x86" Family: "windows"

### Source control system (optional)

Celerio leverages source control (SVN and CVS) usage to provide features
allowing the user to take control over certain generated file.

Before generating such file, Celerio always check if a file of the same
name exists on disk and is present under source control.

Celerio assumes that files under source control should not be
overwritten arbitrarily.

To really take advantage of Celerio's collision features, you must use a
source-control system for the project you intent to generate with
Celerio.

Celerio
-------

Celerio is distributed as a Maven 2 plugin. Celerio plugin is hosted on Jaxio Maven 2 private repository.

### Access Jaxio Maven 2 repository

As explained at ()[http://maven.apache.org/settings.html#Servers] the username and password (provided by Jaxio once you purchase a License) 
should be set in your settings.xml file. Here is how:

{% highlight xml %}

<settings ...>
... 
  <servers>
    <server>
      <id>springfuse-repository</id>
      <username>YOUR_USERNAME</username>
      <password>YOUR_PASSWORD</password>
    </server>
  </servers>
  <profiles>
    <profile>
      <id>celerio</id>
      <activation>
        <activeByDefault>true</activeByDefault>
      </activation>
      <repositories>
        <repository>
          <id>springfuse-repository</id>
          <url>http://maven2.springfuse.com/</url>
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
          <id>springfuse-repository</id>
          <url>http://maven2.springfuse.com/</url>
          <releases>
            <enabled>true</enabled>
          </releases>
          <snapshots>
            <enabled>false</enabled>
          </snapshots>
        </pluginRepository>
       </pluginRepositories>
     </profile>
    </profiles>
...
</settings>

{% endhighlight %}

### License key installation

Celerio License Key comes as a jar file named brand-1.0.0.jar.

To install it, run this maven command from a folder containing the brand-1.0.0.jar file provided by Jaxio:

> mvn install:install-file -Dfile=brand-1.0.0.jar -DgroupId=com.jaxio.celerio -DartifactId=brand-Dversion=1.0.0 -Dpackaging=jar
