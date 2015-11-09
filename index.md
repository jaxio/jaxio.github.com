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
		<h4>Générateur de code Java pour applications orientées données</h4>
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
	    Suivez la <a href="/documentation/celerio/">documentation...</a>
	</p>
</div>
<div class="span4">
	<h1>Contribuer</h1>
	<p>
	    <a href="/documentation/celerio/templates.html#learn-by-example">Templates de génération</a> open source...
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
