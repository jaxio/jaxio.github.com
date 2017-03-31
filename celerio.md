---
layout: french
title: Celerio de Jaxio, une Software Factory Java
description: Celerio est une « Software Factory » qui permet d’automatiser en partie le développement informatique des applications web en Java. 
---

## Fonctionnement et fonctionnalités de Celerio

Celerio est un `générateur de code` conçu pour industrialiser le développement des `applications orientées données`.

![great](/images/green-check.gif) Pour fonctionner Celerio prend en 
entrée le modèle le plus classique qui soit, le modèle entité­-relation utilisé 
par toutes les bases de données relationnelles. Ce modèle est extrait par "reverse engineering" 
en se connectant à la base de données relationnelle cible (Oracle, My Sql, DB2, etc.).

![great](/images/green-check.gif) A partir de ce modèle qui est bien connu et maitrisé des développeurs, mais aussi 
d'un fichier de configuration permettant de rafiner le modèle (renommage, héritage, etc.),
Celerio interprète des templates de générations écrites en Velocity.

![great](/images/green-check.gif) Jaxio fournit avec Celerio des templates de générations regroupées en packs 
('Backend', 'JSF 2', etc...). Ces templates couvrent la majorité
des problématiques rencontrées dans des applications orientées données.
Ne cherchant pas à réinventer la roue, bien au contraire, le code Java généré
par ces templates utilise les meilleures technologies Open Source en suivant 
les meilleures pra­tiques.

![great](/images/green-check.gif) Les templates de générations proposées par Jaxio peuvent être éditées et bien entendu
d'autres templates de de générations peuvent être développées soit par vous-même soit par Jaxio. 

![great](/images/green-check.gif) Il est important de noter que `Celerio n’est pas requis à l’exécution`.

### Principales fonctionnalités

* Reverse engineering base de données
    * Tables, colonnes, contraintes, commentaires
	* Support les clés composée ainsi que les relations x-to-one composées
	* Détection des clés auto-générées (dépend du driver jdbc)
	* Dump du résultat du reverse dans un fichier XML pour éviter les reverse inutiles
* Conventions par défaut
    * Nommage des entités et des champs 
    * Associations many-to-one, one-to-one, many-to-many
    * Optimistic locking  
* Configuration
	* Filtrage des tables
	* Définition de règles pour le mapping des colonnes
	* Renommange des champs
	* Héritage
	* Configuration des associations x-to-one et de leur inverse
	* Label des champs
	* Spécialisation des champs: champ de recherche, champ de formulaire, champ présent dans liste de résultats.
* Templates de génération simples à développer grâce aux contextes Celerio accessibles depuis les templates.	
* Détection des modifications apportées au code généré lors des regénérations
* Intégration à Maven 3: Celerio est distribué par défaut sous forme de plugin Maven.


### Packs de templates de générations

Jaxio fournit en Open Source, plusieurs packs de templates de génération.

La figure ci-dessous illustre le fonctionnement de Celerio et donne un aperçu des couches générées par les packs de templates 'Backend' et 'JSF2'.

<img src="/images/celerio/celerio-overview.png" width="100%"/>

#### Pack Angular 4 + Spring Boot + Prime NG etc.

Ce pack est en cours de développement.

Code source: [https://github.com/jaxio/celerio-angular-quickstart](https://github.com/jaxio/celerio-angular-quickstart)

#### Pack 'Java EE 7 webapp' (backend et frontend, sans Spring)

Code source: [https://github.com/jaxio/javaee-lab](https://github.com/jaxio/javaee-lab)

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
	* recherche avec hibernate search
	* Pagination
	* Named-query
	* Utilisation des "generic" Java
	* Transactions avec @javax.transaction.Transactional
* Génération des vues/controller pour JSF
	* Utilisation des facelets
	* Utilisation des composants PrimeFaces
* Conf Shiro
* etc.

#### Pack 'Backend' (avec Spring)

Code source: [https://github.com/jaxio/pack-backend-jpa](https://github.com/jaxio/pack-backend-jpa)

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
	* recherche avec hibernate search
	* Pagination
	* Named-query
	* Utilisation des "generic" Java
	* Transactions avec Spring Framework (@Transactional)
* Génération des Tests unitaires
* etc.

#### Pack 'JSF 2 & PrimeFaces' (avec Spring)

Code source: [https://github.com/jaxio/pack-jsf2-spring-conversation](https://github.com/jaxio/pack-jsf2-spring-conversation)

* Utilise le résultat du pack 'Backend'
* Génération des conversations
	* Ré-utilisation des conversations
	* Utilisation du persistence contexte étendu
* Génération des vues
	* Utilisation des facelets
	* Utilisation des composants PrimeFaces
* Sécurité avec Spring Security
* etc.

Voir le billet <a href="http://www.springfuse.com/2013/03/05/jsf2-conversation.html">Conversation with JSF2</a> publié sur le blog de SpringFuse.  
