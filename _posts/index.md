---
layout: default
---

## Tags

### Site
{{ site.tags }}

### Page
{{ page.tags }}

### Loop
{% for tag in site.tags %}
  Name: {{ tag | first }},
  count: {{ tag | last | size}}
{% endfor %}
