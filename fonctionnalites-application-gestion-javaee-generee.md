---
layout: french
title: Fonctionnalités d'une application générée par Celerio
description: Fonctionnalités d'une application de gestion JavaEE générée par Celerio 
---

Fonctionnalités d'une application générée par Celerio
=====================================================

*Cette documentation se veut fonctionnelle. Elle est intentionnellement dépourvue d'aspects purement techniques (i.e code source Java).
Elle est destinées aussi bien aux MOAs et chefs/directeurs de projets qu'aux développeurs.*



* [Celerio](#celerio)
* [Fonctionnalités de l'application générée](#fonctionnalites-app-generee)
	* [Fonctionnalités des écrans de recherche générés](#fonctionnalites-ecrans-recherche-generes)
	* [Fonctionnalités des écrans d'éditions générés](#fonctionnalites-ecrans-editions-generes)
* [Navigation et Conversation](#navigation)
* [Conversation et Base de Données](#conversation-bdd)
* [Gestion des erreurs](#gestion-erreurs)
* [Gestion du login](#gestion-login)
* [Ergonomie et accessibilité](#ergonomie-accessibilite)
* [Localisation](#localisation)
* [Logs](#logs)

<a name="celerio"></a>
Celerio
-------

### Introduction

Le générateur de code Celerio permet de générer le code source d'une application web Java à partir
d'un schéma de base de données et d'un fichier de configuration.

**Une application générée par Celerio peut servir:**

* de base pour un prototypage rapide permettant la mise au point des spécifications (collaboration MOA/MOE)
* de point de départ pour le développement d'une application métier.

Le code source produit permet aux développeurs de gagner un temps important sur toutes les parties techniques
répétitives du projet, les aide à structurer le développement et à se former.

**Le code généré reste générique. Il traite les cas métiers transverses comme:**

* la recherche 'par l'exemple'
* l'enregistrement en base de donnée
* la validation
* etc.

**Il traite aussi des problématiques d'ergonomie communes comme:**

* l'affichage de messages d'information ou d'erreur
* la mise en page
* la navigation au clavier
* etc.

Enfin, le code généré inclut des composants (Java, Facelets, etc.) pouvant être ré-utilisés dans le code écrit à la main.

Le coeur du métier de l'application cible, notamment les écrans, est développé manuellement par les développeurs mais
dans le cas où une caractéristique métier a un caractère générique, elle peut être prise en compte dans les templates
de générations par les développeurs et ainsi être générée.

En un mot, Celerio ne remplace pas les développeurs, mais se met à leur service.

Dans cette documentation nous utilisons le terme `Entité` pour désigner la représentation dans le monde Java d'une ligne dans une `Table SQL`.

Nous utiliserons le terme `champ ou propriété d'une entité` pour désigner la représentation dans le monde Java d'une `Colonne SQL`.

** Une application générée par Celerio est une application de type `SCRUD`.**

* `S` earch: recherche d'entités
* `C` reate : création d'entité
* `R` ead : consultation d'une entité en lecture seule
* `U` pdate: mise à jour d'une entité
* `D` elete: suppression d'une entité

En fonction de votre besoin, plusieurs types d'applications peuvent être générés. 


### Documentation

* [Manuel de référence de Celerio](http://www.jaxio.com/documentation/celerio/) 
* [Publications Jaxio](/celerio-livre-blanc.html)


### Générer une application d'exemple avec Celerio

La génération d'une application d'exemple permet:

* de découvrir les fonctionnalités offertes par le code généré
* de se familiariser avec la stack technique proposée

#### Génération en ligne

Vous pouvez générer gratuitement une application d'exemple [depuis la cette page](/celerio-service.html).

#### Génération avec Celerio

Pour ceux qui disposent d'une Licence d'utilisation de Celerio, tout se fait directement en ligne de commande:

**Pour générer une application d'exemple**, depuis une console, exécuter la commande suivante:

	mvn com.jaxio.celerio:maven-bootstrap-plugin:3.0.106:bootstrap

Puis suivez les instructions à la console.

Une fois que vous avez répondu aux différentes questions (choix de la stack, nom du projet, package Java principal),
le bootstrap crée quelques fichiers indispensables pour pouvoir générer un projet avec Celerio, dont ceux-ci:

* nomduprojet/pom.xml
* nomduprojet/src/main/sql/h2/01-create.sql : un example de schéma de base de données
* nomduprojet/src/main/config/maven-celerio-plugin.xml : Un exemple de configuration Celerio pour le schéma ci-dessus

Pour lancer la génération, déplacer-vous dans le projet

	cd nomduprojet/

et exécutez la commande suivante:

	mvn -Ph2,db,metadata,gen jetty:run

L'option `-P` ci-dessus active les `profils` maven suivants:

* `h2`: indique que l'on souhaite travailler avec la base H2 (pour info, h2 est utilisé par défaut, donc vous pouvez omettre ce profil). Il y a aussi un profil 'oracle'.
* `db`: indique que l'on souhaite exécuter le script 01-create.sql et donc créer la base de données
* `metadata`: indique que l'on souhaite faire le reverse de la base H2. Le résultat du reverse est dumpé dans le fichier nomduprojet/src/main/config/metadata.xml
* `gen`: indique que l'on souhaite générer le projet. Notez que la génération utilise le fichier metadata.xml

Une fois générée et lancée, ouvrez votre navigateur et rendez-vous sur [http://localhost:8080](http://localhost:8080/)
Pour se loguer, utiliser admin/admin

**Pour relancer l'application**, si ni la base ni la configuration n'ont changé, il suffit d'exécuter:

	mvn jetty:run


**Si seule la configuration a changé**, exécuter:

	mvn -Pgen jetty:run


**Si la base de données a changé**, il faut refaire le reverse, exécuter donc:
	
	mvn -Pdb,metadata,gen jetty:run


<a name="fonctionnalites-app-generee"></a>
Fonctionnalités de l'application générée
----------------------------------------

### Introduction

Pour chaque Table SQL, Celerio génère un écran de recherche principal et un écran d'édition.

L'écran de recherche comporte des champs pour saisir les critères de la recherche et un tableau listant 
les résultats de la recherche.

L'écran d'édition permet l'édition des champs simples ainsi que l'édition des entités liées. 
Une entité liée peut elle même avoir des liens vers une ou plusieurs autres entités. 
Ces liens forment un graphe ayant pour origine l'entité de départ.
Les écrans générés par Celerio permettent à l'utilisateur de naviguer dans ce graphe en mode édition
ou en mode lecture seule.

Les modifications apportées à l'entité et aux entités liées sont propagées en base de données, 
non pas à mesure que l'utilisateur navigue dans le graphe en apportant des modifications, 
mais lorsque l'utilisateur clique explicitement sur le bouton «Enregistrer» présent uniquement sur 
l'écran d'édition de l'entité d'origine.

Le schéma ci-dessous présente les différentes étapes de l'édition:

* L'utilisateur recherche l'entité qu'il souhaite éditer
* L'écran d'édition principal de l'entité sélectionnée permet de gérer l'entité et de naviguer vers les entités liées
* L'utilisateur peut quitter la page d'édition principale et revenir à l'écran de recherche en enregistrant les modifications apportées ou pas.

Le mode 'sub' est utilisé lorsque l'utilisateur navigue dans le graphe de l'entité principal.
Il permet de faire apparaitre les boutons "Envoyer" et "Retour" et de masquer le bouton
"Enregistrer" présent uniquement dans l'écran d'édition principal.

![ex](/images/exemples/app-conversation-graph.png)


<a name="fonctionnalites-ecrans-recherche-generes"></a>
### Fonctionnalités des écrans de recherche générés

Pour chaque table SQL, à l'exception des tables de liaison n-n n'ayant pas de colonne métier, un écran de recherche est généré.

Un écran de recherche comporte des champs pour saisir les critères de la recherche et un tableau listant les résultats de la recherche.

Lorsque l'utilisateur clique sur "Rechercher", une requête Ajax est envoyée au serveur et la zone 
de résultats est rafraichie avec les résultats renvoyés par le serveur.

> La touche "Entrée" du clavier permet aussi de lancer la recherche

#### Critères de recherche saisis par l'utilisateur

A chaque colonne SQL est associée en fonction de sa nature un ou plusieurs champs de recherche.

Les champs de recherche renseignés pour chaque colonne sont utilisés pour construire une condition (prédicat).

Un `ET logique` est appliqué à l'ensemble des prédicats construits pour former la clause `WHERE` de la requête SQL.

Des critères techniques, non visibles par l'utilisateur peuvent être pris en compte coté serveur, par exemple:

* la *case sensitivity*
* les jointures
* l'utilisation d'un `LIKE` ou non sur les champs de type chaine de caractères

##### Champ chaine de caractères (`VARCHAR`)

Un composant permet de saisir le texte devant être présent dans le champ correspondant des entités recherchées.

![exemple](/images/bullet_go.png) **exemple:**

![ex](/images/exemples/search-criteria-string.png)

> ![info](/images/info.png) par défaut ce champ ne propose aucune auto-complétion qui pourrait être calculée coté serveur.

![predicate](/images/bullet_star.png) **Prédicat SQL qui en découle:**

En fonction de la configuration choisie par le développeur, le prédicat prend différentes formes, par défaut nous avons:

	lower(COLONNE) like '%texte_saisi%'


C'est à dire une recherche insensible à la casse, utilisant un `LIKE` avec la wild card `%` ajoutée automatiquement comme préfixe et suffixe.


##### Champ boolean

Un champ boolean peut prendre par définition 2 valeurs. Ces valeurs sont proposées à l'utilisateur sous forme de cases à cocher.
Dans le cas où la colonne correspondante peut aussi prendre une valeur nulle, une case supplémentaire *Non Renseigné* est proposée.

![exemple](/images/bullet_go.png) **exemple:**

![ex](/images/exemples/search-criteria-boolean.png)

![predicate](/images/bullet_star.png) **Prédicat SQL qui en découle:**

Chaque case cochée correspondant à une valeur entraine une condition de type

	COLONNE = valeur_cochée


Si la case *Non Renseigné* est cochée la condition est:

	COLONNE is null


Si plusieurs cases sont coché, un `OU logique` (disjonction) est appliqué, par exemple:

	COLONNE = valeur_cochée OR COLONNE is null


Voir [démo du composant|http://www.primefaces.org/showcase/ui/selectManyCheckbox.jsf]

##### Champ énumération

Un champ énumération prend par définition un jeu prédéterminé de valeurs, ces valeurs sont proposées à l'utilisateur sous forme de cases à cocher.
Dans le cas où la colonne associée peut aussi prendre une valeur nulle, une case supplémentaire *Non Renseigné* est proposée.

![exemple](/images/bullet_go.png) **exemple:**

![ex](/images/exemples/search-criteria-enumeration.png)

![predicate](/images/bullet_star.png) **Prédicat SQL qui en découle:**

Chaque case cochée correspondant à une valeur entraine une condition de type

	COLONNE = valeur_cochée


Si la case *Non Renseigné* est cochée la condition est:

	COLONNE is null


Si plusieurs cases sont coché, un `OU logique` (disjonction) est appliqué, par exemple:

	COLONNE = valeur_cochée OR COLONNE is null


Voir [démo du composant](http://www.primefaces.org/showcase/ui/selectManyCheckbox.jsf)

##### Champ date ou nombre

Pour les dates et les nombres, Celerio génère par défaut 2 champs de saisie pour permettre une recherche par intervalle.

![exemple](/images/bullet_go.png) **exemple nombre:**

![ex](/images/exemples/search-criteria-number.png)

![exemple](/images/bullet_go.png) **exemple date:**

![ex](/images/exemples/search-criteria-date.png)

![predicate](/images/bullet_star.png) **Prédicat SQL qui en découle:**

Si le champ `Du/Entre` est renseigné et que le champ `Au/Et` est laissé vide, le prédicat prend la forme:

	COLONNE >= valeur_DE

Si le champ `Du/Entre` est laissé vide et que le champ `Au/Et` est renseigné, le prédicat prend la forme:

	COLONNE =< valeur_A

Si les 2 champs sont renseignés, le prédicat devient:

	COLONNE BETWEEN valeur_DE AND valeur_A

![info](/images/info.png) Une option désactivée par défaut dans les écrans permet d'inclure dans la recherche les valeurs nulles.
La condition `COLONNE IS  NULL` est alors ajouté avec un OU logique. Par exemple, en reprenant le prédicat ci-dessus on obtient:

	COLONNE BETWEEN valeur_DE AND valeur_A OR COLONNE IS NULL

Voir [démo du composant date](http://www.primefaces.org/showcase/ui/calendarHome.jsf)

##### Cas des "Foreign Keys" (many-to-one)

Pour une colonne de type `Foreign Key`, un champ auto-complete est proposé.
Il permet de rechercher (avec auto-complétion) et sélectionner la ou les cibles possibles que les entités recherchées doivent avoir.

Par défaut le texte saisi est utilisé comme critère de recherche sur toutes les colonnes de
type chaine de caractère de la table cible. La requête SQL construite utilise une jointure avec la table cible.

![exemple](/images/bullet_go.png) **exemple:**

![ex](/images/exemples/search-criteria-may-to-one.png)

![exemple](/images/info.png) Le code généré ne permet actuellement pas de rechercher les entités ayant une foreign key non renseignée.

![predicate](/images/bullet_star.png) *Prédicat SQL qui en découle:*

Si le champ est renseigné avec une ou plusieurs valeurs ou qu'un champ de l'entité cible apparait 
dans la liste des résultats, un `LEFT OUTER JOIN` est réalisé avec la table cible.

Pour chaque valeur du champ une condition d'égalité avec la `Primary Key` de la table cible est appliquée:

	COLONNE = PK_cible_choisie

Si plusieurs valeurs sont renseignées, un `OU logique` (disjonction) est appliqué, par exemple:

	COLONNE = PK_cible_1_choisie OR COLONNE = PK_cible_2_choisie

Voir [démo du composant multi auto-complete](http://www.primefaces.org/showcase/ui/autoCompleteMultiple.jsf)

##### Cas des many-to-many

Si l'entité possède une association many-to-many avec une autre entité, un champ multi auto-complete est proposé.
Il permet de rechercher (avec auto-complétion) et sélectionner la ou les cibles qui doivent être présentes dans 
l'association many-to-many des entités recherchées.

![exemple](/images/bullet_go.png) **exemple:**

![ex](/images/exemples/search-criteria-many-to-many.png)

![exemple](/images/info.png) Le code généré ne permet actuellement pas de rechercher les entités ayant une association vide.

![predicate](/images/bullet_star.png) **Prédicat SQL qui en découle:**

Pour chaque valeur renseigné, en découle un `INNER JOIN` entre la TABLE d'origine et la table intermédiaire 
puis un `INNER JOIN` entre la table intermédiaire et la table cible.

Pour chaque valeur du champ une condition d'égalité avec la Primary Key de la table cible est appliquée:

	COLONNE = PK_cible_choisie

Si plusieurs valeurs sont renseignées, un `ET logique` (conjonction) est appliqué.

#### Fonctionnalités sur les résultats

Les résultats sont affichés à l'aide du [composant DataTable de PrimeFaces](http://www.primefaces.org/showcase/ui/datatableHome.jsf).


#### Pagination serveur

les résultats sont paginés. Seule la première page est affichée. Si besoin des liens permettant
d'aller vers les pages suivantes sont disponibles.

La pagination serveur signifie simplement que seules les données affichées sont chargées depuis la base.

Voir [démo du composant pagination](http://www.primefaces.org/showcase/ui/datatablePagination.jsf)

#### Colonnes triables

Toutes les colonnes sont par défaut triables.
Coté Java, le développeurs peut configurer un tri par défaut.

Si la colonne correspond à une foreign key, le tri est effectué non pas sur la valeur de la foreign
key mais sur une colonne (au choix du développeur) de la table cible. La requête SQL coté serveur aura une jointure avec la table cible.

Le sens du tri est représenté par une flèche haut ou bas.

Voir [démo de tri](http://www.primefaces.org/showcase/ui/datatableSortingMultiple.jsf)

![exemple](/images/info.png) Le tri multiple n'est pas encore pris en compte dans l'application générée (version actuelle 3.0.96)

#### Contenu d'une colonne

L'affichage dépend du type du champ et de sa valeur:

* Si le champ est de type texte et est non nul, il est affiché tel quel
* Si le champ n'a pas de valeur, rien n'est affiché.
* Si champ correspond à une énumération, la valeur logique (cad pas forcément la valeur stockée en base) correspondante est affichée. Cette valeur peut être localisée.
* Si le champs correspond à un boolean (i.e. Vrai, Faux), une icône est affichée: check verte pour Vrai et croix rouge pour Faux.
* Si le champ correspond à une foreign key et que celle-ci est non nulle alors l'entité cible est affichée dans la colonne. Le composant Java XxxPrinter (remplacer Xxx par le nom de l'entité) de l'entité cible est utilisé pour convertir l'entité cible en texte.

#### Actions pour chaque ligne

Les actions possibles dépendent du mode de recherche.

Le mode 'Recherche' ('sub'=false pour les développeurs) est le mode classique utilisé en début de navigation pour choisir l'entité à éditer.

Il se distingue du mode 'Sélection' ('sub'=true pour les développeurs) qui permet de choisir une ou plusieurs entités liées pour la ou les rattacher à l'entité d'origine.

##### En mode Recherche

![exemple](/images/bullet_go.png) Voici un exemple de résultats de recherche.

![ex](/images/exemples/search.png)

`Edition:` Un clic sur l'icône "Crayon" ![ex](/images/exemples/pencil.png), *ou tout simplement sur la ligne*, permet de naviguer vers 
la page d'édition de l'entité correspondante.

`Consultation:` Un clic sur l'icône "Oeil" ![ex](/images/exemples/eye.png) permet de naviguer vers la page d'édition, en mode lecture 
seule, de l'entité correspondante.

`Suppression:` Un clic sur l'icône "Croix rouge" ![ex](/images/exemples/cross.png) permet de supprimer l'entité correspondante.
Une boite de dialogue contextuelle demande la confirmation de la suppression. Par défaut le bouton "Non" est pré-sélectionné.

![exemple](/images/bullet_go.png) **exemple:**

![ex](/images/exemples/search-delete-dialog.png)

##### Mode Sélection ('sub')

Ce mode est utilisé pour rechercher une entité liée et la rattacher à l'entité d'origine.

`Sélection unique:` Sur la droite de chaque ligne une icône permettant de sélectionner l'entité correspondante est affichée.
Un clic sur l'icône "Check verte" ![ex](/images/exemples/accept.png), *ou tout simplement sur la ligne*, permet de sélectionner l'entité
tout en revenant à l'écran précédent.

![ex](/images/exemples/search-selection.png)

`Sélection multiple`: Sur la gauche de chaque ligne une check box permet de sélectionner l'entité correspondante.
Un clic sur l'icône "check verte" ![ex](/images/exemples/accept.png) située en bas à gauche du tableau de résultats permet de 
sélectionner tous les éléments cochés tout en revenant vers l'écran précédent.

![exemple](/images/bullet_go.png) **exemple:**

![ex](/images/exemples/search-selection-multiple.png)

![exemple](/images/info.png) La sélection multiple n'est activée que pour les associations n-n.

#### Cas limite: pas de résultats

Lorsqu'il n'y a pas de résultats à afficher, un message approprié est affiché dans la première ligne du tableau.

#### Affichage du nombre total de résultats

Le nombre total de résultats est affiché au dessus du tableau sur la gauche.
En fonction des cas, le message varie:

* `cas 0 résultat`: *Aucun résultat trouvé*
* `cas 1 résultat`: *Il y a un résultat*
* `cas 2 résultats et plus`: *Il y a N résultats où n est le nombre total de résultats en chiffres.*

#### Alternance de la couleurs des lignes

Les lignes paires et impaires n'ont pas la même couleur afin de faciliter la lecture.

#### Surbrillance de la ligne au survol du curseur

La ligne survolée par le curseur de la souris change de couleur pour simplifier la lecture.

#### Réaction au clic sur une ligne de résultat

L'utilisateur peut cliquer directement sur une ligne de résultat pour éditer l'entité correspondante.

#### Ligne sélectionnée mémorisée

La ligne sélectionnée est mémorisée. Lorsque l'utilisateur revient sur l'écran de recherche après avoir
édité une entité sélectionnée, la ligne correspondant à l'entité apparait en surbrillance.

#### Alignement des colonnes

Par défaut le contenu des colonnes est centré



<a name="fonctionnalites-ecrans-editions-generes"></a>
### Fonctionnalités des écrans d'édition générés

Pour chaque table SQL, à l'exception des tables de liaison n-n n'ayant pas de colonne métier, un écran d'édition est généré.

#### Edition des champs simples

Les champs simples ont leur label positionné sur la gauche mais aligné à droite de manière à rendre constant l'espace entre le label et le champ.

Lorsque le champ est requis, il est en gras suivi d'un astérisque.

##### Chaine de caractère courte

Un simple champ de saisi est proposé.

![exemple](/images/bullet_go.png) **exemple:**

![ex](/images/exemples/edit-string-required.png)

##### Chaine de caractère longue

Un champ de saisi sur plusieurs ligne (avec auto-resize) est proposé.

Voir [composant input text area](http://www.primefaces.org/showcase/ui/inputTextarea.jsf)

##### Nombre

Un simple champ de saisi est proposé. Un [composant spinner](http://www.primefaces.org/showcase/ui/spinner.jsf) peut être utilisé à la place.

![exemple](/images/warning.gif) Attention le spinner ne permet pas de laisser le champ vide (nul).

##### Date

Un composant calendrier est proposé. La saisie de la date à la main est possible. Le format à respecter est yyyy-mm-dd, par exemple 2013-02-15

![exemple](/images/bullet_go.png) **exemple:**

![ex](/images/exemples/edit-date.png)

Voir [composant calendar|http://www.primefaces.org/showcase/ui/calendarBasic.jsf]

##### Date avec heure

Idem que pour le champ date avec en plus l'heure. Le format est par défaut est
yyyy-mm-dd hh:mm

##### Boolean

Une checkbox 2 états est proposée.

![exemple](/images/info.png) Si la valeur nulle doit pouvoir être renseignée, un autre composant doit être utilisé.

![exemple](/images/bullet_go.png) **exemple:**

![ex](/images/exemples/edit-boolean.png)

Voir [composant boolean checkbox|http://www.primefaces.org/showcase/ui/selectBooleanCheckbox.jsf]

##### Enumération

Un radio bouton est proposé par valeur possible.

![exemple](/images/bullet_go.png) **exemple:**

![ex](/images/exemples/edit-enumeration.png)

![exemple](/images/info.png) La valeur nulle n'est pas proposée par défaut.

Voir [composant radio|http://www.primefaces.org/showcase/ui/selectOneRadio.jsf]

#### Edition des associations

##### Gestion et édition des champs de type foreign key (x-to-one)

Pour ce type champ plusieurs actions sont possible:

* ![ex](/images/exemples/find.png) Recherche et sélection d'une nouvelle cible à positionner dans le champ. Cette action entraine l'utilisateur vers l'écran de recherche correspondant. Depuis l'écran de recherche, l'utilisateur peut soit revenir (bouton «Retour») soit sélectionner une entité depuis la liste des résultats.
* ![ex](/images/exemples/add.png) Création d'une nouvelle cible et positionnement de celle ci dans le champ
* ![ex](/images/exemples/pencil.png) Edition de la cible déjà renseignée. Cette action entraine l'utilisateur sur l'écran d'édition de l'entité correspondante.
* ![ex](/images/exemples/eye.png) Consultation de la cible déjà renseignée. Cette action entraine l'utilisateur sur l'écran d'édition, en mode lecture seule, de l'entité correspondante.

![exemple](/images/bullet_go.png) **exemple:**

![ex](/images/exemples/edit-many-to-one.png)

L'utilisateur peut également saisir directement du texte dans le champ et récupérer des suggestions sélectionnables (auto-complétion).

![ex](/images/exemples/edit-many-to-one-auto-complete.png)

Voir [composant auto-complete|http://www.primefaces.org/showcase/ui/autoCompleteBasic.jsf]

##### Gestion et édition des entités pointant vers l'entité en édition (one-to-many)

Le développeur peut configurer Celerio pour que les tables pointant vers l'entité en cours d'édition soient visibles depuis la page d'édition de cette dernière.

Les entités ainsi liées sont présentées dans un tableau non paginé, dans un Tab dédié.

**Les actions possibles sont:**

* ![ex](/images/exemples/add.png) ajout par création d'une entité liée

** Et pour chaque ligne:**

* ![ex](/images/exemples/eye.png) consultation de l'entité liée
* ![ex](/images/exemples/pencil.png) édition de l'entité liée
* ![ex](/images/exemples/cross.png) suppression de l'entité liée (en ajax, avec au préalable un dialogue modal de confirmation contextuel)

![exemple](/images/bullet_go.png) **exemple:**

![ex](/images/exemples/edit-tab-one-to-many.png)

![exemple](/images/info.png) Le Tab sélectionné est mémorisé. Ainsi lorsque l'utilisateur revient sur la page d'édition le Tab attendu est bien sélectionné.

L'édition ou l'ajout d'une entité liée entraine l'utilisateur vers l'écran d'édition de l'entité correspondante.

Cet écran secondaire d'édition ne comporte pas de bouton "Enregistrer" mais un bouton "Envoyer" permettant 
à l'utilisateur de soumettre ses modifications pour qu'elles soient prises en compte lorsqu'il cliquera sur
le bouton "Enregistrer" de l'écran d'édition principal.

Depuis cet écran secondaire, l'utilisateur peut s'il le souhaite revenir en arrière sans soumettre le formulaire
en cliquant soit sur le bouton "Retour" prévu à cet effet soit en cliquant sur le fil d'Ariane.

![exemple](/images/info.png) Par défaut la suppression d'une entité liée en one-to-many `entraine bien` la suppression de la ligne
correspondante en base de données lorsque l'utilisateur clique sur «Enregistrer» dans l'écran principal d'édition.

##### Gestion et édition des entités liées par une table intermédiaire (many-to-many)

Tout comme pour les entités liées par une association de type one-to-many, les entités liées en many-to-many,
c'est à dire via une table intermédiaire technique non visible par l'utilisateur, sont présentées dans un
tableau non paginé dans un Tab dédié.

**Les actions possibles sont:**

* ![ex](/images/exemples/find.png) ajout par sélection d'une ou plusieurs entités existantes: Cette action entraine l'utilisateur vers l'écran de recherche correspondant. Depuis l'écran de recherche, l'utilisateur peut soit revenir (bouton «Retour») soit sélectionner une ou plusieurs entités depuis la liste des résultats.
* ![ex](/images/exemples/add.png) ajout par création d'une entité liée

**Et pour chaque ligne:**

* ![ex](/images/exemples/eye.png) consultation de l'entité liée
* ![ex](/images/exemples/pencil.png) édition de l'entité liée
* ![ex](/images/exemples/cross.png) suppression de l'entité liée (en ajax, avec au préalable un dialogue modal de confirmation contextuel)

L'édition ou l'ajout d'une entité liée entraine l'utilisateur vers l'écran d'édition de l'entité correspondante.

![exemple](/images/info.png) Par défaut la suppression d'une entité liée en many-to-many **n'entraine pas** sa suppression de la base de
données lorsque l'utilisateur clique sur «Enregistrer».

![exemple](/images/bullet_go.png) **exemple:**

![ex](/images/exemples/edit-tab-many-to-many.png)


#### Validation et messages d'erreur

##### Affichage

Dans le code généré nous avons pris le parti de ne pas afficher les messages d'erreurs à coté des champs pour 
simplifier la mise en page et proposer une solution accessible.

Les messages d'erreurs sont tous affichés sous la barre de menu.

Chaque message est cliquable et permet de positionner le curseur directement dans le champ concerné.
Par défaut le texte signalant qu'il y a une ou plusieurs erreurs récupère le focus lorsqu'il apparait.
En appuyant sur "Entrée" (clavier) l'utilisateur passe le focus au premier message d'erreur.
En appuyant de nouveau sur "Entrée", le curseur est positionné dans le champ en erreur correspondant.

![exemple](/images/info.png) Les lecteurs d'écrans lisent le texte ayant le focus.

![exemple](/images/bullet_go.png) **exemple:**

![ex](/images/exemples/validation.png)

##### Validation par défaut

###### Colonne NOT NULL

Pour toute colonne NOT NULL dans la base de données, le code généré applique une validation de type "requis".
Le label du champ apparait en gras.

###### Contrainte d'unicité
Pour toute colonne ayant une contrainte d'unicité en base, le code généré fait une vérification en base lors 
de la validation du champ correspondant. En cas de non respect de la contrainte d'unicité, un message d'erreur approprié est affiché.

![exemple](/images/bullet_go.png) **exemple:**

![ex](/images/exemples/validation-unique.png)



<a name="navigation"></a>
### Navigation

#### Navigation et Conversation

##### Conversation

Chaque fois qu'un utilisateur accède à une page de recherche principale depuis un menu ou la page d'accueil,
il débute ce que nous appelons une nouvelle Conversation. Cette 'Conversation' lui permet entre autres choses
d'éditer une entité et les entités qui y sont éventuellement rattachées selon une navigation entre écrans très stricte.
Chaque écran à l'intérieur de la conversation peut être vu comme un état dans lequel on peut entrer et sortir sous certaines conditions.

##### Boutons Navigateur et Conversation

L'utilisateur ne doit pas utiliser les boutons 'Back' et 'Forward' du navigateur mais doit utiliser les boutons
ou icônes mises à sa disposition dans les écrans. Le cas échéant le serveur redirigera l'utilisateur
vers la page courante de la conversation.

##### Liens non contrôlés par la Conversation

Certains liens comme le lien vers la page d'accueil ne sont pas contrôlés par la Conversation.
Si ils sont cliqués l'utilisateur perd les données fraichement saisies (c'est à dire jamais soumises) et 'quitte'
la conversation sans qu'elle soit perdue. Il peut ensuite y revenir en utilisant le menu 'Conversations'.

##### Plusieurs Conversations en parallèle

L'utilisateur peut ouvrir plusieurs conversations en parallèle et passer de l'une à l'autre à l'aide du menu 'Conversations'.
Les liens du menu 'Conversations' ne sont pas contrôlés par la Conversation courante (voir explication ci-dessus).

Dans l'exemple ci-dessous, l'utilisateur a 2 conversations ouvertes. Le lien vers la conversation courante est désactivé.

![exemple](/images/bullet_go.png) **exemple:**  

![ex](/images/exemples/conversation-menu.png)

##### Limitation du nombre de conversations

Par défaut le nombre maximum de conversation que l'utilisateur peut ouvrir en parallèle est fixé à 5.
Si l'utilisateur tente d'ouvrir plus de conversations, une page d'erreur explicite est affichée:

![exemple](/images/bullet_go.png) **exemple:**  

![ex](/images/exemples/conversation-max-error.png)

##### Désactiver les conversations simultanées

Si vous ne souhaitez pas que l'utilisateur puisse ouvrir plusieurs conversations en parallèle,
il suffit de configurer le nombre maximum de conversations ouvrables en parallèle à 1.

##### Terminer une conversation

Pour terminer une conversation il suffit de cliquer sur le bouton "Quitter" de la page de recherche principale,
page qui est la première page rencontrée quand on démarre une conversation.

#### Barre de menu

##### Page de recherche principale

La barre de menu sur la page de recherche principale permet les actions suivantes:

* Quitter la page: ce qui termine la Conversation.
* Créer une nouvelle entité en cliquant sur "Nouveau".

![exemple](/images/bullet_go.png) **exemple:**

![ex](/images/exemples/menubar-search.png)

##### Ecran édition principale

La barre de menu sur la page d'édition principale permet 2 actions:

* Fermer
* Supprimer

![exemple](/images/info.png) Notez que le bouton "Supprimer" est désactivé s'il s'agit d'une création d'entité.

![exemple](/images/bullet_go.png) **exemple:**

![ex](/images/exemples/menubar-edit.png)

###### Fermer

*Un clic sur "Fermer"* ouvre systématiquement un dialogue modal demandant à l'utilisateur d'enregistrer ses éventuelles modifications.

![exemple](/images/bullet_go.png) **exemple:**

![ex](/images/exemples/menubar-dialog-close.png)

![exemple](/images/warning.gif) D'un point de vu technique, faire apparaitre le dialogue ci-dessus uniquement dans le cas où l'utilisateur
aurait apporté des modifications, est lourd à mettre en oeuvre de manière systématique.

Si l'utilisateur **clique sur "Oui"** et que la validation passe. L'entité est enregistrée en base de données,
le dialogue disparait et l'écran de recherche est affiché. Un message signale à l'utilisateur que l'enregistrement a réussi.

![exemple](/images/info.png) Le cas où l'utilisateur clique sur "Oui" alors qu'il a préalablement saisi des données invalides est traité:
le dialogue disparait et les messages d'erreur sont affichés correctement sur la page d'édition.

Si l'utilisateur **clique sur "Non"** le dialogue disparait et l'écran de recherche est affiché.
Ses éventuelles modifications sont perdues.

###### Supprimer

**Un clic sur "Supprimer"** ouvre un dialogue modal demandant à l'utilisateur de confirmer la suppression. "Non" est présélectionné.
S'il clique sur "Non", le dialogue disparait.

![exemple](/images/bullet_go.png) **exemple:**

![ex](/images/exemples/menubar-dialog-delete.png)

S'il clique sur "Oui", l'entité est supprimée de la base de donnée et l'écran de recherche est affiché.
Un message signalant la suppression est affiché.

![exemple](/images/bullet_go.png) **exemple:**

![ex](/images/exemples/delete-message.png)

##### Page de recherche pour sélection d'entité liée

La barre de menu sur la page de recherche (en mode Sélection, 'sub'=true pour les développeurs) permet une seule action:

* Retour

![exemple](/images/bullet_go.png) **exemple:**

![ex](/images/exemples/menubar-select.png)

![exemple](/images/info.png) L'action "Retour" permet de ne rien sélectionner.

##### Page d'édition d'entité liée

La barre de menu sur la page d'édition d'une entité liée ('sub'=true pour les développeurs) permet une seule action:

* Retour

![exemple](/images/bullet_go.png) **exemple:**

![ex](/images/exemples/menubar-select.png)

![exemple](/images/info.png) L'action "Retour" ne soumet aucune donnée.
![exemple](/images/warning.gif) L'action "Retour" n'annule pas les changements qui auraient pu être apportés à l'entité liée.

#### Navigation et Validation

Lorsque l'utilisateur édite une entité, il peut naviguer dans le graphe de l'entité pour éditer les entités liées.

Mais que faut-il faire lorsque l'utilisateur souhaite naviguer vers un écran d'édition secondaire (entité liée) alors
qu'il y a des données invalides ou incomplètes dans l'écran d'édition principal ?

**Il y a 3 possibilités techniquement faisables:**

* Bloquer la navigation et afficher des messages d'erreur: le plus simple pour les développeurs mais peut être, en fonction des cas, contraignant pour les utilisateurs.
* Permettre la navigation sans conserver les valeurs fraichement saisies, qu'elles soient valides ou pas: très simple également pour les développeurs mais probablement inacceptable pour les utilisateurs.
* Permettre la navigation en conservant toutes valeurs, qu'elles soient valides ou pas.

La politique à adopter dépend du métier. **Par défaut l'application générée par Celerio adopte la 3ème solution**.

![exemple](/images/important.png) La 3ème solution est adaptée pour l'entité principale.
En revanche pour les entités liées il est conseillé d'utiliser la 1ère solution pour éviter le 
cas où l'utilisateur déciderait, après avoir saisi des données invalides et navigué en avant dans le graphe 
depuis l'entité liée, de finalement revenir vers l'entité principal à l'aide de l'action "Retour". 

#### Navigation via le fil d'Ariane

L'application générée propose un fil d'Ariane lors de l'édition d'une entité. Pour des raisons techniques seul
l'avant dernier élément du fil d'Ariane est cliquable. Un clic sur cet élément revient à cliquer sur le bouton "Retour".

![exemple](/images/info.png) Le fil d'Ariane n'apparait pas sur l'écran de recherche principal.

![exemple](/images/bullet_go.png) **exemple:**

![ex](/images/exemples/navigation-fil-ariane.png)



<a name="conversation-bdd"></a>
### Conversation et Base de Données

Une conversation permet d'éditer une entité (et son graphe) de manière isolée, sans pour autant poser de
lock en base de données. Dans un environnement multi-utilisateurs, des conflits peuvent apparaitre si 2 utilisateurs
cherchent à modifier la même entité (qu'elle soit principale ou liée) depuis 2 conversations actives en même temps.

#### Fraicheur des données

##### Recherche

Au cours d'une même conversation, tous les écrans de recherches (popup auto-complete comprises) rencontrés présentent
à l'utilisateur des données fraiches c'est à dire des données correspondant à ce qu'il y a en
base de données au moment de la recherche.

##### Edition

Au cours d'une même conversation, les données présentées sur les pages d'éditions ne sont chargées qu'une seule
fois, même si vous faites des va-et-vient entre les écrans d'édition ou forcez le rechargement de la page à
l'aide du bouton de votre navigateur.

![exemple](/images/important.png) Au cours d'une édition il se peut donc que vous soyez en train de manipuler des données qui ont été entre 
temps modifiées par un autre utilisateur.

#### Conflit entre modifications concurrentes

Pour une table SQL donnée, l'optimistic locking est mis en oeuvre par le code généré par Celerio si la table
comporte une colonne pour le Versionning.

Dans ce cas, il est possible de détecter les modifications concurrentes au moment ou l'utilisateur enregistre ses modifications.

En cas d'erreur de modification concurrente, par défaut le code généré par Celerio termine la conversation 
courante et affiche un message d'erreur.

![exemple](/images/bullet_go.png) **example:**

![ex](/images/exemples/conversation-optimistic-locking.png)

Bien entendu ce comportement peut être ajusté en fonction du besoin métier. 

![exemple](/images/important.png) Présenter à l'utilisateur recevant ce message ce qui a été modifié par l'autre utilisateur est assez complexe à
mettre en oeuvre de manière systématique. Le code généré par Celerio ne couvre pas ce cas.

> ![exemple](/images/info.png) Il est fortement recommandé d'avoir une colonne dédiée au versionning 
> dans toutes vos tables SQL pour permettre la détection de modifications concurrentes.



<a name="gestion-erreurs"></a>
### Gestion des erreurs

#### Page non trouvée

Si l'utilisateur demande une page qui n'existe pas, une page "not found" est affichée.
Cette page comporte les menus habituels pour que l'utilisateur puisse revenir rapidement dans le droit chemin.

#### Erreur grave

En cas d'erreur grave, une page d'erreur est affichée.
En mode développement vous pouvez tester cette page en utilisant le menu "Debug" de la barre de menu.

![exemple](/images/bullet_go.png) **exemple:**

![ex](/images/exemples/menu-debug.png)

#### Expiration de session

Ce cas est traité  même dans le cas où l'expiration de session est détectée par le serveur lors de l'arrivée d'une requête Ajax.
L'utilisateur est renvoyé sur la page de login avec un message approprié.



<a name="gestion-login"></a>
### Gestion du login

#### Page de login

Une page de login simple est générée.

![exemple](/images/bullet_go.png) **exemple:**

![ex](/images/exemples/login.png)

#### Logout

Un lien permet de se déconnecter.


<a name="ergonomie-accessibilite"></a>
### Ergonomie et accessibilité

L'application générée prend en compte certaines règles d'ergonomie et d'accessibilité.

#### Gestion du focus

* Lorsqu'une page de recherche s'affiche, le focus est donné au premier champ de recherche.
* Lorsqu'une page d'édition s'affiche, le focus est donné au premier champ d'édition.
* Lorsqu'une page d'édition s'affiche en mode lecture seule, le focus est donné au bouton "Retour".
* Lorsqu'un dialogue de confirmation apparait, le bouton le plus naturel récupère le focus.

#### Gestion d'"Entrée" au clavier

* Appuyer sur la touche "Entrée" depuis une page de recherche revient à cliquer sur le bouton "Rechercher".
* Appuyer sur la touche "Entrée" depuis une page de d'édition principale revient à cliquer sur le bouton "Enregistrer".
* Appuyer sur la touche "Entrée" depuis une page de d'édition secondaire revient à cliquer sur le bouton "Envoyer".


<a name="localisation"></a>
### Localisation

Toutes les textes des pages sont dans des fichiers de ressources.
L'application générée par Celerio permet de passer du français à l'anglais.

### Bandeau environnement de déploiement

Un bandeau correspondant à l'environnement de déploiement est affiché dans le coin haut gauche de tous les écrans.
Les différents environnements sont:

* Développement
* Intégration
* Recette
* Production (en général on n'affiche pas le bandeau...)

![exemple](/images/bullet_go.png) **exemple:**

![ex](/images/exemples/bandeau-dev.png)

<a name="logs"></a>
### Logs

Chaque ligne de log contient l'id de la session utilisateur ainsi que son login. Ceci permet d'extraire des logs facilement
l'intégralité d'une session d'un utilisateur donné.




