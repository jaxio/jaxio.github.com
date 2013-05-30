---
layout: french
title: Jaxio
---

<div id="myCarousel" class="carousel slide hero-unit">
  <ol class="carousel-indicators">
    <li data-target="#myCarousel" data-slide-to="0" class="active"></li>
    <li data-target="#myCarousel" data-slide-to="1"></li>
    <li data-target="#myCarousel" data-slide-to="2"></li>
    <li data-target="#myCarousel" data-slide-to="3"></li>
  </ol>
  <!-- Carousel items -->
  <div class="carousel-inner">
    <div class="active item">
	    <h1>Celerio</h1>
		<h4>Générateur de code Java pour applications orientées données</h4>
		<div class="carousel-caption">
			<p>&gt; Reverse de votre base de données</p>
			<p>&gt; Génération de code configurable</p>
			<p>&gt; Des semaines de R&amp;D gagnées en quelques secondes</p>
		</div>
    </div>
    <div class="item">
	    <h1>Celerio</h1>
		<h4>Restez au top des technos</h4>		
		<div class="carousel-caption">
			<p>&gt; Le code généré suit les derniers standards</p>
			<p>&gt; 100% du code généré est déjà testé</p>
			<p>&gt; Les fonctionnalités avancées sont au rendez-vous</p>
		</div>	
    </div>
    <div class="item">
	    <h1>Celerio</h1>
		<h4>Restez autonome</h4>
	     <div class="carousel-caption">
			<p>&gt; Vous avez la maitrise totale du code généré</p>
			<p>&gt; La qualité du code simplifie l'auto-apprentissage</p>
			<p>&gt; Celerio n'intervient pas à l'exécution</p>
		</div>
    </div>
    <div class="item">
	    <h1>Celerio</h1>
		<h4>Réduisez vos cycles de développements</h4> 
		<div class="carousel-caption">
			<p>&gt; Focus sur la partie métier de l’application sous une semaine</p>
			<p>&gt; Homogénéité du code généré d’un projet à l’autre</p>    
			<p>&gt; Droit au changement et à l'erreur</p>    
		</div>
    </div>
  </div>
  <!-- Carousel nav -->
  <a class="carousel-control left" href="#myCarousel" data-slide="prev">&lsaquo;</a>
  <a class="carousel-control right" href="#myCarousel" data-slide="next">&rsaquo;</a>
</div>

<script type="text/javascript">
$('.carousel').carousel({
  interval: 10000
});
</script>

<!-- Example row of columns -->
<div class="row">
<div class="span4">
	<h1>Evaluer Celerio</h1>
	<ul>
	<li>
		<a href="/celerio-service.html">Evaluer Celerio sur votre base de données, en utilisant la version en ligne de Celerio.</a>
	</li>
	<li>
		<a href="https://github.com/jaxio/generated-projects">Exemple de code déjà généré sur GitHub</a>	
	</li>
	</ul>
</div>
<div class="span4">
	<h1>Success Story</h1>
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
	<h1>Actualités</h1>
	<ul>
    {% for post in site.posts limit:3 %}
    	<li><a href="{{ post.url }}">{{ post.date | date_to_string }} - {{ post.title }}</a></li>
	{% endfor %}
	</ul>
	<p><a class="btn" href="actualites.html">Plus d'actualités &raquo;</a></p>
</div>
</div>
