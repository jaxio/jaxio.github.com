---
layout: french
title: Les bonnes pratiques pour des tests seleniums robustes
comments: true
date: 2013-09-30
---

# Selenium

[Selenium](http://docs.seleniumhq.org/) est un outil qui permet d'effectuer des [tests fonctionnels](http://referentiel.institut-agile.fr/acceptance.html) web automatisés.

Celui-ci permet d'exécuter des scénari web sur la plupart des [navigateurs](http://docs.seleniumhq.org/about/platforms.jsp#browsers), [plateformes](http://docs.seleniumhq.org/about/platforms.jsp#operating-systems) et [languages](http://docs.seleniumhq.org/about/platforms.jsp#programming-languages).


# Selenium IDE

[Selenium IDE](http://docs.seleniumhq.org/projects/ide/) est un plugin Firefox qui permet de capturer des tests, de les éditer puis de les exécuter.

![Selenium IDE](/images/blog/selenium/selenium-ide-google.png)

Ici est présenté le résultat de l'enregistrement de Selenium IDE pour la recherche du mot `Jaxio` sur [google.fr](http://google.fr) et du click sur le premier lien.

On ne peut faire plus simple.

# Cas d'usage

Pour comprendre mieux Selenium, et savoir ce que l'on peut lui demander il est nécessaire de prendre un cas d'usage fonctionnel non trivial qui utilise des composants riches.

Pour cela prenons l'application générée par [Celerio](/celerio.html) que vous pouvez reproduire [vous même en ligne](/celerio-service.html).

## Objectif

En tant qu'administrateur je veux modifier un compte existant, m'identifier avec, me déconnecter, et me réidentifier en tant qu'administrateur et remettre le compte dans l'état précédent.

## Actions métier

Le chemin fonctionnel souhaité est donc le suivant:

1. S'identifier en tant qu'administrateur
1. Faire une recherche et sélection un compte
1. Modifier les attributs de ce compte
1. Ajouter un role déjà existant au compte
1. Créer un role et l'ajouter au compte
1. Ajouter un document au compte
1. Sauver le compte
1. Se déconnecter
1. S'identifier en tant que le nouveau compte
1. Se déconnecter
1. Se reconnecter en tant qu'administrateur
1. Remettre en état le compte

<object width="425" height="344"><param name="movie" value="http://www.youtube.com/v/WIkJTdsQyFc&hl=en&fs=1"></param><param name="allowFullScreen" value="true"></param><embed src="http://www.youtube.com/v/WIkJTdsQyFc&hl=en&fs=1" type="application/x-shockwave-flash" allowfullscreen="true" width="425" height="344"></embed></object>


## Actions utilisateur

Ce chemin fonctionnels correspond aux étapes unitaires suivantes

1. S'identifier en tant qu'administrateur
    * Aller sur la page d'acceuil
    * Cliquer sur le lien connexion
    * Taper `admin` dans le champ identifiant
    * Taper `admin` dans le champ mot de passe
    * Cliquer sur le bouton de login
1. Faire une recherche et selection un compte
    * Cliquer sur le lien 'Compte utilisateur'
    * Tapper `homer` dans le champ identifiant
    * Selectionner `homer` dans la liste de completion proposée
    * Selectionner la ligne où `homer` est affichée
1. Modifier les attributs de ce compte
    * Tapper `cnorris` comme identifiant
    * Tapper `kickass` comme mot de passe
    * Tapper `gmail@chucknorris.com` comme email
    * Choisir `Paris` comme adresse
1. Ajouter un role déjà existant au compte
    * Selectionner l'onglet `Droits attribués`
    * Cliquer sur le bouton de recherche de droits
    * Tapper `admin` comme nom
    * Cliquer sur rechercher
    * Selectionner `ROLE_ADMIN`
1. Créer un role et l'ajouter au compte
    * Cliquer sur le bouton de création de droits
    * Tapper `ROLE_GOD`
    * Enregistrer le role
1. Ajouter un document au compte
    * Selectionner l'onglet `Documents attachés`
    * Cliquer sur le bouton d'ajout de documents
    * Cliquer sur le bouton d'upload
    * Selectionner un document
1. Sauver le compte
    * Cliquer sur le bouton `sauver`
1. Se déconnecter
    * Cliquer sur le bouton `deconnexion`

etc etc

## Résultat

La liste complète des actions capturées par l'outil est présenté ci-dessous

<script src="https://gist.github.com/framiere/6777267.js"></script>


## Les faiblesses de ce script

En l'état la capture de Selenium IDE n'est pas utilisable pour les raisons suivantes:

1. Le test ne reflète pas toutes les actions réalisées
1. Le test est dépendant du temps
1. Le test est dépendant du DOM
1. Le test est dépendant de la langue
1. Le test est dépendant du cookie courant
1. Le test n'est pas refactorable
1. Le test n'est pas composable
1. Le test ne peut s'executer rapidement
1. Le test n'est pas lisible par le métier
1. Les étapes du test ne sont pas exprimées clairement
1. Les actions ne sont pas suivies de vérifications

# Axes d'amélioration

Plusieurs axes d'amélioration s'offrent à nous.

## Cookies 
Nous pouvons ajouter du code pour supprimer les cookies

<script src="https://gist.github.com/framiere/6777605.js"></script>

## Refactoring
Nous pouvons refactorer le code pour extraire les actions métiers

<script src="https://gist.github.com/framiere/6777332.js"></script>


## Dom & Page factory pattern
Le [Page factory pattern](https://code.google.com/p/selenium/wiki/PageFactory) permet de résoudre en partie la dépendance forte au DOM

<script src="https://gist.github.com/framiere/6777584.js"></script>

## Actions de vérifications

Nous pouvons ajouter des tests en plus pour vérifier le résultat des actions par exemple

<script src="https://gist.github.com/framiere/6778003.js"></script>

## Gestion du temps

Pour s'affranchir du temps, il est nécessaire d'utiliser les mécanismes de réessai de Selenium de façon judicieuse. L'utilisation de `Thread.sleep()` à tire larigot est à proscrire.

<script src="https://gist.github.com/framiere/6780992.js"></script>

## Composant
L'objet WebElement est un objet trop bas niveau, nous pouvons utiliser des helpers pour être plus efficaces

<script src="https://gist.github.com/framiere/6781000.js"></script>

## Bonne pratiques logicielle

Nous pouvons également appliquer l'ensemble des bonnes pratiques de développement pour améliorer le script original et ainsi le rendre propre et efficace.

Toutefois, ces actions techniques pour améliorer le code de test masquent un sujet plus important.

# Que voulons nous tester ?

Pour une application de gestion, il me semble que les bénéfices les plus interressants apportés par Selenium sont sur des cas fonctionnels en tant que succession d'action sur l'application... et non pas en tant que test d'IHM.

Ces cas fonctionnels sont donc décorrélés du dom... et presque du web.

Dans un mode parfait, le cas d'usage devrait être exprimé dans un dsl, et celui-ci devrait s'executer.

Ce dsl devrait proposer sous une forme ou une autre les fonctionnalités utilisées pour la production d'IHM.


## Recherche de comptes utilisateurs

La recherche d'utilisateur présente les fonctionnalités suivantes:

1. Une barre d'action
    * pour créer un objet
    * pour quitter la recherche
1. Un formulaire de recherche
    * qui contient des composants autocomplete
    * qui contient des composants d'interval de date
    * qui peut être réininitialisé, sauvé, chargé
1. Un tableau
    * avec des headers cliquable pour trier par colonne
    * un composant de pagination
1. Des lignes de résultat
    * que l'on peut supprimer
    * que l'on peut éditer
    * que l'on peut visualiser
    * que l'on peut selectionner

Ces fonctionnalités sont les mêmes sur les autres entités manipulées par l'application.

### AccountSearch

Il faudrait à l'image du code des composants qui produisent la page via le DOM, produire code client qui les consomme via selenium et arriver à ce genre de chose:

<script src="https://gist.github.com/framiere/6781001.js"></script>

Les composants `Table`, `EntityAction`, `Messages`, `OrderBy`, `Autocomplete`, `ManyBooleans`, `ChooseEnum`, `StringRange` doivent être écrits de façon indépendantes des tests.

### EntityAction

Le composant `EntityAction` n'est qu'un aggregat de boutons

<script src="https://gist.github.com/framiere/6781005.js"></script>

### Button
Un bouton est lui-même définit comme suit

<script src="https://gist.github.com/framiere/6781010.js"></script>

En tant que client nous pouvons désormais ajouter ce type d'action

<script src="https://gist.github.com/framiere/6781016.js"></script>
### OrderBy

Le composant `OrderBy` est écrit en connaissant comme le composant serveur produit le DOM côté client. Nous pouvons alors créer les méthodes `isUp()`, `isDown()`, `up()`, `down()`

<script src="https://gist.github.com/framiere/6781023.js"></script>

En tant que client nous pouvons désormais ajouter ce type d'assertions

<script src="https://gist.github.com/framiere/6781065.js"></script>

### ChooseEnum & Typage

La selection de la civilité est interressante, voici le code 

<script src="https://gist.github.com/framiere/6781023.js"></script>

Cela permet d'avoir côté test unitaire de la completion avec le type de donnée adéquate.

<script src="https://gist.github.com/framiere/6781069.js"></script>

### Autocomplete

La suite est de componentariser les autocomplete qui est un composant plus complexe, celui-ci fait appel a de nombreuses requêtes Xpath 

<script src="https://gist.github.com/framiere/6781030.js"></script>

## AccountEdit

Pour la page d'édition d'un compte, nous avons ceci

<script src="https://gist.github.com/framiere/6781812.js"></script>

## Test client 

Vous l'aurez compris, une fois le code du composant produit, nous pouvons créer des tests totalement décorrolés de leur implémentations

<script src="https://gist.github.com/framiere/6777910.js"></script>

## SeleniumTest

L'objet [AccountSearch](https://gist.github.com/framiere/6781001#file-accountsearch-java) ne contient que des références de composants, leur instanciation est réalisé à la façon du Page Factory Pattern.
Sauf qu'ici, leur instanciation est récursive, une class annotée `@Page` peut avoir des propriétés `@Page`. Les propriétés déjà créées sont wrappées également.

<script src="https://gist.github.com/framiere/6781034.js"></script>

# Test documentés

Après ces pérégrinations techniques, il faut revenir au besoin client final, et savoir communiquer au métier ce que réalise le test.

Pour cela, nous avons ajouté des méthodes qui présentent lors des actions réalisées sur le dom des messages et ajouté [FollowVisually](https://gist.github.com/framiere/6781129) à la classe de test

<script src="https://gist.github.com/framiere/6781039.js"></script>

La notification en elle même est l'appel direct à un composant javascript déjà présent sur la page cliente.

<script src="https://gist.github.com/framiere/6781045.js"></script>

Nous pouvons désormais présenter au client final le test, et pouvons itérer sur ce qui est à tester fonctionnellement.


<object width="425" height="344"><param name="movie" value="http://www.youtube.com/v/yX6HhUohjIk&hl=en&fs=1"></param><param name="allowFullScreen" value="true"></param><embed src="http://www.youtube.com/v/yX6HhUohjIk&hl=en&fs=1" type="application/x-shockwave-flash" allowfullscreen="true" width="425" height="344"></embed></object>