---
layout: page
title: Posts
permalink: /posts/
weight: 5
sitemap:
  priority: 0.9
---

<div class="page">

{% for post in site.posts %}

    {% assign category = site.my_categories | where: "slug", post.category %}
    {% assign category = category[0] %}
    
    {% if category %}
        {% capture category_content %}<a class="label" href="{{ category.url }}">{{ category.name }}</a>{% endcapture %}
    {% endif %}

  	{% capture month %}{{ post.date | date: '%m%Y' }}{% endcapture %}
  	{% capture nmonth %}{{ post.next.date | date: '%m%Y' }}{% endcapture %}
  	
    {% if month != nmonth %}
  		{% if forloop.index != 1 %}
  		</ul>
  		{% endif %}
  		<h1>{% include utils/date_custom_short.html date = post.date %}</h1>
  		<ul class="related-posts">
  	{% endif %}

    {% include page/post-list-item.html %}

{% endfor %}
  	</ul>
</div>
