---
layout: page
title: Archives
icon: fas fa-archive
order: 3
permalink: /archives/
---

Every write-up, contest reflection, and algorithm note—collected in chronological order.

{% if site.posts.size > 0 %}
{% for post in site.posts %}
- `{{ post.date | date: "%Y-%m-%d" }}` [**{{ post.title }}**]({{ post.url | relative_url }})
{% endfor %}
{% else %}
> The archive will begin with the first published post.
{: .prompt-info }
{% endif %}
