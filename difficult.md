---
layout: default
---

# Difficult Assignments

<ul>
{% for post in site.tags["Difficult"] %}
<li><a href="assignments{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
</ul>