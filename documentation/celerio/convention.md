---
layout: refdoc
title: Celerio Guide - Conventions 
---
[ << prev ](modification.html) [ index ](index.html) [ next >> ](configuration-file.html)

# 9. Conventions and integration

Celerio has some built-in conventions. When these conventions are
followed, Celerio generates some specific features.

For example, by simply following some columns naming
convention, you can rely on Celerio to generate all the infrastructure
code and configuration that will allow you to handle file upload and
download in your web application, in an optimal way.

* [Primary key conventions](#primary-key-conventions)
	* [Numerical Primary Keys](#numerical-primary-keys)
	* [Primary Keys with 32 characters](#primary-keys-with-32-characters)
	* [Primary Keys with 36 characters](#primary-keys-with-36-characters)
	* [Other Primary Keys](#other-primary-keys)
* [Spring Security Integration](#spring-security-integration)
	* [Account Entity](#account-entity)
	* [Role entity](#role-entity)
* [ACCOUNT_ID column & Hibernate Filter](#accountid-column--hibernate-filter)
* [Version column and optimistic locking](#version-column-and-optimistic-locking)
* [Many to many and inverse attribute](#many-to-many-and-inverse-attribute)
* [File Upload and Download](#file-upload-and-download)
* [Audit in entity](#audit-in-entity)
* [Audit log table](#audit-log-table)
* [Saved search table](#saved-search-table)


## Primary key conventions

### Numerical Primary Keys

Each numerical primary key column is mapped with `@GeneratedValue` and
`@Id` annotations.

> **Important**
>
> If your database does not support identity columns, you should create
> the sequence `hibernate_sequence`. Please refer to Hibernate
> reference documentation for more advanced alternatives.

### Primary Keys with 32 characters

By convention, for all primary keys that are `char(32)`, Celerio maps the
column with the following annotations

{% highlight java %}

@GeneratedValue(generator = "strategy-uuid")
@GenericGenerator(name = "strategy-uuid", strategy = "uuid")
@Id

{% endhighlight %}

As it uses Hibernate's `UUIDHexGenerator`, no sequence is needed for these primary keys.

### Primary Keys with 36 characters

By convention, for all primary keys that are `char(36)`, Celerio maps the
column with the following annotations

{% highlight java %}

@GeneratedValue(generator = "strategy-uuid2")
@GenericGenerator(name = "strategy-uuid2", strategy = "uuid2")
@Id

{% endhighlight %}

As it uses Hibernate's `UUIDGenerator`, no sequence is needed for these primary keys.

### Other Primary Keys

For primary key that are `char(x)` where x is different from 32 or 36, Celerio
map the column with an `assigned` generator, which means you must
provide manually the primary key value.


## Spring Security Integration

The generated `UserDetailsServiceImpl` service is the link between the SpringSecurity's world
and your user's credential information. As you expect, this class must know how to
access the user's login and password.

The generated implementation of this service varies depending on whether you follow
or not Celerio's conventions.

If your login credential are stored in your database, then you should make sure that the entity 
playing the role of the `account` entity has the properties expected by Celerio (e.g. login &amp; password).

Once Celerio has found your account entity, it looks up the account entity's many to many associations.
If it finds a many to many association whose target entity is also playing the role
of a `Role` entity (e.g. it has an 'authority' property), then it uses it.

> **Note**
>
> The 'Account' and the 'Role' entities do not need to be named Account and Role.

### Account entity

By convention, Celerio considers that an entity is the `Account` entity if it has the following properties:

<table class="table table-bordered table-striped">
  <thead>
    <tr>
      <th>Expected property</th>
      <th>Type</th>
      <th>Required?</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
  <tr>
    <td>username, login, userName, identifiant, email, emailAddress, mail</td>
    <td>String</td>
    <td>Yes</td>
    <td>Login used by the end user to authenticate to this web application</td>
  </tr>
  <tr>
    <td>password, pwd, passwd, motDePasse</td>
    <td>String</td>
    <td>Yes</td>    
    <td>Password (in clear) used by the end user to authenticate to this web application</td>
  </tr>
  <tr>
    <td>enabled, isEnabled</td>
    <td>Boolean</td>
    <td>No</td>    
    <td>Only enabled users (enabled == true) can login.</td>
  </tr>
  </tbody>
</table>

Bear in mind that you can match this convention using Celerio configuration.
Let's assume that your users' credentials are stored in a table named
User that has 2 columns user_login and user_password.

Such a table would not be considered by default as the account table.

To have Celerio consider this table as the account table, you must map the 
user_login and user_password to one of the expected properties, for example:

{% highlight sql %}
<entityConfig tableName="USER">
	<columnConfigs>
		<columnConfig columnName="user_login" fieldName="username" />
		<columnConfig columnName="user_password" fieldName="password" />
	</columnConfigs>
</entityConfig>	
{% endhighlight %}

> **Note**
>
> If no `Account` entity is found, the `UserDetailsServiceImpl` simply returns hard coded users and roles.


### Role entity

By convention, Celerio considers that an entity is the `Role` entity if it has the following property:

<table class="table table-bordered table-striped">
  <thead>
    <tr>
      <th>Expected property</th>
      <th>Type</th>
      <th>Required</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>  
  <tr>
    <td>authority, roleName, role, nameLocale</td>
    <td>String</td>
    <td>Yes</td>
    <td>The role name for example: ROLE_USER, ROLE_ADMIN</td>
   </tr>
  </tbody>   
</table>

By default, the configuration file generated by Celerio expects the content
of the 'authority' column (or one of its variants) to match one of these role names:

* ROLE_USER
* ROLE_ADMIN

This is just a convention coming from Security's official documentation, if it does not suit your needs,
you can change it manually in the files that uses these role names.


Here is a sample SQL script (H2 Database) that complies to Celerio conventions

{% highlight sql %}

CREATE TABLE ACCOUNT (
    account_id char(32) not null, 
    login varchar(255) not null,
    password varchar(255) not null,
    email varchar(255) not null,

    constraint account_unique_1 unique (login),
    constraint account_unique_2 unique (email),
    primary key (account_id)
);

CREATE TABLE ROLE (
    role_id smallint generated by default as identity,
    name_locale varchar(255) not null,

    constraint role_unique_1 unique (name_locale),
    primary key (role_id) 
);

CREATE TABLE ACCOUNT_ROLE (
    account_id char(32) not null,
    role_id smallint not null,

    constraint account_role_fk_1 foreign key (account_id) references ACCOUNT,
    constraint account_role_fk_2 foreign key (role_id) references ROLE,
    primary key (account_id, role_id) 
);

{% endhighlight %}

## ACCOUNT_ID column & Hibernate Filter

When a table contains a foreign key pointing to the `Account` table,
Celerio assumes that the content of this table belongs to the user
represented by the `account_id` foreign key.

An hibernate filter is configured to make sure that this table is loaded
only by the current user.

The filter is enabled by the `HibernateFilterInterceptor`. Of course
this default behavior may not always suit your needs. There are two ways
of disabling it:

1.  Remove the `@Filter` annotation from the Entity. This imply taking
    control over the entity.

2.  Disable the filter programmatically using the HibernateFilterContext
    generated helper.

3.  Disable globally this convention in Celerio's configuration file.

## Version column and Optimistic Locking

If your table has a column named `VERSION` whose type is compatible with the JPA `@Version` annotation, 
then by convention Celerio assumes that you want to enable an optimistic locking strategy.

As a result, the corresponding property is annotated with `@Version`.

You can disable this convention in the configuration using columnConfig's version attribute.

## Many to many and inverse attribute

Which side of the @ManyToMany relation is marked as `inverse="true"` ? 

By convention, the side whose corresponding column's order is the highest on the 'middle table'.
You can override this convention using the parent's columnConfig's 'inverse' attribute. 

## File Upload and Download

When the following properties are present simultaneously in an entity, 
Celerio generates various helper methods to ease file manipulation from the
web tier to the persistence layer.

<table class="table table-bordered table-striped">
  <thead>
    <tr>
      <th>Properties</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
  <tr>
    <td><i>prefix</i>FileName</td>
    <td>Boolean</td>
    <td>File name</td>
  </tr>
  <tr>
    <td><i>prefix</i>Size or <i>prefix</i>Length or <i>prefix</i>ContentLength</td>
    <td>Int</td>
    <td>Length of the binary</td>
  </tr>
  <tr>
    <td><i>prefix</i>ContentType</td>
    <td>String</td>
    <td>Content type of the file</td>
  </tr>
  <tr>
    <td><i>prefix</i>Binary or <i>prefix</i>Content or <i>prefix</i>Blob</td>
    <td>Blob</td>
    <td>Binary content of the file</td>
  </tr>
  </tbody>
</table>

Example: Here is the corresponding SQL code using `mydoc` as a prefix

{% highlight sql %}

mydoc_content_type      varchar(255)    not null,
mydoc_size              integer         not null,
mydoc_file_name         varchar(255)    not null,
mydoc_binary            bytea,

{% endhighlight %}

With the column names above, the property names calculated by convention by Celerio match exactly 
the properties required to activate the file upload/download feature. 

This convention allows you to upload a file transparently, 
save it to the corresponding table, then download it using a simple URL.

## Audit in entity

For a given entity, it is often desirable to know:

* when this entity was created and who created it
* the last time this entity was updated and who updated it

With JPA this feature can be implemented using `@PreUpdate` and `@PrePersist` annotations in the entity itself.

By convention, Celerio activates this feature if *any* of the following properties is present in an entity.

<table class="table table-bordered table-striped">
  <thead>
    <tr>
      <th>Description</th>
      <th>Type</th>
      <th>Required properties</th>
    </tr>
  </thead>
  <tbody>
  <tr>
    <td>Creation author</td>
    <td>String</td>
    <td>creationAuthor or creationBy or creePar</td>
  </tr>
  <tr>
    <td>Creation date</td>
    <td>Date</td>
    <td>creationDate or dateCreation</td>
  </tr>
  <tr>
    <td>Last modification author</td>
    <td>String</td>
    <td>lastModificationAuthor or lastModificationAt or derniereModificationPar or modifiePar</td>
  </tr>
  <tr>
    <td>Last modification date</td>
    <td>Date</td>
    <td>lastModificationDate or dateDerniereModification or derniereModification</td>
  </tr>
  </tbody>
</table>

**Example:**

{% highlight sql %}

creation_date            timestamp,
creation_author          varchar(200),
last_modification_date   timestamp,
last_modification_author varchar(200),

{% endhighlight %}

With the column names above, the property names calculated by convention by Celerio match exactly 
the properties required to activate the entity auditing feature. 
 
In case your column names are slightly different, you can set the `fieldName` attribute in the corresponding 
column's columnConfig, and the feature will be activated. For example:

{% highlight xml %}
<columnConfig columnName="last_update" fieldName="lastModificationDate"/>
{% endhighlight %}

Here is how looks the Java code in the corresponding entity:

{% highlight java %}
    @PrePersist
    protected void prePersist() {
        if (AuditContextHolder.audit()) {
            setCreationAuthor(AuditContextHolder.username());
            setCreationDate(new Date());
        }
    }

    @PreUpdate
    protected void preUpdate() {
        if (AuditContextHolder.audit()) {
            setLastModificationAuthor(AuditContextHolder.username());
            setLastModificationDate(new Date());
        }
    }
{% endhighlight %}


## Audit log table

The simple auditing feature allows you to track for each entity creation/modifications/deletion events and store them in a
dedicated table. More generally this feature allow you to save any relevant event.
 
The audit log is stored in a single table. Its implementation uses JPA listeners.

This feature is simple in the sense that it does not rely on a framework such as Envers, which can be complex to apprehend.
Instead, the implementation is simple and probably satisfactory for 90% of the cases.

By convention, Celerio generates the source code that implements this feature if the following conditions are met:

An audit entity must be present. Its name must be either:

* Audit
* AuditLog
* AuditTrail

This audit entity must have the following properties:

<table class="table table-bordered table-striped">
  <thead>
    <tr>
      <th>Required properties</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>  
  <tr>
    <td>author, auteur</td>
    <td>String</td>
    <td>Author</td>
  </tr>
  <tr>
    <td>event</td>
    <td>String</td>
    <td>Event type</td>
  </tr>
  <tr>
    <td>stringAttribute1, attribute1, string1</td>
    <td>String</td>
    <td>First attribute</td>
  </tr>
  <tr>
    <td>stringAttribute2, attribute2, string2</td>
    <td>String</td>
    <td>Second attribute</td>
  </tr>
  <tr>
    <td>stringAttribute3, attribute3, string3</td>
    <td>String</td>
    <td>Third attribute</td>
  </tr>
  </tbody>
</table>

> For entity related event:
>
> * event is either Creation, Modification, Deletion
> * First attribute is used to store the entity name
> * Second attribute is used to store the entity id
> * Third attribute is used to store a description of the modifications
>

**SQL Example:**

{% highlight sql %}

CREATE TABLE AUDIT_LOG (
    id                 int not null IDENTITY,
    author             varchar(256),
    event              varchar(256),
    event_date         timestamp,
    string_attribute_1 varchar(256),
    string_attribute_2 varchar(256),
    string_attribute_3 varchar(256),
    primary key (id)
);

{% endhighlight %}

With the column names above, the property names calculated by convention by Celerio match exactly 
the properties required to activate the simple database auditing feature. 
 
In case your column names are slightly different, you can set the fieldName attribute in the corresponding 
column's columnConfig, and the feature will be activated. For example:

{% highlight xml %}
<columnConfig columnName="event_type" fieldName="event"/>
{% endhighlight %}

## Saved search table

When the following columns are present simultaneously in a
table, Celerio will use these columns to propose the user to save its searches

The table should be called either `saved_search` or `saved_search_form`

The table should contain the following columns

<table class="table table-bordered table-striped">
  <thead>
    <tr>
      <th>Property</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
  <tr>
    <td>formClass, formClassname</td>
    <td>String</td>
    <td>Fully qualified name of the search form type</td>
  </tr>
  <tr>
    <td>name</td>
    <td>String</td>
    <td>Name given to the saved search</td>
  </tr>
  <tr>
    <td>formContent</td>
    <td>byte[]</td>
    <td>Serialized form of the search</td>
  </tr>
  <tr>
    <td>accountId</td>
    <td>String</td>
    <td>FK linked to Account (not required)</td>
  </tr>
  </tbody>  
</table>

Example: 

{% highlight sql %}

CREATE TABLE SAVED_SEARCH (
    id                            int not null IDENTITY,
    name                          varchar(128) not null,
    form_classname                varchar(256) not null,
    form_content                  bytea,
    account_id                    char(36) not null,

    constraint saved_search_fk_1 foreign key (account_id) references ACCOUNT,
    primary key (id)
);

{% endhighlight %}
