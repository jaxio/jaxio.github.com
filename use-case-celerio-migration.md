---
layout: french
title: Migration d'application client-serveur avec Celerio
description: Celerio est une « Software Factory » qui permet d’automatiser en partie le développement informatique des applications web en Java. 
---

# Migration d'application client-serveur avec Celerio

## Ce document a servi de support lors de notre intervention au MD Day 2010.
Voici en téléchargement l'intégralité de l'article :
<a href="/documents/jaxio-migration-application-client-serveur.pdf" onclick="javascript:urchinTracker ('documents/jaxio-migration-application-client-serveur.pdf');"><img src="/images/pdf-icon.png" width="16" height="16"/>Migration d'application client-serveur avec Celerio</a>

## Résumé
Les applications de gestion ont souvent un modèle métier proche du modèle de base de données associé. Une partie des spécifications de ces applications est classique: il faut permettre aux utilisateurs d’effectuer des recherches, de naviguer à travers les entités du modèle en suivant les relations entre elles, de faire des mises à jour, etc. 

Cette particularité rend ces applications fastidieuses à développer si l’on ne dispose pas d’outils pour automatiser la partie technique.

Ainsi, dès les années 70 des éditeurs ont su proposer des AGL puissants aux directions informatiques. Mais aujourd’hui ces AGL arrivent en fin de vie.

Dans ce document nous partageons notre expérience de la migration de plusieurs applications de gestion client-serveur développées avec un AGL propriétaire vers une application Web développée en Java à la Banque de France.

Pour rester maître de la partie purement métier durant la migration et conserver une architecture propre, il a été décidé sciemment de ne pas utiliser d’outils de transformation de modèle à modèle. 

Pour optimiser la tâche des développeurs et assurer une maintenance sur le long terme nous utilisons trois principaux leviers :

* Le modèle entités-relations existant,
* Une architecture évolutive adaptée au développement des applications de gestion basée sur Spring Framework, JPA, JSF, Spring WebFlow, RichFaces, Ajax,
* Le générateur de code Celerio.

Enfin nous décrivons de manière objective les avantages et les contraintes de notre approche et la vision à plus long terme.
