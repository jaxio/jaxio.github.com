---
layout: french
title: Jaxio
---
<!-- Main hero unit for a primary marketing message or call to action -->
<div class="hero-unit">
    <h1>Celerio</h1>
	<p>Industrialisez le développement de vos applications Java EE orientées données
    <p><a href="https://github.com/jaxio/generated-projects" class="btn btn-primary btn-large">Exemple de
     code généré &raquo;</a></p>
</div>

<!-- Example row of columns -->
<div class="row">
<div class="span4">
	<h2>Evaluer Celerio</h2>
	<ul>
	<li>
		<a href="http://www.springfuse.com">Pour évaluer Celerio sur votre base de données, rendez-vous sur www.springfuse.com, la version en ligne de Celerio.</a>
	</li>
	</ul>
</div>
<div class="span4">
	<h2>Success Story</h2>
	<ul>
	<li>
		<a href="/celerio-livre-blanc.html">Une banque centrale utilise Celerio. Présenté lors du MD Day 2010...</a>
	</li>
	<li>
		<a href="reference-steria.html">Steria utilise Celerio pour développer la banque en ligne BforBank...</a>
	</li>
	<li>
		<a href="reference-editeur.html">Celerio est utilisé par Eptica pour le développement d'un nouveau 
		produit réalisé pour répondre aux besoins d'une grande banque...</a>
	</li>
	</ul>
</div>
<div class="span4">
	<h2>Actualités</h2>
	<ul>
    {% for post in site.posts limit:3 %}
    	<li><a href="{{ post.url }}">{{ post.date | date_to_string }} - {{ post.title }}</a></li>
	{% endfor %}
	</ul>
	<p><a class="btn" href="actualites.html">Plus d'actualités &raquo;</a></p>
</div>
</div>
