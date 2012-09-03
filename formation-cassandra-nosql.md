---
layout: french
title: Formation sur Cassandra, base NoSQL
description: Formation sur Cassandra en partenariat avec Zenika 
---

## Formation sur Cassandra

<div class="row">
<div class="span2">
<a href="http://cassandra.apache.org"><img align="right" src="/images/logo/cassandra-logo.png" align="left"/></a>
<a href="http://www.zenika.com"><img align="left" src="/images/partners/logo-zenika.gif"/></a>
</div>
<div class="span10">
<p>Nos formations sont proposées en partenariat avec la société Zenika.</p>
<p>Inscriptions et tarifs directement sur le site de <a href="http://www.zenika.com/formation_cassandra.php" target="new">Zenika</a>.</p>
</div>
</div>

### Introduction
Cette formation couvre de manière approfondie l'architecture et le fonctionnement opérationnel de Cassandra, une base de données distribuée NoSQL permettant de manipuler une énorme quantité de données structurées. 

Cassandra est reconnue pour ses performances, sa capacité à monter en charge horizontalement et son architecture symétrique sans point unique de défaillance.

**Public**: Administrateurs / Architectes / Développeurs

**Répartition**: 50% théorique, 50% pratique

### Objectifs

* Comprendre l'architecture et le fonctionnement de Cassandra
* Monter un cluster Cassandra
* Se familiariser avec les outils d'administration/monitoring Cassandra

#### Pré-requis:

* Notions de réseau nécessaire
* Etre à l'aise avec une ligne de commande

### Présentation

#### Introduction

* Pourquoi Cassandra
* Origine de Cassandra
* Singularités de Cassandra

#### Data Model Cassandra

* Column / Row / Column Family, Keyspace
* Patterns d'usages

#### Réplication

* Principe de fonctionnement
* Consistent hashing
* Notion de token et de ring
* Stratégie de placement des réplicas

#### Ecriture

* Principe de fonctionnement
* Tuneable consistency
* Exemples de scénario en écriture
* Reprise sur erreur après écriture
* Fonctionnement du Hinted hand-off
* Communication entre les serveurs (gossiping)
* Mise à jour (update)
* Delete

#### Lecture

* Principe de fonctionnement
* Tuneable consistency
* Read repair
* Exemples de scénarios en lecture

#### Redémarrage d'un serveur down

* Problématique du delete distribué
* Reprise des données (node repair)

#### Performance en écriture

* Commit log
* MemTable
* SSTable
* Compaction

#### Performance en Lecture

* Bloom Filter
* Caches
* Index

#### Multi Data Center

* Snitch et NetworkTopologyStrategy
* Local & Each Quorum

#### Outillage et monitoring Cassandra

* nodetool
* cassandra-cli
* JMX
* Architecture SEDA

#### Opérations et travaux pratiques

* Installation Cassandra
* Ecriture / lecture
* Mise en place d'un cluster
* Monitoring
* Les différents fichiers Cassandra
* Calcul de Token
* Ajout d'un noeud dans le cluster
* Sauvegarde à chaud
* Sauvegarde incrémentale
* Restauration d'un noeud
* Import/export de données

