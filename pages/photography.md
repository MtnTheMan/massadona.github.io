---
layout: page
title: "Photography"
permalink: /photography/
---

## Photography Portfolio

<ul>
  {% assign photography_posts = site.categories.photography %}
  {% if photography_posts %}
    {% for post in photography_posts %}
      <li>
        <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
        {% if post.date %} – {{ post.date | date: "%b %-d, %Y" }}{% endif %}
      </li>
    {% endfor %}
  {% else %}
    <li>No photography posts yet. Check back soon!</li>
  {% endif %}
</ul>
