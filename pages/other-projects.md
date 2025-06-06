---
layout: page
title: Other Art / Creative Projects
permalink: /other-projects
---

## Other Art / Creative Projects

<ul>
  {% assign other_posts = site.categories.other-projects %}
  {% if other_posts %}
    {% for post in other_posts %}
      <li>
        <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
        {% if post.date %} – {{ post.date | date: "%b %-d, %Y" }}{% endif %}
      </li>
    {% endfor %}
  {% else %}
    <li>No “Other” creative work published yet. Stay tuned!</li>
  {% endif %}
</ul>
