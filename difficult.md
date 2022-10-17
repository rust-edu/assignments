# Difficult Assignments

<ul>
{% for post in site.tags["Difficult"] %}
<li><a href="https://rust-edu.org/assignments{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
</ul>