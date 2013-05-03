---
layout: french
title: Celerio Guide - Conventions 
---

Conventions and integration
===========================

Celerio has some built-in conventions. When these conventions are
followed, Celerio generates cleaner Java code and some specific
features. 

For example, by simply following some columns naming
convention, you can rely on Celerio to generate all the infrastructure
code and configuration that will allow you to handle file upload and
download in your web application, in an optimal way.

Camel case conventions
----------------------

### Underscore '_' Enables Java Camel Case Syntax

'Camel Case' syntax is standard Java code convention. When Celerio
encounters the character underscore `_` in a table’s name or a column’s
name, it skips it and converts to upper case the next character when
generating classes, variables or methods related to this table, or
column.

For example, if your table name is `BOOK_COMMENT`, the generated entity
class will be named `BookComment`; a variable holding `BookComment`
instance will be named `bookComment` and a setter will be named
`setBookComment`, etc.

### Native camel case support

If your table's and/or Column use a camel case syntax and if the
JDBC driver preserves this syntax, then Celerio takes it into account
when generating classes, variables or methods related to this table, or
column.

For example, if your table name is `bankAccount`, the generated entity
class will be named `BankAccount`; a variable holding `BankAccount`
instance will be named `bankAccount` and a setter will be named
`setBankAccount`, etc.

Choosing explicit names for your tables and columns is thus very
important as it improves your source code readability without the burden
of relying on configuration.

Primary key conventions
-----------------------

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

### Other Primary Keys

For primary key that are `char(x)` where x is different from 32, Celerio
map the column with an `assigned` generator, which means you must
provide manually the primary key value.

The Account table
-------------------

The `Account` table is a special table that contains the user's login and
password columns and eventually the email and enabled columns. It has an
important role during the login phase. It is also used by the
`AccountContext` generated class which store the current `account`
information in the current thread.

Celerio detects automatically your `Account` table. An account table
candidate is expected to have at least the following columns:

<table class="table">
  <thead>
    <tr>
      <th>Column</th>
      <th>Mapped Java Type</th>
      <th>Description</th>
    </tr>
  </thead>
  <tr>
    <td>username, login, user_name, identifiant</td>
    <td>String</td>
    <td>Login used by the end user to authenticate to this web application</td>
  </tr>
  <tr>
    <td>password, pwd, passwd, mot_de_passe, motdepasse</td>
    <td>String</td>
    <td>Password (in clear) used by the end user to authenticate to this web application</td>
  </tr>
</table>


If no `Account` table candidate is found, Celerio will do as if it had
found one and will generate a mock Account DAO implementation that
returns 2 dummy users (user/user and admin/admin) instead of generating
a real JPA DAO implementation. It is up to you to replace this DAO
implementation with your own implementation.

> **Note**
>
> You may also elect the account table by configuration.

The 'Role' table
----------------

The `Role` table is a special table that contains the account's roles. To
be detected by Celerio, it must have a many-to-many or a many-to-one
relationship with the found `Account` table and contain the following
mandatory column:

<table class="table">
  <thead>
    <tr>
      <th>Column</th>
      <th>Mapped Java Type</th>
      <th>Description</th>
    </tr>
  </thead>
  <tr>
    <td>authority, role_name, role, name_locale</td>
    <td>String</td>
    <td>The generated code relies on the following authority's values: ROLE_USER, ROLE_ADMIN</td>
   </tr>
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

Other optional account columns
--------------------------------

### The Email column

If the detected `Account` table has an email column, it is used by the
generated code in few places, mostly as an illustration of the
EmailService usage.

<table class="table">
  <thead>
    <tr>
      <th>Column</th>
      <th>Mapped Java Type</th>
      <th>Description</th>
    </tr>
  </thead>
  <tr>
    <td>email, email_address, emailAddress, mail</td>
    <td>String</td>
    <td>The user email</td>
  </tr>
</table>


### The Enabled column

If the detected Account table has an enabled column, it is used by the
generated code related to Spring Security integration.

<table class="table">
  <thead>
    <tr>
      <th>Column</th>
      <th>Mapped Java Type</th>
      <th>Description</th>
    </tr>
  </thead>
  <tr>
    <td>enabled, is_enabled, isenabled</td>
    <td>Boolean</td>
    <td>Only enabled users (enabled == true) can login.</td>
  </tr>
</table>

<a name="conventions-file-download"></a>
## File Upload and Download

When the following columns are present simultaneously in a
table, Celerio generates various helper methods to ease file
manipulation from the web tier to the persistence layer.

<table class="table">
  <thead>
    <tr>
      <th>Column</th>
      <th>Mapped Java Type</th>
      <th>Description</th>
    </tr>
  </thead>
  <tr>
    <td><i>prefix</i>_file_name</td>
    <td>Boolean</td>
    <td>File name</td>
  </tr>
  <tr>
    <td><i>prefix</i>_size or <i>prefix</i>_length or <i>prefix</i>_content_length</td>
    <td>Int</td>
    <td>Length of the binary</td>
  </tr>
  <tr>
    <td><i>prefix</i>_content_type</td>
    <td>String</td>
    <td>Content type of the file</td>
  </tr>
  <tr>
    <td><i>prefix</i>_binary or <i>prefix</i>_content or <i>prefix</i>_blob</td>
    <td>Blob</td>
    <td>Binary content of the file</td>
  </tr>
</table>

Example: Here is the corresponding SQL code using `mydoc` as a prefix

{% highlight sql %}

mydoc_content_type      varchar(255)    not null,
mydoc_size              integer         not null,
mydoc_file_name         varchar(255)    not null,
mydoc_binary            bytea,

{% endhighlight %}

This convention allows you to upload a file transparently, 
save it to the corresponding table, then download it using a simple URL.


<a name="conventions-audit-entity"></a>
## Auditing entity

When the following columns are present simultaneously in a
table, Celerio will use these columns to save the creation and last update using jpa listeners.

<table class="table">
  <thead>
    <tr>
      <th>Column</th>
      <th>Mapped Java Type</th>
      <th>Description</th>
    </tr>
  </thead>
  <tr>
    <td>creation_author, creation_by, cree_par</td>
    <td>String</td>
    <td>Creation author id</td>
  </tr>
  <tr>
    <td>creation_date, date_creation</td>
    <td>Date</td>
    <td>Creation date</td>
  </tr>
  <tr>
    <td>last_modification_author, last_modification_at, derniere_modification_par, modifie_par</td>
    <td>String</td>
    <td>Last modification author id</td>
  </tr>
  <tr>
    <td>last_modification_date, date_derniere_modification, derniere_modification</td>
    <td>Date</td>
    <td>Last modification date</td>
  </tr>
</table>


Example: 

{% highlight sql %}

creation_date            timestamp,
creation_author          varchar(200),
last_modification_date   timestamp,
last_modification_author varchar(200),

{% endhighlight %}

<a name="conventions-audit-table"></a>
## Audit table

When the following columns are present simultaneously in a
table, Celerio will use these columns to save the creation and last update using jpa listeners.

The table should be called either

* audit
* audit_log
* audit_trail

The table should contain the following columns

<table class="table">
  <thead>
    <tr>
      <th>Column</th>
      <th>Mapped Java Type</th>
      <th>Description</th>
    </tr>
  </thead>
  <tr>
    <td>author‚ auteur</td>
    <td>String</td>
    <td>Account id</td>
  </tr>
  <tr>
    <td>event</td>
    <td>String</td>
    <td>event type</td>
  </tr>
  <tr>
    <td>string_attribute_1, attribute_1, string_1</td>
    <td>String</td>
    <td>First attribute</td>
  </tr>
  <tr>
    <td>string_attribute_2, attribute_2, string_2</td>
    <td>String</td>
    <td>Second attribute</td>
  </tr>
  <tr>
    <td>string_attribute_3, attribute_3, string_3</td>
    <td>String</td>
    <td>Third attribute</td>
  </tr>
</table>

<a name="conventions-saved-search"></a>
## Saved search table

When the following columns are present simultaneously in a
table, Celerio will use these columns to propose the user to save its searches

The table should be called either `saved_search` or `saved_search_form`

The table should contain the following columns

<table class="table">
  <thead>
    <tr>
      <th>Column</th>
      <th>Mapped Java Type</th>
      <th>Description</th>
    </tr>
  </thead>
  <tr>
    <td>form_class, form_classname</td>
    <td>String</td>
    <td>Fully qualified name of the search form type</td>
  </tr>
  <tr>
    <td>name</td>
    <td>String</td>
    <td>Name given to the saved search</td>
  </tr>
  <tr>
    <td>form_content</td>
    <td>blob</td>
    <td>Serialized form of the search</td>
  </tr>
  <tr>
    <td>account_id</td>
    <td>String</td>
    <td>FK linked to Account (not required)</td>
  </tr>
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


ACCOUNT_ID column & Hibernate Filter
-------------------------------------

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

Version column and Optimistic Locking
-------------------------------------

If your table has a column named `VERSION` whose type is an int, Celerio
assumes by convention that you want to enable an optimistic locking
strategy. As a result, the property is annotated with `@Version` .

Many to many and inverse attribute
----------------------------------

Which side of the relation is marked as `inverse="true"` ? By convention,
the side whose corresponding column's order is the highest on the
'middle table'.
