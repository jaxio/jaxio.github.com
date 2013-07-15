---
layout: doc
title: Celerio Guide - Writing templates
---

Writing Celerio Templates
=========================

* [Introduction](#intro)
* [Learn by example](#learn-by-example)
* [Templates folder](#templates-folder)
* [Template name conventions](#template-name-conventions)
* [Create a new template](#create-new-template)
* [Entity namers](#entity-namers)
* [Create a new templates pack](#create-templates-pack)


<a name="intro"></a>
Introduction
------------

Celerio code generation templates are written in Velocity.

Celerio can either execute templates packaged in a jar file or templates that are present in a regular folder.

Jaxio provides some ready-to-use generation templates.
These templates are packaged in template packs.

A template pack is a simple jar. To be executed by Celerio this jar file must be explicitly added
as a Celerio's dependency when running Celerio.

Jaxio provides various template packs with Celerio. New template packs can be
written either by Jaxio's consultants or Celerio's end users.

<a name="learn-by-example"></a>
Learn by example
----------------

The simplest way to get started with Celerio templates is to modify existing templates.

Templates source code is available directly by simply unjarring the templates pack jar file
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
  
<a name="template-name-conventions"></a>
Template name conventions
-------------------------

There are several kinds of template:

### Bootstrap templates

A 'bootstrap template' is interpreted only when Celerio is run in 'bootstrap' mode.
The bootstrap mode is active when you use the Celerio's maven-bootstrap-plugin.

Its name must have this form: `TemplateName.boot.vm.ext` where:

* TemplateName is a logical name for your template. It is not used by Celerio.
* boot stands for bootstrap
* vm means the templates is written in Velocity.
* ext is your file extension. It is used by Celerio.

Example: `pom.boot.vm.xml`

### Per entity template

A 'per entity template' is interpreted for each entity.
Its name must have this form: `TemplateName.e.vm.ext` where:

* TemplateName is a logical name for your template. It is not used by Celerio.
* e stands for entity.
* vm means the templates is written in Velocity.
* ext is your file extension. It is used by Celerio.

Example: `Controller.e.vm.java`

### Per projet template

A 'per project template' is interpreted one time per projet.
Its name must have this form: `TemplateName.p.vm.ext` where:

* p stands for project.

### Per enum template

A 'per enum template' is interpreted for each enum.
Its name must have this form: `TemplateName.enum.vm.ext`

### Per composite primary key template

A 'per composite primary key template' is interpreted for each composite primary key.
Its name must have this form: `TemplateName.cpk.vm.ext` where:

* cpk stands for composite primary key

### Static files

Static files are not interpreted, they are just copied as is by Celerio.

Exemple: `afolder/img.gif` would be copied to `yourProjectRootFolder/afolder/img.gif` 

<a name="create-new-template"></a>
Create a new Template
----------------------

### Writing a new 'per entity' Java template

**Let's assume that:**

* you want to create for each entity an Access Control List class.
* each ACL class should be named EntityNameAcl
* each class should be located under the security sub package

**Here is the template code:**

{% highlight java %}
$output.java($entity.acl)##

public class $output.currentClass {
    // var: $entity.acl.var
    // type: $entity.acl.type
    // fullType: $entity.acl.fullType
    // package: $entity.acl.packageName
	// type: $entity.acl.type
	// path: $entity.acl.path
	// packageName: $entity.acl.packageName
	// fullType: $entity.acl.fullType
	// var: $entity.acl.var
	// varUp: $entity.acl.varUp
	// vars: $entity.acl.vars
	// varsUp: $entity.acl.varsUp
	// adder: $entity.acl.adder
	// adders: $entity.acl.adders
	// contains: $entity.acl.contains
	// getter: $entity.acl.getter
	// getters: $entity.acl.getters
	// remover: $entity.acl.remover
	// removers: $entity.acl.removers
	// setter: $entity.acl.setter
	// setters: $entity.acl.setters
	// editer: $entity.acl.editer
}
{% endhighlight %}

The 'acl' entity's property implements Celerio's `Namer` Interface.
The `Namer` interface exposes simple getter methods to access var name, getter name, etc...

Before running Celerio with the above template, you must first create the 'acl' Namer.
To do so, open the celerio-templates-packs.xml file and add the following `celerioTemplateContext` element:

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

<a name="entity-namers"></a>
### Entity's Namers

By default Celerio has some built-in namers. Theses can be used from your entity templates:

<table class="table table-bordered table-striped">
	<caption>Entity's namer properties</caption>
	<thead>
	<tr>
		<th>property name</th>
		<th>description</th>
	</tr>
	</thead>
	<tbody>	
	<tr>
		<td>dao</td>
		<td>Namer for the entity Dao Interface</td>
	</tr>
	<tr>
		<td>dao</td>
		<td>Namer for the entity Dao Implementation</td>
	</tr>
	<tr>
		<td>repository</td>
		<td>Namer for the entity Repository Implementation</td>
	</tr>
	<tr>
		<td>controller</td>
		<td>Namer for the entity Controller Implementation</td>
	</tr>
		<td>...</td>
		<td>...</td>
	</tr>
	</tbody>
</table>	

<a name="create-templates-pack"></a>
Create a templates pack
-----------------------

A template pack is simply a set of Celerio templates packaged in a jar file.
The project must have the following structure:   

<pre>
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
</pre>

The celerio.txt file contains some meta information that Celerio engine and the Celerio maven-bootstrap-plugin use.
Here is a celerio.txt file example:

<pre>
packName=pack-${project.artifactId}
packCrypted=false
packDescription=Java EE backend pack: almost pure JPA 2 with Hibernate 4. No Spring at all. CDI.
packDescription2=Some more info
packCommand=mvn -Ph2,db,metadata,gen test
</pre>

It is important that the packName starts with the prefix 'pack-'.
