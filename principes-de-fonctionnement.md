---
layout: french
title: Le principe de fonctionnement de l'usine logicielle Celerio
description: Modèle de base de données + convention = Génération socle technique 
---

# Celerio est pragmatique
## Principe de fonctionnement

Pour fonctionner Celerio prend en entrée le modèle le plus classique qui soit, le modèle entité-relation utilisé par toutes les bases de données relationnelles. 

A partir de ce modèle, qui est bien connu et maitrisé des développeurs, mais également de conventions simples et/ou  d’un fichier de configuration, 
Celerio génère automatiquement le code source Java couvrant les problématiques techniques transverses telles que 

* la sécurité, 
* la persistance des données, 
* la répartition de charge, 
* l'internationalisation, 
* le cache de données, 
* etc.

<img src="/images/celerio/celerio-principes.png" alt="Principe de fonctionnement de Celerio"/>


Le socle technique, généré en quelques secondes, correspond à ce qu’une équipe d’ingénieurs
expérimentés mettrait au point au bout d’un temps se chiffrant en plusieurs semaines voire
en plusieurs mois selon la complexité du modèle.

Ce socle est écrit suivant les règles de l’art et utilise les meilleures technologies Open Source.
Votre applicatif métier utilise les composants générés prêts à l’emploi.

