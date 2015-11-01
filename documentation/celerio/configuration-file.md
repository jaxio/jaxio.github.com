---
layout: refdoc
title: Celerio Guide - Configuration File
---

[ << prev ](convention.html) [ index ](index.html) [ next >> ](configuration.html)
9. Configuration File
=====================

Main Configuration File
-----------------------

The main Celerio configuration file is by convention located under
`${basedir}/src/main/config/celerio-maven-plugin/celerio-maven-plugin.xml`.
This configuration file must respect the `celerio.xsd` schema present in the same directory.

> *Note*: The bootstrap goal creates a configuration file that can be used as a starting point.

Splitting the `entityConfigs` tag
---------------------------------

The `entityConfigs` tag can be declared in the main Celerio
configuration file AND/OR in sub-configuration files that are included
in the main configuration file. Note that, for such included files, only
the `entityConfigs` tag is loaded by Celerio. Here is an example:

{% highlight xml %}
<celerio>
 <includes>
  <include filename="celerio-maven-plugin-secondary.xml"/>
 </includes>
 <entityConfigs>
  <entityConfig tableName="BOOK" subPackage="fromMainConfig"/>
 </entityConfigs>                    
</celerio>
{% endhighlight %}

And here is the included file

{% highlight xml %}
<celerio>
 <entityConfigs>
  <entityConfig tableName="ACCOUNT" subPackage="fromIncludeConfig"/>
 </entityConfigs>
</celerio>
{% endhighlight %}

Template pack configuration file
--------------------------------

The template packs can be declared in the main Celerio configuration
file OR in the file
`${basedir}/src/main/config/celerio-maven-plugin/celerio-template-packs.xml`
. When this file is present Celerio loads from it the `packs` tag
instead of loading it from the main Celerio configuration file.

{% highlight xml %}
<celerio>  
 <configuration>
  <packs>
   <pack name="pack-backend" enable="true">
    <filenames><!-- do not generate these files -->
     <filename include="false" pattern="src/main/resources/database.properties" />
     <filename include="false" pattern="src/main/resources/hibernate.properties" />
     <filename include="false" pattern="src/main/resources/log4j.properties" />
     <filename include="false" pattern="src/main/resources/ehcache/local.xml" />
    </filenames>
   </pack>            
  </packs>    
 </configuration>    
</celerio>
{% endhighlight %}

This separated file was mainly introduced for multi-module maven
projects. The various modules (e.g. core and web) shares the same
`celerio-maven-plugin.xml` configuration file but uses their own
`celerio-template-packs.xml` configuration file.
