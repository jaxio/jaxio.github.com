---
layout: french
title: Jaxio
---

<div id="myCarousel" class="carousel slide hero-unit">
  <ol class="carousel-indicators">
    <li data-target="#myCarousel" data-slide-to="0" class="active"></li>
    <li data-target="#myCarousel" data-slide-to="1"></li>
  </ol>
  <!-- Carousel items -->
  <div class="carousel-inner">
    <div class="active item">
	    <h1>Celerio</h1>
		<h4>Générateur de code pour applications orientées données</h4>
		<h4>Nouveau: templates de génération pour Angular 2.0</h4>
		<div class="carousel-caption">
			<p>&gt; Reverse de votre base de données</p>
			<p>&gt; Génération de code configurable</p>
			<p>&gt; Des semaines de R&amp;D gagnées en quelques secondes</p>
		</div>
    </div>
    <div class="item">
	    <h1>Celerio</h1>
		<h4>Générateur de code Java pour applications orientées données</h4>
		<div class="carousel-caption">
			<p>&gt; Templates de génération Open Source</p>
			<p>&gt; 100% du code généré est déjà testé</p>
			<p>&gt; Les fonctionnalités avancées sont au rendez-vous</p>
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
	<h1>Utiliser Celerio</h1>
	<p>
        Suivez notre guide pour générer en quelques minutes vôtre <a href="/documentation/celerio/installation.html">1er projet avec Celerio...</a>
	</p>
</div>
<div class="span4">
	<h1>Open Source</h1>
	<p>
	     Celerio:
	     <ul>
	        <li><a href="https://github.com/jaxio/celerio">Celerio</a></li>	     
         </ul>
	     Template Packs:
	     <ul>
	        <li><a href="https://github.com/jaxio/celerio-angular-quickstart">Web App Angular 2.0 + PrimeNG + Spring Boot</a></li>
	        <li><a href="https://github.com/jaxio/javaee-lab">Web App Java EE 7</a> (No Spring Inside)</li>
	        <li><a href="https://github.com/jaxio/pack-backend-jpa">Backend JPA 2 / Hibernate + Lucene + Spring</a></li>
	        <li><a href="https://github.com/jaxio/pack-jsf2-spring-conversation">Pack JSF 2 + Spring</a></li>
         </ul>
	     Projets associés:
         <ul>
	        <li><a href="https://github.com/jaxio/jpa-query-by-example">JPA Query By Example (avec Spring)</a></li>
         </ul>
	</p>
	<p>
        Vous pouvez aussi <a href="/documentation/celerio/templates.html">écrire vos templates</a>.
	</p>
</div>
<div class="span4">
	<h1>Actualités</h1>
	<ul>
    {% for post in site.posts limit:3 %}
    	<li><a href="{{ post.url }}">{{ post.date | date_to_string }} - {{ post.title }}</a></li>
	{% endfor %}
		<li><a href="actualites.html"> [ ... ] </a></li>
	</ul>
	<p>
</div>
</div>
