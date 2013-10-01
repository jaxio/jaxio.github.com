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

Pour cela prenons l'application générée [Celerio](/celerio.html) que vous pouvez reproduire [vous même en ligne](/celerio-service.html).

## Objectif

En tant qu'administrateur je veux modifier un compte existant, m'identifier avec, me déconnecter, et me réidentifier en tant qu'administrateur et remettre le compte dans l'état précédent.

## Actions métier

<object width="425" height="344"><param name="movie" value="http://www.youtube.com/v/WIkJTdsQyFc&hl=en&fs=1"></param><param name="allowFullScreen" value="true"></param><embed src="http://www.youtube.com/v/WIkJTdsQyFc&hl=en&fs=1" type="application/x-shockwave-flash" allowfullscreen="true" width="425" height="344"></embed></object>

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

## Actions utilisateur

Pour faire cela il y a des étapes intermédiaires

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

Pour s'affranchir du temps, il est nécessaire d'utiliser les mécanismes de réessai de Selenium.


{% highlight java %}
    public void waitUntilRemoved(final By by) {
        try {
            until(new ExpectedCondition<Boolean>() {
                @Override
                public Boolean apply(WebDriver from) {
                    from.manage().timeouts().implicitlyWait(0, TimeUnit.SECONDS);
                    try {
                        return from.findElements(by).isEmpty();
                    } catch (NoSuchElementException e) {
                        return true;
                    } finally {
                        from.manage().timeouts().implicitlyWait(driverWaitBeforeStopInSeconds, TimeUnit.SECONDS);
                    }
                }
            });
        } catch (RuntimeException e) {
            error("not removed " + by, e);
        }
    }

    public void waitUntilDisplayed(final WebElement webElement) {
        try {
            until(new ExpectedCondition<Boolean>() {
                @Override
                public Boolean apply(WebDriver from) {
                    return webElement.isDisplayed();
                }
            });
        } catch (RuntimeException e) {
            error("element " + webElement.getTagName() + " is not displayed", e);
        }
    }

    public boolean until(Function<WebDriver, Boolean> function) {
        // iterate until we have no more StaleElementReferenceException
        while (true) {
            try {
                return tryUntil(function);
            } catch (StaleElementReferenceException e) {
                //
            }
        }
    }

    private boolean tryUntil(Function<WebDriver, Boolean> function) {
        // test the function now
        if (function.apply(webDriver)) {
            return true;
        }
        // nope ? ok, once more
        if (function.apply(webDriver)) {
            return true;
        }
        // test for 1 second with very rapid tests
        try {
            if (new WebDriverWait(webDriver, 1).until(function)) {
                return true;
            }
        } catch (Exception e) {
            // no op
        }
        // ok it's still not ready, so let's wait
        return new WebDriverWait(webDriver, driverWaitBeforeStopInSeconds).until(function);
    }
{% endhighlight  %}

## Bonne pratiques

Nous pouvons appliquer toutes les bonnes pratiques de développement pour améliorer le script original en code propre et efficace.

Toutefois, ces actions techniques pour améliorer le code de test masquent un sujet plus important.

# Que voulons nous tester ?

Pour une application de gestion, il me semble que les bénéfices les plus interressants apportés par Selenium sont sur des cas fonctionnels en tant que succession d'action sur l'application, et non en tant que test d'IHM.

Ces cas fonctionnels sont donc décorrélés du dom... et presque du web.

Dans un mode parfait, le cas d'usage devrait exprimé dans un dsl, et celui-ci devrait s'executer.

Ce dsl devrait proposer sous une forme ou une autre les fonctionnalités utilisées pour la production d'IHM.


## Recherche de comptes utilisateurs

La production de recherche d'utilisateur présente les fonctionnalités suivantes:

1. Formulaire de recherche
    a. qui contient des composants autocomplete
    a. qui contient des composants d'interval de date
1. Un tableau
    a. avec des headers cliquable pour trier par colonne
    a. des résultats
    a. un composant de pagination
1. Des lignes de résultat
    a. que l'on peut supprimer
    a. que l'on peut éditer
    a. que l'on peut visualiser
    a. que l'on peut selectionner

Ces fonctionnalités sont les mêmes sur les autres entités manipulées par l'application.

Il faut donc les componentariser pour travailler au mieux.



# Résultat final

<script src="https://gist.github.com/framiere/6777910.js"></script>

1. Les étapes du test sont exprimées clairement
1. Toute action est suivie d'une vérification
1. Le test n'est pas dépendant du temps
1. Le test n'est pas dépendant du DOM
1. Le test n'est dépendant de la langue
1. Le test n'est pas dépendant du cookie courant
1. Le test est refactorable
1. Le test est composable
1. Le test s'execute rapidement