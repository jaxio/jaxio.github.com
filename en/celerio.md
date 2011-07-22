---
layout: english
title: Celerio , a java software factory
---
# Celerio: Code Generation Tool for Java

## Celerio

Celerio is a code generator tool for data-oriented application

<div class="prepend-1 span-23 last">
<p><img src="/images/green-check.gif"/>Celerio uses as input the entity-relationship model used by all relational databases. 
This model is present in production and most developers understand it.

To obtain this model, Celerio connects to your database and does a "reverse engineering" of your database schema. Celerio supports Oracle, My Sql, Postgres, DB2, etc.

<p><img src="/images/green-check.gif"/>The model can be augmented using a configuration file. You may for example configure inheritance, rename some variables, declare some bi-directional associations, etc.
Then Celerio executes code generation templates written in Velocity.</p>

<p><img src="/images/green-check.gif"/>Jaxio ships with Celerio  code generation templates organized into packs ('Backend' pack, 'SpringMVC3' pack, 'JSF 2' pack, etc...). 
These templates address most use cases of data-oriented applications.

The code that Celerio generates leverages the best and latest Open Source technologies and standard and follows the best practices.</p>
</div>

Of course Celerio is not required at Runtime.

<img src="/images/celerio/celerio-overview.png"/>

The above figure illustrates Celerio an dci-dessus illustre le fonctionnement de Celerio et donne un aperçu des couches générées par les packs de templates 'Backend' et 'JSF2'.


## Main features

* Database Reverse engineering
    * Tables, columns, contraints, comments
	* Support composite key ans les clés composée ainsi que les relations x-to-one composées
	* Detects auto-generated keys (depends on driver jdbc)
	* Dumps the reversed metadata in an XML file to avoid useless reverse operation.
* Convention over Configuration
    * Camel case naming for entity and fields 
    * Simple many-to-one, one-to-one, many-to-many
    * Optimistic locking  
* Configuration
	* Filter tables
	* Mapping rules for columns data type
	* Field renaming
	* Business key
	* Inheritance
	* Refine x-to-one and many-to-many associations
	* Inverse association one-to-many, one-to-one, many-to-many
	* Labels for fields
	* Field visibility in search form, edit form, and search results tables
	* etc.
* Write your own generation templates, using existing one as source of inspiration
* Detect and preserver code modification across regeneration. 
* Integrate with Maven 2/3: Celerio comes as a Maven plugin. No IDE required!


# Templates packs

## 'Backend' pack
* Génération des entitées JPA2 (Hibernate)
	* Associations many-to-one, one-to-many, one-to-one, one-to-one inverse, many-to-many, many-to-many inverse
	* Héritage
	* Bean Validation
	* Optimistic Locking
	* Formattage des dates
	* Cache de second niveau
	* Support des principaux types (BigInteger, BigDecimal, Integer, String, etc...)
	* Helper pour les formats binaires (Blob)
* Génération des DAOs et services associés
	* Recherche avancée par l'exemple (inclus les entitées liées au 1er degré)
	* Pagination
	* Named-query
	* Utilisation des "generic" Java
	* Transactions avec Spring Framework (@Transactional)
* Génération des Tests unitaires
* etc.

## 'Spring MVC 3 & JQuery' Pack
* Utilise le résultat du pack 'Backend'
* Génération des controlleurs
* Génération des vues
	* Auto-complete ajax configuration
	* formulaire
	* read-only avec navigation dans le graphe des entitées liées
	* Critères de recherche (intervalle de date, ajax, etc.)
	* Résultats de recherche paginés (ajax)
	* Layout avec BluePrint Css
* Sécurité avec Spring Security

<p>Voir aussi le billet "<a href="http://www.springfuse.com/2011/05/04/generate-spring-mvc3-jquery-jpa2-crud-applications.html">Generate Spring MVC3, JQuery, JPA2 CRUD Application</a>" publié sur le blog de SpringFuse.</p>

## 'JSF 2 & PrimeFaces' Pack
* Utilise le résultat du pack 'Backend'
* Génération des conversations (Spring Web Flow)
	* Ré-utilisation des conversations
	* Utilisation du persistence contexte étendu
* Génération des vues
	* Utilisation des facelets
	* Utilisation des composants PrimeFaces
* Sécurité avec Spring Security
* etc.

Voir aussi le billet "<a href="http://www.springfuse.com/2011/01/04/springfuse-generates-primefaces-with-spring-webflow-frontend.html">Springfuse generates Primefaces with Spring Web Flow front end</a>" publié sur le blog de SpringFuse.  
