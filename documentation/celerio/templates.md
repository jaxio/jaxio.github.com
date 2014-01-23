---
layout: english
title: Celerio Guide - Writing templates
---

Writing Celerio Templates
=========================

* [Introduction](#intro)
* [Learn by example](#learn-by-example)
* [Templates folder](#templates-folder)
* [Create a templates pack](#create-templates-pack)
* [Template name conventions](#template-name-conventions)
	* [Bootstrap template](#bootstrap-template)
	* [Per entity template](#entity-template)
	* [Per project template](#project-template)
	* [Per enum template](#enum-template)
	* [Per composite primary key template](#cpk-template)
	* [Static files](#static-files)
* [Template context](#template-context)
* [Entity namers](#entity-namers)
* [Create an entity namer](#create-entity-namer)
* [Celerio SPI](#celerio-spi)

<a name="intro"></a>
Introduction
------------

Celerio code generation templates are written in [Velocity](http://velocity.apache.org/engine/releases/velocity-1.7/user-guide.html).

Celerio can either execute templates packaged in a jar file or templates that are present in a regular folder.

Jaxio provides some ready-to-use generation templates. These templates are packaged in template packs.

A template pack is a simple jar. To be executed by Celerio this jar file must be explicitly added
as a Celerio's dependency when running Celerio.

Jaxio provides various template packs with Celerio. New template packs can be
written either by Jaxio's consultants or Celerio's end users.

<a name="learn-by-example"></a>
Learn by example
----------------

The simplest way to get started with Celerio templates is to modify existing templates.

Templates source code is available directly by simply un-jarring the templates pack jar file
or if the pack is encrypted (legacy) by simply downloading the associated jar file sources.

<a name="templates-folder"></a>
Templates folder
----------------
Before writing templates please update your Celerio configuration in order to specify your templates folder location.

In celerio-templates-packs.xml declare your Celerio template source folder using the template pack element.
For example:

{% highlight xml %}
<packs>
	<!-- ... other packs skipped... -->
	<pack name="pack-custom" path="src/main/celerio" enable="true" />
</packs>
{% endhighlight %}

In the above example, Celerio considers any file present under 'src/main/celerio' and its subfolder as a Celerio template.

<a name="create-templates-pack"></a>
Create a templates pack
-----------------------

A template pack is simply a set of Celerio templates packaged in a jar file.
The project must have the following structure:   

	pom.xml
	src
	   |- main
	          |- resources
	                      |- META-INF
	                      |          |- celerio.txt
	                      |       
	                      |- celerio
	                                |- bootstrap
	                                |           |- pom.boot.vm.xml
	                                |           |- anytemplate.boot.vm.xml
	                                |- packName
	                                           |- **/yourtemplate.e.vm.java

The celerio.txt file contains some meta information that Celerio engine and the Celerio maven-bootstrap-plugin use.
Here is a celerio.txt file example:

	packName=pack-${project.artifactId}
	packCrypted=false
	packDescription=Java EE backend pack: almost pure JPA 2 with Hibernate 4. No Spring at all. CDI.
	packDescription2=Some more info
	packCommand=mvn -Ph2,db,metadata,gen test

It is important that the packName starts with the prefix `pack-`.

<a name="template-name-conventions"></a>
Template name conventions
-------------------------

There are several kinds of template:

<a name="bootstrap-template"></a>
### Bootstrap templates

A _bootstrap template_ is interpreted only when Celerio is run in _bootstrap mode_.
The _bootstrap mode_ is active when you use the Celerio's maven-bootstrap-plugin.

Its name must have this form: `TemplateName.boot.vm.ext` where:

* `TemplateName` is a logical name for your template. It is not used by Celerio.
* `boot` stands for bootstrap
* `vm` means the templates is written in Velocity.
* `ext` should be the same as the extension of the generated file. It is used by Celerio.

Example: `pom.boot.vm.xml`

<a name="entity-template"></a>
### Per entity template

A 'per entity template' is interpreted for each entity.
Its name must have this form: `TemplateName.e.vm.ext` where:

* `e` stands for entity.

Example: `Controller.e.vm.java`

<a name="project-template"></a>
### Per projet template

A 'per project template' is interpreted one time per projet.
Its name must have this form: `TemplateName.p.vm.ext` where:

* `p` stands for project.

Example: `web.p.vm.xml`

<a name="enum-template"></a>
### Per enum template

A 'per enum template' is interpreted for each enum.
Its name must have this form: `TemplateName.enum.vm.ext`

* `enum` stands for enum.

Example: `EnumController.enum.vm.java`

<a name="cpk-template"></a>
### Per composite primary key template

A 'per composite primary key template' is interpreted for each composite primary key.
Its name must have this form: `TemplateName.cpk.vm.ext` where:

* `cpk` stands for composite primary key

Example: `CompositePkBridge.cpk.vm.java`

<a name="static-files"></a>
### Static files

Static files are not interpreted, they are just copied as is by Celerio.

Exemple: `afolder/img.gif` would be copied to `yourProjectRootFolder/afolder/img.gif` 

<a name="template-context"></a>
Template context
----------------

The template context is the velocity execution context. It exposes your project metamodel and various facilities.  

Here is the list of object variables present in the template context:

<a name="api-output"></a>
### output

Full javadoc: [output - TemplateExcecution](/documentation/celerio-api/com/jaxio/celerio/template/TemplateExecution.html)

The `output` has several roles:

#### `output` controls where to write the result of the template evaluation

At the beginning of your template, use `output` to specify where to write the generated file.
For example:

	$output.java($Root,"MyClass")

Writes the result of the template evaluation to _src/main/java/your-root-package/MyClass.java_

> _** Note **: Check the full list of [GeneratedPackage](/documentation/celerio-api/com/jaxio/celerio/convention/GeneratedPackage.html), that you can pass as the first argument of $output.java_ 

	$output.java($WebSecurity, "LoginForm")##

Writes the result of the template evaluation to _src/main/java/your-root-package/web/security/LoginForm.java_

> _** Note **: the class name is available using $output.currentClass_

#### `output` manages Java imports

One of the challenge of writing a java template is to manage the Java imports.
You have to make sure that you do not import twice the same class or that you do not import useless classes.

`$output.require` or `$output.requireStatic` methods help you in this task.

	$output.require("org.apache.shiro.SecurityUtils")##

Make sure "import org.apache.shiro.SecurityUtils;" is present in the generated file. 

Note that you can use $output.require conditionally, anywhere in your template code, for example:

	#if(something)
	$output.requireStatic("com.google.common.collect.Lists.newArrayList")
	$output.require("java.util.List")
	#end

In case 'something' evals to true, the 2 import statements will be properly inserted in your 
generated java file, not in the middle of a java method of course.

#### `output` provides helper for annotation and Java imports

	$output.dynamicAnnotation("com.company.project.MyAnnotation")

returns _@MyAnnotation_ and ensure _import com.company.project.MyAnnotation;_ statement is properly generated. 

### project

The `project` is the metamodel root. The project contains all the entities.

Full javadoc: [project - Project](/documentation/celerio-api/com/jaxio/celerio/model/Project.html)

### entity

The `entity` references the current entity when working with per entity or composite pk templates

Full javadoc: [entity - Entity](/documentation/celerio-api/com/jaxio/celerio/model/Entity.html)

** Root and primary key **  

* $entity.root returns the root entity or the entity itself.
* $entity.root.primaryKey returns the entity's [PrimaryKey](/documentation/celerio-api/com/jaxio/celerio/model/PrimaryKey.html).

** Lists ** 

The entity gives you access to various kind of lists. Lists are often encapsulated in 
a [SimpleListHolder](/documentation/celerio-api/com/jaxio/celerio/util/SimpleListHolder.html) or a 
[CurrentAndFlatListHolder](/documentation/celerio-api/com/jaxio/celerio/util/CurrentAndFlatListHolder.html).

The list contains mainly the following types:

* [Attribute](/documentation/celerio-api/com/jaxio/celerio/model/Attribute.html)
* [Relation](/documentation/celerio-api/com/jaxio/celerio/model/Relation.html)
* [Entity](/documentation/celerio-api/com/jaxio/celerio/model/Entity.html)

** namers **

The entity provides getters to predefined [Namer](/documentation/celerio-api/com/jaxio/celerio/support/Namer.html).
see section below.

### enum

The `enum` references the current enum when working with per enum template

Full javadoc: [enum - EnumType](/documentation/celerio-api/com/jaxio/celerio/model/EnumType.html)

** example: **

	public class $output.currentClass extends GenericEnumController<${enum.model.type}> { ...

<a name="entity-namers"></a>
### Entity's Namers

Full javadoc: [Namer](/documentation/celerio-api/com/jaxio/celerio/support/Namer.html)

An entity namer allows you to construct easily some var/method/class name that are derived from the current entity name.
The entity object, has several built-in namers but other namers can be created using Celerio configuration.

One of the most used namer is the `model` namer. Assuming that the current entity full name is com.example.domain.Bank here are the various value returned when invoking
the namer's methods:

* $entity.model.var: bank 
* $entity.model.type: Bank
* $entity.model.fullType: com.example.Bank
* $entity.model.packageName: com.example  
* $entity.model.path: com/example
* $entity.model.varUp: Bank
* $entity.model.vars: banks
* $entity.model.varsUp: Banks
* $entity.model.adder: addBank
* $entity.model.adders: addBanks
* $entity.model.contains: containsBank
* $entity.model.getter: getBank
* $entity.model.getters: getBanks
* $entity.model.remover: removeBank
* $entity.model.removers: removeBanks
* $entity.model.setter: setBank
* $entity.model.setters: setBanks
* $entity.model.editer: editBank
* $entity.model.has: isBankSet

Another commonly used namer is the repository namer, here are more returned value example:

* $entity.repository.var: bankRepository
* $entity.repository.type: BankRepository
* $entity.repository.fullType: com.example.repository.BankRepository
* $entity.repository.getter: getBankRepository
* etc.

By default Celerio has some built-in namers. Theses can be used from your entity templates:

* model: namer for the entity itself
* repository
* controller
* etc..

<a name="create-entity-namer"></a>
### Create an entity namer

**Let's assume that:**

* you want to create for each entity an Access Control List class.
* each ACL class should be named EntityNameAcl
* each class should be located under the security sub package

**Here is the content of the entityAcl.e.vm.java template that you may create:**

	$output.java($entity.acl)##
	
	public class $output.currentClass {
	    // var: $entity.acl.var
	    // type: $entity.acl.type
	    // etc...    
	}

The 'acl' entity's property implements Celerio's `Namer` Interface.
The `Namer` interface exposes simple getter methods to access var name, getter name, etc...

Before running Celerio with the above template, you must first create the 'acl' Namer.
To do so, open the celerio-template-packs.xml file and add the following `celerioTemplateContext` element:

{% highlight xml %}
<packs>
	<celerioTemplateContext>
		<entityContextProperties>
			<entityContextProperty property="acl" subPackage="security" suffix="Acl"/>
		</entityContextProperties>			
	</celerioTemplateContext>

	<!-- ... other packs skipped... -->
	<pack name="pack-custom" path="src/main/celerio" enable="true" />
</packs>
{% endhighlight %}

<a name="celerio-spi"></a>
### Celerio Service Provider Interface (SPI)

Celerio exposes the following SPIs:

* [ProjectSpi](/documentation/celerio-api/com/jaxio/celerio/spi/ProjectSpi.html)
* [EntitySpi](/documentation/celerio-api/com/jaxio/celerio/spi/EntitySpi.html)
* [AttributeSpi](/documentation/celerio-api/com/jaxio/celerio/spi/AttributeSpi.html)
* [RelationSpi](/documentation/celerio-api/com/jaxio/celerio/spi/RelationSpi.html)

You can create some extension by following [ServiceLoader](http://docs.oracle.com/javase/6/docs/api/java/util/ServiceLoader.html) convention.
Your extension must be packaged in a jar file containing your classes and the expected service files.

	src
	   |- main
	          |- resources
	                      |- META-INF
	                                 |- services
                                                |
                                                - com.jaxio.celerio.spi.ProjectSpi
                                                - com.jaxio.celerio.spi.EntitySpi
                                                - com.jaxio.celerio.spi.AttributeSpi                                                
                                                - com.jaxio.celerio.spi.RelationSpi                                                

You extension implementation must be listed in one of the file above.

Assuming you have created an entity extension whose full type is _com.jaxio.celerio.spi.example.ExampleEntity_,
the content of the _com.jaxio.celerio.spi.EntitySpi_ file must be: 

	com.jaxio.celerio.spi.example.ExampleEntity

Here is our dummy ExampleEntity extension source code:

{% highlight java %}
package com.jaxio.celerio.spi.example;

import com.jaxio.celerio.model.Entity;
import com.jaxio.celerio.spi.EntitySpi;

public class ExampleEntity implements EntitySpi {

    private Entity entity;
    
    @Override
    public void init(Entity entity) {
        this.entity = entity;
    }

    @Override
    public String velocityVar() {
        return "example";
    }

    public String getHello() {
        return "Hello from ExampleEntity: this entity has " + entity.getCurrentAttributes().size() + " attributes";
    }
}
{% endhighlight %}

To activate it and be able to use it through `$entity.example` and invoke `$entity.example.hello` from your templates, 
add the extension's jar as a dependency when running maven-celerio-plugin, for example:

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
						<artifactId>maven-celerio-plugin</artifactId>
						<version>${maven-celerio-plugin.version}</version>
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
								<artifactId>backend-jpa</artifactId>
								<version>${maven-celerio-plugin.version}</version>
							</dependency>
							<dependency>
								<groupId>com.yourcompany.celerio</groupId>
								<artifactId>my-celerio-extension</artifactId>
								<version>1.0.0</version>
							</dependency>
						</dependencies>
					</plugin>
				</plugins>
			</build>
{% endhighlight %}

