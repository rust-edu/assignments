---
layout: default
---

# Easy Assignments 9:42 am

<ul>
{% for post in site.tags["Easy"] %}
<li><a href="{{ post.url }}">{{ post.title }}</a>{{ post.url }}</li>
{% endfor %}
</ul>