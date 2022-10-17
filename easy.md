---
layout: default
---

# Easy Assignments 9:35 am

<ul>
{% for post in site.tags["Easy"] %}
<li><a href="{{site.baseurl}}{{ post.url }}">{{ post.title }}</a>{{site.baseurl}}{{ post.url }}</li>
{% endfor %}
</ul>