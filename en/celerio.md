---
layout: english
title: Celerio , a code generation tool for data-oriented application written in Java.
---

## Celerio Overview

Celerio is a code generator tool for data-oriented application.

![great](/images/green-check.gif) Celerio uses as input the entity-relationship model used by all **relational databases**. 
This model is present in production and most developers understand it.
To obtain this model, Celerio connects to your database and performs a **reverse engineering of your 
database schema**. Celerio supports Oracle, My Sql, Postgres, DB2, etc.

![great](/images/green-check.gif) The model can be augmented using a **configuration file**. 
You may for example **configure inheritance**, rename some variables, declare some **bi-directional associations**, etc.
Then Celerio executes code generation templates written in Velocity

![great](/images/green-check.gif) Celerio comes with **ready-to-use code generation templates** organized 
into templates packs ('Backend' pack', 'JSF 2' pack, etc...). 
These templates address most use cases of data-oriented applications.

![great](/images/green-check.gif) You can of course **write your own generation templates**, 
using existing one as source of inspiration

### Celerio Main features

* Database reverse engineering
    * Tables, columns, contraints, comments
	* Detects auto-generated keys (depends on driver jdbc)
	* Support composite key and composite relations
	* Dumps the reversed metadata in an XML file to avoid useless reverse operation.
* Convention over Configuration
    * Camel case naming for entity and fields 
    * Sensitive default for many-to-one, one-to-one and many-to-many
    * Optimistic locking
* Configuration
	* Filter tables
	* Mapping rules for columns data type
	* Field renaming
	* Business key
	* Inheritance
	* Refine x-to-one, and x-to-many associations
	* Inverse association one-to-many, one-to-one, many-to-many
	* Labels for fields
	* Field visibility in search form, edit form, and search results tables
	* etc.
* Detect and preserve code modification across regeneration 
* Integrate with Maven 2/3: Celerio comes as a Maven plugin. No IDE required!

### Code Generation Templates Packs

Jaxio provides these templates packs by default

The code that gets generated leverages the best and latest Open Source technologies and standards and follows the best practices.

Of course Celerio is not required at Runtime.

Here is a high level overview of Celerio and the generated code

<img src="/images/celerio/celerio-overview-en.png" width="100%"/>


#### Pack Angular 2 + Spring Boot + Prime NG etc.

Pack development is in progress. 

Code source: [https://github.com/jaxio/celerio-angular-quickstart](https://github.com/jaxio/celerio-angular-quickstart)

#### 'Java EE 7 webapp' (backend and frontend, without Spring)

Sources: [https://github.com/jaxio/javaee-lab](https://github.com/jaxio/javaee-lab)

* JPA2 entities (Hibernate)
	* Supports many-to-one, one-to-many, one-to-one, one-to-one inverse, many-to-many, many-to-many inverse
	* Inheritance
	* Bean Validation
	* Optimistic Locking
	* Date formatting
	* 2d level cache
	* Support main java type mapping (BigInteger, BigDecimal, Integer, String, etc...)
	* Generate helper methods for files (Blob)
* DAOs and services
	* Search by example with first level associated entities
	* Pagination
	* Named-query
	* Leverage generics
	* Transactions (using @javax.transaction.Transactional)
	* Hibernate Search
* Generate advanced CRUD features for all entities
* View generation
	* Uses facelets
	* Uses PrimeFaces
* Shiro ready
* etc.

#### 'Backend' pack (with Spring)

Sources: [https://github.com/jaxio/pack-backend-jpa](https://github.com/jaxio/pack-backend-jpa)

* JPA2 entities (Hibernate)
	* Supports many-to-one, one-to-many, one-to-one, one-to-one inverse, many-to-many, many-to-many inverse
	* Inheritance
	* Bean Validation
	* Optimistic Locking
	* Date formatting
	* 2d level cache
	* Support main java type mapping (BigInteger, BigDecimal, Integer, String, etc...)
	* Generate helper methods for files (Blob)
* DAOs and services
	* Search by example with first level associated entities
	* Pagination
	* Named-query
	* Leverage generics
	* Transactions with Spring Framework (@Transactional)
	* Hibernate Search
* Generate Unit tests
* etc.

#### 'JSF 2 & PrimeFaces' Pack (with Spring)

Sources: [https://github.com/jaxio/pack-jsf2-spring-conversation](https://github.com/jaxio/pack-jsf2-spring-conversation)

* Depends on 'Backend' Pack
* Generate advanced CRUD features for all entities
* View generation
	* Uses facelets
	* Uses PrimeFaces
	* Uses Conversation (home made scope)
* Spring Security ready
* etc.

<p>Read our blog entry "<a href="http://www.springfuse.com/2013/03/05/jsf2-conversation.html">Conversation with JSF2</a>"</p>  
