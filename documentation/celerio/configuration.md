---
layout: refdoc
title: Celerio Guide - Configuration 
---

[ << prev ](configuration-file.html) [ index ](index.html) [ next >> ](templates.html)

# 11. Celerio Configuration

A thorough configuration file example is provided when you create a project out of the sample H2 database that we provide.
The file is located under `src/main/config/celerio-maven-plugin/celerio-maven-plugin.xml`.

This configuration file allows you to override conventions and control more precisely what is generated. 
For example, you may want to rename some variables, make some associations bidirectional, change a type, etc.

Here are the main configuration points:

* [Inline documentation through XSD](#inline-documentation-through-xsd)
* [Entity ID](#entity-id)
	* [Use a SEQUENCE per TABLE](#use-a-sequence-per-table)
	* [Use a custom Id generator](#use-a-custom-id-generator)
* [Entity and property names](#entity-and-property-names)
	* [Underscore '_' enables Java camel case syntax](#underscore--enables-java-camel-case-syntax)
	* [Native camel case support](#native-camel-case-support)
	* [Force an entity name](#force-an-entity-name)
	* [Force a property name](#force-a-property-name)
	* [Advanced property name calculation](#advanced-property-name-calculation)
* [Type Mapping](#type-mapping)
	* [Force a type mapping locally](#force-a-type-mapping-locally)
	* [Number mapping customization](#number-mapping-customization)
	* [Date mapping customization](#date-mapping-customization)               
* [Enum Mapping](#enum-mapping)
	* [Enum persisted as String](#enum-persisted-as-string)
	* [Enum persisted as Integer](#enum-persisted-as-integer)
	* [Advanced Enum mapping](#advanced-enum-mapping)
* [Associations](#associations)  
	* [@ManyToOne](#manytoone)
	* [@OneToMany](#onetomany)
	* [@ManyToOne and @OneToMany with composite foreign key](#manytoone-and-onetomany-with-composite-foreign-key)
	* [@OneToOne](#onetoone)
	* [Inverse @OneToOne](#inverse-onetoone)
	* [@ManyToMany](#manytomany)
	* [@ManyToOne and @OneToMany with an intermediate table](#manytoone-and-onetomany-with-an-intermediate-table)
* [Inheritance](#inheritance)  
	* [Single table strategy inheritance](#single-table-strategy-inheritance)  
	* [Joined strategy inheritance](#joined-strategy-inheritance)  
	* [Table per class strategy inheritance](#table-per-class-strategy-inheritance)  

## Inline documentation through XSD
Before editing your configuration file, make sure that Eclipse displays
the documentation present in the `celerio.xsd` file and that it suggests
the available tags. From Eclipse, you cannot work efficiently without
the help of the XSD documentation.

![Tag completion and documentation under
Eclipse](images/celerio-eclipse-xsd-completion.png)

## Entity ID

If you rely on conventions, you do not need to configure anything
regarding Ids. These examples are for advanced usage.

### Use a SEQUENCE per TABLE

In case you use a sequence per table to generate your primary key
values, you must configure Celerio in order to take it into account.
Here is an example:

{% highlight xml %}
<entityConfigs>
  <entityConfig tableName="ADDRESS" sequenceName="ADDRESS_SEQ"/>
</entityConfigs>
{% endhighlight %}


assuming the PK of the ADDRESS table is mapped to an Integer, here is
how would look the generated code:

{% highlight java %}
@Column(name = "ADDRESS_ID", nullable = false, unique = true, precision = 5)
@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "ADDRESS_SEQ")
@Id
@SequenceGenerator(name = "ADDRESS_SEQ", sequenceName = "ADDRESS_SEQ")
public Integer getAddressId() {
  return addressId;
}
{% endhighlight %}

You can configure globally the sequence name pattern 

{% highlight xml %}
<sequences>
   <sequence tableNamePattern="*" sequenceName="SEQ_{TABLE_NAME}" />
</sequences>
{% endhighlight %}

### Use a custom Id generator

In certain cases, generally when you work with legacy databases, you may
need to use a custom Id generator in order to be consistent with the
legacy system. Here is an example:

{% highlight xml %}
<entityConfig tableName="ADDRESS">
  <columnConfigs>
    <columnConfig columnName="ADDRESS_ID">
      <generatedValue generator="myCustomerGenerator" />
      <genericGenerator name="myCustomerGenerator" strategy="com.yourcompany.appli.customgen.CustomerGenerator">
        <parameters>
          <parameter name="sequence" value="YOUR_EVNTUAL_SEQ" />
        </parameters>
      </genericGenerator>
    </columnConfig>
  </columnConfigs>
</entityConfig>
{% endhighlight %}

leads to:

{% highlight java %}
@Column(name = "ADDRESS_ID", nullable = false, unique = true, precision = 5)
@GeneratedValue(generator = "myCustomerGenerator")
@GenericGenerator(name = "myCustomerGenerator", 
 strategy = "com.yourcompany.appli.customgen.CustomerGenerator", 
 parameters = @Parameter(name = "sequence", value = "YOUR_EVNTUAL_SEQ"))
@Id
public Integer getAddressId() {
  return addressId;
}          
{% endhighlight %}

## Entity and property names

### Underscore '_' enables Java camel case syntax

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

### Force an entity name

By default, an entity name is deduced from the Table name. To force the
entity name to a different value, use the `entityName` attribute of the
`entityConfig` element, for example.

{% highlight xml %}
<entityConfigs>
  <entityConfig tableName="ACCOUNT" entityName="UserAccount"/>    
 </entityConfigs>
{% endhighlight %}

### Force a property name

By default, a property name is deduced from the column name. To force
the property name to a different value, use the `fieldName` attribute of
the `columnConfig` element, for example.

{% highlight xml %}
<entityConfigs>
  <entityConfig tableName="ACCOUNT">
    <columnConfigs>
      <columnConfig columnName="user_dob" fieldName="birthDate"/>
    </columnConfigs>
  </entityConfig>
</entityConfigs>
{% endhighlight %}

leads to:

{% highlight java %}
Date birthDate;
{% endhighlight %}

### Advanced property name calculation

By default Celerio calculates Java entity/field name based on the underlying table/column name.

If your database schema has some naming convention using prefixes, this can lead to non friendly entity/column name.

Hopefully, you can configure Celerio to pre-process all your table names and column names before applying its convention to compute the entity/column name.  
  
Here are some configuration examples:

{% highlight xml %}

<configuration>
  <conventions>
    <tableRenamers>
      <tableRenamer regexp="tbl_" replace="" />
    </tableRenamers>
    <columnRenamers>
	  <columnRenamer regexp="^.{3}_" replace="" />
	  <columnRenamer regexp="qrtz_" replace="Quartz_" />
    </columnRenamers>
  </conventions>
</configuration>

{% endhighlight %}  

In that case, assuming we have a table named `tbl_account`, the entity will be named `Account` instead of `TblAccount`.

Column names such as `XYZ_SOMETHING_MEANINGFUL` will give `sometingMeaningful` instead of `xyzSometingMeaningful`.

## Type Mapping

Celerio has some conventions regarding type mapping. You can change them
either locally or globally using rules.

### Force a type mapping locally

You can force the mapped type using the `mappedType` attribute of the
columnConfig element. For example to force a mapping to an Integer you
would do:

{% highlight xml %}
<entityConfigs>
  <entityConfig tableName="ACCOUNT">
    <columnConfigs>
      <columnConfig columnName="year" mappedType="M_INTEGER"/>
    </columnConfigs>
  </entityConfig>
 </entityConfigs>
{% endhighlight %}

### Number mapping customization

You can configure number mapping rules globally. For example, to map all
columns whose size and decimal digits are \> 1 to BigDecimal, proceed as
follow:

{% highlight xml %}
<configuration>
  <numberMappings>
    <numberMapping mappedType="M_BIGDECIMAL" columnSizeMin="1" columnDecimalDigitsMin="1"/>  
  </numberMappings>
</configuration>
{% endhighlight %}

First rule that matches is used. For example to map to either Boolean,
Double or BigDecimal you can do:

{% highlight xml %}
<configuration>
  <numberMappings>
    <numberMapping mappedType="M_BOOLEAN" columnSizeMin="1" columnSizeMax="2" columnDecimalDigitsMin="0" columnDecimalDigitsMax="1"/>    
    <numberMapping mappedType="M_DOUBLE" columnSizeMin="1" columnSizeMax="11" columnDecimalDigitsMin="1" columnDecimalDigitsMax="4"/>
    <numberMapping mappedType="M_BIGDECIMAL" columnSizeMin="11" columnDecimalDigitsMin="4"/>  
  </numberMappings>
</configuration>              
{% endhighlight %}

Note that the `columnSizeMin` is inclusive and `columnSizeMax` is
exclusive.

### Date mapping customization

You can configure date mapping rules globally. For example, to map all
date/time/timestamp column to Joda Time LocalDateTime, proceed as
follow:

{% highlight xml %}
<configuration>
  <dateMappings>
    <dateMapping mappedType="M_JODA_LOCALDATETIME" />  
  </dateMappings>
</configuration>
{% endhighlight %}

And for example to map differently the columns whose name is VERSION you
can add the following mapping rule:

{% highlight xml %}
<configuration>
  <dateMappings>
    <dateMapping mappedType="M_UTILDATE" columnNameRegExp="VERSION"/>  
    <dateMapping mappedType="M_JODA_LOCALDATETIME" />  
  </dateMappings>
</configuration>
{% endhighlight %}


## Enum Mapping

To map a column to an Java Enum using the `@Enumerated` value, see:

* [Enum persisted as String](#enum-string) 
* [Enum persisted as Integer](#enum-ordinal) 

To map your enum to a value that is different from the value returned by either 
the enum's name() or enum's ordinal() method, see:

* [Advanced Enum mapping](#enum-custom) 

### Enum persisted as String
In this example, the persisted value is the value returned by the enum's name() method.

**Value persisted in the database**: Either "MR" or "MS".

**Celerio configuration**:
{% highlight xml %}
<celerio>
	<entityConfig tableName="ACCOUNT">
		<columnConfigs>
			<columnConfig columnName="civility" sharedEnumName="Civility" />
		</columnConfigs>
	</entityConfigs>

	<sharedEnumConfigs>
		<sharedEnumConfig name="Civility" type="STRING">
			<enumValues>
				<enumValue value="MR" label="Mister" />
				<enumValue value="MS" label="Miss" />
			</enumValues>
		</sharedEnumConfig>
	</sharedEnumConfigs>
</celerio>
{% endhighlight %}

You can configure multiple languages too

{% highlight xml %}
<sharedEnumConfig name="Civility" type="STRING">
	<enumValues>
		<enumValue value="MR">
			<labels>
				<label value="Mister" />
				<label value="Monsieur" lang="fr" />
			</labels>
		</enumValue>
		<enumValue value="MS">
			<labels>
				<label value="Miss" />
				<label value="Madame" lang="fr" />
			</labels>
		</enumValue>
	</enumValues>
</sharedEnumConfig>
{% endhighlight %}

**Generated code**:
{% highlight java %}
public enum Civility {
    MR, MS;
    public String getLabel() {
        return ResourcesUtil.getInstance().getProperty("Civility_" + name());
    }
}

// In Account.java
@Column(name = "CIVILITY")
@Enumerated(STRING)
public Civility getCivility() {
    return civility;
}
{% endhighlight %}

### Enum persisted as Integer
In this example, the persisted value is the value returned by the enum's ordinal() method.

**Value persisted in the database**: Either 0 (for 'MR') or 1 (for 'MS').

**Celerio configuration**:
{% highlight xml %}
<celerio>
	<entityConfig tableName="ACCOUNT">
		<columnConfigs>
			<columnConfig columnName="civility" sharedEnumName="Civility" />
		</columnConfigs>
	</entityConfigs>

	<sharedEnumConfigs>
		<sharedEnumConfig name="Civility" type="ORDINAL">
			<enumValues>
				<enumValue value="MR" label="Mister"/>
				<enumValue value="MS" label="Miss" />
			</enumValues>
		</sharedEnumConfig>
	</sharedEnumConfigs>
</celerio>
{% endhighlight %}

**Generated code**:
{% highlight java %}
public enum Civility {
    MR, MS;
    public String getLabel() {
        return ResourcesUtil.getInstance().getProperty("Civility_" + name());
    }
}

// In Account.java
@Column(name = "CIVILITY")
@Enumerated
public Civility getCivility() {
    return civility;
}
{% endhighlight %}

### Advanced Enum mapping
This mapping allows you to customize the persisted value instead of relying on the name() or ordinal() enum's method.

**Value persisted in the database**: Either "MR" or "MS".

**Celerio configuration**:
{% highlight xml %}
<celerio>
	<entityConfig tableName="ACCOUNT">
		<columnConfigs>
			<columnConfig columnName="civility" sharedEnumName="Civility" />
		</columnConfigs>
	</entityConfigs>

	<sharedEnumConfigs>
		<sharedEnumConfig name="Civility" type="CUSTOM">
			<enumValues>
				<enumValue name="MISTER" value="MR" label="Mister"/>
				<enumValue name="MISS" value="MS" label="Miss"   />
			</enumValues>
		</sharedEnumConfig>
	</sharedEnumConfigs>
</celerio>
{% endhighlight %}

**Generated code**:
{% highlight java %}
public enum Civility implements LabelizedEnum {
    MISTER("MR"), //
    MISS("MS");
    private final String value;

    /**
     * @param value The value that is persisted in the database.
     */
    Civility(String value) {
        this.value = value;
    }

    /**
     * @return the value that is persisted in the database.
     */
    public String getValue() {
        return value;
    }

    /**
     * @return the Civility instance having its value matching exactly the passed value. 
     */
    public static Civility fromValue(String value) {
        if (value == null) {
            return null;
        }
        for (Civility e : Civility.values()) {
            if (value.equals(e.getValue())) {
                return e;
            }
        }
        return null;
    }

    @Override
    public String getLabel() {
        return ResourcesUtil.getInstance().getProperty("Civility_" + name());
    }
}

// In Account.java
@Column(name = "CIVILITY")
@Type(type = "org.jadira.usertype.corejava.PersistentEnum", parameters = { //
@Parameter(name = "enumClass", value = "com.mycompany.myproject.domain.Civility"), //
            @Parameter(name = "valueOfMethod", value = "fromValue"), //
            @Parameter(name = "identifierMethod", value = "getValue") //
})
public Civility getCivility() {
    return civility;
}
{% endhighlight %}

## Associations

### @ManyToOne

By default, Celerio generates the code for a `@ManyToOne` association
when it encounters a column having a `foreign key` constraint and no
`unique` constraint.

The variable name of the many to one association is deduced by default
from the `fieldName` of the column. For example if the `fieldName` is
`addressId` , the many to one variable name will be `address` . In case
where the `fieldName` already matches the name of the target entity,
Celerio adds the "Ref" suffix to the variable name. Here are few
simplified examples:

{% highlight java %}
// column name is 'addr_id' 
Address addr;

// column name is 'address'
Address address;

// column name is 'anything_else'
Address anythingElse;
{% endhighlight %}

In any case, use the `manyToOneConfig` element to force a different
variable name. For example:

{% highlight xml %}
<columnConfig columnName="addr_id">
    <manyToOneConfig var="myAddress"/>
</columnConfig>
{% endhighlight %}

will lead to

{% highlight java %}
Address myAddress;
{% endhighlight %}

The `manyToOneConfig` element also allows you to tune the JPA fetch type
and the JPA cascade types. Please refer to the XSD for more information.

If you have some inheritance involved on the 'one' side of the many to
one association, the table referenced by the foreign key is not enough
to identify the target entity. In that case, set the `targetEntityName`
attribute of the `columnConfig` element. For example:

{% highlight xml %}
<columnConfig columnName="address_id" targetEntityName="HomeAddress"/>
{% endhighlight %}

On legacy schema, the foreign key constraint may not be present and
Celerio will not generate the many to one association you would expect.
Hopefully you can configure Celerio to do as if a foreign key constraint
was present by setting the `targetTableName` attribute of the
`columnConfig` element. For example:

{% highlight xml %}
<columnConfig columnName="address_id" targetTableName="ADDRESS"/>
{% endhighlight %}

### @OneToMany

One to many association is configured on the 'many' side of the
association, more precisely on the same `columnConfig` as the one used
for the associated many to one association. This may be a bit confusing
at first but it has the advantage to group together, both associations
on the side that really owns the association.

Celerio generates the code for one to many association when a many to
one association is present and when the `associationDirection` attribute
of the `columnConfig` element is `BIDIRECTIONAL` . For example:

{% highlight xml %}
<entityConfig tableName="Account">
    <columnConfig columnName="address_id" 
                  associationDirection="BIDIRECTIONAL"/>
</entityConfig>
{% endhighlight %}

will lead (assuming address\_id refers to Address) to something like:

{% highlight java %}
// in Account.java
Address address;
{% endhighlight %}

{% highlight java %}
// In Address.java
List<Account> accounts;
{% endhighlight %}

In the example above `accounts` is simply the plural of the Account
entity that Celerio guessed. We were of course lucky on this one.

Use the `oneToManyConfig` element of the `columnConfig` to set the
name of the one to many association to a different value. As you will
see, you can also set the name of an element of the collection to
control the name of the associated helper methods that Celerio generates
(adder, remover, etc.). Here is an example:

{% highlight xml %}
<entityConfig tableName="Account">
    <columnConfig columnName="address_id" 
                  associationDirection="BIDIRECTIONAL">
        <oneToManyConfig var="people" elementVar="resident"/>
    </columnConfig>
</entityConfig>
{% endhighlight %}

will lead to

{% highlight java %}
// In Address.java
List<Account> people;

public void addResident(Account resident) {
// skip...
}
{% endhighlight %}

The `oneToManyConfig` element also allows you to tune the JPA fetch type
and the JPA cascade types. Please refer to the XSD for more information.


### @ManyToOne and @OneToMany with composite foreign key

By default, Celerio generates the code for a `@ManyToOne` association
when it encounters a composite foreign key.

The `manyToOneConfig` should be a child of the columnConfig corresponding to the **first column of the composite foreign key**.

As for regular @OneToMany, you can use the `oneToManyConfig` element to generate the inverse association. 
Do not forget to set the `associationDirection` attribute of the `columnConfig` element to `BIDIRECTIONAL`

### @OneToOne

By default, Celerio generates the code for a `@OneToOne` association
when it encounters a column having a `foreign key` constraint AND a
`unique` constraint.

One to one association configuration is very similar to many to one association.

To change the variable name, the JPA fetch type or the cascade types of the
one to one association, use the `oneToOneConfig` element of the `columnConfig`
element. Here is an example:

{% highlight xml %}
<entityConfig tableName="account">
	<columnConfigs>
		<columnConfig columnName="address_id">
			<oneToOneConfig var="myAddress" />
		</columnConfig>
	</columnConfigs>
</entityConfig>
{% endhighlight %}

will lead to

{% highlight java %}
// In Account.java
Address myAddress;
{% endhighlight %}

### Inverse @OneToOne

Inverse one to one association is for one to one association what one to
many association is for many to one association.

Celerio generates the code for inverse one to one association when a one
to one association is present and when the `associationDirection`
attribute of the `columnConfig` element is `BIDIRECTIONAL` .

Inverse one to one association is configured on the owning side of
association, that is on the `columnConfig` that has the foreign key and
unique constraints.

As for one to many association configuration, this may be a bit
confusing at first but it has the advantage to group together, both
associations on the side that really owns the association.

To change the variable name, the JPA fetch type or the cascade types of the
inverse one to one association, use the `inverseOneToOneConfig` element of the `columnConfig`
element. Here is an example:

Here is an example:

{% highlight xml %}
<entityConfig tableName="account">
	<columnConfigs>
		<columnConfig columnName="address_id" associationDirection="BIDIRECTIONAL">
			<oneToOneConfig var="myAddress" />
			<inverseOneToOneConfig var="owner" />
		</columnConfig>
	</columnConfigs>
</entityConfig>
{% endhighlight %}

will lead to

{% highlight java %}
// In Account.java
Address myAddress;

// In Address.java
Account owner;
{% endhighlight %}

### @ManyToMany

Many to many association necessarily involves a join table. When Celerio
detects a join table, it generates the code for the many to many
relation. Celerio assumes that a table is a join table when it has 2
foreign keys and no other columns, except eventually a primary key
column and a column used for optimistic locking.

To fine tune the many to many association, you must declare an
entityConfig for the join table. You may use the `manyToManyConfig`
element. to set the related variables and adder/remover/etc. method
names. You can use the `inverse` attribute to force the inverse side of
the association. For example:

{% highlight xml %}
<entityConfig tableName="account_role" associationDirection="BIDIRECTIONAL">
 <columnConfigs>
  <columnConfig columnName="account_id">
   <manyToManyConfig var="theAccounts" elementVar="anAccount"/>
  </columnConfig>
  <columnConfig columnName="role_id" inverse="true">
   <manyToManyConfig var="theRoles" elementVar="aRole"/>                
  </columnConfig>
 </columnConfigs>
</entityConfig>
{% endhighlight %}

#### In case the join table is not detected

In case Celerio does not detect the join table, for example if an
extra column is present, you can force it by setting to `true` the
`middleTable` attribute of the `entityConfig` element.

#### Inverse side convention

Which side of the @ManyToMany relation is marked as `inverse="true"` ? 

By convention, the side whose corresponding column's order is the highest on the 'middle table'.
You can override this convention using the parent's columnConfig's 'inverse' attribute. 

### @ManyToOne and @OneToMany with an intermediate table

*Since Celerio 3.0.101*

Configuration is very similar to many to many configuration above except that you should use the `manyToOneConfig` element for the forward association and
the `OneToManyConfig` element for the inverse association. For example:

{% highlight xml %}
<entityConfig tableName="ADDRESS_PERSON">
  <columnConfigs>
    <columnConfig columnName="address_id">
	  <oneToManyConfig var="addresses" elementVar="address" />
    </columnConfig>
    <columnConfig columnName="person_id" inverse="true">
      <manyToOneConfig var="person">
        <cascades>
          <cascade type="ALL" />
        </cascades>
      </manyToOneConfig>
    </columnConfig>
  </columnConfigs>
</entityConfig>
{% endhighlight %}

## Inheritance

By default Celerio does not try to guess any inheritance strategy.

However, you can configure Celerio to take your inheritance strategy into account.

### Single table strategy inheritance

The `SINGLE_TABLE` strategy maps all entities in the hierarchy to the same table.

Please find below a schema example and the corresponding Celerio configuration.

{% highlight sql %}
DROP ALL OBJECTS;

CREATE TABLE ROCKET (
	rocket_id		char(32)		not null,
	name			varchar(255)	not null,
	weight			smallint,
	discriminator	char(4)			not null,
	seats_count		smallint,
	primary key (rocket_id)
);
{% endhighlight %}

Here is the corresponding `SINGLE_TABLE` inheritance configuration: 

{% highlight xml %}
<entityConfigs>
	<entityConfig tableName="ROCKET" subPackage="one">
		<inheritance strategy="SINGLE_TABLE" discriminatorColumn="discriminator" discriminatorValue="ROCK" />
		<columnConfigs>
			<columnConfig columnName="rocket_id" />
			<columnConfig columnName="name" />
			<columnConfig columnName="weight" />
		</columnConfigs>
	</entityConfig>
	<entityConfig entityName="SpaceShip" subPackage="two">
		<inheritance parentEntityName="Rocket" discriminatorValue="SPAC" />
		<columnConfigs>
			<columnConfig columnName="seats_count" />
		</columnConfigs>
	</entityConfig>
</entityConfigs>
{% endhighlight %}

### Joined strategy inheritance 

The `JOINED` strategy uses a different table for each entity in the hierarchy.

Please find below a schema example and the corresponding Celerio configuration.

{% highlight sql %}
DROP ALL OBJECTS;

CREATE TABLE ACCOUNT (
	account_id	char(32)		not null,
	login		varchar(255)	not null,
	password	varchar(255)	not null,
	email		varchar(255)	not null,
	is_enabled	bool,
	civility	char(2),
	first_name	varchar(255),
	last_name	varchar(255),
	version		smallint		default 0,

	constraint account_unique_1 unique (login),
	constraint account_unique_2 unique (email),
	primary key (account_id)
);

CREATE TABLE ADMINISTRATIVE_ACCOUNT (
	account_id	char(32)		not null,
	fax			varchar(255)	not null,	
	country		varchar(255)	not null,	
	city		varchar(255),
	primary key (account_id)
);

CREATE TABLE ENTERPRISE_ACCOUNT (
	account_id		char(32)		not null,
	company_name	varchar(255)	not null,
	inception_date	timestamp		not null,	
	revenue			integer			not null,	
	dummy			integer,
	is_ethical		bool			not null,	
	primary key (account_id)
);
{% endhighlight %}

Here is the corresponding `JOINED` inheritance configuration: 

{% highlight xml %}
<entityConfigs>
	<entityConfig tableName="ACCOUNT">
		<inheritance strategy="JOINED" />
		<columnConfigs>
			<!-- We do not mention all columns on purpose, we expect Celerio to add missing columns... -->
			<columnConfig columnName="login" fieldName="username" />
		</columnConfigs>
	</entityConfig>

	<entityConfig tableName="ADMINISTRATIVE_ACCOUNT" entityName="AdminAccount">
		<inheritance parentEntityName="Account" />
		<columnConfigs>
			<!-- We do not mention all columns on purpose, we expect Celerio to add missing columns... -->
			<columnConfig columnName="fax" fieldName="faxNumber" />
		</columnConfigs>
	</entityConfig>

	<entityConfig tableName="ENTERPRISE_ACCOUNT">
		<inheritance parentEntityName="AdminAccount" />
		<columnConfigs>
			<!-- We do not mention all columns on purpose, we expect Celerio to add missing columns... -->
			<columnConfig columnName="revenue" fieldName="revenuePerYear" />
		</columnConfigs>
	</entityConfig>
</entityConfigs>
{% endhighlight %}

### Table per class strategy inheritance

*This feature is not implemented* 



