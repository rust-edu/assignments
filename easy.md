---
layout: default
---

# Easy Assignments

<ul>
{% for post in site.tags["Easy"] %}
<li><a href="{{ post.url }}">{{ post.title }}</a>{{ post.url }}</li>
{% endfor %}
</ul>