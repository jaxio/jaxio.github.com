---
layout: refdoc
title: Celerio Guide - Installation
---
[ << prev ](introduction.html) [ index ](index.html) [ next >> ](bootstrap.html)
2. Celerio Installation
=======================

* [Requirements](#requirements)
    * [JDK 1.8](#requirements-jdk)
    * [Maven 3](#requirements-maven3)
* [Celerio](#celerio)
    * [Bootstrap a project with Celerio](#celerio-bootstrap)
    * [Celerio branding](#celerio-branding)


<a name="requirements"></a>
Requirements
------------

<a name="requirements-jdk"></a>
### JDK 1.8 or above

Celerio requires at least JDK 1.8.0_25 to run.

*Download and install the latest JDK from [http://java.oracle.com](http://java.oracle.com)*

Once installed, make sure you can run `javac` from the command line. Open a command line and run:

	javac –version

You should get a result similar to this:

    javac 1.8.0_25

<a name="requirements-maven3"></a>
### Maven 3

Celerio is distributed as a Maven plugin. Celerio requires Maven 3.

*Download and install Maven 3 from [http://maven.apache.org](http://maven.apache.org)*

Once installed, make sure you can run Maven from the command line.
Open a command line and run:

	mvn –v

You should get a result containing a line similar to this:

    Apache Maven 3.3.3 (7994120775791599e205a5524ec3e0dfe41d4a06; 2015-04-22T13:57:37+02:00)


<a name="celerio"></a>
Celerio
-------

<a name="celerio-bootstrap"></a>
### Bootstrap a project with Celerio

Type the following command in a console:

    mvn com.jaxio.celerio:bootstrap-maven-plugin:4.0.4:bootstrap

Then follow the instructions in [Bootstrapping a project with Celerio](bootstrap.html).

<a name="celerio-branding"></a>
### Branding file...

The first time Celerio is executed it creates the file `.celerio/branding.properties` in your home directory as well as
`.celerio/logo.png`

To change the default root package of the generated source code, you can open this file and change the property:

    root_package=com.jaxio

You may also replace the default `logo.png` image without your own!
