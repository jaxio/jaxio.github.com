---
layout: refdoc
title: Celerio Guide - Bootstrapping a real project with Celerio
---
[ << prev ](bootstrap.html) [ index ](index.html) [ next >> ](extraction.html)


# 4. Bootstrap a real project with Celerio

## Create a new project (no existing database case)

If you want to create a new project using Celerio, and you have no database yet, simply follow the wizard
as explained in the [previous chapter](bootstrap.html). When you are asked to select a sample database schema, select the
simplest one (named `empty`).

	mvn com.jaxio.celerio:bootstrap-maven-plugin:4.0.12:bootstrap

To add new entities, simply edit the sample sql file that was generated in the `src/main/sql/h2` folder
and re-run the command, in general:

    mvn -Pdb,metadata,gen generate-sources

## Create a new project (from an existing database case)

First of all, **DO NOT USE A PRODUCTION DATABASE** You could loose some data.

### Follow the wizard

Follow the wizard as explained in the [previous chapter](bootstrap.html). When you are asked to select a sample
database schema, select the simplest one (named `empty`).

	mvn com.jaxio.celerio:bootstrap-maven-plugin:4.0.12:bootstrap

Once it completes, **do not run any command** beside `cd <your_app>`

### Delete sample sql file

To avoid any surprise, **delete or rename** the sample SQL file that was generated in the `src/main/sql/h2` folder.
We do not want you to execute that script by mistake since you are going to use your own database.

### Edit pom.xml

Open the generated pom.xml file and provide all the information to access your database instead of the
sample H2 database:
{% highlight xml %}

<!-- **** CHANGE THE PROPERTIES BELOW TO USE YOUR OWN DATABASE **** -->
<!-- **** DO NOT USE A PRODUCTION DATABASE **** -->
<jdbc.type>h2</jdbc.type><!-- see 'db' profile, it is used to find the sql script -->

<!-- h2 jdbc driver -->
<jdbc.groupId>com.h2database</jdbc.groupId>
<jdbc.artifactId>h2</jdbc.artifactId>
<jdbc.version>1.4.190</jdbc.version>

<!-- h2 jdbc settings -->
<jdbc.driver>org.h2.Driver</jdbc.driver>
<jdbc.url>jdbc:h2:~/.h2/${database.name};MVCC=TRUE;FILE_LOCK=NO</jdbc.url>
<jdbc.user>${database.user}</jdbc.user>
<jdbc.password>${database.password}</jdbc.password>
<jdbc.catalog></jdbc.catalog>
<hibernate.dialect>org.hibernate.dialect.H2Dialect</hibernate.dialect>
<sql-maven-plugin.delimiter>;</sql-maven-plugin.delimiter>

{% endhighlight %}

**Tips:**

* You may need to [install manually a JDBC driver](miscellaneous.html).
* You may need to grant "Read metadata permissions" to your database user.

### Reverse your database

Then try until it works to reverse your database, by running the following command:

    mvn -Pmetadata generate-sources

When it works, you should get:

    [INFO] Write metadata.xml file to src/main/config/celerio-maven-plugin/metadata.xml
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------

For your own curiosity you may open the generated metadata.xml file... but **DO NOT MODIFY IT**,
it is used by Celerio to generate the source code.

### Generate the source code

Once database reverse works OK you may try to generate the source code

    mvn -Pgen generate-sources

### To continue...

This is only the beginning...
you can customize the generation in 3 ways:

* by changing the [configuration](configuration.html)
* by following some [conventions](convention.html)
* or by creating or modifying existing [code generation templates](templates.html)
