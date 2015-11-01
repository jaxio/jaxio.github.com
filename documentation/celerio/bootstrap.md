---
layout: refdoc
title: Celerio Guide - Bootstrapping a project
---
[ << prev ](installation.html) [ index ](index.html) [ next >> ](extraction.html)

3. Bootstrapping a project with Celerio
=======================================

Bootstrap the skeleton
----------------------

To create a new project skeleton, invoke the `bootstrap` goal of the `bootstrap-maven-plugin`.

But before doing so, make sure you have defined the *celerio* profile in your settings.xml file
[as explained here](installation.html#requirements-settings).

Then from a command line executes:

	mvn -Pcelerio com.jaxio.celerio:bootstrap-maven-plugin:4.0.0:bootstrap

Then follow the instructions...

Once done, Celerio prints on the console the command you should run (don't run it yet), for example:

    [INFO] ============================================
    [INFO]  PLEASE EXECUTE THE FOLLOWING COMMANDS:

    cd myapp
    mvn -Ph2,db,metadata,gen jetty:run

    [INFO]  Then open the page http://localhost:8080/

Before running the commands above, you can check which files the bootstrap has generated or copied.
In general it includes:

* `pom.xml` : A complete Maven POM file.
* `src/main/sql/h2/01-create.sql` : an SQL script to create, init and
  drop a local H2 database. This is an example database that Celerio is going to reverse.
* `src/main/config/celerio-maven-plugin/celerio-maven-plugin.xml` : A Celerio's configuration file.
* `src/main/config/celerio-maven-plugin/celerio-template-packs.xml` : Another Celerio's configuration file.

You can now either run the commands as is, or modify the generated `pom.xml` file to use your own database.

Reverse, generate and run
-------------------------

You are now ready to:

* create the database out of the sample SQL script provided,
* reverse this database schema and generate the corresponding source code and configuration files,
* build the generated project,
* and run the resulting web application (assuming you selected a web app...)

To do so, from your project folder execute the Maven command printed on the console, for example:

    cd myapp
    mvn –Ph2,db,metadata,gen jetty:run

> **Note**
>
> Please note that this command may take a while to complete the first time as Maven needs to download all
> the necessary plugins and dependencies to build and run the project.

The `-P` option stands for *profile*. In the above command, we activate:

* `h2` profile to use an H2 database and driver
* `db` profile to create a new database,
* `metadata` profile to reverse it
* `gen` profile to generate the corresponding source code.

All these profiles are declared in the main `pom.xml` file. The `jetty:run` starts an embedded web server.

Now you can try out your new web application. Open [http://localhost:8080/](http://localhost:8080/) in your browser.
Can you log in as admin/admin? If yes, congratulations!, all went well...

Unit Test
---------

Celerio also generates some unit tests. To make sure they all pass, simply run:

    myapp>mvn test

As you can notice here, we have not set any profile since there is no need to re-extract the database meta data
or re-generate the source code.

Import in Eclipse
-----------------

> **Note**
>
> We assume here that you have already configured Eclipse's Maven
> plugin. If not please refer to [http://m2eclipse.sonatype.org/](http://m2eclipse.sonatype.org/).

You can now import your first generated project into Eclipse. Make sure to import it as an *existing Maven project*.

![Eclipse Maven - Import as Maven
project](images/celerio-eclipse-import-as-maven-projet.png)

> **Important**
>
> Once imported please add manually to your build path the following
> Java folders
>
> -   `src/main/generated-java`
>
> -   `src/test/generated-java`
>
> To do so, from Eclipse, simply right-click on each folder and select
> the menu 'Build Path' -\> 'Use as source folder'.

Run Celerio from Eclipse
------------------------

Since Celerio is a Maven plugin, you can invoke it as any other Maven plugin from Eclipse.
Right-click on your project and select the 'Run As' -\> 'Run Configurations...' menu.
From there you can set the Maven command to invoke and save this setting in a `.launch` file at the root
of your project. This 'Run configuration' will appear in your top level menu.
