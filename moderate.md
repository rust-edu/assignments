---
layout: default
---

# Moderate Assignments

<ul>
{% for post in site.tags["Moderate"] %}
<li><a href="{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
</ul>