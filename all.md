---
layout: default
---

# All Assignments

<ul>
  {% for post in site.posts %}
    <li>
      <a href="assignments{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>