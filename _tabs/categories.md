---
layout: page
title: Categories
icon: fas fa-stream
order: 1
permalink: /categories/
---

Browse the blog by subject. Algorithm families, data structures, contest notes, and mathematical ideas will each find their place here.

{% if site.categories.size > 0 %}
{% assign sorted_categories = site.categories | sort %}
{% for category in sorted_categories %}
{% assign category_slug = category[0] | slugify | url_encode %}
- [**{{ category[0] }}**]({{ category_slug | prepend: '/categories/' | append: '/' | relative_url }}) — {{ category[1].size }} {% if category[1].size == 1 %}post{% else %}posts{% endif %}
{% endfor %}
{% else %}
> Categories will appear here as soon as the first categorized post is published.
{: .prompt-info }
{% endif %}
