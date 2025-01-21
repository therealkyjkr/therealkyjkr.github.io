---

layout: category
title: 일상
permalink: /personal/
---

<h1>{{ page.title }}</h1>

<ul>
  {% for post in site.posts %}
    {% if post.category == "personal" %}
      <li>
        <a href="{{ post.url }}">{{ post.title }}</a>
        <small>{{ post.date | date: "%Y-%m-%d" }}</small>
      </li>
    {% endif %}
  {% endfor %}
</ul>
