---
layout: doc
title: Celerio Guide - Conventions 
---

Conventions and integration
===========================

Celerio has some built-in conventions. When these conventions are
followed, Celerio generates some specific features.

For example, by simply following some columns naming
convention, you can rely on Celerio to generate all the infrastructure
code and configuration that will allow you to handle file upload and
download in your web application, in an optimal way.

* [Primary key conventions](#conventions-pk)
	* [Numeric](#conventions-pk-numeric)
	* [32 chars](#conventions-pk-32chars)
	* [36 chars](#conventions-pk-36chars)
	* [Other](#conventions-pk-other)
* [Account table](#conventions-account-table)
	* [Optional columns](#conventions-account-other-columns)		
* [Role table](#conventions-role-table)
* [ACCOUNT_ID column & Hibernate Filter](#conventions-hibernate-filter)
* [Version column and optimistic locking](#conventions-version-optimistic-locking)
* [Many to many and inverse attribute](#conventions-many-to-many-inverse-attribute)
* [File Upload and Download](#conventions-file-download)
* [Audit in entity](#conventions-audit-entity)
* [Audit log table](#conventions-audit-table)
* [Saved search](#conventions-saved-search)


<a name="conventions-pk"></a>
Primary key conventions
-----------------------

<a name="conventions-pk-numeric"></a>
### Numerical Primary Keys

Each numerical primary key column is mapped with `@GeneratedValue` and
`@Id` annotations.

> **Important**
>
> If your database does not support identity columns, you should create
> the sequence `hibernate_sequence`. Please refer to Hibernate
> reference documentation for more advanced alternatives.

<a name="conventions-pk-32chars"></a>
### Primary Keys with 32 characters

By convention, for all primary keys that are `char(32)`, Celerio maps the
column with the following annotations

{% highlight java %}

@GeneratedValue(generator = "strategy-uuid")
@GenericGenerator(name = "strategy-uuid", strategy = "uuid")
@Id

{% endhighlight %}

As it uses Hibernate's `UUIDHexGenerator`, no sequence is needed for these primary keys.

<a name="conventions-pk-36chars"></a>
### Primary Keys with 36 characters

By convention, for all primary keys that are `char(36)`, Celerio maps the
column with the following annotations

{% highlight java %}

@GeneratedValue(generator = "strategy-uuid2")
@GenericGenerator(name = "strategy-uuid2", strategy = "uuid2")
@Id

{% endhighlight %}

As it uses Hibernate's `UUIDGenerator`, no sequence is needed for these primary keys.

<a name="conventions-pk-other"></a>
### Other Primary Keys

For primary key that are `char(x)` where x is different from 32 or 36, Celerio
map the column with an `assigned` generator, which means you must
provide manually the primary key value.

<a name="conventions-account-table"></a>
The Account table
-------------------

The `Account` table is a special table that contains the user's login and
password columns and eventually the email and enabled columns. It has an
important role during the login phase. It is also used by the
`AccountContext` generated class which store the current `account`
information in the current thread.

Celerio detects automatically your `Account` table. An account table
candidate is expected to have at least the following columns:

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
    <td>username, login, userName, identifiant</td>
    <td>String</td>
    <td>Login used by the end user to authenticate to this web application</td>
  </tr>
  <tr>
    <td>password, pwd, passwd, motDePasse</td>
    <td>String</td>
    <td>Password (in clear) used by the end user to authenticate to this web application</td>
  </tr>
  </tbody>
</table>


If no `Account` table candidate is found, Celerio will do as if it had
found one and will generate a mock Account DAO implementation that
returns 2 dummy users (user/user and admin/admin) instead of generating
a real JPA DAO implementation. It is up to you to replace this DAO
implementation with your own implementation.

> **Note**
>
> You may also elect the account table by configuration.

<a name="conventions-account-other-columns"></a>
### Other optional account columns

<a name="conventions-account-email"></a>
#### The Email column

If the detected `Account` table has an email column, it is used by the
generated code in few places, mostly as an illustration of the
EmailService usage.

<table class="table table-bordered table-striped">
  <thead>
    <tr>
      <th>Property</th>
      <th>Java Type</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
  <tr>
    <td>email, emailAddress, mail</td>
    <td>String</td>
    <td>The user email</td>
  </tr>
  </tbody>  
</table>

<a name="conventions-account-enabled"></a>
#### The Enabled column

If the detected Account table has an enabled column, it is used by the
generated code related to Spring Security integration.

<table class="table table-bordered table-striped">
  <thead>
    <tr>
      <th>Property</th>
      <th>Java Type</th>
      <th>Description</th>
    </tr>
  </thead>
  </tbody>
  <tr>
    <td>enabled, isEnabled</td>
    <td>Boolean</td>
    <td>Only enabled users (enabled == true) can login.</td>
  </tr>
  </tbody>
</table>

<a name="conventions-role-table"></a>
The 'Role' table
----------------

The `Role` table is a special table that contains the account's roles. To
be detected by Celerio, it must have a many-to-many or a many-to-one
relationship with the found `Account` table and contain the following
mandatory column:

<table class="table table-bordered table-striped">
  <thead>
    <tr>
      <th>Property</th>
      <th>Java Type</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>  
  <tr>
    <td>authority, roleName, role, nameLocale</td>
    <td>String</td>
    <td>The generated code relies on the following authority's values: ROLE_USER, ROLE_ADMIN</td>
   </tr>
  </tbody>   
</table>

Here is a sample SQL script (H2 Database) that complies to Celerio conventions

{% highlight sql %}

CREATE TABLE ACCOUNT (
    account_id char(32) not null, login
    varchar(255) not null,
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

<a name="conventions-hibernate-filter"></a>
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

<a name="conventions-version-optimistic-locking"></a>
## Version column and Optimistic Locking

If your table has a column named `VERSION` whose type is compatible with the JPA `@Version` annotation, 
then by convention Celerio assumes that you want to enable an optimistic locking strategy.

As a result, the corresponding property is annotated with `@Version`.

You can disable this convention in the configuration using columnConfig's version attribute.

<a name="conventions-many-to-many-inverse-attribute"></a>
## Many to many and inverse attribute

Which side of the @ManyToMany relation is marked as `inverse="true"` ? 

By convention, the side whose corresponding column's order is the highest on the 'middle table'.
You can override this convention using the parent's columnConfig's 'inverse' attribute. 

<a name="conventions-file-download"></a>
## File Upload and Download

When the following columns are present simultaneously in a
table, Celerio generates various helper methods to ease file
manipulation from the web tier to the persistence layer.

<table class="table table-bordered table-striped">
  <thead>
    <tr>
      <th>Properties</th>
      <th>Mapped Java Type</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
  <tr>
    <td><i>prefix</i>fileName</td>
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

<a name="conventions-audit-entity"></a>
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
      <th>Mapped Java Type</th>
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


<a name="conventions-audit-table"></a>
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
      <th>Description</th>
      <th>Mapped Java Type</th>
      <th>Required properties</th>
    </tr>
  </thead>
  <tbody>  
  <tr>
    <td>Author</td>
    <td>String</td>
    <td>author or auteur</td>
  </tr>
  <tr>
    <td>Event type</td>
    <td>String</td>
    <td>event</td>
  </tr>
  <tr>
    <td>First attribute</td>
    <td>String</td>
    <td>stringAttribute1 or attribute1 or string1</td>
  </tr>
  <tr>
    <td>Second attribute</td>
    <td>String</td>
    <td>stringAttribute2 or attribute2 or string2</td>
  </tr>
  <tr>
    <td>Third attribute</td>
    <td>String</td>
    <td>stringAttribute3 or attribute3 or string3</td>
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


<a name="conventions-saved-search"></a>
## Saved search table

When the following columns are present simultaneously in a
table, Celerio will use these columns to propose the user to save its searches

The table should be called either `saved_search` or `saved_search_form`

The table should contain the following columns

<table class="table table-bordered table-striped">
  <thead>
    <tr>
      <th>Property</th>
      <th>Java Type</th>
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
