---
layout: refdoc
title: Celerio Guide - Code Generation
---
[ << prev ](extraction.html) [ index ](index.html) [ next >> ](delete-generated-files.html)

# 6. Code generation

* [Principle](#principle)
* [Details of celerio-maven-plugin](#details-of-celerio-maven-plugin)
* [Simple Usage](#simple-usage)
* [Advanced: Where to generate?](#advanced-where-to-generate-)

## Principle

The code generation step is performed by the `generate` goal of the `celerio-maven-plugin`, a Maven plugin
provided by Jaxio.

The code generation step is driven by several factors:

*   *The database meta data:* It serves as a main entry model. The
    various column's constraints such as `NOT NULL` , the foreign keys,
    etc. are exploited by Celerio in the code generation process.
*   *Celerio conventions*
*   *Celerio configuration*
*   *Celerio template packs*
*   *Generated code modifications*

![Source code generation is driven by several
factors](images/celerio-generation.png)

As for database schema extraction, the code generation step does not
need to be performed at each build. *To prevent useless generation* ,
the simplest approach is to declare a Maven profile dedicated to code
generation. As an example you can use the `pom.xml` that the Celerio
`bootstrap` goal produces.

## Details of celerio-maven-plugin

### Full name

* name: com.jaxio.celerio:celerio-maven-plugin:4.0.7
* goal: generate
* maven phase: generate-sources

This plugin uses the metadata.xml file produced by the dbmetadata-maven-plugin:extract-metadata goal.

### Attributes

#### skip
* expression: celerio-maven-plugin.skip
* default value: false
* description: Should the source code generation be skipped ? This is a common pattern in Maven, 
where you can skip plugins using profiles to fully adapt your build.

#### xmlConfiguration
* expression: celerio-maven-plugin.configuration
* default-value: ${basedir}/src/main/config/celerio-maven-plugin/celerio-maven-plugin.xml
* description: The relative path to the Celerio configuration file.

#### xmlTemplatePacksOverride
* expression: celerio-maven-plugin.packs.configuration
* default value: ${basedir}/src/main/config/celerio-maven-plugin/celerio-template-packs.xml
* descrption: The relative path to a Maven Celerio configuration file dedicated to override the template packs definition present in the main xml configuration file.
This configuration file is useful when working on multi-modules project. Indeed you can set for each module exactly the template packs that should be
used. Keep in mind that only the template packs definition will be extracted from this file.

#### xmlMetadata
* expression: celerio-maven-plugin.xml.metadata
* default value: ${basedir}/src/main/config/celerio-maven-plugin/metadata.xml
* description: The relative path to the metadata.xml file produced by the dbmetadata-maven-plugin:extract-metadata goal.
If this file exists it will be used, otherwise Celerio will access the database directly.
The main purpose of this file is to speed-up the generation process, as for large database schema the reverse engineering takes time. An other very
important benefit of this feature is to store the file in your source control, thus having a reproducible build.

#### outputDirectory
* expression: celerio-maven-plugin.outputDir
* default value: ${basedir}
* description: set the base folder for generated files. It could be for example set to `target` if you want to clearly separate the generated files 
from all the manually written files.

## Simple Usage

In your pom.xml create a dedicated profile to execute the plugin, then run:

	mvn -Pgen generate-sources

Here is the `gen` profile detail:

{% highlight xml %}
	<profile>
		<!-- ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ -->
		<!-- Generate the code using Celerio -->
		<!-- ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ -->
		<id>gen</id>
		<build>
			<defaultGoal>generate-sources</defaultGoal>
			<plugins>
				<plugin>
					<groupId>com.jaxio.celerio</groupId>
					<artifactId>celerio-maven-plugin</artifactId>
					<version>${celerio-maven-plugin.version}</version>
					<executions>
						<execution>
							<id>Generates files using the extracted database schema.</id>
							<goals>
								<goal>generate</goal>
							</goals>
						</execution>
					</executions>
					<dependencies>
						<dependency>
							<groupId>com.jaxio.celerio.packs</groupId>
							<artifactId>pack-backend-jpa</artifactId>
							<version>1.0.0</version>
						</dependency>
					</dependencies>
				</plugin>
			</plugins>
		</build>
	</profile>
{% endhighlight %}

## Advanced: Where to generate ?

By default, the files that Celerio generate are rooted in the folder where your pom.xml resides (the Maven ${baseDir} folder).

You can configure it to be a different folder using the `${celerio-maven-plugin.outputDir}` expression.

> **Note**
> The location of a generated file is defined in the generation template relatively to the Celerio outputDir.

The location of the generated Java file depend on the outputDir.

When Celerio outputDir is the same as Maven baseDir, the Java files are generated under:

* ${celerio-maven-plugin.outputDir}/src/main/generated-java
* ${celerio-maven-plugin.outputDir}/src/test/generated-java

Whereas when Celerio outputDir is different from Maven baseDir, the Java files are generate under:

* ${celerio-maven-plugin.outputDir}/src/main/java
* ${celerio-maven-plugin.outputDir}/src/test/java
