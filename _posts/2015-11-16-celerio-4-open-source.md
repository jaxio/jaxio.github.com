---
layout: french
title: Celerio 4 est Open Source
comments: true
date: 2015-11-16
---

# Celerio 4

Jaxio est heureux d'annoncer que Celerio 4 notre générateur de code est désormais Open Source (Licence Apache v2).

Prérequis:

* [Java Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Maven 3.3.3](https://maven.apache.org/download.cgi)

Pour générer un projet à partir d'une base de données d'exemple il suffit de lancer la commande:

    mvn com.jaxio.celerio:bootstrap-maven-plugin:bootstrap

Pour en savoir plus, consultez la [documentation](/documentation/celerio).

Le code source de Celerio est sur [GitHub](https://github.com/jaxio/celerio).

Les templates de génération sont également Open Source:

* backend: [https://github.com/jaxio/pack-backend-jpa](https://github.com/jaxio/pack-backend-jpa)
* frontend: [https://github.com/jaxio/pack-jsf2-spring-conversation](https://github.com/jaxio/pack-jsf2-spring-conversation)

D'autres packs sont en cours de développement:

* App Java EE 7 complète avec Celerio: [https://github.com/jaxio/javaee-lab](https://github.com/jaxio/javaee-lab)

Nous comptons ajouter des packs prochainement (Angularjs 2 etc...)

Mais vous pouvez aussi créer vos templates... :-)

Happy coding!
