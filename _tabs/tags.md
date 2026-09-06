---
layout: page
title: Tags
icon: fas fa-tags
order: 2
permalink: /tags/
---

A quick index of the techniques and ideas covered on this blog—from graphs and dynamic programming to number theory and implementation details.

{% if site.tags.size > 0 %}
<div id="tags" class="d-flex flex-wrap mx-xl-2">
  {% assign sorted_tags = site.tags | sort %}
  {% for tag in sorted_tags %}
  <div>
    <a class="tag" href="{{ tag[0] | slugify | url_encode | prepend: '/tags/' | append: '/' | relative_url }}">
      {{ tag[0] }} <span class="text-muted">{{ tag[1].size }}</span>
    </a>
  </div>
  {% endfor %}
</div>
{% else %}
> Tags will appear here as soon as the first tagged post is published.
{: .prompt-info }
{% endif %}
