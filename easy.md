---
layout: default
---

# Easy Assignments

<ul>
{% for post in site.tags["Easy"] %}
<li><a href="{{site.baseurl}}{{ post.url }}">{{ post.title }}</a>{{site.baseurl}}{{ post.url }}</li>
{% endfor %}
</ul>