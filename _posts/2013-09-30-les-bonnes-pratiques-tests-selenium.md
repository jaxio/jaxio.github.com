---
layout: french
title: Les bonnes pratiques pour des tests seleniums robustes
comments: true
date: 2013-09-30
---

# Selenium

[Selenium](http://docs.seleniumhq.org/) est un outil qui permet d'effectuer des [tests fonctionnels](http://referentiel.institut-agile.fr/acceptance.html) web automatisés.

Celui-ci permet d'exécuter des scénarii web sur la plupart des [navigateurs](http://docs.seleniumhq.org/about/platforms.jsp#browsers), [plateformes](http://docs.seleniumhq.org/about/platforms.jsp#operating-systems) et [languages](http://docs.seleniumhq.org/about/platforms.jsp#programming-languages).

Cet article propose d'utiliser au mieux le typage de java pour aider dans la réalisation de tests fonctionnels automatisés. 

Si vous êtes impatient et voulez voir directement une vidéo du test Selenium [allez ici](http://www.youtube.com/watch?v=yX6HhUohjIk) et pour le code du test [c'est par là](https://gist.github.com/framiere/6777910).

# Selenium IDE

[Selenium IDE](http://docs.seleniumhq.org/projects/ide/) est un plugin Firefox qui permet de capturer des tests, de les éditer puis de les exécuter.

![Selenium IDE](/images/blog/selenium/selenium-ide-google.png)

Ici est présenté le résultat de l'enregistrement de Selenium IDE pour la recherche du mot `Jaxio` sur [google.fr](http://google.fr) et du click sur le premier lien de la page de résultat.

On ne peut faire plus simple.

# Cas d'usage

Pour comprendre mieux Selenium, et savoir ce que l'on peut lui demander il est nécessaire de prendre un cas d'usage fonctionnel non trivial qui utilise des composants riches.

Pour cela prenons l'application générée par [Celerio](/celerio.html) que vous pouvez reproduire [vous même en ligne](/celerio-service.html) et prenons un cas d'usage plus conséquent.

## Objectif

En tant qu'administrateur je souhaite modifier un compte existant, me déconnecter pour ensuite m'identifier avec, puis me déconnecter pour me réidentifier en tant qu'administrateur et remettre enfin le compte modifié dans l'état précédent.

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

Voici un screencast de ce cas d'usage réalisé "manuellement".

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
    * Sélectionner `homer` dans la liste de completion proposée
    * Sélectionner la ligne où `homer` est affichée
1. Modifier les attributs de ce compte
    * Tapper `cnorris` comme identifiant
    * Tapper `kickass` comme mot de passe
    * Tapper `gmail@chucknorris.com` comme email
    * Choisir `Paris` comme addresse
1. Ajouter un role déjà existant au compte
    * Sélectionner l'onglet `Droits attribués`
    * Cliquer sur le bouton de recherche de droits
    * Tapper `admin` comme nom
    * Cliquer sur rechercher
    * Sélectionner `ROLE_ADMIN`
1. Créer un role et l'ajouter au compte
    * Cliquer sur le bouton de création de droits
    * Tapper `ROLE_GOD`
    * Enregistrer le role
1. Ajouter un document au compte
    * Sélectionner l'onglet `Documents attachés`
    * Cliquer sur le bouton d'ajout de documents
    * Cliquer sur le bouton d'upload
    * Sélectionner un document
1. Sauver le compte
    * Cliquer sur le bouton `sauver`
1. Se déconnecter
    * Cliquer sur le bouton `deconnexion`

etc.

La présentation est assez longue, mais notez que chaque élément de cette liste représente bien une action réalisée par l'utilisateur.

## Résultat brut

La liste complète des actions capturées par l'outil est présenté ci-dessous

<script src="https://gist.github.com/framiere/6777267.js"></script>


## Les faiblesses de ce script

En l'état la capture de Selenium IDE n'est pas utilisable pour les raisons suivantes:

1. Le test est incomplet car il ne reflète pas toutes les actions réalisées
1. Le test ne vérifie pas chacune des actions réalisées
1. Le test est dépendant du temps
1. Le test est dépendant du DOM
1. Le test est dépendant de la langue
1. Le test est dépendant du cookie courant
1. Le test n'est pas refactorable
1. Le test n'est pas composable
1. Le test ne peut s'executer rapidement
1. Le test n'est pas lisible par le métier
1. Le test ne présente pas clairement les actions réalisées

## Axes d'amélioration

Plusieurs axes d'amélioration s'offrent à nous.

### Cookies 

Nous pouvons ajouter du code pour supprimer les cookies

<script src="https://gist.github.com/framiere/6777605.js"></script>

### Refactoring

Nous pouvons refactorer le code pour extraire les actions métiers

<script src="https://gist.github.com/framiere/6777332.js"></script>


### Dom & Page factory pattern

Nous pouvons mettre en oeuvre le [Page factory pattern](https://code.google.com/p/selenium/wiki/PageFactory) qui permet à la fois d'apporter du typage et de résoudre en partie la dépendance forte au DOM et .

<script src="https://gist.github.com/framiere/6777584.js"></script>

### Actions de vérifications

Nous pouvons ajouter des tests en plus pour vérifier le résultat des actions par exemple

<script src="https://gist.github.com/framiere/6778003.js"></script>

### Gestion du temps

Pour s'affranchir du temps de rendu des pages et des modifications du DOM, il est nécessaire d'utiliser [les mécanismes de réessais](http://selenium.googlecode.com/git/docs/api/java/org/openqa/selenium/WebDriver.Timeouts.html) de Selenium de façon judicieuse. L'utilisation de `Thread.sleep()` à tire larigot étant à proscrire.

<script src="https://gist.github.com/framiere/6780992.js"></script>

### Bonne pratiques logicielle

Nous pouvons également appliquer l'ensemble des bonnes pratiques de développement pour améliorer le script original et ainsi le rendre propre et efficace.

### Helpers de WebElement

L'objet [WebElement](http://selenium.googlecode.com/svn/trunk/docs/api/java/org/openqa/selenium/WebElement.html) est un objet de trop bas niveau, nous pouvons utiliser des helpers pour être plus efficaces

<script src="https://gist.github.com/framiere/6781000.js"></script>

<script src="https://gist.github.com/framiere/6782278.js"></script>

La problématique liée à l'utilisation de helpers est que la liste de fonctionnalités qui prennent des `WebElement` est très longue.

La complétion dans votre IDE favori ne pourra pas vous aider sur les opérations possibles pour un `WebElement` et un contexte donné.

Il faudrait que le l'élément porte lui-même les actions qu'il peut réaliser. Ainsi un bouton ne serait pas `webElement`, mais un un objet de class `Button` où seule la méthode `click()` serait disponible.


### Recherche de comptes utilisateurs

La recherche d'utilisateur présente à l'utilisateur les fonctionnalités suivantes:

1. Une barre d'action contenant
    * un bouton pour créer un objet
    * un bouton pour quitter la recherche
1. Un formulaire de recherche
    * avec des composants autocomplete
    * avec des composants d'intervalle de date
    * pouvant être réininitialisé, sauvé, chargé
1. Un tableau
    * avec des headers cliquables pour trier par colonne
    * un composant de pagination
1. Des lignes de résultat que l'on peut au choix supprimer, éditer, visualer et sélectionner

Ces fonctionnalités sont les mêmes sur les autres entités manipulées par l'application.

### AccountSearch

Il faudrait qu'à l'image du code des composants qui produisent la page via le DOM, produire code client qui les consomme via selenium.

Ceci dans le but d'arriver à la description de la page:

<script src="https://gist.github.com/framiere/6781001.js"></script>

Les composants `Table`, `EntityAction`, `Messages`, `OrderBy`, `Autocomplete`, `ManyBooleans`, `ChooseEnum`, `StringRange` doivent être écrits de façon indépendantes des tests.

### EntityAction

Le composant `EntityAction` n'est qu'un aggregat de boutons

<script src="https://gist.github.com/framiere/6781005.js"></script>

### Button
Un bouton est lui-même définit comme suit

<script src="https://gist.github.com/framiere/6781010.js"></script>

Nous pouvons désormais ajouter dans le test ce type d'action

<script src="https://gist.github.com/framiere/6781016.js"></script>
### OrderBy

Le composant `OrderBy` est écrit en connaissant comme le composant serveur produit le DOM côté client. Nous pouvons alors créer les méthodes `isUp()`, `isDown()`, `up()`, `down()`

<script src="https://gist.github.com/framiere/6781023.js"></script>

Dans le test nous pouvons donc réaliser les actions et assertions suivantes:

<script src="https://gist.github.com/framiere/6781065.js"></script>

### ChooseEnum & Typage

La selection de la civilité est interressante, voici le code 

<script src="https://gist.github.com/framiere/6781023.js"></script>

Cela permet d'avoir côté test unitaire de la completion avec le type de donnée adéquate.

<script src="https://gist.github.com/framiere/6781069.js"></script>

### Autocomplete

La suite est de componentariser les autocomplete qui est un composant plus complexe, celui-ci fait appel à de nombreuses requêtes [Xpath](http://www.w3schools.com/xpath/) 

<script src="https://gist.github.com/framiere/6781030.js"></script>

## AccountEdit

Pour la page d'édition d'un compte nous reprenons la même recette pour arriver à ceci: 

<script src="https://gist.github.com/framiere/6781812.js"></script>

## Test client 

Vous l'aurez compris, une fois le code du composant produit, nous pouvons créer des tests totalement décorrolés de leur implémentations.
Votre IDE pourra vous guider sur les actions possible sur chaque composant.

<script src="https://gist.github.com/framiere/6777910.js"></script>

## SeleniumTest

L'objet [AccountSearch](https://gist.github.com/framiere/6781001#file-accountsearch-java) ne contient que des références de composants, leur instanciation est réalisé à la façon du Page Factory Pattern.
Sauf qu'ici, leur instanciation est récursive, une class annotée `@Page` peut avoir des propriétés `@Page`. Il est à noter que les propriétés déjà créées seront également wrappées.

<script src="https://gist.github.com/framiere/6781034.js"></script>

# Test documentés

Après ces pérégrinations techniques, il faut revenir au besoin client final, et savoir communiquer au métier ce que réalise le test.

Pour cela, nous avons ajouté des méthodes qui présentent visuellement les actions effecutées. L'ajout de l'annotation [FollowVisually](https://gist.github.com/framiere/6781129) à la classe de test permet d'activer ces messages.

<script src="https://gist.github.com/framiere/6781039.js"></script>

La notification en elle même est l'appel direct à un composant javascript déjà présent sur la page cliente.

<script src="https://gist.github.com/framiere/6781045.js"></script>

Le highlight des composants cliqués est également présent.

Nous pouvons désormais présenter au client final le test, et pouvons améliorer les messages, itérer sur ce qui est à tester fonctionnellement, et réaliser en direct les nouveaux tests.

<object width="425" height="344"><param name="movie" value="http://www.youtube.com/v/yX6HhUohjIk&hl=en&fs=1"></param><param name="allowFullScreen" value="true"></param><embed src="http://www.youtube.com/v/yX6HhUohjIk&hl=en&fs=1" type="application/x-shockwave-flash" allowfullscreen="true" width="425" height="344"></embed></object>

Le build continu lui execute les tests sans effets visuels, ainsi ceux-ci se déroulent aussi vite que le rendu et la vitesse de processing des pages le permettent.

