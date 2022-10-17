---
layout: default
---

# All Assignments

<ul>
  {% for post in site.posts %}
    <li>
      <a href="https://rust-edu.org/assignments{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>