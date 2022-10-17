---
layout: default
---

# Easy Assignments

<ul>
{% for post in site.tags["Easy"] %}
<li><a href="assignments{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
</ul>