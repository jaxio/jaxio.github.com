---
layout: refdoc
title: Celerio Guide - Configuration 
---
[ << prev ](troubleshooting.html) [ index ](index.html) [ next >> ](changelog.html)
14. Miscellaneous
=================

* [Install Oracle Jdbc driver](#install-oracle-jdbc-driver-maven-repository)
* [Install DB2 Jdbc driver](#install-db2-jdbc-driver-maven-repository)

<a name="install-oracle-jdbc-driver-maven-repository"></a>
## Install Oracle Jdbc driver in your Maven local repository

If you are using Oracle, you must first install your Oracle JDBC driver in your local Maven repository.

Here is how to proceed:

### Step 1- Download the Oracle JDBC driver

Please download manually the Oracle JDBC driver <a href="http://www.oracle.com/technetwork/database/features/jdbc/index-091264.html">from Oracle web site</a>.

### Step 2- install your Oracle JDBC driver in your local Maven repository

We follow the instructions from this Maven FAQ <a href="http://maven.apache.org/general.html#importing-jars" target="_new">I have
a jar that I want to put into my local repository. How can I copy it in?</a>

In this example, we assume that your Oracle JDBC driver is in a file called <strong>classes12_g.jar</strong>

Open a console and go to the folder containing the classes12_g.jar file.

    c:\oracle\jdbc>dir
    23/06/2008  13:02		 2 044 594 classes12_g.jar

Let's assume you want to register your driver under the group id 'com.oracle', use 'oracle' as the name of the artifact
id and that you want the version to be '10.2.0.2.0'.

You can now run the following command:

    c:\oracle\jdbc>mvn install:install-file -Dfile=classes12_g.jar -DgroupId=com.oracle \
    -DartifactId=oracle -Dversion=10.2.0.2.0 -Dpackaging=jar -DgeneratePom=true

    [INFO] Scanning for projects...
    [INFO] Searching repository for plugin with prefix: 'install'.
    [INFO] ------------------------------------------------------------------------
    [INFO] Building Maven Default Project
    [INFO]	task-segment: [install:install-file] (aggregator-style)
    [INFO] ------------------------------------------------------------------------
    [INFO] [install:install-file]
    [INFO] Installing c:\oracle\jdbc\classes12_g.jar to C:\Users\Nicolas\.m2\repository\com\oracle\oracle
    \10.2.0.2.0\oracle-10.2.0.2.0.jar
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESSFUL
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 1 second
    [INFO] Finished at: Tue Feb 03 12:58:23 CET 2009
    [INFO] Final Memory: 3M/5M
    [INFO] ------------------------------------------------------------------------

### Step 3 - Done

You can now add the driver dependency in your pom.xml
{% highlight xml %}
<dependency>
	<groupId>com.oracle</groupId>
	<artifactId>oracle</artifactId>
	<version>10.2.0.2.0</version>
</dependency>
{% endhighlight %}
 
You are now ready to reverse your Oracle database using Celerio and generate a complete Java/JPA/Hibernate/Spring project!

<a name="install-db2-jdbc-driver-maven-repository"></a>

## Generate a project from an IBM DB2 database schema

Reversing a DB2 database with Celerio requires few additional steps that we describe here.

### Install the JDBC Driver

The DB2 jdbc driver is not available on maven central repository. You must download it from <a href="http://www-01.ibm.com/software/data/db2/express/download.html">IBM site</a>
and install it manually. 

In addition to the driver, you must also install a license for the driver to function normally.

Assuming you have found your way on IBM site, you should have the 2 following jars handy:

* db2jcc4.jar
* db2jcc_license_cu.jar

Let's assume the driver version your downloaded is "9.7.0.4"

Open a console, go to the folder containing these 2 jar files, and run the following commands to deploy them in your local maven repository:

    mvn install:install-file -Dfile=db2jcc4.jar -DgroupId=com.ibm.db2 -DartifactId=db2jcc4 -Dversion=9.7.0.4 -Dpackaging=jar -DgeneratePom=true
&nbsp;

    mvn install:install-file -Dfile=db2jcc_license_cu.jar -DgroupId=com.ibm.db2 -DartifactId=db2jcc_license_cu -Dversion=9.7.0.4 -Dpackaging=jar -DgeneratePom=true

You can now add the driver dependency in your pom.xml
{% highlight xml %}
<dependency>
    <groupId>com.ibm.db2</groupId>
    <artifactId>db2jcc_license_cu</artifactId>
    <version>9.7.0.4</version>
</dependency>
{% endhighlight %}
