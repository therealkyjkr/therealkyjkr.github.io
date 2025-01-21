---

layout: page
title: 일상
permalink: /personal/
---

<ul class="post-list">
  {% for post in site.posts %}
    {% if post.category == "personal" %}
      <li>
        <span class="post-meta">{{ post.date | date: "%Y-%m-%d" }}</span>
        <h3>
          <a class="post-link" href="{{ post.url }}">{{ post.title }}</a>
        </h3>
      </li>
    {% endif %}
  {% endfor %}
</ul>