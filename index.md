---
title: elephantum.github.com
---

{% for post in site.posts limit: 3 %}
  <div class="post_title">
    <h1><a href="{{ post.url }}">{{ post.title }}</a></h1>
  </div>
  <div class="post">
    {{ post.content }}
  </div>
  <div class="post_info">
    <p>
    Created on {{ post.date | date:"%Y-%m-%d" }}; <a href="{{ post.url }}#disqus_thread">Comments</a>
    <br />
    Tags: {{ post.tags | array_to_sentence_string }}
    </p>
  </div>
{% endfor %}

{% if site.posts.size > 3 %}
  <hr />
  <h1>Older Posts</h1>
  <ul>
  {% for post in site.posts offset: 3 %}
    <li>{{ post.date | date:"%Y-%m-%d" }} &raquo; <a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
  </ul>
{% endif %}
