---
layout: french
title: Jaxio
---
<!-- Main hero unit for a primary marketing message or call to action -->
<div class="hero-unit">
    <h1>CELERIO</h1>
    <h3>La génération de code pour le humains</h3>
	<p><img src="/images/green-check.gif"/> Générez un code propre | 
	<img src="/images/green-check.gif"/> Apprenez du code généré | 
	<img src="/images/green-check.gif"/> Focalisez sur votre savoir faire</p>
    <p><a href="/celerio.html" class="btn btn-primary btn-large">En savoir plus &raquo;</a></p>
</div>

<!-- Example row of columns -->
<div class="row">
<div class="span4">
	<h2>Evaluer Celerio</h2>
	<ul>
	<li>
		<a href="http://www.springfuse.com">Pour évaluer le code généré par Celerio, rendez-vous sur www.springfuse.com, la version en ligne de Celerio.</a>
	</li>
	</ul>
</div>
<div class="span4">
	<h2>Success Story</h2>
	<ul>
	<li>
		<a href="/celerio-livre-blanc.html">Une banque centrale utilise Celerio. Présenté lors du MD Day 2010. &raquo;</a>
	</li>
	<li>
		<a href="reference-steria.html">Steria utilise Celerio pour développer la banque en ligne BforBank... [...]</a>
	</li>
	<li>
		<a href="reference-editeur.html">Celerio est utilisé par Eptica pour le développement d'un nouveau produit réalisé pour répondre aux besoins d'une grande banque. [...]</a>
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
