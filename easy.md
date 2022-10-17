---
layout: default
---

# Easy Assignments 9:44 am

{{ site.baseurl }}

<ul>
{% for post in site.tags["Easy"] %}
<li><a href="https://rust-edu.org/assignments{{ post.url }}">{{ post.title }}</a>{{ post.url }}</li>
{% endfor %}
</ul>