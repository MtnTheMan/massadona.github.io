---
layout: page
title: Videos
permalink: /videos
---

## Videos

<ul>
  {% assign video_posts = site.categories.videos %}
  {% if video_posts %}
    {% for post in video_posts %}
      <li>
        <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
        {% if post.date %} – {{ post.date | date: "%b %-d, %Y" }}{% endif %}
      </li>
    {% endfor %}
  {% else %}
    <li>No videos uploaded yet. Check back soon!</li>
  {% endif %}
</ul>
