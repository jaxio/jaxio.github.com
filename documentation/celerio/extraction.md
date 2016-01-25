---
layout: refdoc
title: Celerio Guide - Database Schema extraction
---
[ << prev ](bootstrap.html) [ index ](index.html) [ next >> ](generation.html)
5. Database Schema extraction
=============================

* [Principle](#principle)
* [Details of dbmetadata-maven-plugin](#details)
* [Simple Usage](#usage)

<a name="principle"></a>
Principle
---------

The database schema extraction is performed by the `extract-metadata` goal of the `dbmetadata-maven-plugin`,
a Maven plugin provided by Jaxio.

This plugin connects to a relational database using JDBC, retrieves the database schema metadata and write it
into an XML file named `metadata.xml`.

> **Note**
>
> The metadata file produced by the dbmetadata plugin is not meant to be edited
> manually. If you need to change some settings, you should either
> change your database schema or use Celerio's configuration file.

While pretty straightforward the schema extraction may take several minutes on large database schemas
(e.g. more than 200 tables). *To prevent useless extraction* , the simplest approach is to declare a Maven profile
dedicated to database extraction and activate it only when needed (i.e. each time you modify the database schema).

The `metadata.xml` file produced by this plugin is used by Celerio's generate goal.

<a name="details"></a>
Details of dbmetadata-maven-plugin
-----------------------------------

### Full name

* name: com.jaxio.celerio:dbmetadata-maven-plugin:4.0.4
* goal: extract-metadata
* maven phase: generate-sources

### Attributes

#### skip
* expression: celerio-maven-plugin.skip
* default value: false
* description: Should the database meta data extraction be skipped ? 
This is a common pattern in Maven, where you can skip plugins using profiles to fully adapt your build.

#### jdbcDriver
* expression: jdbc.driver
* description: Set the JDBC driver class
* example: org.postgresql.Driver

#### jdbcUrl
* expression: jdbc.url
* description: Set the JDBC url to connect to your database. Make sure that you connect with enough privileges to access the meta data information.
* example: jdbc:h2:~/.h2/sampledatabase

#### jdbcUser
* expression: jdbc.user
* description: Set the JDBC user, this user needs to have the privilege to access the database metadata.

#### jdbcPassword 
* expression: jdbc.password
* description: Set the JDBC password.

#### jdbcCatalog
* expression: jdbc.catalog
* description: Set the JDBC catalog.

#### jdbcOracleRetrieveRemarks 
* expression: jdbc.oracleRetrieveRemarks
* default value: false
* description: Should the Oracle remarks be retrieved ? Please note that this will impact the speed of the reverse engineering of your database.

#### jdbcOracleRetrieveSynonyms 
* expression: jdbc.oracleRetrieveSynonyms
* default value: true
* description: Should the synonyms be retrieved ?

#### jdbc.reverseIndexes
* expression: jdbc.reverseIndexes
* default value: true
* description: When false, disable completely reverse of indexes. Can be useful when reversing large database full of data as reversing indexes can be slow.

#### jdbc.reverseOnlyUniqueIndexes
* expression: jdbc.reverseOnlyUniqueIndexes
* default value: true
* description: When true, reverse only indexes for unique values; when false, reverse indexes regardless of whether unique or not. Can be useful when reversing large database full of data as reversing indexes can be slow.

#### jdbcReverseViews
* expression: jdbc.reverseViews 
* default value: false
* description: Should this plugin also reverse VIEWS?

#### jdbcSchema 
* expression: jdbc.schema
* description: Set the JDBC schema.

#### targetFilename 
* expression: maven-metadata-plugin.targetFilename 
* default value: `${basedir}/src/main/config/celerio-maven-plugin/metadata.xml`
* description: The fully qualified name of the XML file created by this plugin.

<a name="usage"></a>
Simple Usage
------------

In your pom.xml, declare as *properties* the necessary *expressions* that the plugin needs to connect to your database and reverse it.
Then create a dedicated profile to execute the plugin.

{% highlight xml %}

<properties>
	<jdbc.groupId>com.h2database</jdbc.groupId>
	<jdbc.artifactId>h2</jdbc.artifactId>
	<jdbc.version>1.3.167</jdbc.version>
	<jdbc.driver>org.h2.Driver</jdbc.driver>
	<jdbc.url>jdbc:h2:~/.h2/mydbname;MVCC=TRUE</jdbc.url>
	<jdbc.user>admin</jdbc.user>
	<jdbc.password></jdbc.password>
</properties>

<!-- skip -->
	
<profile>
	<!-- ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ -->
	<!-- Extract the database metadata -->
	<!-- ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ -->
	<id>metadata</id>
	<build>
		<defaultGoal>generate-sources</defaultGoal>
		<plugins>
			<plugin>
				<groupId>com.jaxio.celerio</groupId>
				<artifactId>dbmetadata-maven-plugin</artifactId>
				<version>${celerio-maven-plugin.version}</version>
				<executions>
					<execution>
						<id>Extract the database schema.</id>
						<goals>
							<goal>extract-metadata</goal>
						</goals>
					</execution>
				</executions>
				<dependencies>
					<dependency>
						<groupId>${jdbc.groupId}</groupId>
						<artifactId>${jdbc.artifactId}</artifactId>
						<version>${jdbc.version}</version>
					</dependency>
				</dependencies>
			</plugin>
		</plugins>
	</build>
	<repositories>
		<repository>
				<id>jaxio-repository</id>
				<url>http://maven.jaxio.com/repository</url>
		</repository>
	</repositories>
	<pluginRepositories>
		<pluginRepository>
				<id>jaxio-repository</id>
				<url>http://maven.jaxio.com/repository</url>
		</pluginRepository>
	</pluginRepositories>
</profile>
{% endhighlight %}
		