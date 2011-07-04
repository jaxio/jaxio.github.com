---
layout: french
title: Jaxio
---

<div id="homepage">
<style>
div#message {
	background-color: lightgrey;
}
#actualites ul li {
	margin-left: 0px;
}
#homepage a {
color: black;
}

</style>
<section id="message" class="span-24 last">
	<h1>Vous développez des applications orientées données en Java?</h1>
	<h2>
		<a href="/celerio.html">Celerio vous aide à réaliser vos projets...</a>
	</h2>
	<h2>
		<a href="/celerio.html">... dans les temps</a>
	</h2>
	<h2>
		<a href="/celerio.html">... en réduisant vos coûts.</a>
	</h2>
	<h2>
		<a href="/celerio.html">... avec la qualité au rendez-vous</a>
	</h2>
</section>
<section id="springfuse" class="span-8">
	<h1>Springfuse</h1>
	<p>
		<a href="http://www.springfuse.com">Springfuse, la version en ligne de Celerio permet de générer des projets Java à partir d'une base de données (la votre).</a>
	</p>
	<p>
		<a href="http://www.springfuse.com">SLe code généré permet aux juniors de se former par l'exemple et de démarrer un projet en partant sur des bases solides.</a>
	</p>
	<p>
		<a href="http://www.springfuse.com">SPour utiliser Springfuse, rendez-vous sur www.springfuse.com</a>
	</p>
</section>
<section id="success-story" class="span-8">
	<h1>Success story Celerio</h1>
	<p>
		<a href="2010/09/10/md-day-2010-journee-du-model-driven-25-novembre-2010-paris.html">Une banque centrale utilise Celerio. Vous en saurez plus lors du MD Day 2010. [...]</a>
	</p>
	<p>
		<a href="reference-steria.html">Steria utilise Celerio pour développer la banque en ligne BforBank... [...]</a>
	</p>
	<p>
		<a href="reference-editeur.html">Celerio est utilisé par Eptica pour le développement d'un nouveau produit réalisé pour répondre aux besoins d'une grande banque. [...]</a>
	</p>
</section>
<section id="actualites" class="span-8 last">
	<h1>Actualités Jaxio</h1>
	<ul class="homepage">
                {% for post in site.posts limit:5 %}
                <li><a href="{{ post.url }}">{{ post.date | date_to_string }} - {{ post.title }}</a></li>
                {% endfor %}
	</ul>
</section>
</div>
