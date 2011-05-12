---
layout: index
title: elephantum.github.com
---

{% for post in site.posts limit: 3 %}

Blog
====

[{{ post.title }}]({{ post.url }})
----------------------------------

{{ post.content }}

{{ post.date | date:"%Y-%m-%d" }}

{% endfor %}

{% if site.posts.size > 3 %}
  {% for post in site.posts offset: 3 %}
    <li>{{ post.date | date:"%Y-%m-%d" }} &raquo; <a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
  </ul>
{% endif %}
