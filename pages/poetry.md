---
layout: page
title: Poetry / Creative Writing
permalink: /poetry
---

## Poetry / Creative Writing

<ul>
  {% assign poetry_posts = site.categories.poetry %}
  {% if poetry_posts %}
    {% for post in poetry_posts %}
      <li>
        <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
        {% if post.date %} – {{ post.date | date: "%b %-d, %Y" }}{% endif %}
      </li>
    {% endfor %}
  {% else %}
    <li>No poetry pieces posted yet. Stay tuned!</li>
  {% endif %}
</ul>
