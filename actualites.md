---
layout: french
title: Actualités Jaxio
---
## Actualités Jaxio

{% for post in site.posts %}
* {{ post.date | date_to_string }}: [{{ post.title }}]({{ post.url }})
{% endfor %}

