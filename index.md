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
	<h1>Celerio est un générateur de code pour Java</h1>
	<h3>
		<img src="/images/green-check.gif"/>Reverse de votre base de données
	</h3>
	<h3>
		<img src="/images/green-check.gif"/>Génération d'applications web orientées données 
	</h3>
	<h3>
		<img src="/images/green-check.gif"/>Templates de générations pour Spring, JPA 2, JSF 2 + PrimeFaces , Spring WebFlow, Spring MVC 3, etc...
	</h3>
	<h3>
		<img src="/images/green-check.gif"/>Apprenez du code généré, créez vos templates, itérez, gagnez des semaines!
	</h3>
	<br/>
	<br/>
</section>
<section id="springfuse" class="span-8">
	<h1>Evaluer Celerio</h1>
	<p>
		<a href="http://www.springfuse.com"><img src="/images/green-check.gif"/>Pour évaluer le code généré par Celerio, rendez-vous sur www.springfuse.com, la version en ligne de Celerio.</a>
	</p>
	<p>
		<a href="http://www.springfuse.com"><img src="/images/logo/springfuse.png"/></a>
	</p>
</section>
<section id="success-story" class="span-8">
	<h1>Success story Celerio</h1>
	<p>
		<a href="/celerio-livre-blanc.html"><img src="/images/green-check.gif"/>Une banque centrale utilise Celerio. Présenté lors du MD Day 2010. [...]</a>
	</p>
	<p>
		<a href="reference-steria.html"><img src="/images/green-check.gif"/>Steria utilise Celerio pour développer la banque en ligne BforBank... [...]</a>
	</p>
	<p>
		<a href="reference-editeur.html"><img src="/images/green-check.gif"/>Celerio est utilisé par Eptica pour le développement d'un nouveau produit réalisé pour répondre aux besoins d'une grande banque. [...]</a>
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
